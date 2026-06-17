# LLM Observatory

An AI cost, value and routing intelligence console for a quant engineering estate. It traces every dollar of inference spend to a cost centre, reframes that spend against the productivity it buys, and runs a transparent routing engine that picks the right-sized model per request. Built as a single self-contained page with no build step and no external calls, so it runs anywhere and cannot fail on a missing dependency.

Live demo: https://sush2328.github.io/llm-observatory/

## Why this exists

Most AI cost tools stop at "here is what you spent". For an engineering org where one productive seat is worth far more than the entire model bill, that framing is incomplete and can lead to the wrong decisions. This console answers three questions an AI operations function actually owns:

1. Where is the spend going, and who is accountable for it (attribution and chargeback).
2. Is that spend generating a return, and where is the return strongest (value and ROI).
3. Are we using the right model for each job, and what does getting that wrong cost (routing).

It then adds the control surface around those three: a what-if simulator for planning, vendor economics for sourcing, and a governance view for risk.

## What is inside

| Section | What it does |
| --- | --- |
| Cost attribution | Per-team chargeback with unit economics and a week-over-week anomaly overlay |
| Value and ROI | Treats AI as a P&L line: estimated value created, net of spend, with a return multiple per team. Assumptions are editable so the model is auditable |
| Routing playground | Scores every eligible model on four dimensions and selects the highest scorer. Fully interactive and inspectable |
| What-if simulator | Live recompute of burn, net value and the 12-month trajectory as you move four operating levers |
| Vendor intelligence | Token pricing, context limits, cache discounts, latency class and a cost-vs-latency view |
| Governance | Alerts, model proliferation, API key hygiene and provider concentration risk |

## The routing engine

Each inbound request is described by four numbers: reasoning complexity, context size needed, latency sensitivity and cost sensitivity. The engine scores every model in the catalogue and selects the highest-scoring eligible one.

1. **Eligibility filter.** A model is excluded if its context window cannot hold the request, or if its reasoning capability is below what the task needs. Excluded models are kept in the log with the reason, for audit.
2. **Per-dimension fit (0 to 100).** Reasoning and context fit reward a model that meets the need without large overkill, since overkill means overpaying. Latency fit comes from the model's latency class. Cost fit is the inverse of the model's blended token price.
3. **Policy weights, reweighted per request.** The org sets base weights (reasoning 35, context 25, latency 20, cost 20). Each request then scales the latency and cost weights by its own sensitivity, and the four weights renormalise to sum to 1. A latency-critical request pulls weight toward fast models; a cost-sensitive batch job pulls weight toward cheap ones.
4. **Score and select.** Final score is the weighted sum of the four fits. Highest eligible score wins; the runner-up and the full breakdown are logged.

This is deliberately a transparent scoring model rather than a black box. Every routing decision can be explained from the inputs, which is what makes it defensible in a governance review.

## The value model

```
seats        = sum of active seats per team
hours_saved  = seats x hours_per_seat_per_week x 4.33 weeks
value        = hours_saved x blended_value_per_hour
net_value    = value - inference_spend
roi          = value / inference_spend
```

Hours saved are sourced from team self-report plus tool telemetry. The two assumptions that move the result, value per hour and hours saved per seat, are exposed as sliders so the case can be stress-tested rather than asserted.

## Architecture

- Single `index.html`. HTML, CSS and JavaScript in one file.
- No frameworks, no build step, no package install, no network calls at runtime.
- Charts are hand-rolled SVG, generated from the data, so there is no charting dependency to break.
- Data is synthetic and internally consistent. Pricing is illustrative and meant to be replaced with negotiated rates.

This stack was a deliberate choice. A live console that depends on a CDN, an API key or a build pipeline has more ways to fail in front of an audience than a single file that runs offline.

## Run locally

Open `index.html` in a browser. That is the whole setup. To serve it over a local web server instead:

```
python3 -m http.server 8000
# then open http://localhost:8000
```

## Deploy

Hosted on GitHub Pages from the `main` branch. Any push to `main` redeploys. See `DEPLOY.md` for the full step by step.

## Notes on data and pricing

All figures are illustrative and exist to demonstrate the model, not to report a real estate. Before using any number in a real setting, replace the team estate in the `TEAMS` array, the model catalogue in `MODELS`, and the pricing with current negotiated rates.
