# Crux-Evals (`crux-evals`) — Crucible Assay

**Universal Open-Protocol MCP & Hook Reasoning Evaluators for Agentic Coding Harnesses**

In metallurgy, a **Crucible (`crux`)** refines ore under heat, while an **Assay (`crux-evals`)** independently tests the purity of what comes out of the crucible. **`Crux-Evals`** provides an independent, out-of-process **Evaluator Bank** that helps coding agents navigate complex software and scientific problems **without ever grading their own work**.

---

## Why `Crux-Evals`?

1. **100% Harness-Agnostic via Open Protocols (`.mcp.json` + `SKILL.md` + `hooks.json`):**
   Works out-of-the-box with **Crucible (`crux`)**, **Claude Code**, **Cursor**, **Gemini CLI**, **OpenAI Codex CLI**, **Windsurf**, and **Cline**—requiring **zero changes to harness source code**.
2. **True Epistemic Firewall (Zero Self-Grading):**
   When an agent or harness calls a `crux-evals` MCP tool, the tool schema accepts **only objective pointers** (`task_objective`, `target_paths`, `invariants`). The MCP server process reads `git diff HEAD`, parses ASTs, checks file mtimes, and runs boundary probes in an isolated `/tmp` sandbox **directly from disk**.
3. **Complementary Navigation Signal (Compass, Not Police):**
   By default, evaluators do not lock the agent's keyboard. They return structured navigational intelligence:
   - `alignment_score` (`[0.0, 1.0]`) & `progress_delta` (`[-1.0, +1.0]` Process Advantage)
   - `blind_spots`: Unhandled edge cases, architectural couplings, or loop stagnation
   - `counterexamples`: Concrete failing boundary inputs and stack traces executed in `/tmp`
   - `unverified_claims`: Assumptions not yet backed by empirical test runs
   - `suggested_next_probes`: Actionable commands or reads to resolve uncertainty
4. **Zero-Friction Extensibility (`crux-evals new <id>`):**
   Drop any folder containing `evaluator.yaml` + `module.py` into `crux_evals/modules/` or `<workspace>/.crux_evals/plugins/` (or run `crux-evals new my_evaluator`) and it is **automatically discovered** by both the MCP Server (`tools/list`, `tools/call`) and the Cascade Runner.

---

## The 5 Core Evaluators

| Stage | Evaluator ID | MCP Tool Name | Paradigm | What It Hydrates From Disk | Complementary Value Returned |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Stage 1** | `trajectory_sentinel` | `eval_trajectory_compass` | **Deterministic** (`<2ms`) | Action hashes, normalized error fingerprints, diff state hashes | Detects exact loops, error-signature stagnation across edits, A↔B oscillations, and read spirals; suggests orthogonal probes. |
| **Stage 1** | `clean_patch_and_contract` | `eval_invariant_and_patch` | **Deterministic** (`<20ms`) | `git diff HEAD`, AST syntax trees, secret regexes, test watermarks | Catches deleted/weakened test assertions (Anti-Goodhart), tautological asserts, secret leaks, syntax errors, and unverified edits. |
| **Stage 2** | `epistemic_grounding_auditor` | `eval_epistemic_grounding` | **Hybrid** | Read/edit file ledger + raw command stdout vs. `proposed_claims` | Flags unverified completion claims and enforces read-before-edit provenance. |
| **Stage 2** | `blind_bars_judge` | `eval_blind_judge` | **Blind LLM Judge** | `task_objective` + anonymous `git diff` + raw test output (**Actor CoT stripped**) | Scores `0.0 \| 0.5 \| 1.0` Behaviorally Anchored Rating Scales (`BARS`) with position-swap calibration and Conservative Bayesian LCB. |
| **Stage 3** | `adversarial_falsifier` | `eval_adversarial_prober` | **Adversarial Breaker** | Touched `.py` ASTs + isolated `/tmp` sandbox execution | Synthesizes and executes boundary-value probes (`[]`, `0`, `-1`, `{}`, `''`); reports **only** reproducible runtime crashes. |

Plus **`eval_run_suite`**, which executes the full multi-stage cascade in a single MCP tool call.

---

## Quickstart

### 1. Connect via Open MCP (`.mcp.json`) — Works in Claude Code, Cursor, Gemini CLI, Codex, Crux
Add `.mcp.json` to your project root:
```json
{
  "mcpServers": {
    "crux-evals": {
      "command": "python3",
      "args": ["-m", "crux_evals.mcp_server", "--workspace", "."]
    }
  }
}
```

### 2. List Discovered Evaluators
```bash
python3 -m crux_evals.cli list
```

### 3. Add a New Evaluator in 5 Seconds (`crux-evals new`)
`Crux-Evals` uses an auto-discovery `EvaluatorRegistry`. You never need to edit central wiring files to add a new evaluator:

```bash
# Scaffold a new built-in or workspace-local evaluator plugin:
python3 -m crux_evals.cli new sql_cost_guard \
  --paradigm deterministic \
  --description "Audits SQL queries for missing partition filters and unbounded CROSS JOINs."
```

This creates a self-contained plugin folder (`evaluator.yaml` + `module.py`) that is immediately live in `tools/list` as `eval_sql_cost_guard` and in `eval_run_suite`!

You can also drop workspace-specific evaluators into `<workspace>/.crux_evals/plugins/<my_plugin>/` or set `CRUX_EVALS_PLUGIN_PATH=/path/to/plugins`.

### 4. Run the Test Suite
```bash
python3 -m unittest discover -s tests -v
```
