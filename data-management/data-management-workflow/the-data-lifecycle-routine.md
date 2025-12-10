# The Data Lifecycle Routine

_(A flow chart showing: MRI Scanner -> Lab Server. Then splitting: Server -> SharePoint (Backup) and Server -> Hyak (Process) -> Server (Results) -> Drive (Figures))_

Follow this routine for every new subject acquired.

## **Step 1: Ingestion (Day 0)**

1. Transfer: Move data from the MRI Scanner/Behavioral PC to the Lab Server (`DATA/sourcedata/`).
2. Conversion: Run `dcm2bids` on the Lab Server to generate `DATA/rawdata/`.
3. De-identification: Verify no PHI exists in `rawdata`.

### **Step 2: Sync & Backup (Day 1)**

1. SharePoint Sync: Immediately upload the new `sourcedata` and `rawdata` to SharePoint.
   * _Why?_ If the server crashes tomorrow, we can rebuild. If we lose the DICOMs, the money is wasted.
2. Code Push: Push any changes in `CODE/` to GitHub/GitLab.

**Step 3: Processing (Day 2+)**

1. Transfer to Hyak: `rsync` the BIDS data for the specific subject to Hyak's temporary scratch space.
2. Execution: Run the pipeline (e.g., fMRIPrep) on Hyak.
3. Return: `rsync` the results (`DATA/derivatives/`) from Hyak back to the Lab Server.
4. Cleanup: Delete the raw BIDS data from Hyak to save space/cost.

**Step 4: Dissemination (Project End)**

1. Figure Generation: Run notebooks on the Lab Server to generate plots.
2. Drive Sync: Upload the final plots from `MANUSCRIPT/figures/` to Google Drive for the team to write the paper.
