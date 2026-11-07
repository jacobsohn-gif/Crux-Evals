---
name: crux-evals
description: Consult independent, ground-truth-hydrated reasoning evaluators (Trajectory Compass, Clean Patch & Contract, Epistemic Grounding, Provenance-Blind BARS Judge, and Adversarial Falsifier) via the `crux-evals` MCP server to uncover blind spots, edge-case counterexamples, and next-step probes without self-grading.
---

# Crux-Evals (Crucible Assay) — Agent Navigation Skill

When solving complex engineering or scientific problems, never grade your own work in your head. Use the **`crux-evals` MCP Server** as an independent external sensor bank that inspects workspace files, `git diff`, ASTs, and isolated `/tmp` sandboxes directly from disk.

## Available MCP Evaluator Tools

1. **`eval_trajectory_compass`** *(Stage 1 — Deterministic)*
   - **When to call:** Whenever debugging a failure for >2 steps or when edits feel circular.
   - **Value provided:** Computes Process Advantage (`progress_delta`), flags action/error-signature stagnation or patch oscillation, and suggests orthogonal diagnostic probes.

2. **`eval_invariant_and_patch`** *(Stage 1 — Deterministic)*
   - **When to call:** After modifying code or tests (`edit_file` / `write_file`).
   - **Value provided:** Verifies AST syntax, checks for accidental test-assertion deletion (Anti-Goodhart) or secret leaks, validates formal invariants, and checks whether empirical tests have been run since the latest edit.

3. **`eval_epistemic_grounding`** *(Stage 2 — Hybrid)*
   - **When to call:** Before concluding a root-cause investigation or summarizing verification results.
   - **Value provided:** Cross-checks your `proposed_claims` against the raw tool execution ledger so no unverified assumption slips through.

4. **`eval_blind_judge`** *(Stage 2 — Provenance-Blind BARS Judge)*
   - **When to call:** When weighing candidate designs or before declaring a complex feature complete.
   - **Value provided:** Evaluates ONLY `<task_objective>`, `<anonymous_git_diff>`, and `<raw_verification_output>` (with your Chain-of-Thought stripped) on `0.0 | 0.5 | 1.0` Behaviorally Anchored Rating Scales.

5. **`eval_adversarial_prober`** *(Stage 3 — Forked Sandbox Breaker)*
   - **When to call:** After implementing non-trivial parsing, math, state-machine, or data-pipeline logic.
   - **Value provided:** Synthesizes and executes boundary-value counterexamples (`[]`, `0`, `-1`, `{}`, `''`) in an isolated `/tmp` sandbox and returns concrete failing inputs + tracebacks if a crash occurs.

6. **`eval_run_suite`** *(Full Cascade)*
   - Runs all registered evaluators (including any custom workspace plugins in `.crux_evals/plugins/`) in one call.
