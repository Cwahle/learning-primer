# Message Brokers

**Date:** 2026-07-15
**Participant:** Connor
**Context:** Learning session anchored to the `ais-tracker` project (Python AIS vessel tracker for the lower Mississippi). Teaching mode per `learning-context.md`: scaffold-don't-solve, make-me-retrieve, first-principles, direct correction, connect back to tracked skills, flag writing typos.

**Concepts covered:** producer/consumer/message · decoupling (space/time/rate) · work queue vs. pub/sub · scatter-gather (fan-out/fan-in) · acknowledgments (acks) · durability / durable subscriptions · delivery semantics (at-most-once / at-least-once / exactly-once) · idempotency · effectively-once · failure-mode asymmetry (loud vs. silent failure).

---

## Connor

> i would like to learn more about message brokers, use the attached primer document.

## Claude

Good topic to anchor to `ais-tracker` — it's almost the textbook motivation for a broker, so start from the *problem* rather than the definition.

Right now the tracker is (presumably) one Python process: it holds the `aisstream.io` WebSocket open, and as each vessel message arrives it does the work inline — check mile-marker crossings, evaluate ETA triggers, fire the Discord notification. One script, one loop, everything in sequence.

That works until it doesn't, and it breaks in a specific way: everything in that loop is **coupled**. Three flavors:

- **In time** — the Discord notifier and the mile-marker alerter run in lockstep because they're the same process on the same loop.
- **In rate** — if the Discord API takes 4 seconds to answer, the loop is *stuck* for 4 seconds. AIS messages arrive the whole time and either queue in memory or get dropped. One slow consumer starves the fast ones.
- **In code** — adding a database logger or web dashboard that also needs those positions means editing the one script and threading data through by hand.

A message broker is the infrastructure that severs that coupling — an intermediary placed *between* whoever produces messages and whoever consumes them. Vocabulary: a **producer** publishes a **message** to the broker; the broker holds and routes it; one or more **consumers** read it.

The first-principles idea: **the producer no longer knows or cares who is listening, and the consumers don't know or care who produced.** Each side only knows the broker. From that one property comes decoupling in *space* (they don't reference each other), *time* (a consumer can be offline and catch up later), and *rate* (each consumer drains at its own speed).

> **[Diagram shown: ais-tracker before/after a broker.]** Before — a single Python process ingests AIS data and runs Discord → mile-marker → ETA in one blocking loop. After — an ingest service publishes to a broker, which holds and routes to three independent consumers (Discord notifier, mile-marker alerter, ETA trigger).

Two connections:

- Connor is *already* a consumer of a message stream — `aisstream.io` publishes AIS positions and the program subscribes over a WebSocket. Running his own broker just pulls that same producer/consumer pattern one hop downstream, inside his system.
- Threads back to other tracks: **Docker** (brokers are the canonical `docker run` workload — RabbitMQ/Kafka ship as images; namespace/cgroup isolation keeps the process boundary clean), **netcat/raw TCP** (a broker is a server on a TCP port speaking a wire protocol — AMQP, MQTT, Kafka's own — same "daemon on a port, speak its language" model as Bandit L14–15), **OpenSSL/TLS** (production broker connections are usually TLS-wrapped, same `s_client` world).

**Retrieval question:** In the current single-loop design, the Discord API hangs for 5 seconds and a towboat crosses a mile marker during that window. What happens to that crossing alert, and why? Then — with a broker in the middle, what changes, and what must the broker *do* with the message in the meantime for the alert to survive?

---

## Connor

> The mental image I'm getting is a deli counter: the customer grabs a ticket (sending data to the broker), and when there's an opening the deli worker (the broker) calls the customer and gives them what they need (sending data out to whatever process needs/can handle the task).

## Claude

Good instinct — and the most important thing in that picture is the ticket. That's the decoupling: the customer doesn't stand frozen at the counter blocking everyone behind them; they grab a ticket, and the interaction is now mediated by a stub of paper.

**Correction (not a nitpick):** the broker is *not* the deli worker. The broker is the **ticket dispenser plus the "now serving" board**. The deli worker who slices the meat and hands over the sandwich is the **consumer**.

Why it's load-bearing: the entire reason a broker buys you anything is that it does *not* do the work. It holds tickets and routes them, full stop. The moment the broker starts slicing meat, it's blocked while it slices — right back to the coupled single loop. A good broker is deliberately dumb: take message, keep it safe, hand it out. All the thinking lives in the consumers.

Corrected mapping:

- producer = the customer placing an order
- message = the order itself
- broker = ticket machine + now-serving board (holds and routes, does no work)
- consumer = the deli worker who pulls the next ticket and fulfills it

Deeper point: the deli is a **work queue** — one customer, one ticket, served *once* by whichever worker is free. Useful (spread a backlog across workers). But `ais-tracker` needs each mile-marker crossing to reach *all three* consumers — that's **pub/sub** (publish/subscribe), fan-out, where the broker copies each message to every interested subscriber. Connor's project is a pub/sub problem.

Also: Connor effectively answered the prior retrieval question — the ticket doesn't vanish while the counter's busy, which is why the crossing survives a 5-second Discord hang; the message sits in the broker until a consumer is ready. In the old single-loop design there was no ticket at all — a stuck loop meant the crossing flew past unprocessed. Holding the message durably in the meantime is the broker's core job.

**Retrieval question:** Using the corrected roles (broker = ticket system, workers = consumers), re-map the deli so it does fan-out — what changes so one incoming vessel message gets handled by all three consumers instead of just the next free one?

---

## Connor

> Example order: 1 lb raw meat, 1 lb cooked deli slices, 1 lb sliced cheese, 1 loaf of bread. Each meat type needs a different worker, so one task goes to one worker and the other meat type to a second; a third worker handles overflow (cheese and bread). After all tasks complete, the order is assembled and given back to the customer. The total goal is given to each worker but each only does their part. For ais-tracker: each function requires the same data but uses it for a different purpose. I think the deli analogy didn't entirely follow the correct line of reasoning, but I think I understand the concept.

## Claude

Right that it drifted — and noticing the drift is the valuable part. The last sentence is the bullseye: "each function requires the same data but uses that data for a different purpose." That line is pub/sub stated correctly. The deli story built around it describes a *different* pattern.

What the deli order actually describes: one order gets *split* into different sub-tasks (raw → worker A, cooked → worker B, cheese/bread → worker C), each worker does a *different piece*, then the pieces are *reassembled* and handed back to the customer. That's a real named pattern — **scatter-gather** (fan-out then fan-in). Useful, but not what `ais-tracker` needs.

Two diagnostics that separate them:

- **Divided vs. copied.** Deli order is *cut up* — each worker gets a different slice. Pub/sub sends the *whole, identical* message to every consumer. A mile-95 upriver crossing goes complete to Discord, the mile-marker alerter, and the ETA trigger. Nobody gets "a piece."
- **Reassembled vs. not.** The deli has a *customer waiting* for the combined result. The AIS consumers have no one waiting — nothing gets recombined; three separate jobs fire independently.

> **[Diagram shown: scatter-gather vs. pub/sub.]** Top row — one order split across three workers doing different parts, then reassembled and returned to the customer. Bottom row — one identical vessel message copied to three independent consumers, with no merge and no reply.

The absence of the return path in the bottom row is the whole point — nobody's assembling a result, so no consumer's slowness holds up any other. Connor understands the concept; the deli just happened to be a scatter-gather kitchen while AIS is a pub/sub kitchen.

**Retrieval question:** In pub/sub, the ETA consumer crashes and is offline for a full minute while 20 vessel messages are published. When it comes back — should it receive the 20 it missed, or are they gone? And: is that the *broker's* decision, or something the *consumer* must arrange in advance?

---

## Connor

> The broker should hold onto the messages until it receives confirmation that the message has been ingested, shouldn't it — that way even if the process crashes you're assured the tasks haven't been lost. I believe that means it's the broker's decision rather than the tool's.

## Claude

The mechanism is right and it has a name: the **acknowledgment (ack)**. The broker hands a message over, marks it "unacknowledged," and only deletes it once the consumer sends back "got it, done." If the consumer crashes before that ack lands, the broker never heard confirmation, so it redelivers.

But that covers a specific failure. Two distinct ways a consumer loses a message:

- **Case 1 — in flight.** Broker delivered, consumer started, then crashed before acking. The ack mechanism nails this: no ack → redeliver.
- **Case 2 — offline.** Consumer was fully down when 20 *new* messages were published. You can't ack a message you never received — the ack protocol has nothing to grab onto. Acks do *not* save you here.

What covers Case 2 is **durability** via a **durable subscription** — the broker keeps a persistent queue for that consumer and keeps *filling it* while the consumer is disconnected (often writing to disk), so the messages survive both the consumer being away and the broker restarting. On reconnect, the queue is waiting.

**Correction:** "the broker's decision rather than the tool's" is backwards. The broker *provides* these guarantees; it doesn't *choose* them. Someone must opt in ahead of time: declare the queue durable, mark the subscription persistent, set retention. If nobody opts in, many brokers *default to dropping* messages for an absent subscriber — plain Redis pub/sub is pure fire-and-forget; MQTT drops too unless a persistent session is configured. The broker offers a *menu* of guarantees; the tool (or topology designer) must *order off it* in advance. Silence gets the weak default.

Deli image: the ack is the worker saying "yep, got that order." Durability is the deli *writing tickets down in a book* instead of shouting them across the counter — so if the worker steps out or the power blips, the book still holds every ticket.

**Retrieval question:** The ETA consumer pulls a message, does the work, fires the Discord alert — then crashes one millisecond before its ack reaches the broker. What does the broker assume, what does it do next, and what does the user see in Discord?

*Writing note:* *conformation* → *confirmation* (conformation means shape/structure).

---

## Connor

> It ends up depending on whether you opted into a durable sub. Even if the end result is achieved (you're notified of a vessel's ETA), if an ack isn't sent the broker will send the message again and await the response, continuing until it receives that ack. In this case the user might get 2 notifications unless the ETA tool can send an ack of its own.

## Claude

Landed the core, and the word that matters: **twice**. Two tightenings.

**Untangle:** durability isn't the deciding factor here. This is Case 1, in-flight — the broker already handed the message over, so redelivery-on-missing-ack kicks in *regardless* of durability. Durability was about a consumer that was *offline* when new messages arrived. Different failure, different mechanism.

**The nailed part:** the broker never heard the ack, so by design it assumes the worst — treats the message as unprocessed and redelivers. It cannot tell "consumer died before doing the work" from "consumer did the work, fired the alert, then died a millisecond before the ack landed." Silence is silence. So it redelivers, the consumer processes again, and fires a **second** Discord notification.

Name to burn in: **at-least-once** delivery. The ack protocol guarantees every message is processed one *or more* times — never silently dropped — but the price is that "one or more" sometimes means two. You cannot get "hold until acked, redeliver on silence" *and* "never a duplicate" for free. Three names to file: at-most-once (might drop, never duplicate), at-least-once (never drop, might duplicate — what Connor derived), exactly-once (the expensive, partly-illusory grail).

**Correction:** "unless the ETA tool sends an ack of its own." The ETA consumer *already is* the one sending the ack — that's the protocol. A second ack wouldn't help; the ack genuinely never made it out before the crash, and no amount of acking fixes a message that died in transit. The fix can't live in the acking.

**Prod toward the fix:** the redelivered message is byte-for-byte the same — same vessel, same ETA, same event. If the thing about to fire the alert could remember it had already handled that specific message, what would it do differently the second time — and does the broker or the consumer hold that memory?

*Writing notes:* "the ETA tool **and** send an ack" → *can* send (typo). Possessive: "a **vessel's** ETA" needs the apostrophe.

---

## Connor

> If the ETA tool remembers where it was before it crashed and receives the instructions again, it can check whether the work has already been completed — in this case yes it has, so it sends the ack to complete the task. I'm thinking of it like an if-then loop: IF the work is not done, THEN finish the work. If it crashes right before it checks that loop again it shouldn't matter, because the check is built into the loop itself. Am I getting that right?

## Claude

Yes — arrived at the right idea, and it has a name: **idempotency**. An operation is idempotent if doing it twice leaves the same result as doing it once. "Check whether the work's already done before doing it" is exactly how you make a consumer idempotent. Two corrections on the path.

**Sharp one — "remembers where it was before it crashed."** A crash wipes the process's memory; everything in RAM is gone instantly. If that "memory" lives in a variable inside the ETA tool, it doesn't survive the very event it's meant to protect against. The memory must be *durable and external* — a database row, a Redis set, a file on disk. Concretely: each message carries a unique ID, and the consumer keeps a persistent record of handled IDs. On redelivery it checks that external store — seen this ID? Skip the work, just ack. Not seen? Do the work, write the ID down, ack. The check is right; it just has to live in a logbook on the shelf, not in the consumer's head.

**Subtle one.** There isn't one step, there are *two*: (a) do the work / fire the alert, and (b) record that you did it. A crash can land in the *gap between them*. Fire the alert, crash before recording → redelivery → check says "not done" → fires again. The window still exists; where you put it decides whether the rare failure is a duplicate or a *missed* alert.

**The whole thread together:** at-least-once *delivery* (broker redelivers on silence) **+** idempotent *processing* (durable dedup check) **= effectively-once** *outcome*. You never stop the duplicate from being **delivered** — you make it **harmless**. And the responsibility landed on the *consumer*, not the broker. That's the clean answer to the "whose job" question: the broker guarantees delivery, the consumer guarantees the *effect* happens once.

**Retrieval question:** Two things to do — fire the Discord alert, and write "sent" in the durable log — and a crash can fall between them. You pick the order: fire-then-log, or log-then-fire. If the crash lands in the gap, what does each cost you? And for a vessel ETA alert specifically, which failure would you rather live with?

*Writing note:* *therefor* → *therefore* ("therefor" is archaic legalese meaning "for that thing").

---

## Connor

> For posterity, I think it should be fire alert → log it. If you log it and then fire the alert and it crashes in the middle, there's a possibility you're never going to get the message; whereas the opposite way you might get a duplicate but at least you're guaranteed to get the message.

## Claude

Right call, and the right *reasoning*. The two orderings mapped onto the two failure modes:

- **Log-then-fire:** crash in the gap → recorded "sent" but never sent. Redelivery → dedup check says "already handled" → skips. The alert is *lost, silently*. At-most-once effect; failure mode is a **miss**.
- **Fire-then-log:** crash in the gap → alert went out but wasn't recorded. Redelivery → check says "not done" → fires *again*. At-least-once; failure mode is a **duplicate**.

The judgment (a missed ETA is worse than a doubled one) is sound because the failures aren't symmetric in *cost*. A duplicate is a mild annoyance a human deletes in a second. A silent miss is invisible — nobody in the maritime ops chain knows an alert *should* have fired, so nobody goes looking. General principle worth keeping: **when you must choose, make your failure mode the loud one, not the silent one.** Fire-then-log fails loud.

**Precision fix:** "at least you're *guaranteed* to get the message." Careful with *guaranteed* — fire-then-log doesn't guarantee delivery either. If the crash lands *earlier* (consumer pulls the message and dies before firing at all), you're covered — but only because the broker keeps redelivering until acked. The real guarantee comes from **at-least-once delivery underneath**, not from the ordering choice. The ordering guarantees something narrower but exactly right: *if* the work ever runs, it runs a whole number of times ≥ 1 — never the silent zero that log-then-fire risks.

**Full stack (who guarantees what):**

- broker → keeps redelivering until acked (**at-least-once delivery**)
- consumer, durable dedup → makes reprocessing harmless (**idempotency**)
- consumer, fire-then-log ordering → makes the rare failure a loud duplicate, never a silent miss

Delivery from the broker, effect-safety from the consumer. That division of labor is the entire mental model — built up from the deli counter.

*Writing note:* *therefor* → *therefore* (recurred).

---

## Session close-out

- **New concepts this session:** message brokers; producer/consumer/message; decoupling (space/time/rate); work queue vs. pub/sub; scatter-gather; acknowledgments; durability / durable subscriptions; delivery semantics (at-most-once / at-least-once / exactly-once); idempotency; effectively-once; loud-vs-silent failure-mode selection.
- **Demonstrated reasoning:** moved from "broker = deli worker" to unprompted reasoning about crash windows, redelivery, and failure-mode asymmetry. Vocabulary was bolted onto instincts already present.
- **Corrections made:** broker ≠ worker (broker = ticket system); scatter-gather ≠ pub/sub; guarantees are opted into, not chosen by the broker; a second ack doesn't fix in-transit loss; crash-resilient "memory" must be durable/external, not in-process.
- **Writing typos flagged:** *conformation* → confirmation; *therefor* → therefore (×2); missing possessive apostrophe (*vessel's*); "and" → "can" typo.
- **Next steps to consider:** which broker (RabbitMQ vs. Kafka vs. MQTT — genuinely different tools); consumer-side dedup store design; hands-on: spin up a broker in a container next to `ais-tracker`.
