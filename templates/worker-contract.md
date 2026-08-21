# Worker Contract Template

Use one contract per independently verifiable job.

## Blank form

```markdown
# Task: <short name>

## Purpose
<One outcome and the question this job must answer.>

## Inputs
- Read: `<exact path or source>`
- Classification: public / internal / restricted
- Allowed fields or sections: <minimum required subset>
- Assumptions already approved: <list or none>

## Owned outputs
- Create/update: `<exact path>`
- Classification and approved audience: <value>
- Retention or review date: <value>
- No other worker may write these paths during this job.

## Allowed work
- <operation>
- <operation>

## Completion conditions
- [ ] <observable artifact condition>
- [ ] <command and expected result, or manual criterion>
- [ ] Output was checked for credentials, personal data, private URLs, local paths, and confidential source content.
- [ ] Report changed files, evidence, uncertainty, checks, and next decision.

## Forbidden boundaries
- Do not guess missing facts.
- Do not expand scope or modify unlisted paths.
- Do not overwrite source material.
- Do not include credentials, personal data, or private-source content in the report.
- Do not copy raw logs, session JSONL, full prompts, or unrestricted source content into Git, checkpoints, or handoffs.
- Do not transfer restricted inputs to another worker unless the contract explicitly authorizes the recipient and fields.

## Failure and checkpoint protocol
- After <threshold>, write a sanitized checkpoint containing completed scope, partial
  findings, safe command summaries, current blocker, and next safe action.
- Store raw restricted artifacts only in an approved non-Git location with access control,
  an owner, and an expiry. Otherwise retain only a redacted reference and rejection reason.
- Delete temporary extracts after verification unless the contract states an approved retention need.

## Return format
1. Changed files
2. Evidence and source identity
3. Uncertainties or rejected evidence
4. Verification performed
5. Data handling and deletion performed
6. Next operator decision
```

## Filled example

```markdown
# Task: Normalize three public research summaries

## Purpose
Convert three source summaries into one comparable table. Answer: which claims are
supported by at least two sources?

## Inputs
- Read: `work/source-a.md`
- Read: `work/source-b.md`
- Read: `work/source-c.md`
- Classification: public
- Allowed fields or sections: claim text, source URL, and publication date.
- Assumptions already approved: publication date is stored as ISO 8601.

## Owned outputs
- Create: `out/comparison.md`
- Create: `out/rejected-evidence.md`
- Classification and approved audience: public after verification.
- Retention or review date: review when any source changes or is withdrawn.
- No other worker may write these paths during this job.

## Allowed work
- Extract claim, source URL, publication date, and support level.
- Merge only claims with equivalent meaning.

## Completion conditions
- [ ] Every input claim appears in comparison.md or rejected-evidence.md.
- [ ] `comparison.md` has columns: claim, sources, date range, confidence, caveat.
- [ ] Run the repository link checker; report its exit status.
- [ ] Output contains no credentials, personal data, private URLs, local paths, or non-public source content.
- [ ] Report changed files, evidence, uncertainty, checks, and next decision.

## Forbidden boundaries
- Do not fetch additional sources.
- Do not infer publication dates or author identity.
- Do not modify `work/`.
- Do not label a single-source claim as corroborated.

## Failure and checkpoint protocol
- If a source cannot be decoded or parsed, record its path and error in
  `out/rejected-evidence.md`; continue with the remaining inputs.
- Preserve ambiguous mappings as separate rows.

## Return format
1. Changed files
2. Evidence and source identity
3. Uncertainties or rejected evidence
4. Link-check result
5. Data handling and deletion performed
6. Claims requiring operator review
```
