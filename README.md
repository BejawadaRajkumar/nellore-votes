# Nellore Votes — Telugu Voter List OCR

**Extract structured voter records from Telugu electoral-roll PDFs using local OCR.**

`nellore-votes` is a Python-based project focused on processing scanned Telugu voter lists, identifying individual voter cards, recognizing Telugu and numeric fields, and exporting the extracted records as structured JSON.

The goal is to make large electoral-roll documents easier to process while preserving the original Telugu text and flagging uncertain results for manual verification.

## Features

- **Telugu OCR:** Recognize Telugu names and other printed fields.
- **Batch PDF processing:** Process multi-page electoral rolls in a single workflow.
- **Voter-card detection:** Separate individual records from three-column page layouts.
- **Structured JSON output:** Store extracted fields in a consistent format.
- **Numeric validation:** Check ages, serial numbers, house numbers, and voter ID formats.
- **Duplicate detection:** Identify potentially duplicated voter IDs.
- **Uncertainty reporting:** Flag unreadable, missing, or conflicting fields for review.
- **Local processing:** Designed to run on Windows without uploading voter records to an external OCR service.

## Technology Stack

| Technology | Purpose |
|---|---|
| Python | Processing pipeline and data extraction |
| PyMuPDF | PDF reading and page rendering |
| OpenCV | Image preprocessing and voter-card boundary detection |
| PaddleOCR | Telugu text recognition |
| Tesseract OCR | Optional comparison engine for selected fields |
| JSON | Structured output and review reports |

The exact OCR engines and package versions should be confirmed against the implementation.

## How It Works

1. Load the electoral-roll PDF.
2. Render pages into high-resolution images.
3. Detect the grid and crop individual voter cards.
4. Preprocess the crops to improve text readability.
5. Recognize Telugu text and numeric fields.
6. Extract fields using the card layout and printed labels.
7. Validate extracted values and flag uncertain records.
8. Export structured JSON and a report of records requiring review.

## Extracted Data

The intended record schema includes:

| Field | Description |
|---|---|
| `page` | Source PDF page number |
| `card_index` | Position of the voter card on the page |
| `serial_number` | Printed voter serial number |
| `part_number` | Electoral-roll part or section reference |
| `name_te` | Voter name in Telugu |
| `relative_name_te` | Relative's name in Telugu |
| `age` | Extracted age |
| `gender` | Normalized gender value |
| `house_number` | House number preserved as text |
| `epic_id` | Voter identification number |
| `review_required` | Whether manual verification is required |
| `validation_issues` | List of detected problems |

The final schema may vary with the source document and implementation.

## Example JSON

```json
{
  "page": 1,
  "card_index": 1,
  "serial_number": 1,
  "part_number": "117-159-24",
  "name_te": "తెలుగు పేరు",
  "relative_name_te": "బంధువు పేరు",
  "age": 28,
  "gender": "male",
  "house_number": "2/1/129",
  "epic_id": "XXXXXXXXXX",
  "review_required": true,
  "validation_issues": [
    "EPIC_ID_REQUIRES_VERIFICATION"
  ]
}
```

This is an illustrative schema, not an actual extracted record.

## Installation

### Requirements

- Windows 10 or Windows 11
- Python 3.11 recommended
- Sufficient RAM and disk space for the PDF and rendered page images
- Internet access for the initial installation and model download, if required

### Setup

Clone the repository:

```powershell
git clone https://github.com/BejawadaRajkumar/nellore-votes.git
cd nellore-votes
```

Create and activate a virtual environment:

```powershell
py -3.11 -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install --upgrade pip
```

Install the project dependencies using its `requirements.txt` if available:

```powershell
pip install -r requirements.txt
```

For a new environment, install a compatible PaddlePaddle CPU runtime and PaddleOCR version according to the [official PaddleOCR documentation](https://www.paddleocr.ai/).

## Usage

Place the input electoral-roll PDF in the configured input directory, then run the project's entry-point script.

For example, if the project exposes a command-line entry point:

```powershell
python src/pipeline.py --input input/voter-list.pdf --output output/records.json
```

The command above illustrates the intended interface. Use the actual script and arguments implemented in the repository.

## Output Structure

A typical output directory could look like this:

```text
output/
├── records.json
├── review.json
├── manifest.json
└── crops/
    ├── page_001_card_001.png
    └── page_001_card_002.png
```

- `records.json`: Extracted voter records.
- `review.json`: Records or fields requiring manual verification.
- `manifest.json`: Processing statistics, page coverage, and errors.
- `crops/`: Source crops for inspecting uncertain results.

These filenames describe the intended output organization.

## Accuracy and Validation

OCR output should not be treated as verified electoral data.

The validation process should:

- Preserve Telugu Unicode text without automatic translation.
- Preserve leading zeros and punctuation in identifiers where applicable.
- Check numeric fields for malformed or implausible values.
- Detect duplicate identifiers and missing fields.
- Flag disagreements between independent OCR passes.
- Retain source page and card references for traceability.
- Require manual verification of voter IDs before treating them as accurate.

OCR confidence scores are useful indicators, not guarantees of correctness. Accuracy should be measured against manually verified sample records.

## Privacy

Electoral rolls can contain personal information. This project is designed around local processing to minimize unnecessary disclosure.

- Do not commit source electoral-roll PDFs or extracted personal data to Git.
- Keep input documents and generated outputs out of public repositories.
- Avoid logging complete voter records unnecessarily.
- Share only the minimum data needed for debugging or testing.

## Limitations

- Accuracy depends on scan resolution, print quality, and image alignment.
- Telugu names may require manual review when characters are blurred or merged.
- Voter-card boundaries can vary between pages.
- Numeric OCR can confuse visually similar characters.
- A syntactically valid voter ID is not proof that the extracted ID is correct.
- Performance depends on the CPU, available memory, and image resolution.

## Roadmap

- [ ] Benchmark Telugu OCR models on representative pages.
- [ ] Improve three-column voter-card boundary detection.
- [ ] Extract all supported fields into a stable JSON schema.
- [ ] Add EPIC ID and numeric-field validation.
- [ ] Generate review reports with links to source crops.
- [ ] Support resumable processing and per-page checkpoints.
- [ ] Measure field-level accuracy against manually verified data.
- [ ] Add automated tests for extraction and validation.

## Contributing

Contributions that improve Telugu recognition, document segmentation, field extraction, validation, and processing reliability are welcome.

When reporting an issue, include the relevant error message and a synthetic or appropriately redacted sample where possible. Do not upload identifiable voter information into public issue trackers.

## License

Add the project's chosen license here before distributing the code. Until a license is specified, reuse and redistribution permissions should not be assumed.

---

**Project:** [BejawadaRajkumar/nellore-votes](https://github.com/BejawadaRajkumar/nellore-votes)
