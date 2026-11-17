# Methodology, measurements and reproducibility

This document describes the conditions recorded for the results in this repository and, just as importantly, what is not available to independently reproduce them. The published CSV and JSON files are selected aggregate evidence. They are not complete run archives.

## Fixed-concurrency Tern 1.3 comparison

The release comparison records aggregate throughput at five concurrency points on NVIDIA L4 hardware. Each Tern/vLLM pair was interleaved over three rounds on the same card/session. The five concurrency points were collected in separate sessions/cards; they are not one continuous scaling sweep.

The vLLM configuration changed with concurrency:

| Users | Recorded vLLM configuration |
|---:|---|
| 1 | GPTQ-Int8 + EAGLE-3 |
| 8 | GPTQ-Int8 + EAGLE-3 |
| 24 | FP8 + draft |
| 64 | FP8 |
| 128 | FP8 |

This means the table compares Tern against the selected vLLM configuration at each point, not against one fixed vLLM configuration. It should not be used to claim a universal performance ranking or a controlled scaling advantage. At 128 users, the recorded vLLM throughput was marginally higher.

## Throughput

The reported throughput is aggregate tokens per second at the stated concurrency, not throughput per user. The published package does not contain complete raw per-request traces, confidence intervals or run-level distributions for every fixed-concurrency cell. Three interleaved rounds are recorded for each pair, but the aggregate table alone does not show the full variability.

## Quality-gate readings

The fixed-concurrency data contains Tern-side values recorded by the campaign:

| Users | Recorded Tern gate |
|---:|---:|
| 1 | 0.9757 stepwise / 0.9748 prefill |
| 8 | 0.9839 |
| 24 | 0.9826 |
| 64 | 0.9800 |
| 128 | 0.9839 |

These are campaign-specific readings. The public package does not currently provide the complete metric definition, reference outputs, evaluation set, sample/token counts, aggregation procedure or acceptance threshold needed to interpret them independently. It also does not include corresponding vLLM-side quality readings from the same sessions. I therefore do not describe these values as a common accuracy score or use them to claim a quality-adjusted win over vLLM.

The CCA-042 table also reports a campaign gate value of 0.987. Its full scoring specification and supporting evaluation artefacts are not included here. The value is reproduced as recorded, not presented as independently verified evidence of equivalent output quality.

Publishing a complete, comparable quality specification and applying it to both systems is a priority for the next benchmark cycle.

## CCA-042 own-kernel campaign

CCA-042 is a separate own-kernel research campaign on an NVIDIA L4 with Qwen2.5-7B-Instruct at 24 concurrent requests. Its reported throughput comparator is vLLM BF16. It is not the Tern 1.3 fixed-concurrency release comparison, and it is not a comparison against vLLM FP8.

Energy is GPU-board energy. It does not include the full host, networking, cooling or facility overhead. The published results are aggregate campaign readings; this repository does not contain the complete harness, raw per-request traces, environment manifest or quality-evaluation artefacts required for independent reproduction. A same-card repeat with a fully specified FP8 comparator and common quality evaluation remains outstanding.

## Long-context experiment — CCA-071 rev 8g, 13 November 2026

The historical long-context data is in [results/long-context/](results/long-context/). It records one NVIDIA L4 and Qwen2.5-7B-Instruct. The multi-user tests used 1,024-token prompts at 64 and 128 users, and 4,096-token prompts at 32 users. A separate single-user long-prompt gate was also recorded. Each multi-user comparison has three measured rounds.

**Version attribution is unresolved.** The run note describes the Tern arm as Tern 1.3. The raw JSON records arm=tern-1.2-lc, profile=tern-1.2 and label=Tern 1.2. I have not reconciled this against a run manifest, so I do not count the experiment as a verified Tern 1.3 result.

The single-user long-prompt gate values were Tern BF16 KV 0.9839, Tern 8-bit KV 0.9831 and vLLM FP8 0.9727. Recorded throughput was 51.4, 50.1 and 29.1 tok/s respectively. The gate is a fixed reference reading; it does not establish quality at every context length or under multi-user load.

At multi-user load, both Tern-labelled variants were slower than vLLM FP8 in all three tested cells:

- 1,024 tokens / 64 users: 361.8 tok/s BF16 KV; 329.3 8-bit KV; vLLM 408.6.
- 1,024 tokens / 128 users: 345.1; 330.2; vLLM 446.4.
- 4,096 tokens / 32 users: 80.3; 66.3; vLLM 111.4.

The 8-bit KV option held all requested KV tokens in the 128-user/1,024-token and 32-user/4,096-token cells, but was slower than BF16 KV in both. The BF16 KV arm held 78% of requested KV tokens at 128 users/1,024 tokens and 84% at 32 users/4,096 tokens. No multi-user quality gate was recorded.

The prefill budget was 2,048 tokens per step for Tern and 8,192 for vLLM. The cross-framework ratios are descriptive, not a controlled like-for-like comparison.

I have said that I later solved a long-context issue. The artefacts included here do not demonstrate that later fix. I need to attach and identify the relevant post-fix run before making that claim part of the evidence.

## Historical random-arrival experiment

A separate CCA-071 Poisson-arrival report on one L4 is included as historical data. It is not a retest of the current Tern release.

| Arrival rate (req/s) | vLLM tok/s | Tern tok/s | Recorded observation |
|---:|---:|---:|---|
| 2.7 | 339.6 | ~342.1 | Tern TPOT 26.1–26.6 ms vs vLLM 35.1 ms |
| 6.2 | 783.4 | ~786.7 | Tern TPOT 43.7–44.4 ms vs vLLM 37.5 ms |
| 12.8 | 1,644 | 1,539.9–1,623.6 | Tern TTFT 1.51–7.05 s across rounds vs vLLM ~0.17 s |
| 20.6 | 2,396.8–2,420.2 | 1,808.4–2,135.3 | Tern at 0.75–0.88× vLLM throughput; queue/in-flight grew 515→780 |

The report records three rounds. It reports no token mismatches, but no quality-gate reading was recorded. At 20.6 req/s, the published Tern TPOT and vLLM TTFT fields are omitted; I do not infer values for them. These results expose high-load queueing and latency concerns. They need a fresh, fully instrumented retest before being used to describe current behaviour.

## Other reported experiments

- CCA-071 128-user validation is a separate campaign spanning more than one L4 session/card. The aggregate readings are published in the README, but a same-card repeat and fully specified common quality evaluation are outstanding.
- The CCA-071 kernel-scheduling comparison is an ablation within Tern, not a vLLM comparison.
- CCA-043 6-bit KV is a fresh-prompt CPU evaluation. It records top-1 agreement and KV footprint; it is not a GPU throughput result.
- CCA-035 SCI is an A100 performance/energy experiment with only 45.8% exact-greedy agreement. It is not a quality-cleared result. GSR is a separate result from the same campaign.

## What a reproducible run should contain

For each new result, I want to preserve and publish, where rights and dependencies permit:

1. Exact model name, revision and weight format.
2. GPU model, memory capacity, card identity pseudonym, driver, CUDA/runtime and host details.
3. Tern and vLLM build identifiers, dependency versions and complete configuration.
4. Workload definition, prompt IDs, input/output lengths, arrival process, sampling parameters and seeds.
5. Warm-up procedure, run duration, repetitions and measurement boundaries.
6. Per-request TTFT, TPOT, latency percentiles, queue/in-flight counts, throughput and memory measurements.
7. Energy measurement method and sensor boundary.
8. A public quality-evaluation specification, reference outputs, evaluation set, sample/token counts, aggregation and threshold, applied to both systems.
9. Raw outputs or appropriately shareable traces, with hashes and a clear provenance record.

This repository does not yet provide all of those items for the historical experiments. I am treating that as work to do, not implying that the existing evidence is already independently reproducible.
