# WebGazer

A simple eye-tracking demo using [WebGazer.js](https://webgazer.cs.brown.edu/) — calibration + real-time gaze estimation. Works fully offline.

## Requirements

- Python 3
- A webcam
- Chrome or Firefox

## How to Run

1. Open a terminal in this folder
2. Start a local server:
   ```
   python -m http.server 8000
   ```
3. Open your browser and go to:
   ```
   http://localhost:8000
   ```

## Usage

1. Click **Start Calibration** and allow camera access
2. Click each of the 9 blue dots **3 times** while looking directly at them
3. After calibration, a red dot will follow your gaze on screen
4. Click **Recalibrate** (bottom-right) to restart calibration at any time

> **Note:** Must be served over HTTP (`localhost`), not opened as a `file://` URL — browsers block WebAssembly from file origins.

## Exporting Data

Click the green **Export JSON** button (bottom-right) during tracking to download a `webgazer_session_<timestamp>.json` file.

### JSON Structure

```json
{
  "exported_at": "2024-01-01T13:00:00.000Z",
  "screen": { "width": 1920, "height": 1080 },
  "calibration": [ ... ],
  "gaze": [ ... ]
}
```

| Field | Description |
|---|---|
| `exported_at` | ISO 8601 datetime when the export button was clicked |
| `screen.width` / `screen.height` | Browser viewport size in pixels at time of session |

---

### `calibration` entries

One entry is recorded for **each click** on a calibration dot (9 dots × 3 clicks = 27 entries total).

```json
{
  "point_vw": 5,
  "point_vh": 5,
  "point_px": 77,
  "point_py": 41,
  "click_n": 0,
  "ms_since_session_start": 3120,
  "frame": "data:image/jpeg;base64,..."
}
```

| Field | Description |
|---|---|
| `point_vw` | Dot position as **% of viewport width** (screen-size independent) |
| `point_vh` | Dot position as **% of viewport height** (screen-size independent) |
| `point_px` | Dot position in **pixels from left** on this specific screen |
| `point_py` | Dot position in **pixels from top** on this specific screen |
| `click_n` | Which click on this dot: `0` = first, `1` = second, `2` = third |
| `ms_since_session_start` | Milliseconds elapsed since calibration began |
| `frame` | Webcam snapshot at the moment of the click, as a base64 JPEG (320px wide). Use this to align with a recorded video. |

---

### `gaze` entries

One entry is recorded for **every gaze prediction** from WebGazer (approximately every 16–33 ms).

```json
{
  "x": 312,
  "y": 240,
  "ms_since_session_start": 8500
}
```

| Field | Description |
|---|---|
| `x` | Estimated gaze position in **pixels from left** of the screen. `-1` if no face was detected. |
| `y` | Estimated gaze position in **pixels from top** of the screen. `-1` if no face was detected. |
| `ms_since_session_start` | Milliseconds elapsed since calibration began |

---

## Replaying with a Recorded Video

The calibration `frame` images and `ms_since_session_start` timestamps let you reproduce gaze estimates from a pre-recorded webcam video:

1. Use `calibration[*].frame` + `calibration[*].point_px/py` to retrain the WebGazer regression model on the recorded frames
2. Use `calibration[*].ms_since_session_start` to align each calibration frame to the correct position in the video timeline
3. Feed subsequent video frames through WebGazer to reproduce the gaze stream
