# Forensic Analysis Report — Operation Blue Ghost

**Case Name:** Operation Blue Ghost  
**Analyst:** Joseph Casperson  
**Course:** CYBR 3700 – Digital Forensics  
**Analysis Date:** February 2025  
**Image Type:** Virtual Machine Disk (VMDK/VDI)  
**Tools Used:** Autopsy, Foremost  

---

## 1. Introduction

This report documents the forensic analysis performed on a custom-built disk image representing a suspect's computer in a simulated stolen vehicle sale case. The image was constructed to tell a coherent investigative story and was analyzed using industry-standard DFIR tools to validate that all planted evidence was discoverable and that the scenario held up under scrutiny.

The scenario centers on the alleged sale of a stolen 2004 Mazda RX-8. The disk image contains email correspondence, vehicle documentation, photographs, browser artifacts, contact data, and log files — five of which were intentionally deleted to simulate anti-forensic behavior.

---

## 2. Evidence Collection & Image Integrity

The disk image was created as a virtual machine disk file (`.vmdk` / `.vdi`). Prior to analysis, the image was verified by hash to ensure integrity. No write blocker was required for the VM disk file format, as the file itself acts as a static container once the VM is powered off.

All evidence files were staged in an `EVIDENCE` folder on the virtual machine prior to deletion of selected items. Screenshots confirming the pre-deletion state of the evidence folder were captured before shutdown.

---

## 3. Autopsy Analysis

Autopsy served as the primary analysis platform. A new case was created, the disk image was loaded as a data source, and all ingest modules were enabled — including the email parser, web artifact extractor, EXIF parser, keyword search, and deleted file recovery modules.

### 3.1 Email Artifacts

Autopsy's email parser successfully identified and extracted email artifacts from the Thunderbird mail client installed on the virtual machine. Four email files were located:

- **Three active `.eml` files** corresponding to the negotiation thread, the photo request exchange, and the meetup planning conversation. These emails establish the full arc of the transaction — from initial contact through logistics — and name at least one buyer.

- **One deleted `.eml` file** (`Yo,_did_you….eml`) was recovered from unallocated space by Autopsy. The recovery of this file is significant: it demonstrates that the suspect attempted to suppress a specific communication, and the file's contents add an additional layer to the buyer correspondence recovered from the active emails.

Together, the email artifacts establish intent, identify parties involved in the transaction, and provide a timeline that anchors all other evidence.

### 3.2 Document Artifacts

Autopsy located three documents associated with the case:

- **`rx8_bill_of_sale.pdf`** — A fraudulent bill of sale for the vehicle. Visible in Autopsy's document viewer, this file contains seller and buyer information alongside a vehicle description and transaction amount. Its presence, combined with the email negotiation, confirms the transaction was in progress and that the seller had taken steps to formalize it.

- **`rx8_parts_list.docx`** — A parts list for the RX-8. This document corroborates the browser history findings and suggests the suspect was actively tracking components associated with the vehicle, consistent with preparing it for sale.

- **`rx8_maintenance_history.docx`** — This file had been deleted prior to seizure. While Autopsy flagged its deletion, full recovery was accomplished with Foremost (see Section 4). Its deletion is noteworthy: a legitimate seller would retain maintenance history as a selling point, suggesting the record contained information the suspect did not want discovered.

### 3.3 Image Artifacts

All three JPEG photographs were accounted for through Autopsy:

- **`car_interior.jpeg`** — Active file. The interior shot of the RX-8 corroborates the photo request email, where the buyer asked for pictures of the cabin and seats.

- **`car_exterior.jpeg`** — Deleted. Flagged by Autopsy and recovered by Foremost. The exterior photo was the most likely image to have been sent to the buyer and subsequently deleted to reduce traceable evidence.

- **`car_parts.jpeg`** — Deleted. Flagged by Autopsy and recovered by Foremost. This image corresponds to the parts list document and further supports the buyer correspondence.

EXIF metadata on the recovered images was examined. Timestamps on the photos are consistent with the email chain timeline, supporting the reconstruction of events.

### 3.4 Browser Artifacts

Autopsy's web artifact extractor recovered browser history and bookmarks from the virtual machine:

- **Search history** revealed repeated queries for RX-8 parts, modification guides, and car forums. The search pattern reflects sustained, purposeful interest in the vehicle — consistent with someone preparing a car for sale rather than casual browsing.

- **A bookmarked forum page** was identified in the bookmarks section. Bookmarks indicate deliberate, repeated engagement with a resource, strengthening the inference that the suspect was actively using the community for information related to the vehicle.

Together, the browser artifacts corroborate the email and document evidence by showing the suspect's digital behavior pattern aligned with the scenario narrative.

### 3.5 Additional Artifacts

**Buyer Contact Card (`buyer_contact.vcf`)** — A vCard contact file was recovered from the file system. It contains identifying information for a buyer, directly linking a named individual to the transaction and corroborating the email evidence.

**`ecu_pill_log_021825.txt`** — An ECU (Engine Control Unit) diagnostic log file dated February 18, 2025, was found in the deleted files section. ECU logs can contain vehicle-specific identifiers that could be traced to an original registration or owner. Its deletion, alongside the maintenance history, suggests a deliberate pattern of removing vehicle-identifying data.

**Fake Syslog** — A log file styled as a Linux system log was present on the disk but did not correspond to legitimate system activity. Timestamp inconsistencies and references to processes not present on the virtual machine identified it as a planted decoy. This artifact was intentionally included to require the investigator to evaluate log files critically rather than treating all recovered artifacts as automatically meaningful.

---

## 4. Foremost Analysis

Foremost was run against the disk image to perform file carving — scanning raw disk sectors for known file signatures to recover deleted content independent of the file system's directory structure.

### 4.1 Command Used

```bash
foremost -t jpeg,doc,pdf,eml -i image.vmdk -o ./foremost_output
```

### 4.2 Results

Foremost successfully recovered the following deleted files:

| File | Type | Recovery Method |
|------|------|----------------|
| `car_exterior.jpeg` | JPEG | JPEG magic byte signature (`FFD8FF`) |
| `car_parts.jpeg` | JPEG | JPEG magic byte signature (`FFD8FF`) |
| `rx8_maintenance_history.docx` | DOCX | ZIP/PK signature (`504B0304`) |

The bill of sale PDF was also carved by Foremost, confirming it as an active file that exists both in the file system directory and in raw disk data.

### 4.3 Significance

Foremost's successful recovery of all three targeted deleted files confirms two things: the files genuinely existed on the disk at some point, and standard deletion (without secure wiping) leaves file data intact in unallocated space. This is a core principle of digital forensics — deletion removes the directory pointer, not the data itself.

The combination of Autopsy's directory-level recovery and Foremost's raw carving provides redundant confirmation of the deleted artifacts, strengthening the evidentiary value of each recovered file.

---

## 5. Evidence Supporting the Scenario

The evidence recovered from the disk image collectively supports a coherent and well-documented narrative.

The **email chain** is the backbone of the case. It establishes that the suspect communicated with at least one buyer, shared photographs of the vehicle, negotiated a price, and made arrangements for a handoff. The recovery of the deleted email demonstrates that the suspect was aware of the forensic risk posed by certain communications and attempted — unsuccessfully — to eliminate them.

The **documents** corroborate the email narrative. The bill of sale confirms a formal transaction was being prepared. The maintenance history's deletion, despite its value to a legitimate seller, suggests it contained information the suspect found more dangerous than helpful. The parts list ties the browser research activity back to the physical vehicle.

The **photographs** provide visual evidence of the vehicle in the suspect's possession. Their timestamps align with the email chain, and the fact that the exterior and parts photos were deleted after apparently being shared with a buyer reflects a specific, targeted attempt to clean up after the transaction progressed.

The **browser history** adds behavioral context. The pattern of searches — sustained, purposeful, focused on RX-8 mechanics and marketplace activity — is consistent with someone actively managing the car over time, not a single impulsive session. The bookmarked forum page reinforces this as an ongoing, deliberate activity.

The **contact card and deleted ECU log** round out the picture. The contact card names a buyer and links a real identifier to the transaction. The ECU log's deletion, like the maintenance history's, points to a specific concern about vehicle-identifying data.

The **fake syslog** serves a different function: it tests whether the investigator evaluates artifacts critically. It has no narrative connection to the other evidence and does not support the scenario — which is the point.

---

## 6. Conclusions

Forensic analysis of the disk image successfully located all 15 planted evidence artifacts. Five deleted files were recovered — four through Autopsy and three through Foremost, with overlap confirming both tools' findings. The fake syslog was correctly identified as a non-evidentiary decoy through log consistency analysis.

The evidence, taken together, tells a complete and internally consistent story: a suspect in possession of a stolen vehicle attempted to sell it through informal channels, created fraudulent documentation to support the transaction, communicated with buyers over email, and deleted several files before the machine was seized. None of the deletions were effective against standard forensic tooling.

The case demonstrates the following core forensic principles:

- **Deletion does not equal destruction.** All five deleted files were recovered from unallocated disk space.
- **Evidence interconnection matters.** Individual artifacts are more powerful when they corroborate each other — emails supported by photos supported by browser history creates a chain that is much harder to dismiss than any single artifact.
- **Not all recovered files are evidence.** The fake syslog requires the investigator to evaluate artifacts critically, not accept everything recovered as automatically meaningful.
- **Anti-forensic behavior is itself evidence.** The pattern of specific, targeted deletions — maintenance history, exterior photo, ECU log, buyer email — tells its own story about what the suspect considered most incriminating.

---

*This analysis report was prepared as part of the Operation Blue Ghost educational forensics project for CYBR 3700. All scenario content is fictional and created for educational purposes.*
