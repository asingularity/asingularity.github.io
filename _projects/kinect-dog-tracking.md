---
layout: page
title: Kinect Dog Tracking
description: Real-time depth-camera dog tracking for interactive canine training
importance: 2
category: applied
img: assets/img/projects/kinect-dog-tracking-cover.png
---

A real-time dog-tracking and interactive-training system built around a Microsoft Kinect v2 depth camera mounted over a play arena, developed for a startup in the pet-tech space. The system tracks a dog's position and movement from overhead depth video and closes the loop with audio cues and an automated treat dispenser to shape the dog's behavior through a sequence of training "games." Developed in 2017.

<div class="row justify-content-sm-center">
    <div class="col-sm-10 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/kinect-demo-with-overlay.png" title="Live tracking overlay" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The live system: an overhead RGB view with the tracked dog outlined (the glowing edge), its arena position and distance to the camera, and the current training-game state and readouts.
</div>

<div class="row justify-content-sm-center">
    <div class="col-sm-10 mt-3 mt-md-0">
        <iframe src="https://www.youtube.com/embed/gPBOArlABxM"
                style="width: 100%; aspect-ratio: 16/9; border: 0;"
                allowfullscreen></iframe>
    </div>
</div>
<div class="caption">
    Demo with the tracking overlay and live game audio.
</div>

## Tracking

The dog is tracked in real time entirely from the Kinect's depth stream, using classical computer vision rather than learned models — chosen so the system runs at interactive frame rates on modest hardware, with no labeled training data, in a fixed overhead-camera setup. This perception and background model were my primary focus in this work, but I also contributed to integrating and testing the demo.

**Depth preprocessing.** Each depth frame is normalized, and pixels the sensor can't resolve (the Kinect returns no value where it gets no return) are detected and set to "far." The frame is optionally downscaled (0.5×) — the single biggest speed lever, moving the pipeline from a few FPS up to roughly real time.

**Adaptive depth background model.** Instead of a generic RGB background subtractor, the core path builds a custom per-pixel model of the *empty arena floor in depth*; the dog is then any region measurably closer to the camera than that floor. The model keeps, per pixel, running tallies of valid versus invalid (no-return) readings plus a running estimate of the floor depth, so it converges to a clean floor and gracefully handles pixels the sensor drops. Crucially, the background is updated **only outside the area where motion is currently happening**: each frame finds the largest moving contour (from a frame-to-frame depth difference), takes its bounding box plus a padding margin, and excludes that box from the update — so a dog standing still never "bakes into" the background. (An OpenCV MOG2 subtractor is available as a drop-in alternative.)

**Position, distance, and "too close."** The foreground mask is denoised with erosion/dilation and used to mask the depth image, from which I extract the dog's **arena position** as a normalized depth center-of-mass `(cx, cy)`, its **distance** from the camera as the depth at that centroid (falling back to the last valid reading when the centroid pixel has no return), and the **closest-point depth**. A dedicated **"dog too close"** state fires when a large foreground blob appears with no valid centroid depth (the dog directly beneath the camera), so the games can respond instead of losing the track.

<div class="row justify-content-sm-center">
    <div class="col-sm-8 mt-3 mt-md-0">
        {% include figure.liquid loading="eager" path="assets/img/projects/kinect-demo-no-overlay.png" title="Depth foreground — dog segmented from the background" class="img-fluid rounded z-depth-1" %}
    </div>
</div>
<div class="caption">
    The tracker's foreground output: the dog isolated from the arena floor by the depth background model, with everything else removed. Position, distance, and motion are all computed from this masked depth.
</div>

<div class="row justify-content-sm-center">
    <div class="col-sm-10 mt-3 mt-md-0">
        <iframe src="https://www.youtube.com/embed/j-uqmydo7qM"
                style="width: 100%; aspect-ratio: 16/9; border: 0;"
                allowfullscreen></iframe>
    </div>
</div>
<div class="caption">
    The same segmentation in motion — after depth background subtraction, only the moving dog remains.
</div>

**Motion energy.** Beyond position, the tracker maintains a motion-energy signal: the frame-to-frame change within the masked depth, scaled by the dog's proximity and fed through a leaky integrator (accumulate-and-decay, clamped to [0,1]). This yields a smooth, bounded measure of how actively the dog is moving, letting the training games distinguish a still dog from an active one.

**Head height / pose.** For posture-based games, head height above the floor is recovered by back-projecting depth into 3-D using the Kinect intrinsics and applying a floor-tilt rotation correction, so a game can reward the dog for bringing its head to a target height — with a feedback tone that slides continuously as the head approaches the target.

**Real-time performance.** The hot per-pixel loops — background update, background differencing, masked-depth / closest-point, and frame differencing — are hand-written in Cython (~2–3× faster than the NumPy versions), which keeps the full pipeline interactive (roughly 6–15 FPS depending on resolution and which signals are enabled) while the live position stream is published to the rest of the system over ZeroMQ (a client reads the latest position in ~2 ms).

## Interactive training loop

Position tracking drives a hierarchy of behavioral state machines:

- **Trial protocols** — single-game logic (move left/right, back up, approach, "hello"), where audio feedback rises in pitch as the dog nears a target position, and a success tone plus a dispensed treat fire on completion.
- **Meta-protocols** — orchestrate sequences of trials with adaptive difficulty and shaping, advancing the dog through progressive training stages.
- A scheduler runs sessions on a daily duty cycle.

## Architecture

A multi-process system with ZeroMQ publish/subscribe messaging between components:

- **Kinect / position server** — captures depth and RGB and publishes the live position estimate.
- **Sound server** — synthesizes and plays tones and cues.
- **Device interface** — drives the treat dispenser and hub hardware over a serial protocol.
- **Recorders** — save synchronized RGB, raw-depth, processed-depth, and audio for each trial.
- **Cloud logging** — streams per-frame position data to Google BigQuery for offline analysis.
- **Embedded firmware** — custom Arduino / Particle code runs the physical dispenser and buttons.

Built in Python with libfreenect2 (the open-source Kinect v2 driver), OpenCV, NumPy, SciPy, Cython, and ZeroMQ, plus embedded C/C++ for the hardware.
