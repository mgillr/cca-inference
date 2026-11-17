# How I ran these benchmarks

## Fixed-concurrency results

The Tern 1.3 freeze compares aggregate throughput with vLLM at five concurrency points on an NVIDIA L4. For each Tern/vLLM pair, I ran three interleaved rounds on the same card/session. I collected the five concurrency points in different L4 sessions/cards.

The vLLM configuration was not the same at every concurrency level: GPTQ-Int8 + EAGLE-3 at 1 and 8 users; FP8 + draft at 24; and FP8 at 64 and 128. So this is not a controlled scaling sweep against one fixed vLLM configuration.

## What the throughput numbers mean

I report aggregate tokens per second at the stated concurrency, not throughput per user. This package does not include complete raw per-request traces, confidence intervals or a full common quality evaluation for every cell.

## Tern quality-gate readings

| Users | Recorded gate |
|---:|---:|
| 1 | 0.9757 stepwise / 0.9748 prefill |
| 8 | 0.9839 |
| 24 | 0.9826 |
| 64 | 0.9800 |
| 128 | 0.9839 |

These are the values recorded by my tests, not universal accuracy scores. I have not included quality readings for the vLLM runs from the same sessions. That needs to be part of the next properly matched comparison.

## Long-context experiment — CCA-071 rev 8g (13 November 2026)

I found this older long-context run in my CCA repository and included its sanitised aggregate measurements in [results/long-context/](results/long-context/). It used one NVIDIA L4 and Qwen2.5-7B-Instruct. I tested 1,024-token prompts at 64 and 128 users, 4,096-token prompts at 32 users, and a separate single-user long-prompt gate. Each multi-user comparison has three measured rounds.

**The version labels do not agree.** My run note calls the Tern arm Tern 1.3. The raw JSON files I reviewed identify arm=tern-1.2-lc, profile=tern-1.2 and label=Tern 1.2. Until I can reconcile that against the run manifest, I am not counting these as verified Tern 1.3 results.

The single-user long-prompt gate passed for all three recorded arms: Tern BF16 KV 0.9839, Tern 8-bit KV 0.9831, and vLLM FP8 0.9727. Throughput was 51.4, 50.1 and 29.1 tok/s respectively. I use this as a fixed reference gate; it does not establish quality at every context length.

At multi-user load, both Tern-labelled variants were slower than vLLM FP8 in all three tested cells:
- 1,024 tokens / 64 users: 361.8 tok/s BF16 KV; 329.3 8-bit KV; vLLM 408.6.
- 1,024 tokens / 128 users: 345.1; 330.2; vLLM 446.4.
- 4,096 tokens / 32 users: 80.3; 66.3; vLLM 111.4.

The 8-bit KV option held all requested KV tokens in the 128-user/1,024-token and 32-user/4,096-token cells, but it was slower than BF16 KV in both. The BF16 KV arm held 78% of requested KV tokens at 128 users/1,024 tokens and 84% at 32 users/4,096 tokens. I did not record a multi-user quality gate.

**The comparison has another limitation:** I used a 2,048-token prefill budget per step for the Tern arms and 8,192 for vLLM. The cross-framework ratios are descriptive, not a controlled like-for-like comparison.

I have said that I later solved a long-context issue. I checked the current CCA branches and the run artefacts available to me. The 13 November run above is the long-context result set I found; I did not find a later post-fix run. I have not included evidence of the reported fix here. That does not mean the fix does not exist; I need to attach the run that demonstrates it.

## Historical random-arrival experiment

I also recorded a separate CCA-071 Poisson-arrival report on one L4. These are historical results, not evidence that I have retested the current Tern release.

| Arrival rate (req/s) | vLLM tok/s | Tern tok/s | What I recorded |
|---:|---:|---:|---|
| 2.7 | 339.6 | ~342.1 | Tern TPOT 26.1–26.6 ms vs 35.1 ms |
| 6.2 | 783.4 | ~786.7 | Tern TPOT 43.7–44.4 ms vs 37.5 ms |
| 12.8 | 1,644 | 1,539.9–1,623.6 | Tern TTFT 1.51–7.05 s across rounds vs ~0.17 s |
| 20.6 | 2,396.8–2,420.2 | 1,808.4–2,135.3 | Tern 0.75–0.88x throughput; queue/in-flight grew 515→780 |

I recorded no token mismatches in that run, but I did not record a quality-gate reading. I need to rerun this under comparable conditions before making a claim about current high-load latency or queueing.

## What I want recorded in future runs

For someone else to reproduce a result, the record should include the exact model/revision; GPU, memory, driver and runtime; Tern/vLLM build identifiers; comparator configuration; prompt set and input/output lengths; sampling/seeds; warm-up, run duration and repetitions; throughput, TTFT, TPOT, p50/p95/p99, memory and energy where available; same-workload quality/correctness; and raw outputs with SHA-256 hashes.