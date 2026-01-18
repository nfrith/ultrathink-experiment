# Test 003: Thinking ON

Date: 2026-01-18

## Setup

- Model: Claude (via Claude Code)
- Thinking mode: ON (default)
- Prompt: "What are the trade-offs between Redis and Memcached for caching?"
- Runs: 8 per condition
- Metric: Response character count

## Raw Data

### Baseline

| Run | Characters |
|-----|------------|
| 1 | 1278 |
| 2 | 1536 |
| 3 | 1293 |
| 4 | 1504 |
| 5 | 1221 |
| 6 | 1254 |
| 7 | 1468 |
| 8 | 1556 |

### /ultrathink Skill

| Run | Characters |
|-----|------------|
| 1 | 3125 |
| 2 | 3966 |
| 3 | 2597 |
| 4 | 4688 |
| 5 | 4389 |
| 6 | 5258 |
| 7 | 2258 |
| 8 | 4926 |

### Embedded Instruction

Prompt suffix: "Think through this with maximum depth and thoroughness."

| Run | Characters |
|-----|------------|
| 1 | 3865 |
| 2 | 5788 |
| 3 | 5998 |
| 4 | 5454 |
| 5 | 4914 |
| 6 | 5255 |
| 7 | 6240 |
| 8 | 5207 |

## Summary Statistics

| Condition | Mean | Min | Max | Ratio vs Baseline |
|-----------|------|-----|-----|-------------------|
| Baseline | 1,389 | 1,221 | 1,556 | 1.00x |
| /ultrathink skill | 3,901 | 2,258 | 5,258 | 2.81x |
| Embedded instruction | 5,340 | 3,865 | 6,240 | 3.85x |

## Observations

1. Skill effect confirmed: /ultrathink produces 2.8x longer responses than baseline
2. Embedded is stronger: Direct instruction produces 3.9x (1.4x more than skill)
3. Higher variance with treatment: Baseline range is tight (335 chars), ultrathink range is wide (3,000 chars)
