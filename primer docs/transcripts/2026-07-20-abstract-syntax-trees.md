# Session Transcript — Abstract Syntax Trees

**Date:** 2026-07-20
**Track(s):** Compilers / language tooling (new); connects to SAT solvers, Python, Security/CTF (angr/Z3/symbolic execution)
**Format:** Socratic, first-principles, scaffolded (no full solutions given up front)

---

## Topic covered: Abstract Syntax Trees (ASTs)

Built the concept from first principles, anchored to Connor's recent SAT work
(parsing a boolean formula into structure before DPLL can run).

### Concepts established (in order)

1. **The root of an expression tree = the operation performed LAST** — the one whose
   result *is* the whole expression's result. Reading order is a red herring;
   evaluation order determines the root.
   - Worked example: `2 + 3 * 4` → root is `+` (not `*`, not the left-most token),
     because `3 * 4` is computed first and `2 + 12` is the final step.
   - Applied to `(a ∨ b) ∧ ¬c` → root is `∧` (AND).

2. **Arity → number of children.** A node's child count equals its operator's arity.
   - `¬` (NOT) is unary → 1 child; `∧`, `∨`, `+`, `*` are binary → 2 children.
   - Generalized: a call like `notify(vessel, marker, eta)` is arity 3 → 3 children;
     `-x` (unary minus) vs `x - y` (binary minus) — same symbol, different arity/shape.

3. **Internal nodes vs. leaves.**
   - Internal node = an **operator** ("moving part"); has children; produces a result
     by acting on them.
   - Leaf = an **operand**; has no children; *is* a value already.
   - The defining test for a leaf is "has no children" — being "at the bottom" is a
     *consequence* of that, not the definition.

4. **Literal vs. variable reference** (two kinds of leaf).
   - **Literal** = value written directly in the source (`4`, `1.5`, `true`, `"vessel"`)
     — you can read the value straight off the page.
   - **Variable reference** = a name that stands for a value stored elsewhere
     (`eta`, `distance`, `speed`) — you'd have to look up what it currently holds.

5. **Why "abstract"?** The tree discards surface text that only helped a human read a
   flat line — parentheses (grouping becomes tree *shape*), whitespace, indentation,
   comments, semicolons — and keeps only the essential structure: *what operates on
   what*. `a = b + c * d` and `a = b + (c * d)` produce the **same** tree.
   - Mentioned the sibling concept: **concrete syntax tree / parse tree** keeps every
     token and paren; the AST is the cleaned-up version.

6. **Why trees instead of flat strings (the payoff).**
   - Every node is a **self-contained subproblem you can reason about locally** —
     children are already handled when you reach the parent.
   - Evaluation is a **bottom-up / post-order traversal** (visit children before parent).
     Connor derived this independently ("work your way up from the bottom and solve").
   - Applications framed as "different visit-each-node operations over the same tree":
     - **Compiler:** emit code per node (post-order); optimizations as tree rewrites
       (`x * 1` → `x`).
     - **Linter:** find bad-shaped nodes (e.g. `=` where `==` was meant inside an `if`).
     - **angr / symbolic execution:** runs a program with a *symbol* instead of a
       concrete input, builds an **AST of path constraints**, hands it to **Z3**
       (SAT/SMT) to ask "is there an input satisfying all this?" → that input is the
       exploit. Full chain: **program → AST of constraints → SAT/SMT solver → concrete
       exploit input.** Ties every recent topic (SAT, CNF, security) together.

---

## Connor's performance / evidence

- **Root = last operation:** initially guessed the root was `a` ("the first thing you
  see"), i.e. reading order. Corrected cleanly; then independently identified `∧` as the
  root of `(a ∨ b) ∧ ¬c` once given the evaluation-order rule.
- **Arity:** derived the unary/binary child-count distinction himself ("NOT only affects
  one thing where OR is comparing 2 different things... single branch and a double
  branch").
- **Leaf vs. internal node:** produced the core distinction unaided — "leaves provide
  data where the branches are the actual moving parts." Hedges ("at the bottom",
  "true/false only") were sharpened.
- **AST structure of `eta = distance / speed`:** drew the structure **correctly and
  unaided** — root `=`, left `eta`, right `/` subtree over `distance`/`speed`. This was
  the hard part and he nailed it.
- **"Abstract":** correctly answered that `a = b + c * d` and `a = b + (c * d)` yield the
  same tree, and articulated that parentheses just denote a self-contained branch that
  becomes tree shape.
- **Bottom-up evaluation:** independently described post-order evaluation as the reason
  ASTs are useful for SAT solving.

## Mistakes / corrections (candidate traps)

1. **Reading order vs. evaluation order:** first instinct was that the root is the
   left-most / first token read. Rule installed: root = operation performed *last*.
2. **operator vs. operand vocabulary:** said "the AND operand" when he meant the AND
   *operator*. Operators are internal nodes; operands are leaves.
3. **literal vs. variable reference confusion:** on `eta = distance / speed`, labeled
   variable names (`eta`, `distance`, `speed`) as "literals" and labeled the `/`
   *operator* as a "variable reference" (category error — `/` is neither; it's an
   operator). After the "can you read the value off the page?" test, classified
   `speed * 1.5 + buffer` correctly (`speed` var-ref, `1.5` literal, `buffer` var-ref).

## Suggested next steps (not yet done)

- Hands-on: use Python's built-in `ast` module to parse a real line
  (`alert = speed * 1.5 + buffer`) and print the actual tree; compare against a hand
  drawing. (Ties to Python track.)
- How a parser *builds* the tree from raw text in the first place (tokenizing +
  precedence).
- Hand-trace a gnarlier / mixed-precedence expression.
