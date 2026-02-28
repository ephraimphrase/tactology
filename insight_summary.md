# 🎛️ Insight Summary — Music Production Video Performance

**Analysis:** Signal-based comparison of 4 YouTube videos in the music production niche  
**Videos analysed:** 2 high-performing, 2 low-performing  
**Signals used:** Silence Ratio · Loudness Variance · Scene Change Frequency · Motion Intensity

---

## What the Data Shows

| Signal | High Performers (avg) | Low Performers (avg) | Direction |
|--------|----------------------|---------------------|----------|
| Silence Ratio | 15.0% | 15.0% | — |
| Loudness Variance | 0.0967 | 0.1204 | complex |
| Scene Changes/min | **3.86** | **1.31** | ↑ Higher is better |
| Motion Intensity | **3.36** | **1.10** | ↑ Higher is better |

The clearest signal separation is in **scene change frequency** and **motion intensity**, both of which are markedly higher in high-performing videos.

> **Note on Loudness Variance:** `low_2` had high loudness variance (0.183) — not from intentional dynamics but from erratic audio mixing (loud music against quiet commentary with no level matching). This highlights that the *quality* of loudness variation matters, not just the quantity.

---

## Insight 1 — Keep the screen active: High performers had 3× more visual activity

> High-performing videos averaged **3.86 scene changes per minute** and a motion intensity of **3.36**, vs **1.31 changes/min** and **1.10** motion intensity for low performers.

Music production content faces the "static DAW screen" problem. High performers fight this by cutting frequently — switching between Plugin UIs, zooming into the Piano Roll, or using webcam picture-in-picture. Low performers remain static for minutes at a time, which causes viewer drop-off.

**What to do:** In editing, aim for a visual change (cut, zoom, transition, or screen switch) every 10–15 seconds at minimum. In live streams, actively move your focus between screen sections — drag windows, scroll the arrangement, or zoom into the active view.

---

## Insight 2 — Make your audio dynamics intentional, not accidental

> Average loudness variance: HIGH = 0.097, LOW = 0.120 — but `low_2`'s high variance (0.183) came from poorly matched audio levels, not intentional dynamics.

The data reveals a nuance: high variance in loudness is only a *positive signal* when it's structured. High-performing tutorials alternate between quiet explanation and loud beat playback in a deliberate pattern. Low performers have loud music fighting quiet speech at random intervals — technically "dynamic" but perceptually chaotic.

**What to do:** Use a reference level (~-14 LUFS integrated for YouTube). Let your voice sit clearly in the mix, and let the music breathe loudly in dedicated playback segments. Avoid the common mistake of playing music at full volume while trying to speak over it.

---

## Insight 3 — Pacing matters more than content depth

> High performers averaged **7.7 minutes** of content; low performers averaged **25.1 minutes**.

Both low-performing videos were long, unedited sessions (21–29 minutes). High performers delivered tightly edited, purpose-driven content in 7–8 minutes. This aligns with YouTube retention data: viewers are far more likely to complete shorter, focused content.

**What to do:** Plan content around a single, clear outcome ("make this type beat from scratch"). Cut everything that doesn't serve that goal. If you want to stream long sessions, repurpose the best 5–10 minutes as a standalone tutorial upload.

---

## Bonus: Quality Score
A weighted scoring model using all 4 signals ranked high vs low performers correctly based on scene change frequency and motion intensity alone. Silence ratio did not differentiate (both groups hit ~15%). The loudness variance signal is useful only alongside qualitative assessment of what drives it.

---

*Analysis by Ephraim Owusu | Tactology Global Technical Test | February 2026*
