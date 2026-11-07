# Crux-Evals (Crucible Assay) — Evaluator Harness Protocol

This workspace is equipped with **Crux-Evals (`crux_evals`)**, an open-protocol MCP & Hook evaluator suite that acts as an independent navigation compass so the coding agent never grades its own work:

1. **`eval_trajectory_compass` (`01_trajectory_sentinel`):** Tracks Process Advantage (`progress_delta`), error-fingerprint stagnation, and patch oscillation.
2. **`eval_invariant_and_patch` (`02_clean_patch_and_contract`):** Audits raw `git diff` for test-assertion tampering (Anti-Goodhart), AST syntax errors, secret leaks, and post-edit verification status.
3. **`eval_epistemic_grounding` (`03_epistemic_grounding_auditor`):** Verifies read-before-edit provenance and grounds claims against raw execution telemetry.
4. **`eval_blind_judge` (`04_blind_bars_judge`):** Provenance-blind cross-family BARS (`0.0 | 0.5 | 1.0`) rubric judge (`actor_claim_text` stripped).
5. **`eval_adversarial_prober` (`05_adversarial_falsifier`):** Synthesizes and executes boundary counterexamples in an isolated `/tmp` sandbox, reporting only reproducible runtime failures.
