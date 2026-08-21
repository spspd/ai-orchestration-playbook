# Orchestration Playbook

## 1. Separate judgment from execution

The operator owns intent, priority, acceptable risk, and final adoption. Workers own bounded execution: reading named inputs, producing named outputs, and running specified checks. This division prevents a fast worker from silently turning an implementation choice into a product decision.

Use three classes of state:

- **Decision:** a question that requires operator judgment.
- **Task:** an executable contract with ready inputs, or a recorded blocker.
- **Fact:** a claim supported by evidence and ready for reuse.

Store durable facts and decisions in a central ledger. Treat dashboards and task JSON as projections that can be regenerated, not as competing sources of truth.

## 2. Write a self-contained worker contract

A worker should not need hidden chat context. Every contract must state:

1. the purpose and question to answer;
2. exact input paths or sources;
3. exact output paths and ownership;
4. allowed changes;
5. measurable completion conditions;
6. boundaries such as no guessing, no scope expansion, and no overwrite;
7. the return format: changed files, evidence, uncertainty, checks, and next decision.

Use the [worker contract template](../templates/worker-contract.md). If a prompt contains non-ASCII text, pass it through a UTF-8 file when the shell or process launcher may alter argument encoding.

## 3. Parallelize by ownership, not by enthusiasm

Run jobs concurrently only when their writes do not overlap and their inputs are ready. A practical wave looks like this:

1. collect independent sources in parallel;
2. normalize each result into a stable schema;
3. reconcile conflicts through one adapter;
4. assemble a candidate output;
5. verify it independently.

Name one owner for every output. For long jobs, require intermediate artifacts that record completed scope, commands already run, partial findings, and the next safe step. A replacement worker should be able to continue without repeating the expensive part.

Concurrency is a budget, not a target. Increase it while queue time falls and collision, retry, and review costs remain controlled.

## 4. Resolve conflicts with an adapter

An adapter is a narrow coordination job used when independently valid outputs disagree in schema, naming, or assumptions. It must not invent new product scope.

Give the adapter:

- both original contracts and outputs;
- the canonical schema or interface;
- a list of mismatches;
- authority to create a mapping or propose a minimal patch;
- a prohibition on discarding conflicting evidence silently.

The adapter returns a compatibility table, chosen mapping, rejected alternatives, and unresolved decisions. If the conflict changes meaning rather than format, send it back to the operator.

## 5. Verify claims, not completion messages

Worker self-report is a routing signal. Verification requires observable evidence:

- confirm expected files exist and are non-empty;
- inspect the diff against the contract;
- rerun deterministic commands in a clean context when feasible;
- compare claims with source identity, timestamps, and hashes;
- distinguish “test file exists” from “test passed”;
- scan for forbidden phrases, secrets, private identifiers, and unsupported numbers.

Use the [verification gate](../templates/verification-gate.md). A failed gate should preserve the bad result and reason in quarantine or history; deleting it hides the failure mode that the retry needs.

## 6. Classify failure before retrying

Classify a failure at the narrowest useful layer:

| Layer | Example | Recovery |
|---|---|---|
| Model | Unsupported inference | Add evidence and a sharper acceptance test |
| Harness | Lost process or missing event | Inspect artifacts; resume from checkpoint |
| Contract | Two workers own one file | Repartition ownership; add an adapter |
| Environment | Encoding or unavailable browser | Change transport or tool; verify bytes/output |

Retry only the failed layer. Pass the failed attempt, reusable artifacts, and revised done condition to the recovery worker.

## 7. Close the loop

For each accepted result, record:

- the fact or decision;
- source and measurement time;
- command or method used;
- limitations and unresolved questions;
- downstream artifacts that depend on it.

This turns a pile of worker outputs into a reusable system. The unit of progress is a verified state transition, not a model call.
