# CCA-071 rev 8g — long-context experiment

**Historical measured result. I have not verified this as a Tern 1.3 run because the source attribution is unresolved.**

- Date: 13 November 2026
- Hardware: one NVIDIA L4
- Model: Qwen2.5-7B-Instruct
- Workloads: 1,024-token prompts at 64 and 128 users; 4,096-token prompts at 32 users; separate single-user long-prompt gate.
- Repetitions: three measured rounds for each multi-user comparison.

## Results

| Workload | Tern BF16 KV | Tern 8-bit KV | vLLM FP8 | BF16 / vLLM | 8-bit / vLLM |
|---|---:|---:|---:|---:|---:|
| Long prompt, 1 user | 51.4 | 50.1 | 29.1 | 1.77x | 1.72x |
| 1,024 tokens, 64 users | 361.8 | 329.3 | 408.6 | 0.89x | 0.81x |
| 1,024 tokens, 128 users | 345.1 | 330.2 | 446.4 | 0.77x | 0.74x |
| 4,096 tokens, 32 users | 80.3 | 66.3 | 111.4 | 0.72x | 0.59x |

Throughput is aggregate tokens/s. The multi-user figures are the median of three round-level measurements transcribed from my experiment note.

## What I found

- The single-user long-prompt gate passed for all three recorded arms: Tern BF16 KV 0.9839, Tern 8-bit KV 0.9831, and vLLM FP8 0.9727.
- At every multi-user long-context load tested, both Tern-labelled variants were slower than the vLLM FP8 comparator.
- The 8-bit KV option held all requested KV tokens in the 128-user/1,024-token and 32-user/4,096-token cells, but it was slower than BF16 KV in both.
- The BF16 KV arm held 78% of requested tokens at 128 users/1,024 tokens and 84% at 32 users/4,096 tokens.
- I did not record a multi-user quality gate.

## Limits on the comparison

I used a 2,048-token prefill budget per step for the Tern arms and 8,192 for vLLM. The cross-framework throughput ratios are descriptive, not a controlled head-to-head. The single-user quality gate is a fixed reference, not proof of quality at every context length.

My experiment note calls the Tern arm Tern 1.3. The raw result JSON records arm=tern-1.2-lc, profile=tern-1.2 and label=Tern 1.2. Until I reconcile that difference, these numbers stay out of my Tern 1.3 headline scorecard.

I have said that I later solved a long-context issue. The CCA branches I reviewed contain this 13 November experiment but no later post-fix result set. I have not attached evidence of the later resolution here.

## Source and sanitisation

I took this summary from my internal CCA research records. I have included aggregate measurements and public model/GPU identifiers only. I have not copied raw JSON, logs, machine identifiers, implementation flags, notebooks or internal repository references.