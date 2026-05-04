---
description: How about data on redcap?
---

# The Storage Ecosystem

We utilize specific storage locations for specific tasks to ensure data safety and efficiency. We do not duplicate the entire directory tree everywhere.

Rule of Thumb:

* Storage Locations are for safe-keeping and "Sources of Truth"
* Active Locations are for computing, coding, and writing.



<figure><img src="../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>

***

## Part 1: Storage & Archives (The "Backups")

_These locations are primarily for data preservation. Treat files here as the "Master Copies."_

### **1. The Lab Server (Central Hub)**

* Role: Keep documents for admin. <mark style="color:$danger;">No PHI</mark>.
*   Contents:

    ```
    Project_Name/
    └──  ADMIN/
    ```
* Note: We do not store raw data here.

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

### **3. UW SharePoint (Behavioral, Clinical and Demographics cloud and Disaster Recovery)**

* Role:&#x20;
  * Secure, HIPAA-compliant, immutable backup for ALL unrecoverable source data (MRI + Behavioral + Clinical + Demographics).
  * Secure, HIPAA-compliant, immutable backup for ALL unrecoverable raw data (MRI + Behavioral + Clinical + Demographics), processed to excluded HIP info and should be ready for share. &#x20;
* Storage Policy: "Raw Assets Only" (No derivatives, no scratch files).
*   Contents:

    <pre><code>Project_Name/
    └── DATA/
        ├── sourcedata/       # The Safety Net (DICOMs, Demographics, Clinical Accessments and Task Behavioral)
        │    ├── <a data-footnote-ref href="#user-content-fn-1">clinical_accessments</a>
        │    ├── <a data-footnote-ref href="#user-content-fn-2">demographics</a>
        │    ├── MRI(<a data-footnote-ref href="#user-content-fn-3">dicom</a>)
        │    └── <a data-footnote-ref href="#user-content-fn-4">behavioral</a>
        ├── rawdata/          # The Standard (BIDS NIfTIs &#x26; TSVs)
        │    ├── clinical_accessments
        │    ├── demographics
        │    ├── MRI(nifit)
        │    └── behavioral  
        └── derivatives/  # Final results synced back from Hyak
    </code></pre>
* Sync Routine:&#x20;
  * assessment video is uploaded here immediately (within 24 hours after gathered by rater):[https://uwnetid.sharepoint.com/:f:/r/sites/i-focus\_fang\_lab/Shared%20Documents/ifocus/DATA/sourcedata/assessment?csf=1\&web=1\&e=dwcgk5](https://uwnetid.sharepoint.com/:f:/r/sites/i-focus_fang_lab/Shared%20Documents/ifocus/DATA/sourcedata/assessment?csf=1\&web=1\&e=dwcgk5)&#x20;
  * all other data should be uploaded here by data manager every half year (1/15; 7/15; by data manager)

***

## Part 2: Active Use (The "Workhorses")

_These locations are temporary workspaces. Data here is transient, changing, or being computed on._

### **1. Hyak (High-Performance Computing)**

* Role: Heavy computation and pipeline execution (fMRIPrep, FSL, PyTorch). <mark style="color:$danger;">No PHI</mark>.
* Storage Policy:&#x20;
  * Scratch Space: Final Code & Derivatives Only (Persistent during project).
  * Scrub Space: Temporal Code and intermediate results (Deleted automatically if not modified for 30 days).
*   Contents

    ```
    # in /gscratch/scrubbed/fanglab
    Project_Name/
    ├── DATA/
    │   ├── sourcedata/       # source to be process: DICOMs, Demographics, Clinical Accessments and Task Behavioral
    │   ├── rawdata/          # The Standard (BIDS NIfTIs & TSVs, standard and safe to share version)
    │   └── derivatives/      # Output of pipelines (preproc, stats)
    └── CODE/                 # Analysis scripts (Pulled from GitHub)
    # in /gscratch/fang
    Project_Name/
    ├── DATA/
    │   └── derivatives/      # Output of pipelines (preproc, stats)
    └── CODE/                 # Analysis scripts (Pulled from GitHub)
    ```
* Sync Routine: Once a month (last work day of each month by data manager)

### **2. GitHub (Code Versioning)**

* Role: Version control, reproducibility, and script sharing. <mark style="color:$danger;">No PHI</mark>.
* Storage Policy: "Code Only" (Strictly NO data).
*   Directory Subset:

    ```
    Project_Name/
    └── CODE/                 # .py, .sh, .ipynb files
    ```
* Workflow: develop code locally -> push to GitHub -> pull to Hyak for execution.

### **3. Google Drive (Collaboration)**

* Role: Collaborative writing, administrative tracking, and presentations. <mark style="color:$danger;">No PHI</mark>.
* Storage Policy: "Documents Only" (Strictly NO Patient Data/PHI).
*   Directory Subset:

    ```
    Project_Name/
    ├── ADMIN/                # Grant text, Schedules
    └── MANUSCRIPT/           # Drafts, Tables, Figures for papers
    ```

### **4. UW SharePoint**

* Role: Secure workspace for daily sensitive data handling.

```
Project_Name/
└── working_dir/ #Secure workspace for daily sensitive data handling
```

### 5. UW REDCap **(**&#x44;emographics & Clinical/Survey Databas&#x65;**)** <mark style="color:red;">TBD</mark>

* Role: The "Source of Truth" for Participant PHI, Demographics, and Survey responses.
* Storage Policy: "System of Record" (Data originates here). strictly NO permanent storage of exported PHI on local laptops.
* Sync Routine (Export): Exported as `.csv` or `.tsv` to Hyak (`DATA/sourcedata/`demographics`/; or DATA/sourcedata/`clinical\_accessments`/`) for analysis every month (last work day of each month by data manager).



[^1]: [https://uwnetid.sharepoint.com/:f:/r/sites/i-focus\_fang\_lab/Shared%20Documents/i-focus/DATA/sourcedata/clinical\_accessments?csf=1\&web=1\&e=HdqSa0](https://uwnetid.sharepoint.com/:f:/r/sites/i-focus_fang_lab/Shared%20Documents/i-focus/DATA/sourcedata/clinical_accessments?csf=1\&web=1\&e=HdqSa0)

[^2]: [https://uwnetid.sharepoint.com/:f:/r/sites/i-focus\_fang\_lab/Shared%20Documents/i-focus/DATA/sourcedata/demographics?csf=1\&web=1\&e=6PRF4e](https://uwnetid.sharepoint.com/:f:/r/sites/i-focus_fang_lab/Shared%20Documents/i-focus/DATA/sourcedata/demographics?csf=1\&web=1\&e=6PRF4e)

[^3]: [https://uwnetid.sharepoint.com/:f:/r/sites/i-focus\_fang\_lab/Shared%20Documents/i-focus/DATA/sourcedata/dicom?csf=1\&web=1\&e=rdQpSy](https://uwnetid.sharepoint.com/:f:/r/sites/i-focus_fang_lab/Shared%20Documents/i-focus/DATA/sourcedata/dicom?csf=1\&web=1\&e=rdQpSy)

[^4]: [https://uwnetid.sharepoint.com/:f:/r/sites/i-focus\_fang\_lab/Shared%20Documents/i-focus/DATA/sourcedata/task\_behavioral?csf=1\&web=1\&e=OSIriY](https://uwnetid.sharepoint.com/:f:/r/sites/i-focus_fang_lab/Shared%20Documents/i-focus/DATA/sourcedata/task_behavioral?csf=1\&web=1\&e=OSIriY)
