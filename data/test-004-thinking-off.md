# Test 004: Thinking OFF (Control)

Date: 2026-01-18

## Purpose

Determine if the instruction effect is specific to thinking tokens or just output verbosity.

**Hypothesis**: If the instruction increases thinking tokens specifically, the effect should disappear or shrink significantly with thinking mode disabled.

## Setup

- Model: Claude (via Claude Code)
- Thinking mode: OFF (`/config` → Thinking mode: false)
- Prompt: "What are the trade-offs between Redis and Memcached for caching?"
- Runs: 8 per condition
- Metric: Response character count

## Raw Data

### Baseline

| Run | Characters |
|-----|------------|
| 1 | 1335 |
| 2 | 1404 |
| 3 | 1464 |
| 4 | 1481 |
| 5 | 1493 |
| 6 | 1533 |
| 7 | 1681 |
| 8 | 1699 |

### /ultrathink Skill

| Run | Characters |
|-----|------------|
| 1 | 3746 |
| 2 | 4120 |
| 3 | 4285 |
| 4 | 4436 |
| 5 | 4771 |
| 6 | 4812 |
| 7 | 5343 |
| 8 | 6119 |

### Embedded Instruction

Prompt suffix: "Think through this with maximum depth and thoroughness."

| Run | Characters |
|-----|------------|
| 1 | 5056 |
| 2 | 5566 |
| 3 | 6728 |
| 4 | 7076 |
| 5 | 7079 |
| 6 | 7325 |
| 7 | 7472 |
| 8 | 8293 |

## Summary Statistics

| Condition | Mean | Min | Max | Ratio vs Baseline |
|-----------|------|-----|-----|-------------------|
| Baseline | 1,511 | 1,335 | 1,699 | 1.00x |
| /ultrathink skill | 4,704 | 3,746 | 6,119 | 3.11x |
| Embedded instruction | 6,824 | 5,056 | 8,293 | 4.52x |

## Comparison: Thinking ON vs OFF

| Condition | Thinking ON | Thinking OFF | Change |
|-----------|-------------|--------------|--------|
| Baseline | 1,389 | 1,511 | +9% |
| Skill | 3,901 (2.81x) | 4,704 (3.11x) | +21% |
| Embedded | 5,340 (3.85x) | 6,824 (4.52x) | +28% |

## Critical Finding

**The effect is STRONGER with thinking disabled, not weaker.**

This is the opposite of what we'd expect if the instruction specifically influenced thinking tokens. The data suggests:

1. The instruction affects **visible output volume** (verbosity/thoroughness)
2. With thinking ON, some of that extra depth may go into thinking tokens (invisible)
3. With thinking OFF, all of it goes into visible output

**Conclusion**: We cannot claim the instruction increases thinking tokens. We can only claim it increases output. The mechanism is not what we assumed.
