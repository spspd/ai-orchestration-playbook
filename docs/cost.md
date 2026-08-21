# Cost and Token Accounting

## Measure usage before assigning currency

The case snapshot reports logged tokens, not a bill. No trustworthy model-specific price table was present in the local evidence, so this repository does not convert usage into money. Apply a current provider price sheet only after splitting input, cached input, and output by model.

## Reproducible aggregation method

For each JSONL session file:

1. Freeze a cutoff timestamp and enumerate files once.
2. Read the **first** session metadata record. A nested file may repeat parent metadata later.
3. Classify the session as top-level execution, interactive, or nested.
4. Find token-usage events and take the last cumulative `total_token_usage` value in that file.
5. Do not sum cumulative events within a session.
6. Record files with missing usage separately; use textual fallbacks only if their semantics are known.
7. Sum categories, verify that category counts equal the file count, and retain the cutoff and script version.

Pseudocode:

```text
for file in frozen_session_file_list:
    meta = first(session_meta in file)
    usage = last(token_count.total_token_usage in file)
    category = classify(meta)
    totals[category] += usage
    coverage[category] += 1
assert sum(coverage) == len(frozen_session_file_list)
```

## Measured snapshot

At 2026-08-20 16:25:44 KST, all 212 enumerated files contained a usage record.

| Category | Sessions | Logged tokens | Share |
|---|---:|---:|---:|
| Top-level execution | 131 | 187,757,345 | 35.45% |
| Interactive | 8 | 91,678,832 | 17.31% |
| Nested | 73 | 250,246,645 | 47.24% |
| **Total** | **212** | **529,682,822** | **100.00%** |

The detailed counters recorded 526,874,085 input tokens and 2,808,737 output tokens. Of the input, 499,131,520 tokens were cached input: **94.73% of input** and **94.23% of total logged tokens**. Cached tokens still appear in the total counter; their billing treatment may differ.

## Interpret the cache ratio carefully

A high cache share can mean repeated stable context was reused. It can also reveal oversized system prompts, parent-history replication in nested sessions, or repeated reading of irrelevant material. Token counts alone cannot distinguish these cases.

Inspect the largest sessions first. Remove repeated tool output, build logs, and irrelevant history from the context boundary. For research archives, index metadata first and load only relevant turns or passages.

## Separate consultation from tool calling

Use two logical lanes even if one product serves both:

- **Consultation lane:** clarify intent, compare options, resolve ambiguity, and approve claims. It benefits from strong reasoning and compact, curated context.
- **Tool-calling lane:** read named inputs, run commands, transform data, and produce bounded artifacts. It benefits from strict contracts, lower variance, and cheap retries.

Route work by error cost:

| Work | Suggested effort | Gate |
|---|---|---|
| Mechanical extraction or formatting | Low | Schema and diff check |
| Bounded research or synthesis | Medium | Source and claim check |
| Conflict resolution or final adoption | High | Independent review or rerun |

The aim is not to minimize every call. It is to spend reasoning and context where an error would propagate, while keeping repeatable execution narrow.

## Cost report checklist

- State cutoff, timezone, file coverage, and category rules.
- Separate input, cached input, and output.
- Separate top-level, interactive, and nested usage.
- Name the model and pricing effective date for monetary estimates.
- Report retries and failed attempts; they are part of cost.
- Keep raw usage and inferred currency in separate columns.
