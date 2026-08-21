# Privacy and Retention

This playbook can process prompts, logs, documents, repository metadata, and operational measurements that contain personal, confidential, or security-sensitive information. Evidence preservation is useful only when it remains lawful, necessary, and controlled.

## 1. Classify before work starts

Assign every input and output one classification:

| Class | Examples | Storage and transfer |
|---|---|---|
| Public | Published sources, synthetic fixtures, approved documentation | May enter Git after verification |
| Internal | Non-public project facts and aggregated operating data | Approved collaborators and controlled storage only |
| Restricted | Credentials, personal data, private prompts, raw logs, session JSONL, customer or employee material | Minimum authorized access; never commit to Git |

When inputs contain mixed classes, apply the highest class unless the required fields can be extracted and verified independently.

## 2. Minimize collection

- Name the exact fields, files, or sections required by the completion condition.
- Prefer structured extracts over copying full source documents or logs.
- Avoid collecting prompt bodies, tool output, local paths, usernames, email addresses, private URLs, hostnames, repository names, or per-session identifiers unless they are required and approved.
- Use synthetic fixtures for examples, tests, and public demonstrations.
- Do not place sensitive values in command-line arguments or standard output when a controlled file or secret store is available.

## 3. Redact before persistence or transfer

Redact or replace:

- credentials, tokens, cookies, private keys, and connection strings;
- names, email addresses, phone numbers, account identifiers, and stable user IDs;
- private repository, project, organization, host, and internal URL identifiers;
- absolute local paths and filenames that contain user or project names;
- exact timestamps, small-group counts, and precise operational totals when they can identify activity or disclose capacity.

Use stable placeholders only when a retry must correlate repeated entities. Keep the mapping outside Git with restricted access and delete it when no longer needed.

## 4. Checkpoints, handoffs, and quarantine

- Checkpoints contain sanitized progress, safe command summaries, unresolved questions, and the next action—not raw logs or full source content.
- Handoffs include only the minimum fields the next worker is authorized to receive.
- Quarantine is an access-controlled, non-Git location. Every item records its owner, reason, approved audience, creation time, and expiry or review date.
- If a failed artifact cannot be sanitized safely, retain a minimal failure record and recollect the source under controlled access if a retry needs it.

## 5. Retention and deletion

The worker contract must define retention. Recommended defaults:

- temporary extracts and raw restricted logs: delete after verification, and no later than seven days unless the operator approves a shorter or longer requirement;
- sanitized checkpoints and rejected-evidence records: review at project close and retain only while they support an active recovery or audit need;
- public artifacts: retain in Git only after the verification gate passes.

Deletion includes temporary directories, generated copies, caches under the operator's control, and restricted quarantine items. Git history is not a deletion mechanism. If sensitive data reaches Git, revoke or rotate credentials first, rewrite affected history when appropriate, and account for clones, forks, pull requests, and cached views.

## 6. Public reporting

- Generalize execution timestamps to an approved reporting period.
- Prefer rounded values, ranges, percentages, or normalized indices over exact totals.
- Suppress small groups and unusual records that enable re-identification.
- Remove provider, model, repository, organization, and environment identifiers unless disclosure is approved.
- State whether metrics are synthetic, rounded, author-reported, or independently verified.
- Publish the method and a synthetic fixture instead of raw private evidence.

## 7. Incident response

If a secret or restricted record is exposed:

1. stop further transfer and preserve only a minimal incident reference;
2. revoke or rotate affected credentials immediately;
3. identify every copied artifact, log, checkpoint, branch, and remote reference;
4. remove or quarantine the data under operator control;
5. notify the responsible owner and follow applicable organizational or legal requirements;
6. update contracts, ignore rules, and verification checks to prevent recurrence.
