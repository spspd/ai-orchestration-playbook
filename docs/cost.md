# Cost and Token Accounting

## Measure usage before assigning currency

The case snapshot reports logged tokens, not a bill. No trustworthy model-specific price table was present in the local evidence, so this repository does not convert usage into money. Apply a current provider price sheet only after splitting input, cached input, and output by model.

## Reproducible aggregation method

For each JSONL session file:

1. Freeze a cutoff timestamp and enumerate files once in a controlled local environment. Do not publish the file list.
2. Read only the fields required for classification and usage. Do not copy prompts, tool output, file paths, user identifiers, or unrelated metadata.
3. Read the **first** session metadata record. A nested file may repeat parent metadata later.
4. Classify the session as top-level execution, interactive, or nested.
5. Find token-usage events and take the last cumulative `total_token_usage` value in that file.
6. Do not sum cumulative events within a session.
7. Record files with missing usage separately; use textual fallbacks only if their semantics are known.
8. Sum categories, verify that category counts equal the file count, and retain the cutoff and script version in an access-controlled record.
9. Delete the frozen file list and temporary extracts after verification unless an approved retention requirement says otherwise.

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

## Privacy-preserving public reporting

The source case used a private frozen snapshot. Exact timestamps, counts, token totals, cache ratios, model identifiers, and per-session values are intentionally withheld from this public repository. They are not necessary to reproduce the aggregation method and can reveal internal capacity, cost, and activity patterns.

For a public report:

- use a period such as a month or quarter instead of an exact execution timestamp;
- prefer ranges, rounded percentages, or normalized indices over exact totals;
- suppress small categories that could identify a person, project, or unusual session;
- describe the population and method without publishing filenames or raw metadata;
- label values as synthetic, rounded, author-reported, or independently verified;
- obtain approval before naming a provider, model, repository, organization, or host environment.

Use synthetic fixtures when demonstrating an aggregation script. Keep exact internal counters in an access-controlled cost record, not in Git history.

## Interpret the cache ratio carefully

A high cache share can mean repeated stable context was reused. It can also reveal oversized system prompts, parent-history replication in nested sessions, or repeated reading of irrelevant material. Token counts alone cannot distinguish these cases.

Inspect the largest sessions locally, without exporting their raw contents. Remove repeated tool output, build logs, and irrelevant history from the context boundary. For research archives, index only approved metadata first and load relevant turns or passages under the same access controls as the source.

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

- Mark the report `public`, `internal`, or `restricted` before collecting data.
- State cutoff, timezone, file coverage, and category rules.
- Separate input, cached input, and output.
- Separate top-level, interactive, and nested usage.
- Name the model and pricing effective date for monetary estimates.
- Report retries and failed attempts; they are part of cost.
- Keep raw usage and inferred currency in separate columns.
- For public reports, generalize timestamps, round values, suppress small groups, and remove source identifiers.
- Record the owner, approved audience, retention period, and deletion date for the underlying evidence.
