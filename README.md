# localmaxxing-arena

Finding the best LLM we can run at home. Local recipes (model × quantized weights × engine × hardware)
compete on the same frozen benchmarks. Each model's official API runs too, as a reference line: it shows
how much a recipe gives up, or doesn't.

## Leaderboard

One row per recipe, one score column per benchmark with `display = true` in
[leaderboard.toml](leaderboard.toml), ranked by **Rating**: an Elo-scale rating fitted on the trials of
every benchmark with `rating = true`, so recipes that ran only some benchmarks still rank fairly against
the rest. *Coverage* is how many current tasks a recipe has run;
fewer tasks means a wider interval. Each score pools every run of that recipe; *k=1/3* flags a score
from fewer attempts than the benchmark defines. Recipe names link to the recipe (exact weights, engine, flags,
hardware), scores to the recipe's per-task stats and runs, and benchmark headers to per-task results and agent
behaviour. *PP 32K* / *TG 32K* are single-stream prefill and decode speeds at a 32K-token context (tok/s,
averaged over the recipe's speed measurements), so you can see how fast a recipe feels on its hardware.
They are shown for information only and don't affect the Rating or the ranking.
*API reference* rows are official APIs; *subscription proxy* rows are unofficial reverse proxies over a
subscription (not an official API, and not reproducible from an API key); *anchor* recipes run every
benchmark to keep ratings comparable across them. The table is rebuilt automatically after every merge.

<!-- leaderboard:start -->
| Recipe | Weights | Reasoning | Hardware | PP 32K (tok/s) | TG 32K (tok/s) | [BFCL-100](benches/bfcl-100/RESULTS.md) | Rating (95% CI) | Coverage |
|---|---|---|---|---|---|---|---|---|
| [DeepSeek-V4.1-Flash · vLLM TP8](recipes/deepseek-v4.1-flash@official-fp8-fp4-vllm-tp8-8xgb10.toml) | official FP8/FP4 | low | 8× GB10 | 9,386 | 96.5 | [69.0%](benches/bfcl-100/recipes/deepseek-v4.1-flash@official-fp8-fp4-vllm-tp8-8xgb10.md) | **1195** (1086–1303) | 100/100 |
| [DeepSeek-V4.1-Flash · Mia EAQ · TensorFold Zig TP2](recipes/deepseek-v4.1-flash@exl3-eaq-2.9bpw-tensorfold-zig-2xspark.toml) | EXL3 EAQ 2.9bpw | low | 2× DGX Spark | 1,759 | 58.5 | [67.0%](benches/bfcl-100/recipes/deepseek-v4.1-flash@exl3-eaq-2.9bpw-tensorfold-zig-2xspark.md) | **1185** (1081–1288) | 100/100 (+10 not shown) |
| [DeepSeek-V4.1-Flash · TensorFold TP2](recipes/deepseek-v4.1-flash@exl3-2.9bpw-tensorfold-2xspark.toml) *(anchor)* | EXL3 2.9bpw | low | 2× DGX Spark | 1,056 | 58.4 | [68.0%](benches/bfcl-100/recipes/deepseek-v4.1-flash@exl3-2.9bpw-tensorfold-2xspark.md) | **1183** (1075–1291) | 100/100 |
| [GLM-5.3 · vLLM TP8](recipes/glm-5.3@int4-int8mix-vllm-tp8-8xgb10.toml) | Int4/Int8 mix | low | 8× GB10 | 2,800 | 62.9 | [56.0%](benches/bfcl-100/recipes/glm-5.3@int4-int8mix-vllm-tp8-8xgb10.md) | **1052** (947–1157) | 100/100 |
| [GLM-5.3-Flash · Mia EXL3 · TensorFold TP2](recipes/glm-5.3-flash@exl3-4bpw-tensorfold-2xspark.toml) | EXL3 4bpw | low | 2× DGX Spark | 1,655 | 49.2 | [55.0%](benches/bfcl-100/recipes/glm-5.3-flash@exl3-4bpw-tensorfold-2xspark.md) | **1041** (937–1146) | 100/100 |
| [GPT-6-Astra · Codex subscription proxy](recipes/gpt-6-astra@codex-proxy.toml) *(subscription proxy)* | cloud (Codex) | low | Codex OAuth proxy | - | - | [52.0%](benches/bfcl-100/recipes/gpt-6-astra@codex-proxy.md) | **1010** (906–1114) | 100/100 |
| [MiniMax-M3.1-Flash · vLLM TP8](recipes/minimax-m3.1-flash@official-mxfp8-nvfp4-vllm-tp8-8xgb10.toml) | Official MXFP8/NVFP4 | low | 8× GB10 | 2,780 | 73.0 | [46.0%](benches/bfcl-100/recipes/minimax-m3.1-flash@official-mxfp8-nvfp4-vllm-tp8-8xgb10.md) | **948** (844–1052) | 100/100 |
| [GPT-6-Luna · Codex subscription](recipes/gpt-6-luna@codex-subscription.toml) *(subscription proxy)* | cloud (Codex) | low | Codex OAuth | - | - | - | **846** (595–1096) | hidden benches only |

Not shown as a column: [SciCode-10](benches/scicode-10/RESULTS.md) (not rated), [SciCode-10-v2](benches/scicode-10-v2/RESULTS.md) (rating)
<!-- leaderboard:end -->

## Benchmarks

| Bench | What it measures | Definition | Results |
|---|---|---|---|
| [bfcl-100](benches/bfcl-100/) | Multi-turn tool use: 100 BFCL v3 multi-turn tasks (base, missing function, missing parameter, long context), low reasoning | [bench.toml](benches/bfcl-100/bench.toml) | [RESULTS.md](benches/bfcl-100/RESULTS.md) |
| [scicode-10](benches/scicode-10/) | Scientific computing: the 10 hardest SciCode test problems (7–15 sequential sub-steps), fractional reward per sub-step | [bench.toml](benches/scicode-10/bench.toml) | no results yet |

## Contributing

Ran a model on your own hardware? Add it: [CONTRIBUTING.md](CONTRIBUTING.md) covers writing a recipe,
running a benchmark and submitting the result as a pull request. A single result is enough to get on
the leaderboard, and runs from different people pool. Want a model benchmarked but can't run it?
[Open a recipe request](https://github.com/kky42/localmaxxing-arena/issues/new?template=recipe-request.yml).
