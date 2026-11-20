# Hi, I'm Abraham

Software engineer working on local LLM inference on AMD hardware. I mostly work on the HIP backend of
[TensorFold](https://github.com/ashhart/TensorFold), on a Ryzen AI Max+ 395 (Strix Halo, gfx1151).

Every change I ship keeps the output byte-identical: the same tokens and the same logits. Each comes with receipts:
- alternated served A/B runs;
- kernel traces;
- the project's kernel contract, with launch records taken from the release toolchain.

## TensorFold: 31 merged pull requests

Highlights:

- **[#1694](https://github.com/ashhart/TensorFold/pull/1694): wide stream rounds reuse cached weights.**
  - Qwen3.8-27B serving is +31% faster at 8 streams and +47% at 16 on gfx1151, with the same tokens.
  - An independent run on RDNA2 (gfx1030) measured +11.6% at 8 streams.
- **[#1663](https://github.com/ashhart/TensorFold/pull/1663): the matrix-core prompt tile without selects and divisions.**
  - Cold prefill of Qwen3.8-27B is 32% faster at 2K and 24.5% faster at 64K (TensorFold 1.2.0).
- **[#1932](https://github.com/ashhart/TensorFold/pull/1932): the planned decode score.**
  - HIP decode is 7.5% to 18.9% faster at 32K and 64K context on Qwen3.6-35B-A3B and Qwen3.8-27B (1.2.2).
- **[#3508](https://github.com/ashhart/TensorFold/pull/3508): the planned decode walk scores head size 256 without element tests.**
  - Decode is 14% to 32% faster at 131K and 258K tokens of context on Qwen3.6-35B-A3B and Qwen3.8-27B, with the same bits (1.2.10).
- **[#3728](https://github.com/ashhart/TensorFold/pull/3728): a lone stream's copy windows are priced from its own timed rounds.**
  - With drafts on, decode is 15% to 39% faster at 131K and 258K tokens of context on Qwen3.6-35B-A3B and Qwen3.8-27B, with the same tokens (fixes [#3390](https://github.com/ashhart/TensorFold/issues/3390); next release).
- **[#3273](https://github.com/ashhart/TensorFold/pull/3273): a plain prompt of 1 or 2 rows takes the 2-row K-parallel tile on gfx1151.**
  - A resumed prompt with 1 or 2 rows left reaches its first token 48% and 64% sooner on Qwen3.8-27B, and 18% and 27% sooner on Qwen3.6-35B-A3B (1.2.10).
- **[#2878](https://github.com/ashhart/TensorFold/pull/2878): the routed prompt tile takes K-parallel passes for experts of few rows.**
  - Cold prompts of 64 to 256 tokens on Qwen3.6-35B-A3B are 11% to 22% faster at one stream, with the same tokens (1.2.8).
- **[#1855](https://github.com/ashhart/TensorFold/pull/1855): narrowest stream columns.**
  - Qwen3.6-35B-A3B chat is about 18% faster with drafts on, and Qwen3.8-27B about 5% (1.2.2).
- **[#2455](https://github.com/ashhart/TensorFold/pull/2455): sampled streams draft with the graphed head chain.**
  - Sampled serving of Qwen3.6-35B-A3B with MTP drafts is 4.1% to 11.6% faster on gfx1151, with the same tokens.
- **[#2582](https://github.com/ashhart/TensorFold/pull/2582): @davidtai's prompt DeltaNet kernels from [#1667](https://github.com/ashhart/TensorFold/pull/1667), carried onto main with gfx1151 receipts.**
  - Prompts take 5-6% less time on Qwen3.8-27B and 6-10% less on Qwen3.6-35B-A3B, with the same bytes.
- **[#1830](https://github.com/ashhart/TensorFold/pull/1830): shared rounds timed at the served streams.**
  - Multi-stream drafting no longer drops into a slower mode (fixes #1695, 1.2.2).

| PR | Change | Release |
|---|---|---|
| [#1542](https://github.com/ashhart/TensorFold/pull/1542) | opt-in `-Dhip-relax-branches` for gfx1100 under clang 21 | 1.2.0 |
| [#1648](https://github.com/ashhart/TensorFold/pull/1648) | banded prefill WMMA GEMM: row blocks share weights | 1.2.0 |
| [#1663](https://github.com/ashhart/TensorFold/pull/1663) | matrix tile inner loop free of selects and divisions | 1.2.0 |
| [#1685](https://github.com/ashhart/TensorFold/pull/1685) | prompt GEMMs write the activation type directly | 1.2.1 |
| [#1788](https://github.com/ashhart/TensorFold/pull/1788) | prompt attention keeps its probabilities in registers, 6 waves | 1.2.1 |
| [#1830](https://github.com/ashhart/TensorFold/pull/1830) | shared rounds timed at the served streams and row buckets | 1.2.2 |
| [#1855](https://github.com/ashhart/TensorFold/pull/1855) | narrowest stream columns at one- and two-row launches | 1.2.2 |
| [#1932](https://github.com/ashhart/TensorFold/pull/1932) | decode score: four keys a warp on long spans | 1.2.2 |
| [#1961](https://github.com/ashhart/TensorFold/pull/1961) | decode softmax stats and partial sums load sixteen ahead | 1.2.2 |
| [#2007](https://github.com/ashhart/TensorFold/pull/2007) | decode router reads the bf16 router rows the model holds | 1.2.3 |
| [#2099](https://github.com/ashhart/TensorFold/pull/2099) | prompt residual add and post-mixer norm in one launch | 1.2.3 |
| [#2117](https://github.com/ashhart/TensorFold/pull/2117) | prompt conv writes q, k and v to their own rows | 1.2.3 |
| [#1694](https://github.com/ashhart/TensorFold/pull/1694) | wide stream rounds reuse cached weights | 1.2.3 |
| [#2233](https://github.com/ashhart/TensorFold/pull/2233) | decode records re-derived with the release hipcc (HIP 7.16) | 1.2.3 |
| [#2247](https://github.com/ashhart/TensorFold/pull/2247) | decode tail issues a row's loads before its branches | 1.2.4 |
| [#2374](https://github.com/ashhart/TensorFold/pull/2374) | resumed == fresh checks start their fresh runs from nothing | 1.2.4 |
| [#2314](https://github.com/ashhart/TensorFold/pull/2314) | DFlash2 part 1: tap a drafter's layers into each stream's ring | 1.2.5 |
| [#2455](https://github.com/ashhart/TensorFold/pull/2455) | sampled streams draft with the graphed head chain | 1.2.5 |
| [#2477](https://github.com/ashhart/TensorFold/pull/2477) | DFlash2 part 2: drafter config and affine-4 weights | 1.2.5 |
| [#2652](https://github.com/ashhart/TensorFold/pull/2652) | shared rows timed with a token a row, lone windows on one token | 1.2.6 |
| [#2661](https://github.com/ashhart/TensorFold/pull/2661) | shared rows' token stride coprime to the vocabulary's span | 1.2.6 |
| [#2087](https://github.com/ashhart/TensorFold/pull/2087) | skewed-row matrix tile, three blocks a WGP on gfx1151 | 1.2.6 |
| [#2582](https://github.com/ashhart/TensorFold/pull/2582) | @davidtai's prompt DeltaNet kernels, carried with gfx1151 receipts | 1.2.6 |
| [#2674](https://github.com/ashhart/TensorFold/pull/2674) | CUDA's DFlash2 drafter config refuses a tap layer named twice | 1.2.6 |
| [#2545](https://github.com/ashhart/TensorFold/pull/2545) | DFlash2 part 3: drafter kernels, each with a planted control | 1.2.6 |
| [#2706](https://github.com/ashhart/TensorFold/pull/2706) | GPU test of Qwen3.8-Flash-Next's fused prompt prep through the layer, bit for bit | 1.2.7 |
| [#2726](https://github.com/ashhart/TensorFold/pull/2726) | DFlash2 part 4: the drafter's engine for the qwen3_5 target | 1.2.8 |
| [#2878](https://github.com/ashhart/TensorFold/pull/2878) | routed prompt tile takes K-parallel passes for experts of few rows | 1.2.8 |
| [#3273](https://github.com/ashhart/TensorFold/pull/3273) | a plain prompt of 1 or 2 rows takes the 2-row K-parallel tile on gfx1151 | 1.2.10 |
| [#3508](https://github.com/ashhart/TensorFold/pull/3508) | the planned decode walk scores head size 256 without element tests | 1.2.10 |
| [#3728](https://github.com/ashhart/TensorFold/pull/3728) | a lone stream's copy windows are priced from its own timed rounds | next release |

In review:
- [#3521](https://github.com/ashhart/TensorFold/pull/3521): a routed prompt plan keeps the shared expert whole, and gfx1151 takes the GEMM tile from 33 rows; prompt passes of 33 to 63 rows on Qwen3.6-35B-A3B take 8% to 20% less time, with the same bytes.
- [#3665](https://github.com/ashhart/TensorFold/pull/3665): DFlash2 part 5, `serve` runs Qwen3.8-27B with its DFlash2 drafter on HIP; at one stream and temperature 0, decode is 2.25x faster in code and 1.87x in chat.
- [#4207](https://github.com/ashhart/TensorFold/pull/4207): Qwen prompts take the policy's 4,096-row prompt step on a lone RDNA 3.5 iGPU; on gfx1151 the cold first token of Qwen3.6-35B-A3B comes 9% to 18% sooner from 2K to 64K prompt tokens, with the same bytes.

Proposed in an issue, landed by the maintainer:
- [#3765](https://github.com/ashhart/TensorFold/issues/3765): fetch-inactive on prompt attention's `v_permlane16`, which drops a copy the tile never needs; cold prompts are 1.5% to 2.7% faster at 131K to 261K tokens on both checkpoints, with the same bits. Landed as [#4080](https://github.com/ashhart/TensorFold/pull/4080), next release.

Proposed, issue first:
- [#3811](https://github.com/ashhart/TensorFold/issues/3811): the rows of a decode round read a stream's keys and values once instead of once a row; at kernel level a round of 2 to 4 rows walks its attention in 16% to 30% less time at 131K to 262K tokens on both checkpoints' shapes, with the same bits.
- [#3939](https://github.com/ashhart/TensorFold/issues/3939): a stream count's shared rounds get their plain price in the count's first rounds instead of its 128th; in a local prototype the first drafted batches of four streams on Qwen3.6-35B-A3B run 8% to 88% faster at 2K to 131K tokens of context, with the same tokens.
- [#4061](https://github.com/ashhart/TensorFold/issues/4061): a Qwen engine with no MTP head gets a plain price for its shared rounds; in a local prototype four streams with copies run 10% to 43% faster on Qwen3.8-27B at 8K to 131K tokens of context, and 12% to 42% faster from 32K on Qwen3.6-35B-A3B served without MTP drafts, with the same tokens.

In progress: DFlash2 speculative decoding on HIP for Qwen3.8-27B, as six chained PRs. Parts 1 to 4 are merged
([#2314](https://github.com/ashhart/TensorFold/pull/2314), [#2477](https://github.com/ashhart/TensorFold/pull/2477),
[#2545](https://github.com/ashhart/TensorFold/pull/2545), [#2726](https://github.com/ashhart/TensorFold/pull/2726)); parts 1 and 2 ship in 1.2.5, part 3 in 1.2.6 and part 4 in 1.2.8. Part 5, the serving wiring, is in review ([#3665](https://github.com/ashhart/TensorFold/pull/3665)), and part 6, depth control for shared rounds, is in review on top of it ([#3918](https://github.com/ashhart/TensorFold/pull/3918)). Measured locally, the whole chain decodes 2.5x faster in code and 2x
in chat at one stream, with the same tokens.
