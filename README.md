# Us-in-My-Mind

A performance prototype using interactive stage design and spatial tracking. The piece visualizes memories that live within personal space. Videos and soundscapes associated with different areas and objects are pulled into vision as the performer moves through the space, creating an introduction to the musical performance. This piece prototypes how to introduce themes, topics, soundscapes, and imagery that will be fleshed out in the songs to follow. 

Built with Max/MSP and live video, the performance space is divided into zones, each correlated to a different video and triggered in real time by the performer's location. This work lives at the beginning of an ongoing exploration in creating a visual world to accompany the acoustic storytelling of a concert. 






**Project documentation:** [bellaciaramitaro.com/us-in-my-mind](https://www.bellaciaramitaro.com/us-in-my-mind)

---

## Overview

**Max/MSP (`spatial_stage_media.maxpat`)** uses a Jitter computer vision pipeline to detect and locate movement within a camera feed. Incoming frames are processed through `jit.change` to isolate new movement, converted to greyscale via `jit.rgb2luma`, and compared between frames using `jit.op @op absdiff` to produce a difference image. This is thresholded at 0.2 (`jit.op @op > @val 0.2`) to create a clean black-and-white motion mask. `jit.3m` then reports the min, median, and max pixel position values from the mask, and an `if $i1 > 5 then bang` condition fires when meaningful motion is detected in a given region.

The stage is divided into three columns using `jit.scissors @columns 3`, corresponding to three spatial zones — entry, centre, and coffee area. Each zone has its own video channel playing a looping `.mp4` file via `jit.playlist`, output through a `jit.gl.meshwarp` object for projection mapping. Zone transitions use `jit.xfade` to crossfade between the outgoing and incoming video states. The final output renders to a 1920×1080 floating window via `jit.world`.

The three video zones and their associated files are:

| Zone | File |
|------|------|
| Left | `entryway.mp4` |
| Centre | `chair area.mp4` |
| Right | `coffee area.mp4` |

A base looping video (`base.mov`) runs as the default state when no zone-specific trigger is active.

---

## Setup

### Hardware Requirements

- Computer running Max/MSP 8.5+
- Camera or webcam mounted overhead, pointing directly down at the stage
- Projector or display output at 1920×1080
- Defined stage or performance area visible beneath the camera

### Max/MSP Setup

1. Open `spatial_stage_media.maxpat` in Max/MSP 8.5 or later.
2. Ensure the **Jitter** package is installed (required for all `jit.*` objects).
3. **Connect your camera.** The patch reads from a live camera input mounted overhead, looking straight down at the stage. Make sure your camera is connected, positioned correctly, and set as the active input source before starting.
4. **Relink the video files.** Each `jit.playlist` object references files from a local path. Replace these with the correct paths to your own media files:
   - `entryway.mp4`
   - `chair area.mp4`
   - `coffee area.mp4`
   - `base.mov` (×3 — used as the default state for each zone)
5. **Open the gates.** Each video channel uses a `onebang` gate that must be manually opened on first run. Click the bang button labeled "bang here to open the gate" in each zone section before starting.
6. Turn on the toggle to activate the camera feed and begin motion detection.
7. The output window (`jit.world MOVE`) will open at 1920×1080. Position and map this to your projector using the `jit.gl.meshwarp` controls in each zone.

### Calibrating Motion Detection

The patch uses `if $i1 > 5 then bang` to filter out noise — only motion readings above 5 trigger a zone state. If the system is too sensitive or not sensitive enough in your environment, adjust this threshold value directly in the `if` object for each zone.

The motion difference threshold is set at `0.2` in `jit.op @op > @val 0.2`. Increase this value to require more significant movement before detection; decrease it for more sensitivity to subtle motion.

### Adjusting Zone Crossfades

Each zone has its own crossfade timing controlled by `delay` and `line` objects. The fade-in and fade-out behaviour can be adjusted by modifying the message boxes and delay values connected to each zone's `jit.xfade` and `line` objects.

| Zone | Hold delay | Fade-out |
|------|------------|----------|
| Left | 50s        | 10s      |
| Centre | 5s       | 10s      |
| Right | 50s       | 10s      |
