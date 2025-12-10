# Behavioral data (TBD)

### Phase 1: Pre-Acquisition (Validation)

_Objective: Ensure the "Instrument" (Code) is calibrated before the participant enters the room._

#### 1. The "Dry Run" Check

Frequency: Before the first subject of any new data collection wave or after any code modification.

Action: Run the task in "Simulation Mode" (or have an RA play it).

Checklist:

* \[ ] Trigger Sync: Does the script wait for the MRI Pulse (e.g., `5` or `t`) before starting? _Critical for timing._
* \[ ] Log Columns: Are `onset`, `duration`, and `response_key` recording correctly?
* \[ ] Timing Drift: Compare the log file's total duration against the scanner's protocol duration. They must match within <1s.

***

### Phase 2: In-Session Real-Time Monitoring (The "Traffic Light" System)

_Objective: Utilize the post-run screen printout (`Accuracy` & `Mean RT`) to intervene immediately._

Role: The Experimenter/RA must record these numbers on the Session Scan Log and apply the following logic before starting the next run.

#### The Decision Matrix

| **Status**      | **Metrics (Example: 2-Choice Task)**                         | **Diagnosis**                           | **Action**                                                       |
| --------------- | ------------------------------------------------------------ | --------------------------------------- | ---------------------------------------------------------------- |
| GREEN           | <p>Acc > 85%</p><p><br></p><p>RT: 400-900ms</p>              | Compliance High.                        | Proceed. No comment needed.                                      |
| YELLOW (Type A) | Acc: 60-80%                                                  | Misunderstood Rules or Confusion.       | Intervene. Re-explain button mapping over intercom.              |
| YELLOW (Type B) | RT < 250ms                                                   | "Button Mashing" (Anticipatory).        | Intervene. "Please slow down; accuracy matters more than speed." |
| YELLOW (Type C) | RT > 1000ms                                                  | Drifting/Sleepiness.                    | Intervene. "Stay with us, try to respond a bit faster."          |
| RED             | <p>Acc &#x3C; 60% (Chance)</p><p><br></p><p>No Responses</p> | Non-Compliance / Sleep / Hardware Fail. | STOP. Enter room. Check button box. Retrain or Abort.            |

#### The "Intercom Script" Protocol

_Standardize the feedback to avoid biasing the subject._

* _For Low Accuracy:_ "Great job. Just a reminder: Index finger is Yes, Middle finger is No. Take a moment to make sure you press the right one."
* _For Sleeping:_ "Doing okay? We missed a few responses there. Let's try to stay sharp for this next one."

***

### Phase 3: Data Ingestion (The "Hygiene" Check)

_Objective: Ensure file integrity immediately after the session (Before participant leaves)._

#### 1. File Verification

Action: Experimenter checks the local `data/` folder on the stimulus laptop.

* Size Check: File size must be > 1 KB. (0 KB = Write error).
* Naming Check: Verify BIDS compliance.
  * _Correct:_ `sub-01_ses-01_task-nback_run-01_events.tsv`
  * _Error:_ `test.csv`, `sub-01_run1_FINAL.csv`.
* Safety Net: If naming is wrong, rename it NOW before uploading to the server.

***

### Phase 4: Automated Quantitative QA (The Pipeline)

_Objective: Run the `behavioral_qc.py` script on the Lab Server (24h Post-Scan) to flag runs for exclusion._

#### 1. Performance Metrics & Thresholds

| **Metric**           | **Definition**                                  | **Exclusion Threshold** | **Rationale**                                               |
| -------------------- | ----------------------------------------------- | ----------------------- | ----------------------------------------------------------- |
| Response Rate        | % of trials with a keypress.                    | < 80%                   | Participant likely fell asleep.                             |
| Performance Accuracy | % Correct (Valid Trials).                       | < 60% (or Chance + 10%) | Random guessing; no task engagement.                        |
| Mashing Index        | % trials with RT < 150ms.                       | > 10%                   | Biologically impossible reaction time; predictive pressing. |
| Lapse Index          | % trials with RT > 2.5 SD of mean.              | > 15%                   | Attentional lapses (Micro-sleeps).                          |
| Response Bias        | % of single-button presses (e.g., 100% "Left"). | > 95%                   | Participant held one button down to get through the run.    |

#### 2. Reaction Time Distribution Analysis

The script generates a histogram for visual inspection.

* Pass: Log-Normal distribution (skewed right).
* Fail (Bimodal): Two peaks. Indicates the subject changed strategy or was confused for half the run.
* Fail (Flat/Uniform): Indicates random button pressing.

***

### Phase 5: REDCap & Clinical QA

_Objective: Ensure "Source of Truth" consistency._

#### 1. The "Golden Link" Check

Action: Automated script compares REDCap exports against MRI filenames.

* Subject ID Match: Does `sub-IFOCUS01` in MRI match `sub-IFOCUS01` in REDCap?
  * _Common Error:_ `sub-IFOCUS001` (extra zero) or `sub-IFOCUS_01` (underscore).
* Session Date Match: Does the MRI Scan Date match the "Visit Date" entered in REDCap?

#### 2. Logic Range Checks

* Demographics: Age 18-90? Sex M/F? (Flag empty fields).
* Surveys: Are scores within valid ranges? (e.g., STAI Anxiety score 20-80).
* Missing Data: Flag any participant with >5% missing responses on standardized questionnaires.

***

### Phase 6: The Action Plan (Master Exclusion Logic)

Scenario A: Hardware Failure

* _Observation:_ Log file exists but contains no triggers or no button presses (Sync failure).
* _Outcome:_ DISCARD BOLD RUN.
* _Reasoning:_ fMRI analysis requires accurate timing vectors. Without them, the brain images are useless.

Scenario B: "Yellow Light" Performance

* _Observation:_ Subject had 65% accuracy (low) but responded to 95% of trials.
* _Outcome:_ Keep Data, Covary in Analysis.
* _Reasoning:_ They were engaged but performed poorly. This is valid behavioral variance.

Scenario C: "Red Light" Performance

* _Observation:_ Subject had <50% accuracy or >20% sleep lapses.
* _Outcome:_ EXCLUDE RUN from Group Stats.
* _Action:_
  1. Move file to `derivatives/excluded_runs/`.
  2. Update `participants.tsv`: Mark `exclude` column with reason "Behavioral Non-Compliance."
