# The Data Lifecycle Routine

## **Phase 1: Data Acquisition & Ingestion (Daily/Weekly)**

_The "Birth" of the data. The goal here is immediate capture in the correct "Source of Truth."_

1. Neuroimaging Data (MRI)
   * Action: Scanner sends DICOMs directly to Flywheel.
   * Timeline: Automatic or within 24 hours of scan.
   * Status: Flywheel becomes the _Master Copy_ for MRI. BIDS conversion and Initial QC happen here.
2. Behavioral Task Data (Psychopy/Matlab)
   * Action: Experimenter uploads raw log files from the testing laptop to The Lab Server (`DATA/sourcedata/behavioral_logs/`).
   * Timeline: Immediately after data collection (max 24 hours).
   * Status: Lab Server becomes the _Master Copy_ for behavioral logs.
3. Clinical & Demographic Data
   * Action: Data entered directly into UW REDCap via eCRF or participant survey link.
   * Timeline: Real-time during the visit.
   * Status: REDCap is the _System of Record_. No PHI is saved to local disks.

***

## **Phase 2: Aggregation & Pre-Processing (Pre-Analysis)**

_Preparing the data for the compute cluster. Merging streams._

1. The "Clinical Export"
   * Action: Export sanitized demographics/survey scores from REDCap as `.csv` or `.tsv`.
   * Destination: Upload to The Lab Server (`DATA/sourcedata/redcap_exports/`).
2. Code Updates
   * Action: Develop analysis scripts locally.
   * Destination: Push changes to GitHub.
   * Constraint: strictly NO data files in the commit.

***

## **Phase 3: Active Computation (The "Workhorse" Cycle)**

_Moving data to the scratch space for heavy lifting._

1. Staging on Hyak
   * Pull Code: `git pull` from GitHub to Hyak `CODE/` directory.
   * Pull Data:
     * Download BIDS NIfTIs from Flywheel -> Hyak.
     * Download Behavioral/REDCap logs from Lab Server -> Hyak.
2. Execution
   * Run pipelines (fMRIPrep, FSL, etc.).
   * Outputs generate in Hyak `DATA/derivatives/`.
   * _Note:_ Intermediate files (scrub space) are left to expire (30-day auto-delete).

***

## **Phase 4: Sync, Backup & Archive (Monthly Routine)**

_The "Safety Net" protocol. This ensures that if Hyak or the Lab Server fails, nothing is lost._

The "Last Work Day of the Month" Checklist:

1. Save the Results (Hyak -> Server)
   * Action: Copy final derivatives (stats maps, preprocessed timeseries) from Hyak to The Lab Server (`DATA/derivatives/`).
   * Reason: Hyak is scratch space; the Lab Server is the permanent home for results.
2. The Disaster Backup (Server -> SharePoint)
   * Action: Sync the Lab Server `DATA/` folder to UW SharePoint.
   * Scope: This captures the Behavioral Logs, REDCap Exports, and the newly synced Derivatives.
3. The MRI Backup (Flywheel -> SharePoint)
   * Action: (Optional/Automated) Ensure new Flywheel datasets are mirrored to SharePoint `sourcedata/`.

***

## **Phase 5: Publication & Dissemination**

_Writing the paper and freezing the project._

1. Drafting
   * Location: Google Drive (`MANUSCRIPT/` folder).
   * Content: Draft text, figures, and grant details. No patient data.
2. Final Archival
   * Action: Upon paper acceptance, the final manuscript (PDF) and the "frozen" code commit hash are saved to The Lab Server (`MANUSCRIPT/` and `CODE/`).
   * Outcome: The project folder on the Lab Server now contains the complete history: Raw Behavioral + Sanitized Clinical + Final Derivatives + Final Paper.





