---
layout: page
title: WiFi Link Quality
description: Bidirectional wireless link quality measurement with mobile robot
img: assets/img/projects/obsidia-wifi-supervision-demo-robot.png
importance: 3
category: obsidia.ai
---

A wireless link quality measurement system built around an iRobot Create 2 controlled from a laptop over WiFi. As the robot is driven around an environment, both the robot (Raspberry Pi) and the controller (laptop) simultaneously record bidirectional network metrics. The collected data is exported to `.mat` format for analysis in MATLAB.

Developed December 2022 to January 2023.

<div class="row">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/obsidia-wifi-supervision-demo-robot.png" title="iRobot Create 2 with Raspberry Pi" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    iRobot Create 2 with Raspberry Pi, controlled from a laptop over WiFi.
</div>

## What It Measures

Both the controller and the robot run parallel recording processes that capture:

- **Ping latency** (both directions) — round-trip time at 10 Hz
- **UDP test packets** (both directions) — sent at 1 kHz with timestamped payloads, allowing measurement of packet loss, jitter, and one-way delay
- **Video stream integrity** — the controller logs whether each MJPEG frame was successfully received
- **WiFi metrics** (robot side) — bit rate, link quality, and signal level parsed from `iwconfig` every second

## Architecture

The system runs as parallel subprocesses on each device. The robot runs data recording, WiFi metric logging, and an MJPEG video server. The controller runs its own recording processes plus a keyboard-driven remote control client that sends HTTP commands to the robot's motor control server.

Each subprocess writes timestamped text files to a dataset directory. Post-processing parses these into arrays and exports `.mat` files for MATLAB analysis. The core signal analysis was done in MATLAB (not included in the repo).

<div class="row">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/obsidia-wifi-supervision-test-all_data.png" title="Full WiFi measurement data" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/obsidia-wifi-supervision-dropout-data.png" title="Detected dropout events" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: Full WiFi measurement data across a test run. Right: Detected dropout events.
</div>

[GitHub repository](https://github.com/asingularity/obsidia-signal-mvp)
