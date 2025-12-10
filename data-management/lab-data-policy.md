# Lab Data Policy

This is a critical first step. For a clinical neuroscience lab, your data policy serves two purposes: it ensures strict compliance with ethical standards (IRB/HIPAA) regarding human subjects, and it sets the standard for scientific rigor (reproducibility, BIDS formatting, etc.).

Here is a comprehensive draft for your Lab Data Policy.

***

## 1. Core Commitment

Our lab is dedicated to the highest standards of data integrity, subject privacy, and scientific transparency. We view data not just as a resource, but as a responsibility. This policy outlines how we handle data from acquisition to archiving.

## 2. Human Subjects & Privacy

Given the clinical nature of our work, protecting our participants is paramount.

* De-identification: All clinical and neuroimaging data must be de-identified immediately upon acquisition. Participants are assigned a unique Subject ID (e.g., `SUB_001`). Keys linking IDs to PII (Personally Identifiable Information) are stored offline in a physically or digitally detached, encrypted location.
* Compliance: All data handling strictly adheres to \[University/Institution Name] IRB protocols and HIPAA guidelines.
* Restricted Access: Raw clinical data is accessible only to authorized lab members who have completed mandatory ethics training (e.g., CITI training).

## 3. Data Organization & Standards

To ensure our work is reproducible, we adhere to community standards for data organization.

* BIDS Compliance: All neuroimaging data (fMRI, MRI, EEG) must be organized according to the Brain Imaging Data Structure (BIDS).
* Code Repositories: Analysis code is treated as part of the data. All scripts (Python, MATLAB, R) must be version-controlled using Git and stored in the lab’s private repositories until publication.
* Documentation: Every dataset must include a `README` file detailing acquisition parameters, preprocessing steps, and known artifacts.

## 4. Storage & Security

* Active Data: Current projects are stored on the \[Lab Server Name/High-Performance Cluster], which is backed up daily.
* Local Storage: Storing unencrypted subject data on personal laptops or portable drives is strictly prohibited.
* Cloud Usage: Use only institution-approved cloud storage solutions (e.g., OneDrive, Share Point, Labserver) for non-sensitive data or de-identified shared data.

## 5. Open Science & Data Sharing

We are committed to open science. Upon publication of results:

* Raw Data: De-identified raw data will be uploaded to public repositories (e.g., OpenNeuro, NIMH Data Archive) whenever consent permits.
* Derived Data: Statistical maps and derived data (ROI time series) will be shared via platforms like NeuroVault or OSF.
* Pre-registration: We encourage the pre-registration of hypotheses and analysis plans for confirmatory studies.

## 6. Departure Policy

When a trainee or staff member leaves the lab:

* All data and code remain the property of the lab.
* The departing member must ensure their project folder is clean, documented, and fully accessible to the PI.
* A "handoff document" must be created detailing the location of raw data and the specific scripts used to generate final figures
