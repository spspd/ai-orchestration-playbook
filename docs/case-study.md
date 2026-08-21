# Case Study: One-Day Document, Research, and Record Pipeline

## Scope and measurement

This case generalizes a private one-day workflow into a document organization, research, and record-processing pipeline. No private repository code or organization-specific material is included.

Two snapshots answer different questions:

| Snapshot | Scope | Result |
|---|---|---:|
| 13:57:42 KST | Top-level execution workers started before cutoff | 75 sessions |
| 16:25:44 KST | All session JSONL files and their last cumulative usage event | 212 files; 529,682,822 tokens |

The later 212-file total contains 131 top-level execution sessions, 8 interactive sessions, and 73 nested sessions. Total logged usage includes cached input. These figures are operating-cost and observability data: a session-file inventory, not a claim that 212 workers ran independently or concurrently.

## Pipeline

The operator converted broad goals into a task graph, assigned exclusive output ownership, ran source collection in waves, and then routed results through assembly and verification. Durable records held accepted facts and open decisions; generated task and dashboard files were treated as projections.

At the earlier cutoff, 73 of 75 top-level sessions had a completion event. Two lacked one: one had produced an artifact, and one was still active. This demonstrated why event status and actual artifacts must be checked together.

## Failure 1: encoding looked broken

**Symptom.** Early Windows console output displayed non-ASCII text as mojibake.

**Risk.** An operator could accept corrupted files or discard valid ones based only on the terminal display.

**Recovery.** The pipeline separated transport/display from stored bytes. Final JSON, HTML, and Markdown artifacts were decoded explicitly as UTF-8 and checked independently. The files passed even though the original display did not.

**Control added.** Specify UTF-8 for prompt files, standard output, and JSONL parsing. When display and file disagree, validate the file bytes before retrying the generation.

## Failure 2: a worker stalled

**Symptom.** A long-running evidence job stopped making observable progress. Another job ended without a completion event despite leaving a usable file.

**Risk.** Blind retries can repeat expensive collection, while trusting process events can discard valid partial work.

**Recovery.** The operator inspected output files and logs, retained the partial report, and dispatched a finishing job from the interruption point. No full recollection was required.

**Control added.** Long contracts require checkpoints: completed items, pending items, commands already run, reusable local artifacts, and the next safe action. Set an inactivity threshold appropriate to the job and replace a worker only after inspecting its artifacts.

## Failure 3: the wrong repository looked plausible

**Symptom.** A retry found a repository with a similar name and produced a plausible evidence report.

**Risk.** Syntactic success—clone completed, files existed, report rendered—could conceal incorrect source identity.

**Recovery.** The result was marked as wrong-source evidence and excluded. A later collection cross-checked ownership, commit history, contributor identity, and an independent artifact before accepting the correct source.

**Control added.** Source identity is part of the completion condition. For private or ambiguous sources, require at least two independent identity signals. Preserve a warning about the rejected source so later workers do not select it again.

## What the numbers do and do not show

The 529.7-million-token log total shows that context reuse, nested work, and broad collection can dominate a high-throughput day. It does not demonstrate quality, productivity, or cost effectiveness on its own. In the same day, at least six attempts in the earlier operational snapshot were classified as failed or interrupted, and four explicit recovery sessions were needed.

The stronger outcome was operational:

- contracts made outputs and forbidden boundaries inspectable;
- ownership allowed parallel collection without shared-file writes;
- adapters isolated reconciliation work;
- checkpoints reduced retry cost;
- gates caught failures that successful tool execution did not.

## Reusable lessons

1. Freeze a cutoff before counting an active session directory.
2. Report top-level, interactive, and nested sessions separately.
3. Treat the final cumulative usage event as a counter, not something to sum across every event.
4. Check source identity, not only source availability.
5. Preserve failed evidence and hand it to the retry.
6. Require artifact inspection in addition to completion events.
