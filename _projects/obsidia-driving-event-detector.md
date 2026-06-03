---
layout: page
title: Obsidia Driving Event Detector
description: IMU-based driving event detection with matched filtering
img: assets/img/projects/obsidia-driving-event-cover.png
importance: 3
category: obsidia.ai
---

A system for detecting driving events (hard acceleration, braking, swerving) from a vehicle-mounted IMU sensor, developed at Obsidia.ai. A Raspberry Pi reads accelerometer and gyroscope data at 833 Hz from an ISM330DHCX sensor, and either records it for offline analysis or runs real-time detection with alerts displayed on a phone via a web dashboard.

Two detection methods are implemented: matched filtering (designed in MATLAB, executed in Cython) and a simpler threshold-with-hysteresis approach in Python. Ground truth events are annotated during recording via keyboard input.

Developed January to March 2023.

<div class="row">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/obsidia-driving-events.png" title="Detected driving events" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Driving events detected from IMU accelerometer and gyroscope data.
</div>

## Detection Methods

**Matched filtering** — Four filter kernels (designed in MATLAB, stored as `.mat` files) detect specific maneuver signatures: acceleration, braking, swerve left, and swerve right. The filters are cross-correlated with the accelerometer buffer in a Cython-optimized loop, with a detection firing when the output crosses its threshold.

**Threshold method** — A simpler approach where an event is detected when acceleration exceeds a threshold for at least 100 ms, with hysteresis to prevent chatter at the threshold boundary.

## System

- **Data acquisition** — ISM330DHCX 6-axis IMU connected to Raspberry Pi via I2C at 833 Hz, with an RGB LED for status feedback
- **Real-time detection** — streams IMU data into overlapping buffers, applies matched filters via Cython, and sends detection alerts to the web dashboard every 200 ms
- **Web dashboard** — serves live accelerometer and gyroscope bar graphs plus an alert banner (green "Normal Driving" or red with event type) to a phone connected to the Pi's WiFi access point
- **Recording and annotation** — records raw IMU data to CSV while an operator marks ground truth events via keyboard, for offline evaluation

<div class="row">
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/obsidia-driving-demo-hardware.jpg" title="Raspberry Pi with ISM330DHCX IMU" class="img-fluid rounded z-depth-1" %}
    </div>
    <div class="col-sm-6 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/obsidia-hardware-in-car.png" title="Hardware installed in vehicle" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    Left: Raspberry Pi with ISM330DHCX IMU sensor. Right: Hardware installed in a vehicle for testing.
</div>

<div class="row justify-content-sm-center">
    <div class="col-sm-10 mt-3 mt-md-0">
        <iframe src="https://www.youtube.com/embed/rV9fEklgZoc"
                style="width: 100%; aspect-ratio: 9/16; max-height: 600px; border: 0;"
                allowfullscreen></iframe>
    </div>
</div>
<div class="caption">
    Demo of real-time driving event detection.
</div>

[GitHub repository](https://github.com/asingularity/obsidia-vehicle-imu)
