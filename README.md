# Hi, I'm Abraham

Software engineer working on local LLM inference on AMD hardware. I mostly work on the HIP backend of
[TensorFold](https://github.com/ashhart/TensorFold), on a Ryzen AI Max+ 395 (Strix Halo, gfx1151).

Every change I ship keeps the output byte-identical: the same tokens and the same logits. Each comes with receipts:
- alternated served A/B runs;
- kernel traces;
- the project's kernel contract, with launch records taken from the release toolchain.

## TensorFold: 21 merged pull requests

Highlights:

- **[#1694](https://github.com/ashhart/TensorFold/pull/1694): wide stream rounds reuse cached weights.**
  - Qwen3.8-27B serving is +31% faster at 8 streams and +47% at 16 on gfx1151, with the same tokens.
  - An independent run on RDNA2 (gfx1030) measured +11.6% at 8 streams.
- **[#1663](https://github.com/ashhart/TensorFold/pull/1663): the matrix-core prompt tile without selects and divisions.**
  - Cold prefill of Qwen3.8-27B is 32% faster at 2K and 24.5% faster at 64K (TensorFold 1.2.0).
- **[#1932](https://github.com/ashhart/TensorFold/pull/1932): the planned decode score.**
  - HIP decode is 7.5% to 18.9% faster at 32K and 64K context on Qwen3.6-35B-A3B and Qwen3.8-27B (1.2.2).
- **[#1855](https://github.com/ashhart/TensorFold/pull/1855): narrowest stream columns.**
  - Qwen3.6-35B-A3B chat is about 18% faster with drafts on, and Qwen3.8-27B about 5% (1.2.2).
- **[#2455](https://github.com/ashhart/TensorFold/pull/2455): sampled streams draft with the graphed head chain.**
  - Sampled serving of Qwen3.6-35B-A3B with MTP drafts is 4.1% to 11.6% faster on gfx1151, with the same tokens.
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
| [#2652](https://github.com/ashhart/TensorFold/pull/2652) | shared rows timed with a token a row, lone windows on one token | unreleased |
| [#2661](https://github.com/ashhart/TensorFold/pull/2661) | shared rows' token stride coprime to the vocabulary's span | unreleased |

In review:
- [#2087](https://github.com/ashhart/TensorFold/pull/2087): a skewed-row matrix tile with three blocks a WGP on gfx1151.
- [#2545](https://github.com/ashhart/TensorFold/pull/2545): DFlash2 part 3, the drafter kernels, each with a planted contract control.
- [#2582](https://github.com/ashhart/TensorFold/pull/2582): @davidtai's prompt DeltaNet kernels from
  [#1667](https://github.com/ashhart/TensorFold/pull/1667), carried onto main with gfx1151 receipts. Prompts take 5-6% less time
  on Qwen3.8-27B and 6-10% less on Qwen3.6-35B-A3B, with the same bytes.

In progress: DFlash2 speculative decoding on HIP for Qwen3.8-27B, as six chained PRs. Parts 1 and 2 are merged
([#2314](https://github.com/ashhart/TensorFold/pull/2314), [#2477](https://github.com/ashhart/TensorFold/pull/2477));
they ship in 1.2.5, and part 3 is in review. Measured locally, the whole chain decodes 2.5x faster in code and 2x
in chat at one stream, with the same tokens.
