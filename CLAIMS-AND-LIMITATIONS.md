# What these results support — and where I draw the line

## What I can say

CCA-042 is my own-kernel research campaign. I report it separately from the Tern 1.3 fixed-concurrency release comparison; I do not attribute the CCA-042 numbers to Tern 1.3.

> In my Tern 1.3 fixed-concurrency L4 comparisons, Tern recorded higher aggregate throughput than the selected vLLM comparator at 1, 8, 24 and 64 concurrent users: 20.1%, 7.1%, 19.0% and 3.3% respectively. At 128 users the two were effectively level, with Tern 0.1% lower. I ran the comparisons in different L4 sessions and changed the vLLM configuration across concurrency points. These are measurements under the conditions shown, not a universal performance ranking.

> I also recorded a separate historical long-context experiment where the Tern-labelled arm had higher single-user long-prompt throughput. The source metadata disagrees on whether that arm was Tern 1.2 or 1.3. At the multi-user long-context loads tested, it was behind the vLLM FP8 comparator. I have left these results out of the Tern 1.3 headline until I resolve the version attribution.

## Claims I won't make

- I won't say “Tern is 20% faster than vLLM” without specifying the one-user condition.
- I won't claim Tern beats vLLM at every concurrency or is universally faster.
- I won't describe 2,638 tok/s as per-user throughput.
- I won't claim state-of-the-art or industry-leading performance without broad independent comparisons.
- I won't imply that all five concurrency points used the same GPU session or an unchanged vLLM baseline.
- I won't say the long-context issue is fixed based on the 13 November experiment; I have not attached a later post-fix run.
- I won't call the historical long-context experiment a verified Tern 1.3 result. The raw JSON labels the arm/profile Tern 1.2, while my run note says Tern 1.3.
- I won't present the long-context figures as a direct head-to-head without disclosing the different prefill budgets (2,048 Tern; 8,192 vLLM).
- I won't claim random-arrival serving is fixed until fresh tests show it.
- I won't claim CCA-073 bit-serial delivers a speedup.
- I won't claim a quality-preserving energy or cost advantage from the CCA-035 SCI campaign: exact-greedy agreement was below 100% in every suite.
- I won't claim Tern 1.3 is cheaper than vLLM from the fixed-concurrency L4 results; this evidence package has no paired power/energy telemetry for that run.
- I won't present fleet ROI or projected cost reductions as measured facts.

## Where the other research stands

- **CCA-072 KX:** my earlier recorded held-out Spearman was 0.829 against a 0.90 target. Kernel execution and implementation gaps remained.
- **CCA-073 bit-serial:** exploratory. My L4 run labelled the timings non-citable upper bounds and found the tested bit-serial operations slower than the FP16 reference. I make no speed claim.
- **A100 memory architecture:** I have not demonstrated an end-to-end Tern performance gain.
- **Devstral, Coder/Heretic, Mistral and Xortron branches:** porting and integration work, not complete, accepted performance packages.

## How I label evidence

I use four labels: **Measured — repository artefact**, **Historical — superseded**, **My report — awaiting artefact**, and **Projection — not measured**. I want it to be clear which is which.