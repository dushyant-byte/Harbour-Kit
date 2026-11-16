# Harbor RL Submission — <Your Name> (@<Your_GitHub_Username>)

## 1. Loom Walkthrough Video
- **Video URL:** [Watch 5-Minute Walkthrough](https://www.loom.com/share/YOUR_VIDEO_ID)
  *(Link sharing is set to "Anyone with the link can view")*

## 2. Task & Calibration Summary
- **Bug 1 (Easy):** The CLI argument check expects 3 `argv` entries instead of 2, so the tool prints a usage error and exits 2 on every valid call.
- **Bug 2 (Medium):** `PENDING` rows are treated as valid, so they inflate `total_valid_amount` and `valid_count`.
- **Bug 3 (Hard):** When a `tx_id` repeats, the "latest" row is chosen by comparing timestamp *strings*, which is wrong across different UTC offsets (and ignores the later-row-wins tie rule).
- **Calibration Scores:** `nop` = `0.00` | `oracle` (`solve.sh`) = `1.00` | Partial `oracle` (Bug 1+2 fixed, Bug 3 broken) = `0.50`

## 3. Ablation & RL Advantage Table (`terminus-2`, `k=3` Trials)

<!-- Fill the rewards from jobs/*/result.json. Dense = reward.txt value.
     Binary = 1 if dense == 1.00 else 0. Use advantage.py to compute the advantages. -->

| Run | Change Tested | Trial 1 (Binary / Dense) | Trial 2 (Binary / Dense) | Trial 3 (Binary / Dense) | Mean (Binary / Dense) |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Run 1: Baseline** | Original 3-bug task | `<0 / 0.50>` | `<...>` | `<...>` | `<...>` |
| **Run 2: Ablation** | `<describe the ONE change>` | `<...>` | `<...>` | `<...>` | `<...>` |

**Advantages (Trial Reward − Mean):**

| Run | Trial 1 (Binary / Dense) | Trial 2 (Binary / Dense) | Trial 3 (Binary / Dense) |
| :--- | :--- | :--- | :--- |
| **Run 1: Baseline** | `<...>` | `<...>` | `<...>` |
| **Run 2: Ablation** | `<...>` | `<...>` | `<...>` |

## 4. Trajectory Forensics (`trajectory.json`)
- **Where the Agent Failed in Run 1:** `<2-3 sentences from YOUR trajectory: which bash command, what it missed>`
- **Ablation Hypothesis & Result:** `<what you changed, what you expected, what happened>`

## 5. Dense Reward & Anti-Cheat Design
- **My Dense Reward Weights (`tests/test.sh`):** Score = 0.20 (clean-input run is correct, which also gates on Bug 1) + 0.30 (status rules, malformed rows, half-up rounding: Bug 2) + 0.50 (duplicate `tx_id` across time zones: Bug 3). Bug 3 gets the largest weight because it is the hard one and the signal I most want the model to learn. `nop` = 0.00 and `oracle` = 1.00.
- **How `solution/hack.sh` Cheated & How I Blocked It:** `hack.sh` replaces `reconcile.py` with a stub that ignores the CSV and prints hardcoded JSON, and also writes `1.0` straight into `reward.txt`. The verifier blocks this by generating fresh random CSVs at verification time (random data, random file path, different working directory), comparing against an independent reference implementation, and deleting any existing `reward.txt` before it starts. `hack.sh` scores `0.00`.
- **Remaining Reward-Hacking Risk:** `<1-2 sentences in your own words. Ideas: an agent could detect the verifier's temp-dir naming (v_*) or its input shape and special-case it; or it could read /proc or the process list while the verifier runs.>`

## 6. AI Tool Workflow
- **AI Tool & Model Used:** `<e.g., Claude / Claude Code / Gemini CLI / Cursor>`
- **How I Avoided Prompt Leakage:** `<2 sentences, honestly: which session wrote environment/ + solution/ and which session wrote instruction.md, and how you kept the bug locations out of instruction.md>`
