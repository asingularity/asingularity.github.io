---
layout: page
title: Business Agent
description: AI agent for autonomous business decisions via MCTS and Bayesian inference
img: assets/img/projects/business-agent-architecture.png
importance: 4
category: research
---

A simulated business environment where an AI agent autonomously makes weekly pricing, marketing budget, and inventory decisions. Designed as a controlled experimental laboratory to answer the question: which components of AI intelligence — inference, planning, and action generation — actually contribute to business decision performance, and under what conditions?

Inspired by the [PlanU paper](https://arxiv.org/pdf/2510.18442), the system uses Monte Carlo Tree Search with distributional (quantile) value estimates, Bayesian belief tracking via particle filters, and LLM-based action proposals via the Claude API.

Developed April to May 2026. Built iteratively with Claude Code.

<div class="row">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/business-agent-architecture.png" title="System architecture" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Simplified architecture: each week the agent observes the environment, updates its beliefs via Bayesian inference, generates candidate actions from proposers, and selects an action via planning (Greedy or MCTS) using a forward model.
</div>

## Architecture

**Environment** — A multi-product retail business simulator with constant-elasticity demand, Poisson noise, seasonal effects, cross-elasticities, and inventory with reorder lag, holding costs, and stockout penalties. Four progressive complexity phases.

**Inference** — Bayesian belief tracking over hidden demand parameters. Implementations include point estimation (MLE), grid approximation, particle filtering (bootstrap SMC with systematic resampling), and an oracle baseline. Uses Poisson log-likelihood with right-censoring for stockout weeks.

**Planning** — Action selection given beliefs and candidate proposals. A greedy 1-step planner, and a full MCTS implementation (~400 lines) with PUCT selection, quantile-based backup (PlanU-style), random-policy rollouts, and root parallelism.

**Proposals** — Candidate action generation for planners to score. Random sampling, a fixed heuristic policy, an LLM proposer (Claude API with formatted state summaries), and a structured mixture proposer with promo-aware reorder strategies.

**Forward Model** — The agent's internal demand model, used by both Greedy and MCTS for one-step simulation and multi-step tree search. Each MCTS simulation draws one coherent parameter set from the inference posterior.

## Experimental Framework

The project defines a systematic baseline comparison matrix where each adjacent comparison isolates exactly one variable:

| Arm | Planning | Uncertainty | Proposals | Tests |
|-----|----------|-------------|-----------|-------|
| B1 | Greedy | Point estimate | Random | Floor baseline |
| B2 | Greedy | Point estimate | LLM | Value of LLM proposals |
| B3 | Greedy | Bayesian | LLM | Value of Bayesian inference |
| B4 | MCTS | Point estimate | LLM | Value of multi-step planning |
| B5 | MCTS | Bayesian | Random | MCTS+Bayesian without LLM |
| **Full** | **MCTS** | **Bayesian** | **LLM** | **All components** |

## Results

<iframe src="https://docs.google.com/presentation/d/e/2PACX-1vTkEulvsZa1MCgTZDvIQHEAHrmrpLgUewT0AR_c-6Inmblktdl2CCOzqCXkPw5gXMnNMTzsfgmSRW0u/pubembed?start=true&loop=true&delayms=3000"
        style="width: 100%; aspect-ratio: 960/569; border: 0;"
        allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true">
</iframe>

## Codebase

~3,500 lines of source code plus ~3,100 lines of tests. Built with Python, NumPy, SciPy, and the Anthropic SDK (Claude API for LLM proposals).

[GitHub repository](https://github.com/asingularity/business-agent)
