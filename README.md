# CardioEHR

EHR data processing and analysis project.


This project contains Python scripts for privacy-preserving processing of EHR data and R scripts for reproducible analysis and visualization.

## Data

The original EHR data are hosted on the OMIX platform under controlled access (accession number: OMIX010546). Please submit a formal request to access the dataset.

## Quick Start

1.  **Clone Repo:**
    ```bash
    git clone https://github.com/biosubmit/CardioEHR.git
    cd CardioEHR
    ```

2.  **Install Dependencies:**
    ```bash
    uv sync
    ```

3.  **Process Data:**
    ```bash
    uv run split_dignosis.py
    ```
   Raw patient demographic tables were first processed using the split_patient_info.py module to remove direct identifiers and harmonize basic demographic attributes. Personal identifiers such as name, national ID number, address, and phone number were removed entirely, and a unique, irreversible pseudonym (patient_pseudo_id) was generated using a salted SHA-256 hash to enable secure linkage across tables. Dates of birth were converted into age or age bands (5–10-year intervals) to protect privacy while retaining demographic information. Free-text notes in demographic records were scanned with pattern-matching rules to remove any residual identifiers. The resulting de-identified patient table retained only non-identifiable attributes such as sex, age group, and pseudo-ID for downstream linkage.
    
    ```bash
    uv run split_lab_test.py
    ```
    Laboratory test data were cleaned and harmonized by the split_lab_test.py module. Test names and result units were mapped to standardized nomenclature according to the institution’s internal terminology dictionary. The script normalized measurement units (e.g., converting mg/dL to mmol/L when applicable) and flagged extreme outliers for review. Test timestamps were transformed to relative time (days since admission) to preserve within-visit temporal structure while preventing exact date disclosure. Free-text fields, including comments and remarks that could contain identifying information, were removed. The resulting dataset preserved numeric results, standardized test names, and time offsets suitable for time-aligned analyses.
    ```bash
    uv run split_patient_info.py
    ```

    ```bash
    uv run split_visit_record.py
    ```
    The split_visit_record.py script standardized visit-level data, including admission and discharge timestamps, attending department, and visit identifiers. Timestamps were converted into standardized POSIX time format and underwent controlled date-shifting by a random offset within ±30 days, applied consistently per patient to preserve temporal relationships while concealing exact calendar dates. Ward, bed, and room identifiers were dropped or aggregated to higher-level hospital units (e.g., department or division). Department names were harmonized to standard labels using an internal dictionary. Each visit record was linked to the corresponding pseudo-ID and assigned a sequential visit index to support longitudinal modeling of admissions.   

5.  **Run Analysis:**
    ```bash
    Rscript analysis.r
    ```


## License

MIT License.
