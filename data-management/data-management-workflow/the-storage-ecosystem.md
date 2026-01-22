---
description: How about data on redcap?
---

# The Storage Ecosystem

We utilize specific storage locations for specific tasks to ensure data safety and efficiency. We do not duplicate the entire directory tree everywhere.

Rule of Thumb:

* Storage Locations are for safe-keeping and "Sources of Truth"
* Active Locations are for computing, coding, and writing.

<figure><img src="../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>

***

## Part 1: Storage & Archives (The "Backups")

_These locations are primarily for data preservation. Treat files here as the "Master Copies."_

### **1. The Lab Server (Central Hub)**

* Role: The aggregator for non-imaging data and the final landing spot for analysis results.
*   Contents:

    ```
    Project_Name/
    ├── ADMIN/
    └── DATA/
        └── sourcedata/       # demographics, clinical_accessments and task_behavioral 
    ```
* Note: We do not store raw MRI data here.
* Sync Routine:&#x20;
  * Behavioral data is uploaded here immediately (within 24 hours after gathered by experimenter).&#x20;
  * smb://172.25.226.40/FangLab/Lab Studies/R01 - Contributions of SFA/DATA/sourcedata/behavioral/self\_others
  * smb://172.25.226.40/FangLab/Lab Studies/R01 - Contributions of SFA/DATA/sourcedata/behavioral/ANT

### **2. Flywheel (MRI Master Database)**

* Role: The "Source of Truth" for all neuroimaging data. Handles acquisition, conversion, and initial QC.
*   Contents:

    ```
    Project_Name/
    └── DATA/
        └── sourcedata/       # Raw DICOMs from scanner(dimcom)
      
    ```
* Key Feature: BIDS curation and "Gears" (containers) for initial QC/preprocessing.
* Sync Routine: MRI data is uploaded here immediately (within 24 hours after gathered by CNH).&#x20;

### **3. UW SharePoint (Disaster Recovery)**

* Role: Secure, HIPAA-compliant, immutable backup for ALL unrecoverable source data (MRI + Behavioral).
* Storage Policy: "Raw Assets Only" (No derivatives, no scratch files).
*   Contents:

    ```
    Project_Name/
    └── DATA/
        ├── sourcedata/       # The Safety Net (DICOMs Demographics, Clinical Accessments and Task Behavioral)
        └── rawdata/          # The Standard (BIDS NIfTIs & TSVs)
        └── derivatives/  # Final results synced back from Hyak
    ```
* Sync Routine:&#x20;
  * assessment video is uploaded here immediately (within 24 hours after gathered by experimenter):[https://uwnetid.sharepoint.com/:f:/r/sites/i-focus\_fang\_lab/Shared%20Documents/ifocus/DATA/sourcedata/assessment?csf=1\&web=1\&e=dwcgk5](https://uwnetid.sharepoint.com/:f:/r/sites/i-focus_fang_lab/Shared%20Documents/ifocus/DATA/sourcedata/assessment?csf=1\&web=1\&e=dwcgk5) (by rater)
  * all other data should be uploaded here by data manager every month (last work day of every month by data manager)

***

## Part 2: Active Use (The "Workhorses")

_These locations are temporary workspaces. Data here is transient, changing, or being computed on._

### **1. Hyak (High-Performance Computing)**

* Role: Heavy computation and pipeline execution (fMRIPrep, FSL, PyTorch).
* Storage Policy:&#x20;
  * Scratch Space: Final Code & Derivatives Only (Persistent during project).
  * Scrub Space: Temporal Code and intermediate results (Deleted automatically if not modified for 30 days).
*   Contents

    ```
    Project_Name/
    ├── DATA/
    │   ├── sourcedata/       # source to be process: DICOMs, Demographics, Clinical Accessments and Task Behavioral
    │   ├── rawdata/          # The Standard (BIDS NIfTIs & TSVs, standard and safe to share version)
    │   └── derivatives/      # Output of pipelines (preproc, stats)
    └── CODE/                 # Analysis scripts (Pulled from GitHub)
    ```
* Sync Routine: Once a month for fMRIPrep output (last work day of each month by data manager)

### **2. GitHub (Code Versioning)**

* Role: Version control, reproducibility, and script sharing.
* Storage Policy: "Code Only" (Strictly NO data).
*   Directory Subset:

    ```
    Project_Name/
    └── CODE/                 # .py, .sh, .ipynb files
    ```
* Workflow: develop code locally -> push to GitHub -> pull to Hyak for execution.

### **3. Google Drive (Collaboration)**

* Role: Collaborative writing, administrative tracking, and presentations.
* Storage Policy: "Documents Only" (Strictly NO Patient Data/PHI).
*   Directory Subset:

    ```
    Project_Name/
    ├── ADMIN/                # Grant text, Schedules
    └── MANUSCRIPT/           # Drafts, Tables, Figures for papers
    ```

### **4.** UW REDCap **(**&#x44;emographics & Clinical/Survey Databas&#x65;**)** <mark style="color:red;">TBD</mark>

* Role: The "Source of Truth" for Participant PHI, Demographics, and Survey responses.
* Storage Policy: "System of Record" (Data originates here). strictly NO permanent storage of exported PHI on local laptops.
* Sync Routine (Export): Exported as `.csv` or `.tsv` to Hyak (`DATA/sourcedata/behavioral/`) for analysis every month (last work day of each month by data manager).

