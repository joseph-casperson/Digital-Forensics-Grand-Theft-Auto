# Tool Methodology — Operation Blue Ghost

Step-by-step guide for reproducing the forensic analysis performed on this disk image.

---

## Environment Setup

### Required Tools

| Tool | Version | Purpose |
|------|---------|---------|
| Autopsy | 4.x+ | Primary analysis platform |
| Foremost | 1.5.7+ | File carving |
| Kali Linux (recommended) | 2023+ | Foremost is pre-installed |

### Optional Tools

- **FTK Imager** — Useful for verifying image integrity and browsing file structure before Autopsy
- **ExifTool** — For extracting JPEG metadata beyond what Autopsy surfaces
- **Volatility** — If RAM image is provided (not included in base image)

---

## Phase 1: Image Verification

Before beginning analysis, verify the integrity of the disk image.

```bash
# Generate MD5 hash of the image
md5sum image.vmdk

# Generate SHA-256 (preferred for forensic documentation)
sha256sum image.vmdk
```

Document both hashes. Any analysis performed on an unverified image may be inadmissible in a real-world context.

---

## Phase 2: Autopsy Analysis

### 2.1 — Create a New Case

1. Launch Autopsy
2. Select **New Case**
3. Fill in case name: `Operation_Blue_Ghost`
4. Set examiner name and case number
5. Click **Finish**

### 2.2 — Add Data Source

1. Click **Add Data Source**
2. Select **Disk Image or VM File**
3. Browse to the `.vmdk` or `.vdi` image file
4. Select timezone (use the system's local timezone or UTC)
5. Click **Next**

### 2.3 — Configure Ingest Modules

Enable **all** of the following ingest modules:

| Module | Purpose |
|--------|---------|
| Recent Activity | Browser history, cookies, downloads |
| Hash Lookup | Flag known-bad files |
| File Type Identification | Identify files by signature, not extension |
| Embedded File Extractor | Extract email attachments |
| Email Parser | Parse `.eml` and Thunderbird mailboxes |
| Keyword Search | Search for case-relevant terms |
| Exif Parser | Extract image metadata |
| Extension Mismatch Detector | Flag files with mismatched extensions |

### 2.4 — Recommended Keyword Searches

Set up the following keyword searches before running ingest:

```
rx8
RX-8
mazda
bill of sale
maintenance
buyer
seller
stolen
```

### 2.5 — Navigating Results

Once ingest completes, use the left panel to navigate:

```
Results
├── Extracted Content
│   ├── Web History          ← Browser artifact E11
│   ├── Web Bookmarks        ← Browser artifact E12
│   └── Email Messages       ← E01, E02, E03, E04
├── Deleted Files            ← E04, E07, E09, E10, E14
└── Views
    ├── File Types
    │   ├── Documents        ← E05, E06, E07
    │   └── Images           ← E08, E09, E10
    └── Deleted Files        ← All deleted artifacts
```

### 2.6 — Exporting Artifacts

Right-click any artifact → **Extract File** to save it for further analysis.

For the full evidence inventory, use **Generate Report** → **Excel/CSV** to export all findings.

---

## Phase 3: Foremost File Carving

Foremost works by scanning raw disk data for known file signatures (magic bytes), making it effective for recovering files that have been deleted and whose directory entries have been overwritten.

### 3.1 — Basic Carving Command

```bash
# Carve all supported file types
foremost -i /path/to/image.vmdk -o ./foremost_output

# Carve specific types only (faster, more focused)
foremost -t jpeg,doc,pdf,eml -i /path/to/image.vmdk -o ./foremost_output
```

### 3.2 — Output Structure

Foremost creates a directory for each file type:

```
foremost_output/
├── audit.txt       ← Summary of all recovered files
├── jpg/            ← Recovered JPEG images (E09, E10)
├── doc/            ← Recovered DOCX files (E07)
├── pdf/            ← Recovered PDF files (E05)
└── eml/            ← Recovered email files (if carved)
```

### 3.3 — Reading the Audit File

```bash
cat foremost_output/audit.txt
```

The audit file lists every carved file with its offset in the disk image, size, and whether it was successfully extracted. Cross-reference these offsets with Autopsy's hex viewer to confirm findings.

### 3.4 — Expected Carved Files

| File | Type | Foremost Folder |
|------|------|----------------|
| `car_exterior.jpeg` | JPEG | `jpg/` |
| `car_parts.jpeg` | JPEG | `jpg/` |
| `rx8_maintenance_history.docx` | DOCX | `doc/` |
| `rx8_bill_of_sale.pdf` | PDF | `pdf/` |

> Note: Foremost may also carve non-deleted files. Cross-reference with Autopsy's active file list to distinguish recovered-deleted from active files.

---

## Phase 4: Metadata Analysis (Optional Extension)

For deeper analysis of the JPEG files, use ExifTool:

```bash
# Install ExifTool (Kali/Debian)
sudo apt install libimage-exiftool-perl

# Extract all metadata from a JPEG
exiftool car_interior.jpeg

# Extract just GPS data
exiftool -GPS* car_interior.jpeg

# Batch process all JPEGs in a folder
exiftool -csv *.jpeg > jpeg_metadata.csv
```

Key fields to examine:
- `DateTimeOriginal` — When the photo was actually taken
- `GPSLatitude` / `GPSLongitude` — Location data (if present)
- `Make` / `Model` — Camera used
- `Software` — Any editing software applied after capture

---

## Phase 5: Documentation

### Evidence Logging Template

For each artifact found, document:

```
Artifact ID:     E##
Filename:        
File Type:       
MD5 Hash:        
Status:          Active / Deleted-Recovered
Location:        [Autopsy path or Foremost offset]
Discovery Tool:  Autopsy / Foremost
Forensic Notes:  
```

### Report Generation

In Autopsy:
1. **Generate Report** → **HTML Report** for a full searchable case report
2. **Generate Report** → **Excel** for artifact inventory spreadsheet
3. Export individual artifacts as needed for evidence documentation

---

## Validation Checklist

Use this checklist to confirm you have located all planted evidence:

- [ ] E01 — Negotiation email (active)
- [ ] E02 — Photo request email (active)
- [ ] E03 — Meetup planning email (active)
- [ ] E04 — Deleted email recovered
- [ ] E05 — Bill of sale PDF located
- [ ] E06 — Parts list DOCX located
- [ ] E07 — Deleted maintenance DOCX recovered
- [ ] E08 — Interior JPEG located
- [ ] E09 — Deleted exterior JPEG recovered
- [ ] E10 — Deleted parts JPEG recovered
- [ ] E11 — Browser search history located
- [ ] E12 — Bookmarked page located
- [ ] E13 — Buyer contact `.vcf` located
- [ ] E14 — Deleted ECU log recovered
- [ ] E15 — Fake syslog identified and evaluated

**Score: __ / 15**

---

*Methodology guide prepared as part of the Operation Blue Ghost educational forensics project.*
