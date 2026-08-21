# AI Output Verification Gate

An output passes only when required checks have evidence. Mark unavailable checks as `N/A` with a reason; do not count silence as a pass.

## 1. Contract and scope

- [ ] Expected output paths exist and are non-empty.
- [ ] The diff changes only paths owned by the worker.
- [ ] Every completion condition has a result.
- [ ] No unapproved scope, dependency, or product decision was added.
- [ ] Source files and durable records were not overwritten.
- [ ] Input/output classification, approved audience, owner, and retention are recorded.

## 2. Commit and change comparison

- [ ] Compare the working diff or commit with the worker's claimed file list.
- [ ] Inspect unexpected generated files, deletions, binary changes, and lockfile churn.
- [ ] If a commit is cited as evidence, verify repository identity, commit ID, author/committer context, and relevant changed lines.
- [ ] Do not use commit count or lines changed as a proxy for contribution quality.

## 3. Re-execution

- [ ] Rerun deterministic checks in a clean or controlled context.
- [ ] Record command, environment, time, exit code, and relevant output.
- [ ] Distinguish test presence from test execution and test success.
- [ ] If rerun is impossible, state why and lower the evidence grade.

## 4. Claims and prohibited language

- [ ] Every number has a source, unit, time, population, and method.
- [ ] Team or system outcomes are not presented as one person's direct action.
- [ ] Later knowledge is not described as an earlier decision.
- [ ] Search for unsupported absolutes or promotional language: `revolutionary`, `perfect`, `fully solved`, `best`, `optimal`, `dramatically improved`.
- [ ] Search for secrets, personal data, private URLs, internal project codes, and confidential source content.
- [ ] Unverified statements are visibly marked and cannot enter the accepted-fact ledger.

## 5. Privacy and retention

- [ ] Only fields required by the contract were collected and retained.
- [ ] Checkpoints, handoffs, command output, and gate records contain no raw logs, session JSONL, credentials, personal data, private URLs, or local absolute paths.
- [ ] Restricted material is outside Git in an access-controlled location.
- [ ] Quarantined material has an owner, reason, approved audience, and expiry or review date.
- [ ] Temporary extracts and rejected raw artifacts were deleted after verification unless an approved retention requirement applies.
- [ ] If a credential was exposed, it was revoked or rotated before repository-history cleanup.
- [ ] Public metrics use approved aggregation, rounding, time generalization, and small-group suppression.

## 6. Evidence grades

Assign the lowest grade that applies to each material claim:

| Grade | Evidence | Allowed use |
|---|---|---|
| A | Independently rerun or verified against primary source | Publish or automate with stated scope |
| B | Primary artifact inspected; rerun unavailable | Publish with limitation |
| C | Corroborated secondary evidence or worker report plus artifact | Internal draft; operator review required |
| D | Worker assertion, memory, or unverified inference | Do not publish or automate |

## 7. Failure handling

- [ ] Preserve a sanitized failed output and rejection reason in quarantine; never commit raw logs or restricted source content.
- [ ] Classify the failure: model, harness, contract, or environment.
- [ ] Pass reusable artifacts and the revised done condition to the retry.
- [ ] Use an adapter for schema/interface conflicts; return semantic conflicts to the operator.

## Gate record

```markdown
Artifact:
Verifier:
Verified at:
Result: PASS / FAIL / CONDITIONAL
Classification and approved audience:
Owner and retention/deletion date:
Highest-risk claim and grade:
Commands run:
Rejected evidence:
Open review items:
```
