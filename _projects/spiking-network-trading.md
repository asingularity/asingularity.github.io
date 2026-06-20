---
layout: page
title: Spiking-Network Trading
description: Predicting and trading financial time series with spiking neural networks
img: assets/img/projects/spiking-trading-cover.png
importance: 3
category: research
---

A research framework for predicting and trading financial time series — intraday stock/ETF prices, plus synthetic benchmarks like sine waves and Mackey–Glass — using spiking neural networks. It was the main experimental codebase for a multi-year stretch of neural-network trading research (~2013–2015).

The core idea: convert a price signal into spike trains, feed them into a recurrent **spiking-neural-network reservoir**, read out the reservoir's activity to **predict the signal a few steps ahead**, and turn those predictions into buy/sell/hold decisions, scored by simulated per-trade profit over historical data. The framework is modular — data source → encoder → network → readout → trader → interface — so each stage has several interchangeable implementations.

## Approach

- **Spike encoding** — a derivative-based encoder converts an analog price signal into spikes, each tagged buy or sell by the direction of change.
- **Spiking reservoir** — a recurrent network of integrate-and-fire units with multi-delay connections and alpha-function synapses, normalized to a useful dynamical regime. Static and stochastic-input variants act as baselines, and parallel/cascade encoder networks feed it multi-channel input.
- **Readout & prediction** — trained readout units learn to forecast the signal's next steps from reservoir activity.
- **Trading & evaluation** — several trader types (MLP, regime-switching, and spiking-network-driven) turn forecasts into actions; historical backtests, optionally modeling a bid/ask spread, score per-trade profit; an oracle "cheating" baseline that peeks at the true future bounds the best achievable result; and a batch harness sweeps parameters across many runs.

## Prediction methods explored

Beyond the spiking reservoir, the project tried several interchangeable predictors behind a common interface:

- **Delay-coordinate "recurrence image"** — recent history as a 2-D image plus a linear readout (detailed below).
- **Delay-coordinate embedding** and **nearest-neighbor trajectory matching** — classic state-space prediction over reconstructed phase space.
- **Echo State Network** and **critical-branching** reservoirs — alternative recurrent readouts (from companion projects `au_net` and `rl_cb_net`).
- **TISEAN wrapper** — the external nonlinear-time-series toolkit, used both as a predictor and a denoiser.

There is also a parallel **options-trading sub-project** with its own data sources, strategies, and runner.

## Delay-coordinate "recurrence image" predictor

One method represents recent price history as a two-dimensional **recurrence image** — each pixel encodes the difference between the signal at two delayed times — and fits a simple linear readout mapping that image to the value several steps ahead.

<div class="row justify-content-sm-center">
    <div class="col-sm-10 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/spiking-trading-recurrence-weights.png" title="Recurrence image (left) and learned readout weights (right)" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    <b>Left:</b> the recurrence image — recent price history encoded as a 2-D image, where each pixel is the difference between the signal at two delayed times; the banded structure reflects the signal's quasi-periodic swings. <b>Right:</b> the linear-readout weights learned to map that image to the value a few steps ahead, reshaped back into the same 2-D layout.
</div>

<div class="row justify-content-sm-center">
    <div class="col-sm-10 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/spiking-trading-prediction.png" title="Forecast vs. outcome" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Forecast vs. outcome on a window of smoothed (low-pass-filtered) market data. <b>Blue:</b> the model's prediction of the value eight steps ahead. <b>Black:</b> the actual value eight steps ahead. <b>Red:</b> the present value, shown as a persistence reference. The prediction follows the broad direction of the larger swings but with substantial error — and on data this smooth, it does not clearly beat the persistence baseline.
</div>

Built in Python with NumPy, SciPy, and Matplotlib, with some experiments bridging to MATLAB/Octave and the external TISEAN toolkit.

[GitHub repository](https://github.com/asingularity/spiking-network-trading)
