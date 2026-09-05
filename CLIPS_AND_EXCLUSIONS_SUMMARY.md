# Smart NVR Reference Guide: Exclusion Zones & Event Video Clips

This guide serves as a permanent reference for the custom **Ignore Zones (Exclusion Masks)** and **On-Demand Event Video Clip Saver** features implemented in your Direct NVR Smart Monitor & Alert Engine.

---

## 📐 1. Detection Architecture & Zone Hierarchy

By default, the engine is designed to require zero upfront setup, but it supports powerful spatial filtering through the interactive canvas:

1. **Default State (Full Frame Scanning)**: 
   If no yellow Scan Windows are drawn, the backend AI scans the **entire frame** and alerts on any allowed classes (persons, vehicles, animals, etc.) detected anywhere in the feed.
2. **Scan Windows (Yellow ROI Polygons)**: 
   When you draw a yellow **Scan Window (ROI)**, the engine changes behavior. It will **only** trigger alert notifications and track objects if the center point (centroid) of the detection falls **inside** the yellow boundary.
3. **Ignore Zones (Red Exclusion Polygons)**: 
   If you draw a red **Ignore Zone (Exclusion Mask)**, the engine will **instantly drop** any object whose center falls inside it. This overrides everything else (both full-frame scanning and yellow ROIs) and is perfect for carving out static false-positive triggers like bird feeders, swaying trees, or flags.

---

## 🚫 2. Exclusion Zones (Ignore Masks)

### How to Use It
1. Click the red **Ignore Zone** button on any camera card.
2. Left-click on the live video stream canvas to place coordinates around the problematic area.
3. Click the green **💾 Save Ignore Area** button to finalize.
4. The ignore zone will display on your stream as a translucent red **`IGNORE ZONE (EXCLUSION)`** polygon.
5. Clicking **Clear** on the camera header will clear both the ROI and Ignore Zone for that camera.

### Technical Implementation
- **Frontend (`public/app.js`)**: Coordinates are normalized to relative scales `[0..1]` and sent via `POST` to `/api/exclusion`. This saves the boundaries to `exclusions.json`.
- **Backend (`server.js`)**: On startup, the server loads `exclusions.json`. During the frame check loop, the centroid of every detected object is checked against the exclusion coordinates via a ray-casting `isPointInPolygon()` algorithm. If matched, the object is immediately discarded:
  ```text
  [EXCLUDE] PERSON on camera "porch" was discarded (within Exclusion Zone).
  ```

---

## 🎥 3. On-Demand Event Video Clip Saver

This feature automatically captures high-definition MP4 event clips straight from Frigate's recording database and embeds them directly into your browser dashboard.

### How to Use It
1. Click **Settings & SMTP** in the header.
2. Under **Detection & Streams**, check **Save Video Clips (from Frigate)**.
3. Specify your preferred **Clip Duration (seconds)** (e.g., `5` seconds or `10` seconds).
4. When a person or tracked object triggers an active alert in your yellow scanning area, let the event happen.
5. A few seconds later, a red **`🎥 Play Clip`** button will slide into the specific alert card in your sidebar.
6. Click it to open a beautiful HTML5 video lightbox player modal where you can watch, pause, or download the recorded MP4 file.

### Technical Implementation
- **deferred download strategy**: Because a security event happens in real-time, Frigate's video segment files are still being written to disk as the alert is triggered.
- **Backend Flow**:
  1. An alert is triggered on a new track.
  2. The backend schedules a deferred task using a Javascript `setTimeout()` for `(settings.detection.clipDuration + 3)` seconds.
  3. Once the timeout fires, it queries Frigate's direct clip packaging endpoint:
     `http://<frigate_ip>:5000/api/<camera_name>/start/<start_unix>/end/<end_unix>/clip.mp4`
     *(It automatically includes a **2-second pre-capture buffer** starting prior to the event timestamp to ensure you capture the initial approach!)*
  4. The binary MP4 is fetched, written to `public/clips/`, and a `clip-saved` Socket.io message is emitted to the browser.
- **Frontend Flow**:
  1. The browser receives `'clip-saved'`.
  2. It targets the specific DOM element by ID (`alert-item-<trackId>`) and injects the red **Play Clip** button with event propagation stopped (so clicking it doesn't trigger camera card flashes).
  3. Clicking it plays the video in the central `#clip-modal` lightbox and halts playback cleanly upon closing.

### ⚠️ Prerequisite Note on Cameras
For video clips to be available, the specific camera **must have recordings enabled inside your main Frigate `config.yml`**:
```yaml
cameras:
  porch:
    record:
      enabled: true  # <--- Required!
```
If recordings are disabled in Frigate (such as for `tapo-deck` or `reo-deck`), Frigate's recording endpoint will return a `400 Bad Request` and no clip will be saved, though real-time overlay box tracking will still work perfectly.
