# Quality Control (QC) Routines

Data is useless if it is noisy. We perform QC at two distinct stages.

**Stage 1: "Raw" QC (Immediately after Conversion)**

* Who: The person who collected the data.
* Tool: Visual Inspection & MRIQC.
* The Checklist:
  1. Check JSONs: Do the `TR` and `PhaseEncodingDirection` match the protocol?
  2. Visual Check: Open the T1w and one functional run in a viewer (fsleyes/ITK-SNAP). Is the brain cut off? Are there massive artifacts?
  3. Run MRIQC: Generate the HTML report.
     * _Flag if:_ FD (Framewise Displacement) > 0.5mm is frequent.
     * _Flag if:_ TSNR (Temporal Signal-to-Noise Ratio) is significantly lower than the lab average.

**Stage 2: "Process" QC (After fMRIPrep/Preprocessing)**

* Who: The data analyst.
* Tool: fMRIPrep HTML Reports.
* The Checklist:
  1. Registration (The "Red Line" Test): Check the alignment between the T1w (structure) and the BOLD (function). Does the red contour line of the brain matching the actual brain image?
  2. Normalization: Did the brain stretch correctly into MNI space?
  3. Distortion Correction: Did the field maps correct the "stretching" in the frontal lobe?
