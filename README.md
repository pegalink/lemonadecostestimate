# Lemonade Credit Meter 🍋

A single-file, no-build cost estimator for [lemonade.gg](https://lemonade.gg) — the Roblox AI coding tool that bills usage in credits (1 credit = $0.20).

**Live site:** https://pegalink.github.io/lemonadecostestimate/

It answers one question: **how many credits will one prompt actually cost**, for each of lemonade's currently available models — Hy3, Gemini 3 Flash, Composer 2.5, Claude Opus 4.6, GPT-5.6 Luna, and GLM 5.2 — all served via OpenRouter at their default reasoning effort.

## What it does

A lemonade prompt isn't one API call — it's an agent loop that, per a lemonade.gg team member, typically runs **300+ steps**, and every step re-sends the whole conversation so far. The calculator models that explicitly:

- Pick a model, or a job-size preset (quick fix / feature / big build), or set your own steps, starting context, context growth per step, and output per step.
- Prompt caching is modeled as a **growing prefix**, not a flat percentage of the whole context: whatever a step already sent last time is (almost) always a cache hit, and only the newly-added tokens are billed fresh. This is calibrated against a real reported data point — GPT-5.6 Luna at ~150-200 steps costing ~0.5 credits — which a flat cache-percentage model overstated by 30-45×.
- Once context nears a model's window, real agent loops summarize/drop older turns instead of growing forever, so context here is capped near the window and holds there for the rest of a long run.
- A "where the money goes" breakdown, a same-job comparison across all six models, and a full pricing table.
- Reasoning effort and cache-hit-rate sliders, plus an optional platform markup multiplier (lemonade's own margin over raw model cost isn't published).

## Prices, and the catches

Pricing is pulled from the OpenRouter models API (default route per model) as of **3 August 2026**:

| Model | $/M in | $/M out |
|---|---|---|
| GPT-5.6 Luna | $0.10 | $0.60 |
| Hy3 | $0.132 | $0.528 |
| GLM 5.2 | $0.615 | $1.932 |
| Composer 2.5 | $0.50 | $2.50 |
| Gemini 3 Flash | $0.50 | $3.00 |
| Claude Opus 4.6 | $5.00 | $25.00 |

1. **GPT-5.6 Luna is currently 50% off** on OpenRouter (a `discount: 0.5` flag on its OpenAI endpoints) — the page has a checkbox to compare against list price once the promo ends. Luna also has a second, pricier tier above 272k prompt tokens, which the tool detects and flags automatically.
2. **Composer 2.5 is not on OpenRouter** — no Cursor models are, as of this writing. Its price here comes from Cursor's own published API pricing, not OpenRouter, and is flagged as such in the model picker.
3. **GLM 5.2's price reflects OpenRouter's default (cheapest) route**, currently Novita at a 56% discount. Lemonade describes it as a "fast option," but there's no separate "-fast" model on OpenRouter — just the one GLM 5.2.

Reasoning-token volume at each model's default effort isn't a published number for any of these models — the per-model multipliers used here are estimates, and the reasoning-effort slider scales them.

## Running it locally

It's one static HTML file with no build step and no dependencies (fonts are inlined as base64 `woff2`, so it works fully offline):

```bash
open index.html          # macOS
xdg-open index.html      # Linux
# or just double-click it, or serve it:
python3 -m http.server 8000
```

## Updating the prices

Everything lives in the `MODELS` array near the top of the `<script>` block in `index.html` — `inp` / `out` / `cache` are $ per 1M tokens. Re-check https://openrouter.ai/api/v1/models when prices move.

## License

No license file yet — treat as "all rights reserved" until one is added. Ask the repo owner before reusing.
