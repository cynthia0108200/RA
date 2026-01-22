
# Mortgage Call Report (MCR) Digitization Project

## Project Overview
This project converts Mortgage Call Report data from PDF files into structured CSV datasets. The goal is to digitize specific financial and operational data for shadow banks.

## Inputs
- **Directory**: `CR` folder containing PDF files.
- **File Types**:
  - `**FC.pdf` (Financial Condition): We extract Schedule C (Income Statement) data.
  - `**General.pdf`: We extract Credit Line (AC codes) and Servicing (LS codes) data.

## Outputs
- `FC_output.csv`: Data from Schedule C of FC files.
- `CreditLine_output.csv`: Credit line data from General files.
- `Servicing_output.csv`: Servicing data from General files.
- `data_summary.txt`: Summary statistics of the processed data.

## Code Description
- `digitize_mcr.py`: Main script.
  - Recursively finds all `.pdf` files in the input directory.
  - Determines file type (FC or General) based on filename and content.
  - Parses headers to extract Company Name, NMLS ID, Year, and Quarter.
  - Extracts variable codes (`C...`, `AC...`, `LS...`) and their associated values.
  - Handles multi-column variable rows dynamically.
  - Handles data irregularities like negative values defined by parentheses `(Value)`.
  - Aggregates all data into pandas DataFrames and saves as CSV.
- `summarize_data.py`: Generates summary statistics (missingness, means) for the output CSVs.

## How to Run

1. **Install Dependencies**:
   ```bash
   pip install pdfplumber pandas
   ```

2. **Run Extraction**:
   ```bash
   python digitize_mcr.py /path/to/CR_folder
   ```
   (If no path is provided, it looks for `CR_sample` by default)

3. **Run Summary**:
   ```bash
   python summarize_data.py
   ```

## Design Decisions & Irregularities
- **Schedule C vs Balance Sheet**: The requirements requested "Balance sheet data... specifically Schedule C". Schedule C typically contains Income Statement data (as confirmed by the `Schedule C: Income` header in the files). The script honors the specific instruction to extract Schedule C variables (`C` codes).
- **Missing Sections**: Some files (e.g., `1Rate, Inc Q1 General.pdf`) state "No information regarding ... provided" for sections like Lines of Credit. In these cases, the metadata is preserved but variable columns are empty/null.
- **Variable Definitions**: As variable definitions may change over time (V5, V6, etc.), the script dynamically creates columns based on the codes found in the text rather than enforcing a rigid schema. Columns are named `Code_Index` (e.g., `C010_0`, `C010_1`) corresponding to the sequence of values found for that code.
- **Negative Values**: Values in parentheses `(1,000)` are converted to negative numbers `-1000`.

## Contact
For questions regarding this digitization process, please contact the developer.

