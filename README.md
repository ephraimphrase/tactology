# Music Production Video Analysis — README

## Overview
This project analyses 4 YouTube videos from the **music production** niche (2 high-performing, 2 low-performing) by extracting audio and video signals, comparing them, and deriving actionable insights for content creators.

---

## How to Run

### 1. Prerequisites

- **Python 3.10 or higher** — [download here](https://www.python.org/downloads/)
- **Git** (optional, for cloning) — [download here](https://git-scm.com/downloads)

> `ffmpeg` does **not** need to be installed separately — the notebook uses `imageio-ffmpeg` which ships a bundled binary automatically.

---

### 2. Clone or download the project

```bash
git clone <repo-url>
cd Tactology_Test
```

Or simply download and unzip the project folder, then open a terminal inside it.

---

### 3. Create and activate a virtual environment

**Windows (PowerShell / CMD):**
```bash
python -m venv .venv
.venv\Scripts\activate
```

**macOS / Linux:**
```bash
python3 -m venv .venv
source .venv/bin/activate
```

You should see `(.venv)` at the start of your terminal prompt confirming the environment is active.

---

### 4. Install dependencies

With the venv active, install all required packages:

```bash
pip install --upgrade pip
pip install yt-dlp librosa opencv-python pandas matplotlib seaborn jupyter ipykernel imageio-ffmpeg nbformat
```

| Package | Purpose |
|---|---|
| `yt-dlp` | Downloading YouTube videos |
| `librosa` | Audio feature extraction |
| `opencv-python` | Video frame processing |
| `pandas` | Data structuring and CSV export |
| `matplotlib` / `seaborn` | Visualisations |
| `imageio-ffmpeg` | Bundled ffmpeg binary (no system install needed) |
| `jupyter` / `ipykernel` | Running the notebook |

---

### 5. Register the virtual environment as a Jupyter kernel

This step ensures Jupyter uses the correct Python environment with all dependencies:

```bash
python -m ipykernel install --user --name=tactology_venv --display-name="Tactology_Venv"
```

---

### 6. Launch the notebook

```bash
jupyter notebook Tactology_Analysis_executed.ipynb
```

When the notebook opens in your browser:
- In the menu bar, click **Kernel → Change Kernel → Tactology_Venv** to confirm the right environment is selected.
- Then click **Kernel → Restart & Run All** to execute all cells from scratch.

---

### 7. What the notebook does

The notebook is self-contained and will:
1. Download the 4 YouTube videos (audio + video tracks) via `yt-dlp`
2. Extract 4 signals from each: silence ratio, loudness variance, scene change frequency, motion intensity
3. Generate comparison charts and export them as PNG files
4. Produce a bonus quality score for each video
5. Export `results.csv` with all signal data

> **Note:** Downloads are skipped automatically if the video files already exist in `data/`.

---

## File Structure

```
Tactology_Test/
├── Tactology_Analysis_executed.ipynb  ← Notebook with all outputs pre-rendered
├── insight_summary.md                 ← 1-page findings (read this)
├── README.md                          ← This file
├── data/                              ← Source video files
│   ├── high_1_video.mp4
│   ├── high_2_video.mp4
│   ├── low_1_video.mp4
│   └── low_2_video.mp4
├── results.csv                        ← Signal data for all 4 videos
├── signal_comparison.png              ← Bar charts per signal
├── loudness_over_time.png             ← Audio energy timelines
├── motion_over_time.png               ← Motion intensity timelines
└── quality_scores.png                 ← Bonus quality score comparison
```

---

## Signal Design — Why Each Signal and Why Those Values

### 1. Silence Ratio
**What it measures:** The proportion of the audio that falls below a perceptual energy threshold — i.e. moments where the creator has gone quiet, left dead air, or cut away from sound.

**Why it matters:** Silence is an attention killer on YouTube. Viewers are conditioned to scroll when audio drops out unexpectedly. High silence ratios are a reliable proxy for poor pacing, unedited raw footage, or low production polish. Conversely, tightly edited videos with intentional pauses (very brief) score better on retention metrics.

**Why the 15th percentile threshold?** A fixed decibel threshold (e.g. -40 dBFS) would be unfair across videos with different recording levels — a quiet home studio recording would be penalised relative to a professionally mastered upload. Using the 15th percentile of each video's own RMS energy as the silence cutoff makes it *self-relative*: a frame is deemed silent if it is in the quietest 15% of that video's own dynamic range. 15% was chosen because it is large enough to capture genuine quiet passages while being small enough to exclude momentary breath sounds or ambient room noise from counting as silence.

---

### 2. Loudness Variance
**What it measures:** The standard deviation of short-term RMS energy across the audio timeline — essentially how much the volume level fluctuates throughout the video.

**Why it matters:** In music production content, loudness dynamics reflect editorial energy. A flat loudness profile often means monotone delivery — talking at one level, no hype moments, no musical demonstration peaks. High variance indicates the creator uses build-ups, drops, or demonstrative moments (playing a beat, showcasing a plugin) that naturally spike energy. These moments are attention hooks and tend to correlate with stronger viewer engagement.

**Why no fixed threshold?** Loudness variance is used as a *comparative signal* between groups (high vs. low performing), not as a pass/fail gate. There is no universally correct variance value — it depends on content style. Instead, the analysis surfaces the *direction* and *magnitude* of the difference between groups, letting the data speak.

---

### 3. Scene Change Frequency
**What it measures:** The rate at which the video cuts to a new shot, switches between screen segments, or makes a major visual jump — measured as the number of such transitions per minute.

**Why it matters:** Pacing is one of the most important editing decisions in tutorial and production content. Too few cuts and the video feels static; viewers lose focus. Frequent cuts, quick zooms, and screen switches signal a well-edited, energetic video that respects the viewer's attention span. YouTube's internal research has consistently linked faster pacing with longer watch time in instructional content.

**Why a mean frame difference threshold of 30 (0–255 scale)?** Frame difference is computed as the mean absolute pixel change between consecutive downscaled frames (320×180). A value of 30 corresponds to roughly a 12% average pixel shift — enough to catch a genuine cut or hard transition, while filtering out minor movements such as mouse cursor motion, scrolling a DAW timeline, or camera micro-drift. Lower thresholds (e.g. 10–15) would generate false positives on any content involving screen scrolling; higher thresholds (e.g. 50+) would miss soft transitions. 30 was calibrated manually against a sample frame and validated to produce counts consistent with a human viewer's perception of a "cut".

**Why every 5th frame?** Processing every frame at 30 fps would be computationally expensive with no meaningful gain — the inter-frame difference between adjacent frames is dominated by minor motion noise rather than structural cuts. Sampling every 5th frame (effective 6 fps) is sufficient to detect scene changes which, by definition, span multiple frames.

---

### 4. Motion Intensity
**What it measures:** The average magnitude of optical flow vectors across sampled frames — how much pixel movement is happening on screen at any given moment, averaged over the full video.

**Why it matters:** Motion is a proxy for visual dynamism. In music production videos, this captures things like: hands moving on a keyboard or pad controller, plugin windows being manipulated, playhead moving on a DAW timeline, or camera panning. High-performing creators tend to keep the screen visually active rather than leaving a static DAW view while they talk. Sustained low-motion passages are a signal that the viewer's visual attention is not being engaged.

**Why Farneback optical flow?** Dense optical flow (Farneback) captures motion everywhere in the frame simultaneously, unlike sparse feature-tracking methods (Lucas-Kanade) which only track selected keypoints. For music production content where the area of motion might be small (e.g. a moving fader, a blinking cursor), dense flow gives a more representative measure of overall visual activity without requiring keypoint seeding.

---

## Key Assumptions

- **Performance classification** is based on public YouTube engagement metrics (views, likes) at time of selection, not platform-provided analytics.
- **Audio analysis** is capped at the first 10 minutes per video to keep processing tractable and comparable across videos of varying lengths.
- Video download quality is intentionally set to low resolution (`worstvideo`) for processing speed. This may slightly reduce frame difference sensitivity but does not materially affect signal rankings.

---

## Limitations

- All signals are heuristic-based and not causal. Higher scene change frequency in a high-performing video does not *prove* it is the cause of high performance — it is a correlation signal.
- Thumbnail quality, SEO, channel authority, and publication timing are major performance drivers that are not captured in this analysis.
- A 4-video sample is too small for statistical significance. This is an exploratory/directional analysis.
- Video download quality is intentionally set to low resolution (`worstvideo`) for processing speed. This may slightly reduce frame difference sensitivity.

---

## Libraries Used

| Library | Purpose |
|---------|---------|
| `yt-dlp` | Downloading YouTube media |
| `librosa` | Audio feature extraction (RMS, loudness) |
| `opencv-python` | Frame-by-frame video processing |
| `pandas` | Data structuring and export |
| `matplotlib` / `seaborn` | Visualisations |
| `numpy` | Numerical computation |
