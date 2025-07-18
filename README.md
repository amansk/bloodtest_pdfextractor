Created by vibe coding with Claude Code. Excuse the code quality.

# Blood Test PDF to CSV Extractor

A robust CLI tool to extract blood test information from lab report PDFs and output the results as CSV files. Supports multiple lab formats including Quest Diagnostics, LabCorp, and Cleveland HeartLab.

## Features

- **Multi-format Support**: Handles Quest Diagnostics (Analyte/Value), LabCorp, Function Health Dashboard, and Vibrant America formats
- **Intelligent Format Detection**: Automatically detects and uses the appropriate extraction method
- **Optional Format Override**: Force specific lab format (`--format=quest`, `--format=labcorp`, or `--format=function_health`) when needed
- **Reference Range Extraction**: Optional extraction of reference ranges with `--include-ranges` flag
- **Comprehensive Extraction**: Extracts 80+ markers including CBC, CMP, hormones, lipids, and fatty acids
- **Single CSV Output**: All markers in one file, preserving the original PDF order
- **Flexible Output Formats**: Standard CSV or enhanced CSV with reference ranges
- **Date Auto-detection**: Automatically extracts test dates from PDF content
- **Value Validation**: Validates extracted values against realistic medical ranges
- **API Support**: FastAPI web service for programmatic access
- **Fallback PDF Reading**: Uses PyPDF2 with pdfplumber fallback for robust text extraction
- **Configurable**: JSON-based configuration for markers and extraction settings

## Installation

1. Install Python dependencies:
```bash
pip3 install -r requirements.txt
```

2. Make the script executable:
```bash
chmod +x pdf_to_csv.py
```

Or use the install script:
```bash
./install.sh
```

## Usage

### Basic Usage
```bash
python3 pdf_to_csv.py your_lab_report.pdf
```

This will create a single CSV file: `your_lab_report.csv` containing all extracted markers in the order they appear in the PDF.

### Specify Output File
```bash
python3 pdf_to_csv.py your_lab_report.pdf --output results.csv
```

This creates: `results.csv` with all extracted markers.

### Verbose Output
```bash
python3 pdf_to_csv.py your_lab_report.pdf --verbose
```

### Extract with Reference Ranges
```bash
python3 pdf_to_csv.py your_lab_report.pdf --include-ranges
# or
python3 pdf_to_csv.py your_lab_report.pdf -r
```

### Force Specific Lab Format
```bash
python3 pdf_to_csv.py your_lab_report.pdf --format quest
python3 pdf_to_csv.py your_lab_report.pdf --format labcorp
python3 pdf_to_csv.py your_lab_report.pdf --format function_health
```

### Command Line Options
- `--output, -o`: Specify the output CSV file path
- `--verbose, -v`: Enable verbose output to see extracted data
- `--include-ranges, -r`: Include reference ranges (MinRange, MaxRange) in output
- `--format`: Force specific lab format (`quest`, `labcorp`, or `function_health`) - auto-detects if not specified
- `--config-dir`: Specify custom configuration directory (default: config)
- `--help`: Show help message

## Supported Lab Formats

### Quest Diagnostics (Most Common)
- **Format**: Analyte/Value structured tables
- **Detection**: Looks for "Analyte" and "Value" headers
- **Examples**: Wild Health reports, most Quest lab reports
- **Extraction**: 80-90 markers typically extracted

### LabCorp + Cleveland HeartLab Combination
- **Format**: LabCorp structured results + Cleveland fatty acid profiles
- **Detection**: Identifies both LabCorp codes (01, 02, 03, 04) and Cleveland HeartLab sections
- **Examples**: Combination reports with comprehensive panels + specialized fatty acid analysis
- **Extraction**: 80+ markers from both lab formats

### Cleveland HeartLab Only
- **Format**: Specialized fatty acid and cardiovascular markers
- **Detection**: Cleveland HeartLab headers with fatty acid data
- **Examples**: Cardiometabolic reports
- **Extraction**: Omega-3, Omega-6, EPA, DHA, ratios

### Function Health Dashboard
- **Format**: Function Health's comprehensive biomarker dashboard export
- **Detection**: Looks for "In Range", "Out of Range", "Biomarkers" patterns and characteristic layout
- **Examples**: Function Health dashboard PDF exports
- **Extraction**: 80-110+ markers including autoimmunity, biological age, hormones, vitamins, minerals
- **Note**: PDFs may have spaced text extraction issues with PyPDF2; the tool automatically uses pdfplumber for better results

## Extracted Markers

The tool extracts a comprehensive set of blood markers across multiple categories:

### Complete Blood Count (CBC)
WBC, RBC, Hemoglobin, Hematocrit, MCV, MCH, MCHC, RDW, Platelets, MPV, Neutrophils, Lymphocytes, Monocytes, Eosinophils, Basophils (both % and absolute counts)

### Comprehensive Metabolic Panel (CMP)  
Glucose, BUN, Creatinine, eGFR, Sodium, Potassium, Chloride, CO2, Calcium, Total Protein, Albumin, Globulin, Bilirubin, Alkaline Phosphatase, AST, ALT

### Lipid Panel & Advanced Lipids
Total Cholesterol, HDL, LDL, Triglycerides, Non-HDL Cholesterol, LDL Particle Number, LDL Small, HDL Large, Apolipoprotein A1, Apolipoprotein B, Lipoprotein(a)

### Hormones
Testosterone (Total & Free), DHEA Sulfate, Sex Hormone Binding Globulin, Cortisol, Estradiol, Progesterone, TSH, Free T4, Free T3

### Fatty Acids & Inflammation
Omega-3 Total, Omega-6 Total, EPA, DHA, DPA, Arachidonic Acid, Linoleic Acid, Omega-6/Omega-3 Ratio, Arachidonic Acid/EPA Ratio, hs-CRP, LP-PLA2

### Vitamins & Metabolic
Vitamin D, Vitamin B12, Vitamin B6, Folate (Serum & RBC), Hemoglobin A1c, Insulin, Ferritin, Iron, TIBC, Homocysteine, Uric Acid, TMAO, Coenzyme Q10

## Output Format

### Standard CSV Format
The CSV file contains all extracted markers in the order they appear in the PDF:
```csv
Marker Name,2024-04-04
GLUCOSE,83
UREA NITROGEN (BUN),14
CREATININE,0.98
EGFR,100
WHITE BLOOD CELL COUNT,6.4
HEMOGLOBIN,16.6
LDL-CHOLESTEROL,80
HDL CHOLESTEROL,72
TESTOSTERONE, TOTAL, MS,589
VITAMIN D, 25-OH, TOTAL,42.6
```

### CSV Format with Reference Ranges
When using the `--include-ranges` flag, the CSV includes reference range columns:
```csv
Marker,MinRange,MaxRange,2025-06-10
LDL-P,,1000,1258
LDL-C (NIH Calc),0,99,113
HDL-C,39,,69
Triglycerides,0,149,40
Glucose,70,99,101
BUN,6,24,15
Creatinine,0.57,1.00,0.67
WBC,3.4,10.8,6.2
Hemoglobin,11.1,15.9,14.4
TSH,0.450,4.500,1.240
```

## Installation as a Command Line Tool

To install as a system-wide command:

```bash
pip3 install -e .
```

Then you can use it as:
```bash
pdf-to-csv your_lab_report.pdf
```

## API Usage

The tool includes a FastAPI web service for programmatic access:

```bash
# Start the API server
python api.py
```

### API Endpoints

**POST** `/convert` - Convert PDF to CSV

Query Parameters:
- `include_ranges` (boolean, optional): Include reference ranges in output (default: false)
- `format` (string, optional): Force specific lab format (`quest`, `labcorp`, or `function_health`) - auto-detects if not specified

**Example API Calls:**

```bash
# Basic conversion
curl -X POST "http://localhost:8000/convert" \
  -H "Content-Type: multipart/form-data" \
  -F "file=@your_lab_report.pdf"

# With reference ranges
curl -X POST "http://localhost:8000/convert?include_ranges=true" \
  -H "Content-Type: multipart/form-data" \
  -F "file=@your_lab_report.pdf"

# Force specific format with ranges
curl -X POST "http://localhost:8000/convert?include_ranges=true&format=quest" \
  -H "Content-Type: multipart/form-data" \
  -F "file=@your_lab_report.pdf"
```

## Real Examples

### Quest Diagnostics Report (2025 April Wild Health)
**Extracted 85 markers** including comprehensive CBC, CMP, hormones, advanced lipids, and fatty acid profiles:

```csv
Marker Name,2025-04-04
WHITE BLOOD CELL COUNT,6.4
HEMOGLOBIN,16.6
GLUCOSE,83
TESTOSTERONE, TOTAL, MS,589
LDL PARTICLE NUMBER,1605
OMEGA-3 TOTAL,7.6
EPA,1.6
DHA,4.0
VITAMIN D, 25-OH, TOTAL,42.6
HEMOGLOBIN A1c,5.3
```

### LabCorp + Cleveland HeartLab Combination (2024 October)
**Extracted 82 markers** from both lab formats in a single comprehensive report:

```csv
Marker Name,2024-10-30
WBC,4.3
Hemoglobin,15.6
Glucose,82
Testosterone,789
Arachidonic Acid/EPA Ratio,11.4
Omega3 Total,6.2
EPA,1.0
DHA,3.5
TMAO (Trimethylamine N-oxide),3.3
Vitamin D, 25-Hydroxy,50.4
```

## Configuration

The tool uses JSON configuration files in the `config/` directory:

### `config/markers.json`
Defines marker patterns, validation ranges, and categories. Organized by:
- `default_markers`: Core lab markers by category (CBC, CMP, hormones, etc.)
- `other_markers`: Additional specialized markers

### `config/settings.json` 
Contains extraction settings:
- `date_patterns`: Regex patterns for date extraction
- `value_patterns`: Patterns for marker-value extraction
- `exclusion_lists`: Keywords to filter out noise
- `extraction_settings`: Thresholds and parameters

## Troubleshooting

### Common Issues
- **No data extracted**: PDF format not recognized. Use `--verbose` to see extracted text
- **Low marker count**: May indicate wrong format detection. Check if PDF has Analyte/Value structure
- **Wrong date**: Date extraction failed, current date used. Edit CSV header manually if needed
- **Missing specific markers**: Pattern matching may need adjustment in `config/markers.json`

### Format-Specific Issues
- **Quest reports**: Should detect Analyte/Value structure and extract 80+ markers
- **LabCorp combination**: Needs both LabCorp codes and Cleveland sections for full extraction
- **Fragmented PDFs**: Tool will attempt fragmented extraction method

### Debug Tips
- Use `--verbose` flag to see extraction method and marker counts
- Check that pdfplumber is installed for problematic PDFs: `pip install pdfplumber`
- Examine extracted text patterns if markers are missing

## Requirements

- Python 3.7+
- PyPDF2 
- pdfplumber (optional, for fallback PDF reading)
- click
- python-dateutil

## Architecture

The tool uses a modular object-oriented design:
- **ConfigLoader**: Manages JSON configuration files
- **ValueValidator**: Validates extracted values against medical ranges  
- **PatternMatcher**: Compiles and manages regex patterns
- **TextProcessor**: Cleans and processes PDF text
- **DateExtractor**: Extracts dates from text
- **LabReportExtractor**: Main extraction logic for different formats
- **BloodTestExtractor**: Orchestrates the entire extraction process
