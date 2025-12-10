# MRI data

## Phase 1: Acquisition & Prevention (In-Scanner)

_Objective: Mitigate artifacts at the source through participant management and real-time monitoring._

#### 1. Participant Coaching

Standardize instructions to prevent "relaxed slumping."

* Script: _"Relax your body completely, but hold your head as still as a statue. Do not swallow or lick your lips during the the loud noises."_
* Padding: Ensure snug padding. If the 32-channel coil is used, verify the nose is centered to prevent coil sensitivity asymmetry.

#### 2. Real-Time Monitoring (The "FirMM" Check)

If real-time motion monitoring (e.g., FIRMM) is active, experimenters must monitor displacement traces.

* Restart Trigger: If >50% of the first 20 frames show motion > 0.4mm.
* "High Motion" Classification: If >75% of frames exceed 0.3mm, the run is compromised.
  * _Action:_ If time permits within the booking slot, re-acquire immediately.

***

## Phase 2: Automated Assessment (The "24-Hour" Rule)

_Objective: Rapid generation of quantitative Image Quality Metrics (IQMs)._

#### 1. Computation

* Timeline: Within 24 hours of data transfer from the Imaging Center.
* Pipeline: Execute `MRIQC` (v0.3 container) on the raw BIDS directory.
* Output: HTML reports and `group_bold.tsv` / `group_T1w.tsv`.

#### 2. Automated Exclusion Flags

The automated script parses IQMs and flags `Poor_Quality = True` if these hard thresholds are violated:

| **Metric**  | **Full Name**          | **Threshold**           | **Logic**                                  |
| ----------- | ---------------------- | ----------------------- | ------------------------------------------ |
| FD (Mean)   | Framewise Displacement | > 0.5 mm                | Global motion average.                     |
| FD (Spikes) | High Motion Volumes    | > 40%                   | % volumes with FD > 0.3mm.                 |
| GSR         | Ghost-to-Signal Ratio  | > 0.3                   | Severe Nyquist ghosting.                   |
| CNR         | Contrast-to-Noise      | < 2.0                   | T1w Grey/White matter differentiability.   |
| SNR (GM)    | Signal-to-Noise (Grey) | < $$ $\mu - 2\sigma$ $$ | Dynamic outlier check (Cohort Mean ± 2SD). |

***

## Phase 3: Expert Visual Inspection (The "Two-Eyes" Protocol)

_Objective: Identify local pathology, geometric wrapping, and subtle artifacts that global metrics miss._

Personnel: Two trained reviewers.

Timeline: Within 1 week of acquisition.

Tool: FSLeyes (module load ncf fsl -> fsleyes file.nii.gz &).

### A. Structural MRI (T1w) Inspection

Pass 1: Geometry & Anatomy (Standard Contrast)

* Check:
  1. Clipping: Is the cerebellum or top-of-head cut off?
  2. Wrapping: Does the nose/face wrap around and overlap brain tissue?
  3. Gross Pathology: Any incidental findings (cysts, lesions)?
  4. Ringing: Look for "ripples" radiating from the skull (Gibbs ringing).

Pass 2: The "Ghost Hunter" (Low Contrast)

* _Action:_ View > Lightbox. Lower Max Brightness to 33%.
* Check:
  1. Background Noise: Look for "TV Static" (RF Noise).
  2. Ghosting: Look for faint, displaced copies of the eyes or skull overlaying the cortex.
  3. Spiking: Look for rigid, high-intensity "barcode" stripes across the image.

Pass 3: Homogeneity (High Contrast)

* _Action:_ Raise Min Brightness to 33%, then 66%.
* Check:
  1. Bias Field: The signal intensity should be symmetrical (L-R) and relatively even (A-P). Dark patches suggest coil failure or poor head positioning.

***

### B. Functional MRI (BOLD) Inspection

Pass 1: Temporal Stability (Movie Mode)

* _Action:_ Tools > Movie Mode. Cycle through volumes.
* Check:
  1. Coverage: Are the Temporal Lobes (ventral) included?
  2. Motion Slices: Look for "striping" or sudden shifts in the image slice-by-slice.
  3. Dropout: Check Oribitofrontal Cortex (OFC) and Temporal poles. Some is normal; total blackness is not.

Pass 2: The "Dancing Ghost" (Low Contrast)

* _Action:_ Movie Mode ON. Lower Max Brightness to 33%.
* Check:
  1. Identify ghosting in the background. If the ghost "dances" (fluctuates wildly) while the head is still, this indicates hardware instability, not just subject motion.

***

## Phase 4: Quantitative Failure Thresholds (The Scorecard)

If a scan hits these numbers, it is flagged for Review/Rescan.

#### Structural (T1w) Thresholds

| **Metric** | **Pass Range** | **Failure Condition**               | **Impact**                                             |
| ---------- | -------------- | ----------------------------------- | ------------------------------------------------------ |
| qi\_1      | < 0.05         | $$ $\ge$ $$ 0.1                     | "Detects artifacts in background (wrapping/ghosting)." |
| qi\_2      | < 0.05         | $$ $\ge$ $$ 0.2                     | "Detects artifacts affecting head (eye motion/coils)." |
| SNR\_WM    | > 20           | < 15                                | "Image is too grainy for segmentation."                |
| CNR        | > 2.5          | < 2.0                               | "Cannot distinguish Grey from White matter."           |
| SNR\_GM    | 10-12          | Out of Range (Mean $$ $\pm$ $$ 2SD) | "Scan is an outlier compared to cohort."               |

#### Functional (BOLD) Thresholds

| **Metric** | **Pass Range** | **Failure Condition** | **Impact**                                     |
| ---------- | -------------- | --------------------- | ---------------------------------------------- |
| Mean FD    | < 0.2mm        | > 0.5mm               | "Subject moved too much overall."              |
| Spikes     | < 20%          | > 40% (at 0.3mm)      | "Too many volumes need scrubbing."             |
| tSNR       | > 50           | < 40                  | "Not enough signal to detect BOLD activation." |
| GSR        | < 0.1          | > 0.3                 | "Severe susceptibility/distortion."            |

***

## Phase 5: Action Plan (The Decision Matrix)

Upon completion of Phase 3 and 4, the reviewer submits a status code.

Scenario A: Scan Fails QC

1. Generate Ticket: Log Subject ID, Run ID, and specific failure reason (e.g., _"Resting State: Spikes > 40%"_ or _"T1w: Severe Wrap"_).
2. Participant Status Check:
   * _Ongoing:_ SCHEDULE RESCAN.
   * _Completed/Unreachable:_ EXCLUDE. (Do not delete raw data. Move `derivatives` to `_exclude` folder).

Scenario B: Scan is Borderline

* _Condition:_ Metrics are slightly off (e.g., FD = 0.51mm) but visual inspection looks usable.
* _Action:_ Flag as "Caution". Include in preprocessing but add as a covariate in group-level analysis.

Scenario C: Scan Passes

* _Action:_ Proceed to fMRIPrep.
