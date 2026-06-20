---
layout: page
title: Auto Transport Optimizer
description: Truck load profitability optimization web app
img: assets/img/projects/obsidia-ato-im.png
importance: 3
category: obsidia.ai
---

A full-stack web demo application for optimizing vehicle transport truck loads. Given an inventory of vehicles with weights, lengths, and destinations, the system compares a standard greedy loading strategy against a QUBO-optimized approach that maximizes profit per truckload.

The demo scenario: ~100 BMW and MINI vehicles at a distribution facility in Oxnard, CA need delivery to Bay Area dealers. Each truck can carry 7–9 cars subject to weight and length constraints. The optimizer selects which vehicles to load together to maximize total profit accounting for per-vehicle revenue, driver costs, and fuel.

Developed September to November 2023.

<div class="row">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/obsidia-ato-im.png" title="Example load optimization results" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Example load optimization results.
</div>

## Architecture

- **Backend** — Flask (Python) REST API handling optimization requests and results (Csaba)
- **Frontend** — React dashboard for inputting vehicle inventory and visualizing optimal load assignments (Csaba)
- **Solver** — MATLAB/Octave QUBO solver that formulates vehicle selection as a quadratic binary optimization problem with weight, length, and dealer-stop constraints, solved via a simulated delta-sigma feedback circuit (Co-founder)

The greedy approach fills trucks by destination proximity; the QUBO approach jointly optimizes vehicle selection to maximize profit across all constraints.

<div class="row justify-content-sm-center">
    <div class="col-sm-10 mt-3 mt-md-0">
        <iframe src="https://www.youtube.com/embed/MrTW1EsNPA4"
                style="width: 100%; aspect-ratio: 16/9; border: 0;"
                allowfullscreen></iframe>
    </div>
</div>
<div class="caption">
    Demo of the auto transport optimizer web application.
</div>

[GitHub repository](https://github.com/obsidia-ai/obsidia-auto-transport-optimizer-demo)
