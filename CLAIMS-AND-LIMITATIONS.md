# What the evidence supports — and where I draw the line

I want this repository to be useful to people who will scrutinise the work, not just read the headline numbers. The distinction I make throughout is between a measurement recorded in a campaign, an interpretation supported by the available artefacts, and a claim that still needs a better experiment.

## What I can say

- **CCA-042:** In one L4 campaign on Qwen2.5-7B-Instruct at 24 concurrent requests, I recorded 1.52–1.63× aggregate throughput and 34.5–39.6% lower GPU-board energy than the vLLM BF16 comparator across the listed workloads. The campaign also recorded a gate value of 0.987. The public package does not define that gate sufficiently to establish independently comparable output quality. The result has not yet been repeated under a fully documented, independently reproducible setup, and the comparator is BF16 rather than vLLM FP8. This is a promising result to test, not proof of a general quality-preserving advantage.
- **Tern 1.3 fixed concurrency:** Under the recorded conditions, Tern aggregate throughput was higher than the selected vLLM comparator at 1, 8, 24 and 64 users by 20.1%, 7.1%, 19.0% and 3.3%. At 128 users, Tern was 0.1% lower. Each pair was interleaved over three rounds on the same L4 card/session, but the concurrency points came from separate sessions/cards and the vLLM configuration changed across rows. The recorded Tern gate values are not a common, independently interpretable quality comparison.
- **Historical random arrivals:** The separate CCA-071 report records near-parity throughput at 2.7 and 6.2 req/s, materially worse Tern TTFT at 12.8 req/s, and 0.75–0.88× Tern/vLLM throughput at 20.6 req/s while the Tern queue/in-flight count grew from 515 to 780. No quality gate was recorded. This is historical evidence and a reason to retest high-load behaviour, not a current-release performance claim.
- **Kernel scheduling:** The CCA-071 within-Tern ablation records 14.0–22.9% higher throughput across the tested concurrency points over three interleaved rounds, with outputs recorded as identical. This is not a vLLM comparison.
- **6-bit KV:** CCA-043 records 0.981 top-1 agreement and 37.5% of the FP16 KV footprint in a fresh-prompt CPU evaluation. It does not establish faster GPU inference.
- **CCA-035 SCI:** The A100 experiment records a large throughput and energy-intensity change, but exact-greedy agreement was 45.8%. It is a performance/energy signal, not a quality-cleared result.

## Claims I won't make

- I won't say “Tern is 20% faster than vLLM” without stating the one-user condition, comparator configuration and test context.
- I won't claim Tern beats vLLM at every concurrency; at 128 users the selected vLLM comparator was marginally faster.
- I won't describe aggregate tokens per second as per-user throughput.
- I won't describe the CCA-042 result as a win over vLLM FP8. The published comparator is vLLM BF16.
- I won't claim that the CCA-042 gate value proves equivalent output quality. Its full definition and evaluation artefacts are not published here.
- I won't claim a quality-adjusted Tern advantage from the fixed-concurrency table. The gate is not fully specified and corresponding vLLM-side quality readings are absent.
- I won't imply that all fixed-concurrency points used the same GPU session or an unchanged vLLM baseline.
- I won't call the historical long-context experiment a verified Tern 1.3 result. The run note and raw JSON disagree on whether the arm was Tern 1.2 or 1.3.
- I won't present the long-context figures as a controlled head-to-head. The prefill budgets differed (2,048 tokens per step for Tern; 8,192 for vLLM), and Tern was slower at every tested multi-user cell.
- I won't claim the long-context issue was fixed based on the 13 November experiment. The later post-fix evidence is not included here.
- I won't claim random-arrival serving is fixed. The historical high-load run shows a throughput deficit and growing queue/in-flight count.
- I won't claim CCA-073 bit-serial delivers a speedup.
- I won't claim a quality-preserving energy or cost advantage from the CCA-035 SCI campaign.
- I won't claim Tern 1.3 is cheaper than vLLM from the fixed-concurrency results; there is no paired power/energy telemetry for that comparison.
- I won't present fleet ROI, projected savings, state-of-the-art status or industry-leading performance as measured facts.

## Where the other research stands

- **CCA-072 KX:** my earlier recorded held-out Spearman was 0.829 against a 0.90 target. Kernel execution and implementation gaps remained.
- **CCA-073 bit-serial:** exploratory. My L4 run labelled the timings non-citable upper bounds and found the tested bit-serial operations slower than the FP16 reference. I make no speed claim.
- **A100 memory architecture:** I have not demonstrated an end-to-end Tern performance gain.
- **Devstral, Coder/Heretic, Mistral and Xortron branches:** porting and integration work, not complete, accepted performance packages.

## Evidence labels

I use these labels to distinguish the status of material:

- **Measured — repository artefact:** a measurement with a corresponding aggregate artefact in this repository. This does not automatically mean independently reproduced.
- **Historical — superseded:** an older result retained for context, not evidence of current-release behaviour.
- **My report — awaiting artefact:** a result I have described but for which the supporting artefact is not included here.
- **Projection — not measured:** an estimate or target, not an observed result.

## What would strengthen the evidence

The main gaps are a public quality metric and evaluation set, matched comparator-side quality measurements, a fixed and fully documented baseline, complete benchmark harness and environment manifests, raw per-request traces, run-level variance, and independent replication. I want the next iteration to close those gaps rather than expand the headline claims.
