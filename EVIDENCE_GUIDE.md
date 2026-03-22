# Evidence Guide — Operation Blue Ghost

This document provides detailed notes on each evidence artifact in the disk image, including its forensic significance, how to locate it, and what it contributes to the overall narrative.

Use this as a reference when working through the image in Autopsy or Foremost.

---

## Evidence Overview

| ID | Filename | Type | Deleted? | Tool |
|----|----------|------|----------|------|
| E01 | Seller–Buyer negotiation email | `.eml` | No | Autopsy |
| E02 | Seller–Buyer photo request email | `.eml` | No | Autopsy |
| E03 | Seller–Buyer meetup email | `.eml` | No | Autopsy |
| E04 | `Yo,_did_you….eml` | `.eml` | **Yes** | Autopsy |
| E05 | `rx8_bill_of_sale.pdf` | PDF | No | Autopsy + Foremost |
| E06 | `rx8_parts_list.docx` | DOCX | No | Autopsy + Foremost |
| E07 | `rx8_maintenance_history.docx` | DOCX | **Yes** | Foremost |
| E08 | `car_interior.jpeg` | JPEG | No | Autopsy + Foremost |
| E09 | `car_exterior.jpeg` | JPEG | **Yes** | Foremost |
| E10 | `car_parts.jpeg` | JPEG | **Yes** | Foremost |
| E11 | Browser history (search queries) | Browser artifact | No | Autopsy |
| E12 | Bookmarked forum page | Browser artifact | No | Autopsy |
| E13 | `buyer_contact.vcf` | Contact card | No | Autopsy |
| E14 | `ecu_pill_log_021825.txt` | Text log | **Yes** | Autopsy |
| E15 | Fake syslog | Text log | No | Autopsy |

---

## Detailed Artifact Notes

---

### E01 — Seller–Buyer Negotiation Email

**Type:** `.eml`  
**Status:** Active  
**Location in Autopsy:** Email Messages → Thunderbird inbox/sent

**Description:**  
The initial contact email thread between the seller and a prospective buyer. Establishes the opening of the transaction, including the seller's description of the vehicle and a quoted asking price.

**Forensic Significance:**  
Establishes the seller's intent to transfer the vehicle for money. Combined with the bill of sale (E05), this confirms a transaction was in progress.

**What to look for:**  
- Sender/receiver addresses
- Timestamps — do they align with photo creation dates?
- Price figures — do they match the bill of sale?

---

### E02 — Photo Request Email

**Type:** `.eml`  
**Status:** Active  
**Location in Autopsy:** Email Messages → Thunderbird

**Description:**  
A buyer requests photographs of the vehicle. The seller responds with images matching E08, E09, and E10.

**Forensic Significance:**  
Links the email correspondence to the photo artifacts. The photos sent match what exists on disk, creating a coherent evidence chain.

**What to look for:**  
- Does the email description match the actual photos?
- Are attachment references present in the email headers?

---

### E03 — Meetup Planning Email

**Type:** `.eml`  
**Status:** Active  
**Location in Autopsy:** Email Messages → Thunderbird

**Description:**  
Later-stage email coordinating a physical handoff of the vehicle. Contains location or timing references.

**Forensic Significance:**  
Demonstrates the transaction moved beyond negotiation into logistics — indicating serious criminal intent rather than exploratory communication.

---

### E04 — `Yo,_did_you….eml` ⚠️ DELETED

**Type:** `.eml`  
**Status:** Deleted — Recovered by Autopsy  
**Location in Autopsy:** Deleted Files section

**Description:**  
An email that was deleted from the disk prior to seizure. The filename suggests informal communication. Content likely contains information the suspect did not want investigators to find.

**Forensic Significance:**  
The act of deletion is itself evidence. Combined with the other deletions (E07, E09, E10, E14), it demonstrates a pattern of intentional evidence destruction.

**Note for investigators:** Pay attention to what specifically was being discussed. The contrast between retained and deleted emails may reveal what the suspect considered most incriminating.

---

### E05 — `rx8_bill_of_sale.pdf`

**Type:** PDF  
**Status:** Active  
**Location in Autopsy:** Documents section; also carved by Foremost

**Description:**  
A bill of sale for the RX-8 containing vehicle details, buyer/seller information, and a transaction amount.

**Forensic Significance:**  
This is a key document — it formalizes the transaction. Investigators should examine whether the seller name, buyer name, and VIN match other artifacts. Fabricated or inconsistent details are themselves evidence of fraud.

**Foremost recovery command:**
```bash
foremost -t pdf -i image.vmdk -o ./output
```

---

### E06 — `rx8_parts_list.docx`

**Type:** DOCX  
**Status:** Active  
**Location in Autopsy:** Documents section

**Description:**  
A list of aftermarket or replacement parts associated with the RX-8.

**Forensic Significance:**  
Suggests the vehicle was being worked on — either to improve its value before sale or to address mechanical issues. Also provides a record of financial investment in the vehicle.

---

### E07 — `rx8_maintenance_history.docx` ⚠️ DELETED

**Type:** DOCX  
**Status:** Deleted — Recovered by Foremost  
**Location:** Foremost output → `doc/` directory

**Description:**  
A maintenance log for the vehicle. Deleted before seizure.

**Forensic Significance:**  
Deletion of a maintenance history is counterintuitive for a legitimate seller (it reduces the car's value). This suggests the log contained identifying information — possibly the real owner's name or a VIN that doesn't match the bill of sale.

**Foremost recovery:**  
Foremost identifies DOCX files by their ZIP-based file signature (`PK\x03\x04`). The file appears in the `doc/` output folder.

---

### E08 — `car_interior.jpeg`

**Type:** JPEG  
**Status:** Active  
**Found by:** Autopsy + Foremost

**Description:**  
Interior photograph of the RX-8. Shows the cockpit, seats, and dashboard.

**Forensic Significance:**  
Corroborates the photo request email (E02). Provides visual evidence of the vehicle being in the suspect's possession.

**What to look for:**  
- EXIF metadata — GPS coordinates, camera make/model, timestamp
- Does the timestamp match the email chain?

---

### E09 — `car_exterior.jpeg` ⚠️ DELETED

**Type:** JPEG  
**Status:** Deleted — Recovered by Foremost  
**Location:** Foremost output → `jpg/` directory

**Description:**  
Exterior photo of the RX-8. Deleted along with `car_parts.jpeg` after the photos were apparently sent to the buyer.

**Forensic Significance:**  
Deletion after sending to a buyer suggests the suspect was trying to remove proof of the photos being taken. Foremost's JPEG carving recovered the file from unallocated space.

---

### E10 — `car_parts.jpeg` ⚠️ DELETED

**Type:** JPEG  
**Status:** Deleted — Recovered by Foremost  
**Location:** Foremost output → `jpg/` directory

**Description:**  
Photo of vehicle components or parts. Deleted alongside `car_exterior.jpeg`.

**Forensic Significance:**  
May show specific identifying parts or aftermarket modifications that could link the vehicle to its true owner.

---

### E11 — Browser History (Search Queries)

**Type:** Browser artifact  
**Status:** Active  
**Location in Autopsy:** Web History section

**Description:**  
Browsing history showing searches for RX-8 parts, car forums, and modification/repair tutorials.

**Forensic Significance:**  
Browser history is behavioral evidence. The pattern of searches — sustained interest in RX-8 mechanics and marketplace activity — is consistent with someone actively managing or preparing a vehicle, not just casually curious.

**What to look for:**  
- Timestamps relative to the email chain
- Specific search terms — do they reference parts mentioned in E06?
- Any searches for "how to remove VIN" or similar red flags

---

### E12 — Bookmarked Forum Page

**Type:** Browser artifact  
**Status:** Active  
**Location in Autopsy:** Web Bookmarks section

**Description:**  
A saved bookmark to an RX-8 community or parts forum.

**Forensic Significance:**  
Bookmarks indicate sustained, deliberate engagement — not a one-time visit. This strengthens the inference that the suspect had ongoing interest in the vehicle's specifications or the community around it.

---

### E13 — `buyer_contact.vcf`

**Type:** Contact card (vCard)  
**Status:** Active  
**Location in Autopsy:** File system → evidence directory

**Description:**  
A `.vcf` contact file containing buyer information including name, phone number, and/or email address.

**Forensic Significance:**  
Directly identifies a person connected to the transaction. This is a concrete link between the suspect and a named individual, corroborating the email evidence and giving investigators a person of interest to follow up with.

---

### E14 — `ecu_pill_log_021825.txt` ⚠️ DELETED

**Type:** Text log  
**Status:** Deleted — Recovered by Autopsy  
**Location in Autopsy:** Deleted Files section

**Description:**  
An ECU (Engine Control Unit) diagnostic log file dated February 18, 2025. Contains vehicle diagnostic data.

**Forensic Significance:**  
ECU logs can contain vehicle-specific identifiers. Deletion of this file suggests the suspect was aware it could reveal information traceable to the original vehicle owner or registration.

---

### E15 — Fake Syslog (Decoy)

**Type:** Text log  
**Status:** Active  
**Location in Autopsy:** File system

**Description:**  
A log file styled to look like a Linux system log, but containing content that does not match legitimate system activity for the disk image's operating environment.

**Forensic Significance:**  
This is a planted red herring. Investigators should note that **not every file on a disk is evidence**. Evaluating the authenticity of log files — checking timestamps, log format consistency, and whether logged events correspond to real system activity — is a critical forensic skill.

**How to identify it as fake:**  
- Timestamps may not align with system activity
- Log entries may reference processes or services not present on the system
- Format inconsistencies compared to legitimate system logs

---

## Investigation Tips

1. **Start with the emails.** They provide the narrative framework that makes all other artifacts meaningful.

2. **Cross-reference timestamps.** Photos, emails, and browser history should form a coherent timeline. Anomalies are worth investigating.

3. **Don't ignore the syslog immediately.** Evaluate it critically before dismissing it — that's the point.

4. **Check EXIF data** on the JPEG files. Metadata embedded in images can provide GPS location, camera information, and timestamps.

5. **Deleted ≠ destroyed.** All five deleted files are recoverable. Foremost is particularly effective for JPEG and DOCX carving.

---

*Evidence guide prepared as part of the Operation Blue Ghost educational forensics project.*
