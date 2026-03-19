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
