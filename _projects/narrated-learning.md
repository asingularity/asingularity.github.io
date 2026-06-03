---
layout: page
title: Narrated Learning
description: Self-supervised learning from sequential data (2016–2023)
img: assets/img/projects/narrated-learning-im-800.png
importance: 1
category: research
---

A research side project exploring self-supervised learning from sequential sensory data. The core idea is that temporal sequences — video frames, sensor readings, time series — contain structure that can be exploited to learn useful representations without labels. The project investigates how to discover recurring patterns, build hierarchical features, and make predictions, drawing on ideas from competitive learning and sparse coding.

Developed from October 2016 to January 2023, the project went through several research directions described below; slides with results follow.

## Research Directions

### 1. Robot Navigation and World Modeling (2016–2019)

A simulated 2D robot navigates an environment with walls, using ray-cast sensors to perceive its surroundings. The robot learns a lookup-table-based model of its sensory inputs and uses predictor ensembles to anticipate future sensor states given motor commands. A task mode tests whether the learned model can support goal-directed navigation to specified regions.

<div class="row">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/narrated-learning-im-800.png" title="Simplified narrated learning simulation" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Simplified narrated learning simulation.
</div>

### 2. Visual Feature Learning with Winner-Take-All Networks (2021–2022)

Video frames (from nature footage, driving video, etc.) are converted into binary events using a simulated event camera, then fed into competitive learning networks. Multiple variants of Winner-Take-All (WTA) networks learn a set of receptive fields (RFs) — small learned image patches that tile the visual input. Each input frame triggers a competition among RFs, and the closest-matching RF updates its weights toward the input.

Key variants explored:
- **Rate-control WTA** — tracks how often each RF wins and adjusts learning rates to prevent any single RF from dominating
- **Iterative WTA** — runs multiple rounds of competition per input frame
- **Tiled WTA** — divides the input image into spatial tiles, each with its own set of competing RFs
- **Multi-layer hierarchical WTA** — stacks multiple layers where higher layers learn features over the outputs of lower layers
- **Coincidence / dynamic coincidence** — learns based on temporal co-occurrence of events rather than spatial similarity alone
- **Indirect learning** — the most recent direction (Sep 2022), using eligibility traces to update RFs indirectly

<div class="row">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/temporal-wta-im-800.png" title="Example receptive fields learnt by winner-take-all networks on a tile of video." class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Example receptive fields learnt by winner-take-all networks on a tile of video.
</div>

### 3. Multi-layer Sequence Prediction (2019–2021)

Learns hierarchical representations where each layer discretizes its input into bins and builds lookup tables or KNN-based associative memories. Uses temporal context (sequences of recent states) as the basis for clustering and prediction. Tested on video, stereo camera data, and a 2D bouncing-ball physics simulation.

### 4. Tile-based Segmentation and Prediction (2019–2020)

Divides video frames into small spatial tiles and learns predictive models per tile — given recent tile states, predict the tile's state several frames ahead. Used dynamic tile allocation and tracked prediction error across training and holdout data.

### 5. MLP Ensemble Prediction (2017–2018)

An ensemble of small multi-layer perceptrons competes to predict the next sensory frame from recent history. At each step, only the MLP with the lowest prediction error is trained (a WTA rule applied to the predictors themselves). Compared ensemble performance against a single monolithic MLP.

## Results (Directions 1–5)

<iframe src="https://docs.google.com/presentation/d/e/2PACX-1vSWPdSgT51uuM8WX6cW6rMgdWPw1mGbZ0ZcdiZZXWx-DeKNmEDiIgI5jCBSi8TmZwKHUecuULCfuFk5/pubembed?start=true&loop=true&delayms=3000"
        style="width: 100%; aspect-ratio: 960/569; border: 0;"
        allowfullscreen="true" mozallowfullscreen="true" webkitallowfullscreen="true">
</iframe>

## Codebase

Built with Python, NumPy, and custom WTA network implementations. The codebase includes multiple experimental branches reflecting the evolution of ideas over time.

[GitHub repository](https://github.com/asingularity/narrated-learning)


### 6. Temporal Event Prediction with Proximal Encoding (2022–2023)

A more direct supervised approach to temporal event prediction, developed as a separate codebase. Video frames are converted into binary events (simulating a neuromorphic event camera), and an MLP learns to predict when each pixel will next change — given only how recently it last changed.

The system encodes temporal context as "proximal times" — the number of frames since a pixel last produced an event (left proximal) and until it next produces one (right proximal). These distances are converted to continuous values via exponential decay, providing a smooth learning signal. The MLP maps left-proximal values to right-proximal values, training online frame-by-frame. Built with TensorFlow/Keras over 56 commits.

[GitHub repository (separate codebase)](https://github.com/asingularity/nearest-neighbor-visual-learning)
