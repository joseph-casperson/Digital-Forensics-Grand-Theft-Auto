# 🚗 Operation Blue Ghost — Digital Forensics Case Image

> A custom-built forensic disk image simulating a GTA-style stolen vehicle sale investigation.  
> Designed for use with Autopsy, Foremost, and standard DFIR tooling.

[![Course](https://img.shields.io/badge/Course-CYBR%203700-blue)](.)
[![Tools](https://img.shields.io/badge/Tools-Autopsy%20%7C%20Foremost-green)](.)
[![Evidence](https://img.shields.io/badge/Evidence%20Items-15%2B-orange)](.)
[![Status](https://img.shields.io/badge/Status-Used%20in%20Future%20Classes-brightgreen)](.)

---

## 📋 Table of Contents

- [Project Overview](#-project-overview)
- [The Scenario](#-the-scenario)
- [Evidence Inventory](#-evidence-inventory)
- [Tools Used](#-tools-used)
- [Repository Structure](#-repository-structure)
- [How to Use This Image](#-how-to-use-this-image)
- [Key Findings](#-key-findings)
- [Skills Demonstrated](#-skills-demonstrated)
- [About](#-about)

---

## 🔍 Project Overview

This project was built as the capstone assessment for **CYBR 3700 – Digital Forensics**. The goal was to construct a realistic, analyzable disk image containing planted evidence, deleted files, and red herrings — then perform a full forensic analysis to validate the planted artifacts.

The image was well-received enough that the **professor adopted it as a lab exercise for future classes**.

### What Makes This Interesting

- A fully self-contained narrative case (not just random files)
- Realistic mix of evidence types: emails, documents, images, browser history, syslogs, and contact cards
- Intentional file deletion to simulate anti-forensic behavior
- Validated against two independent tools (Autopsy + Foremost)
- 15+ distinct evidence artifacts, all recoverable and documented

---

## 🕵️ The Scenario

**Operation Blue Ghost** follows the trail of a suspect attempting to sell a stolen 2004 Mazda RX-8 (blue) through informal online channels.

The disk image represents the suspect's computer and contains:

- **Email correspondence** between the seller and prospective buyers negotiating price, exchanging photos, and coordinating a handoff
- **Vehicle documentation** — a fraudulent bill of sale, maintenance logs, and a parts list — suggesting the car was being prepped for illegal transfer
- **Photos** of the car's exterior, interior, and engine bay, shared with buyers
- **Browser history** showing searches for RX-8 parts, modification guides, and car forums consistent with someone preparing a stolen vehicle for resale
- **A fake syslog** planted as a decoy artifact to complicate the investigation
- **Deleted files** simulating the suspect attempting to cover their tracks before the machine was seized

The investigator's job: reconstruct the chain of events using only what the disk reveals.

---

## 📁 Evidence Inventory

| # | Artifact | Type | Status | Found By |
|---|----------|------|--------|----------|
| 1 | Seller–Buyer email thread (negotiation) | `.eml` | Active | Autopsy |
| 2 | Seller–Buyer email thread (photo request) | `.eml` | Active | Autopsy |
| 3 | Seller–Buyer email thread (meetup planning) | `.eml` | Active | Autopsy |
| 4 | `Yo,_did_you….eml` | `.eml` | **Deleted** | Autopsy (recovered) |
| 5 | `rx8_maintenance_history.docx` | `.docx` | **Deleted** | Foremost (recovered) |
| 6 | `rx8_bill_of_sale.pdf` | `.pdf` | Active | Autopsy + Foremost |
| 7 | `rx8_parts_list.docx` | `.docx` | Active | Autopsy + Foremost |
| 8 | `car_exterior.jpeg` | Image | **Deleted** | Foremost (recovered) |
| 9 | `car_interior.jpeg` | Image | Active | Autopsy + Foremost |
| 10 | `car_parts.jpeg` | Image | **Deleted** | Foremost (recovered) |
| 11 | Browser history (car parts searches) | Browser artifact | Active | Autopsy |
| 12 | Bookmarked RX-8 forum page | Browser artifact | Active | Autopsy |
| 13 | Buyer contact card | `.vcf` | Active | Autopsy |
| 14 | `ecu_pill_log_021825.txt` | Log / Syslog | **Deleted** | Autopsy |
| 15 | Fake syslog (decoy) | Log file | Active | Autopsy |

> **5 files were intentionally deleted** to simulate anti-forensic behavior. All were successfully recovered.

---

## 🛠 Tools Used

### Autopsy
- Full disk image analysis
- File system timeline reconstruction
- Email and browser artifact extraction
- Deleted file recovery
- Keyword search across carved artifacts

### Foremost
- File carving by file signature (magic bytes)
- Recovery of deleted JPEG images and DOCX documents
- Validation of Autopsy's deleted file findings

> **Note:** BulkExtractor was intentionally excluded per assignment requirements — it lacks the breadth to surface all planted evidence types.

---

## 📂 Repository Structure

```
rx8-forensics/
│
├── README.md                   ← You are here
├── CASE_BRIEF.md               ← Narrative write-up of findings
├── EVIDENCE_GUIDE.md           ← Detailed breakdown of each artifact
├── TOOL_METHODOLOGY.md         ← Step-by-step analysis methodology
│
├── docs/
│   ├── scenario_overview.md    ← Full scenario planning document
│   └── analysis_report.md     ← Formal forensic analysis report
│
├── screenshots/
│   ├── autopsy/                ← Autopsy output screenshots
│   ├── foremost/               ← Foremost output screenshots
│   ├── evidence_folder/        ← Pre-deletion evidence folder
│   └── deleted_files/          ← Post-deletion confirmation
│
└── evidence/
    ├── planted/                ← Source files used to build the image
    └── recovered/              ← Files recovered during analysis
```

---

## 🚀 How to Use This Image

### Prerequisites

- [Autopsy](https://www.autopsy.com/download/) (v4.x or later)
- [Foremost](https://github.com/korczis/foremost) or Kali Linux (includes Foremost)
- A VM environment capable of mounting `.vmdk` or `.vdi` disk images

### Loading the Image in Autopsy

1. Open Autopsy → **New Case**
2. Add a data source → select **Disk Image or VM File**
3. Point to the provided `.vmdk` / `.vdi` image file
4. Run all ingest modules (keyword search, email parser, web artifacts, etc.)
5. Work through the [Evidence Guide](EVIDENCE_GUIDE.md) to locate each artifact

### Running Foremost

```bash
# Basic file carving against the image
foremost -t jpeg,doc,pdf,eml -i /path/to/image.vmdk -o ./foremost_output

# Review output directory for recovered files
ls ./foremost_output/
```

### Suggested Investigation Order

1. Start with **email artifacts** — they establish the narrative
2. Cross-reference with **documents** (bill of sale, maintenance log)
3. Examine **photos** for context matching email descriptions
4. Review **browser history** for corroborating searches
5. Investigate **deleted files** — what was the suspect trying to hide?
6. Evaluate the **syslog** — is it real or a decoy?

---

## 🔑 Key Findings

- The **Thunderbird email client** preserved full correspondence between the seller and multiple buyers, including price negotiation and photo exchange
- **Foremost successfully recovered** all 5 deleted files, including the maintenance history `.docx` and two JPEG images
- **Autopsy recovered** the deleted `.eml` file, demonstrating email-level anti-forensic attempts
- **Browser history** corroborated the narrative — searches aligned with preparing a vehicle for sale
- The **fake syslog** served as a realistic red herring, requiring the investigator to evaluate log plausibility before dismissing it
- The **buyer contact `.vcf`** provided a concrete link between the suspect and a named individual

---

## 💼 Skills Demonstrated

| Skill | Details |
|-------|---------|
| **Forensic Image Construction** | Planned and populated a disk image with realistic, interconnected evidence |
| **Anti-Forensic Simulation** | Deliberately deleted files to simulate suspect behavior |
| **Autopsy** | Full case analysis, artifact extraction, deleted file recovery |
| **Foremost** | File carving by signature across multiple file types |
| **Evidence Documentation** | Catalogued 15+ artifacts with type, status, and discovery method |
| **Narrative Forensics** | Constructed a coherent investigative story from fragmented digital evidence |
| **Linux CLI** | Used `dd`, FTK Imager tooling, and command-line carvers |
| **Email Forensics** | Analyzed `.eml` files and Thunderbird mail client artifacts |
| **Browser Forensics** | Extracted and interpreted browsing history and bookmarks |

---

## 📖 About

**Author:** Joseph Casperson  
**Course:** CYBR 3700 – Digital Forensics  
**Outcome:** Professor adopted this image for use in future class labs

The RX-8 in the scenario was inspired by my actual car from Guam — a blue 2006 Mazda RX-8 that I have a lot of affection for. Making the scenario personal made the evidence feel more grounded and the investigation more enjoyable to construct.

---

*This project is shared for educational and portfolio purposes. All scenario content is fictional.*
