# Ultrathink Experiment

An experiment testing whether prompt instructions can influence Claude Code's thinking token consumption after the `ultrathink` keyword was deprecated.

## TL;DR

**We cannot conclusively determine if prompt instructions influence thinking tokens.**

The instruction increases output volume (~3-4x), but a control test with thinking mode disabled showed the effect *persists and is even stronger*. This means we're measuring verbosity, not thinking depth.

### Results

| Test | Condition | Mean Chars | Ratio | n |
|------|-----------|------------|-------|---|
| Thinking ON | Baseline | 1,389 | 1.00x | 8 |
| Thinking ON | /ultrathink skill | 3,901 | 2.81x | 8 |
| Thinking ON | Embedded instruction | 5,340 | 3.85x | 8 |
| Thinking OFF | Baseline | 1,511 | 1.00x | 8 |
| Thinking OFF | /ultrathink skill | 4,704 | 3.11x | 8 |
| Thinking OFF | Embedded instruction | 6,824 | 4.52x | 8 |

## Background

Anthropic deprecated `ultrathink` on January 16th, 2025:

> "Ultrathink no longer does anything. Thinking budget is now max by default."

But the documentation also states:

> "Claude often performs better with high level instructions to just think deeply about a task rather than step-by-step prescriptive guidance."

So the budget ceiling is fixed at 31,999 tokens, but Claude typically stops short. Could a skill influence how much of that budget gets used?

## Hypothesis

A minimal skill that injects "think deeply" instructions could:
1. Influence Claude to use more of the available thinking budget
2. Produce measurably higher thinking token consumption vs baseline
3. Provide a convenient invocation pattern for complex reasoning tasks

## Methodology

### Test Setup

- **Prompt**: "What are the trade-offs between Redis and Memcached for caching?"
- **Conditions**:
  - Baseline (no instruction)
  - `/ultrathink` skill (injected via Claude Code skill system)
  - Embedded instruction ("Think through this with maximum depth and thoroughness.")
- **Runs**: 8 per condition
- **Metric**: Response character count (thinking tokens not directly observable)

### Test Procedure

1. Open a new Claude Code session
2. Run the prompt under each condition
3. Measure response character count
4. Repeat 8 times per condition

### Control Test (test-004)

To determine if the effect was specific to thinking tokens, we ran the same tests with thinking mode disabled (`/config` → Thinking mode: false).

**Hypothesis**: If the instruction specifically increases thinking tokens, the effect should disappear or shrink with thinking disabled.

## Findings

### Key Discovery: Skill Injection Timing

Skills inject as a **synthetic user message AFTER Claude has already begun processing**. The message sequence is:

1. **assistant**: begins answering the question
2. **assistant**: calls Skill tool
3. **user**: tool result "Launching skill..."
4. **user** (synthetic): skill content injected
5. **assistant**: responds to skill

This means skills arrive too late to influence initial thinking. Embedded instructions are present from the first token.

### Critical Finding: Effect Persists Without Thinking

| Condition | Thinking ON | Thinking OFF | Change |
|-----------|-------------|--------------|--------|
| Baseline | 1,389 | 1,511 | +9% |
| Skill | 3,901 (2.81x) | 4,704 (3.11x) | +21% |
| Embedded | 5,340 (3.85x) | 6,824 (4.52x) | +28% |

The effect is **STRONGER** with thinking disabled, not weaker. This is the opposite of what we'd expect if the instruction specifically influenced thinking tokens.

### What This Means

1. The instruction affects **visible output volume** (verbosity/thoroughness)
2. With thinking ON, some extra depth may go into thinking tokens (invisible)
3. With thinking OFF, all of it goes into visible output
4. **We cannot claim the instruction increases thinking tokens**

## Conclusion

The original `ultrathink` keyword set a *minimum* of 32k thinking tokens. Our skill cannot replicate this because:

- We have no access to thinking token parameters
- We can only influence prompts, not model configuration
- The effect we observed is on visible output, not provably on thinking

**Recommendation**: The `/ultrathink` skill should not be used as a thinking token amplifier. The name implies functionality we cannot verify.

## Files

```
ultrathink-experiment/
├── README.md           # This file
├── skill/
│   └── SKILL.md        # The ultrathink skill definition
├── data/
│   ├── test-003-thinking-on.md   # Raw data (thinking enabled)
│   └── test-004-thinking-off.md  # Raw data (thinking disabled)
└── LICENSE
```

## Open Questions

1. **Why does the instruction increase output so much?** Is it actually "thinking more" in some non-token way?
2. **Does skill injection use 1 or 2 API calls?** Network inspection could reveal this.
3. **Could startup hooks inject instructions earlier?** Before processing begins?

## References

- [Extended thinking](https://docs.anthropic.com/en/docs/build-with-claude/extended-thinking)
- [GitHub Issue #18570 - Ultrathink rainbow glow](https://github.com/anthropics/claude-code/issues/18570)
- [GitHub Issue #18072 - MAX_THINKING_TOKENS](https://github.com/anthropics/claude-code/issues/18072)

## License

MIT
