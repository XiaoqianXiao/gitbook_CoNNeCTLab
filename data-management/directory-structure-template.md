# Directory Structure  & Naming Template

## 1. The Golden Rules

* No Spaces: Use underscores (`_`) or hyphens (`-`). (e.g., `process_data.py`, not `process data.py`).
* Relative Paths: All code should run assuming it is located in the `code/` folder. Never hardcode absolute paths like `C:\Users\Name\Desktop`.
* Readmes: Every major folder gets a `README.md` explaining its contents.

## 2. The Directory Tree

We use a standardized directory tree for every project. This ensures that any team member can navigate any project, regardless of who created it. Copy this structure for every new experiment or grant project.

```
Project_Name/
├── README.md                 # Project overview, hypothesis, and personnel
├── ADMIN/                    # IRB, Grants, Protocols (No Data Here!)
│   ├── irb/                  # Consent forms templates, approval letters
│   ├── finance/              # Receipts, orders
│   └── meeting_notes/        # Weekly updates
│
├── DATA/                     # The Core Data Storage
│   ├── sourcedata/           # 1. The Muddy Potato. RAW files exactly as exported (DICOMs, E-Prime logs)
│   ├── rawdata/              # 2. The Peeled Potato. BIDS-converted NIfTI files (Immutable)
│   │   ├── dataset_description.json
│   │   ├── sub-001/
│   │   │   ├── anat/
│   │   │   └── func/
│   │   └── participants.tsv
│   │
│   └── derivatives/          # 3. Processed Data (The result of pipelines)
│       ├── fmriprep/         # Preprocessing outputs
│       ├── freesurfer/       # Surface reconstruction
│       └── first_level/      # Subject-level GLM results
│
├── CODE/                     # Analysis Scripts (Version Controlled)
│   ├── stimuli/              # PsychoPy/E-Prime scripts used during acquisition
│   ├── preprocessing/        # Scripts to convert DICOM -> BIDS -> Derivatives
│   ├── analysis/             # GLM, connectivity, machine learning scripts
│   └── notebooks/            # Jupyter/R notebooks for data visualization/exploration
│
└── MANUSCRIPT/               # Writing and Dissemination
    ├── drafts/               # Word/LaTeX docs
    ├── figures/              # Final high-res figures for publication
    └── tables/               # Output tables (Excel/CSV) for the paper
```

## 3. Folder Dictionary

**`ADMIN/`**

Stores the "paperwork."

* Crucial: Never store subject keys (names linking to IDs) here if this folder is synced to a shared cloud. Subject keys must be kept offline or in a secure, encrypted folder separate from the analysis directory.

**`DATA/`**

This follows the BIDS standard strictly.

* `sourcedata`: This is your safety net. If a conversion script fails, you delete `rawdata` and rebuild it from `sourcedata`. These are your original zip files from the scanner or raw behavioral CSVs.
* `rawdata`: The clean, standard version. Once created, these files should be "Read Only."
* `derivatives`: This is where 90% of the storage space goes. It contains preprocessed data (MNI space images, denoised time series).

**`CODE/`**

* `stimuli`: The exact version of the task presented to the subject.
* `notebooks`: Great for "scratchpad" work. If a notebook generates a final figure, clean it up and save it.

**`MANUSCRIPT/`**

* `figures`: Do not manually drag figures here. Ideally, your scripts in `CODE/` should save the final plots directly to this folder to ensure the figure exactly matches the data.

## 4. File Naming Standards (BIDS)

We strictly follow BIDS to ensure our data is machine-readable. This applies to MRI, fMRI, and Behavioral files.

The Golden Rule: `key-label_suffix.extension`

### **4.1. Neuroimaging Naming**

| **Modality**  | **Template**                   | **Example**                                        |
| ------------- | ------------------------------ | -------------------------------------------------- |
| Anatomical    | `sub-ID_ses-ID_T1w`            | `sub-01_ses-baseline_T1w.nii.gz`                   |
| Resting State | `sub-ID_ses-ID_task-rest_bold` | `sub-01_ses-baseline_task-rest_bold.nii.gz`        |
| Task fMRI     | `sub-ID_ses-ID_task-NAME_bold` | `sub-01_ses-baseline_task-fear_run-01_bold.nii.gz` |

### **4.2. Behavioral Data Naming**

Behavioral data must be converted from proprietary formats (e.g., `.edat3`, `.mat`, `.csv`) into Tab-Separated Values (.tsv).

1\. Task Events (Synced to fMRI) Used for GLM analysis. These files describe exactly what happened during the scan.

* Format: `sub-ID_ses-ID_task-NAME_run-ID_events.tsv`
* Location: Inside the `func/` folder, paired with the `.nii.gz` file.
* Required Columns: `onset`, `duration`, `trial_type`, `response_time`.

2\. Standalone Behavioral Data Used for tasks done outside the scanner (e.g., neuropsychological batteries, computer tasks).

* Format: `sub-ID_ses-ID_task-NAME_beh.tsv`
* Location: Inside a `beh/` folder (e.g., `sub-01/ses-01/beh/`).
* Example: `sub-01_ses-baseline_task-cpt_beh.tsv` (Continuous Performance Test)

### **4.3. Phenotype & Clinical Data**

We do not put clinical details in filenames. Instead, we use the `participants.tsv` file located in the root of `rawdata/`.

Correct `participants.tsv` Example:

Code snippet

```
participant_id  age  sex  group     anxiety_score  drug_condition
sub-001         24   F    patient   12             placebo
sub-002         29   M    control   4              n/a
```

***

## 5. Critical "Do's and Don'ts"

| **Category** | **Correct (Do This)** | **Incorrect (Don't Do This)** | **Why?**                                                                    |
| ------------ | --------------------- | ----------------------------- | --------------------------------------------------------------------------- |
| Separators   | `sub-01_task-rest`    | `sub-01-task-rest`            | Hyphens bind Keys/Values; Underscores bind Pairs.                           |
| Zero Padding | `sub-004`             | `sub-4`                       | Ensures files sort chronologically (01...10) not alphabetically (1, 10, 2). |
| Behavioral   | `events.tsv`          | `data.xlsx`                   | Proprietary formats (Excel, SPSS) are not archival. Use TSV.                |
| Group Info   | `sub-001`             | `sub-PATIENT01`               | Never encode clinical status in the filename. Use `participants.tsv`.       |
