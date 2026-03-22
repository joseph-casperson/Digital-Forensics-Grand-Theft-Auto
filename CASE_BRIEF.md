# CASE BRIEF — Operation Blue Ghost

**Case Name:** Operation Blue Ghost  
**Analyst:** Joseph Casperson  
**Image Type:** Virtual Machine Disk (VMDK/VDI)  
**Analysis Tools:** Autopsy, Foremost  
**Classification:** Simulated / Educational  

---

## Executive Summary

This case involves the forensic analysis of a disk image taken from a suspect's computer in connection with the alleged sale of a stolen 2004 Mazda RX-8. Digital evidence recovered from the disk establishes a clear chain of communication between the suspect and prospective buyers, corroborated by vehicle photographs, falsified documentation, and browser activity consistent with preparing a stolen vehicle for resale.

Five files were deleted from the disk prior to seizure, indicating an attempt to conceal evidence. All five were successfully recovered using Autopsy and Foremost.

---

## Timeline of Events (Reconstructed)

Based on recovered artifacts, the following sequence of events has been reconstructed:

1. **Suspect acquires vehicle** — No direct evidence of acquisition; investigation begins after possession is established
2. **Vehicle preparation** — Browser history shows searches for RX-8 parts, forums, and modification guides, consistent with preparing the car for sale
3. **Documentation fabricated** — A bill of sale, maintenance record, and parts list were created to legitimize the transaction
4. **Photos taken** — Exterior, interior, and engine bay photos were captured and stored on the device
5. **Buyer contact initiated** — Email correspondence begins with at least two prospective buyers; price negotiation and photo exchange documented
6. **Meetup planned** — Later emails contain logistics for an in-person handoff
7. **Evidence destruction attempted** — Five files deleted from the disk, including one email thread, two images, a maintenance log, and an ECU log
8. **Machine seized** — Disk image acquired; deleted files recovered through forensic carving

---

## Evidence Analysis

### Email Evidence

Three active email files and one recovered deleted email were found in the Thunderbird mail client and as `.eml` files on disk.

The emails collectively show:
- Initial contact from a buyer inquiring about the vehicle
- The suspect responding with photos and a description
- Price negotiation across multiple messages
- A final thread discussing a handoff location and time
- The deleted email (`Yo,_did_you….eml`) appears to contain a message the suspect attempted to suppress — its recovery by Autopsy demonstrates that deletion does not constitute destruction in a forensic context

The emails are the backbone of this case. They establish intent, identify at least one buyer, and provide a timeline that anchors the other artifacts.

### Document Evidence

Three documents were found, one of which was deleted:

**`rx8_bill_of_sale.pdf`** — A fraudulent bill of sale for the vehicle. The document contains seller and buyer information, a vehicle description, and a price. Its existence alongside the email negotiations strongly implies the transaction was in progress.

**`rx8_parts_list.docx`** — A list of aftermarket and replacement parts associated with the vehicle. This suggests either that parts were sourced for the car or that the suspect was tracking components to be included in the sale.

**`rx8_maintenance_history.docx` (Deleted, Recovered)** — A maintenance record for the vehicle. Its deletion is notable — a legitimate seller would typically want to preserve maintenance history as a selling point. Deletion suggests the record may have contained information identifying the car's true history or owner.

### Image Evidence

Three JPEG photos of the vehicle were found; two were deleted:

- **`car_interior.jpeg`** (Active) — Interior shot of the RX-8, consistent with photos described in the email exchange
- **`car_exterior.jpeg`** (Deleted, Recovered) — Exterior photo; its deletion alongside the maintenance record suggests a pattern of removing identifying images
- **`car_parts.jpeg`** (Deleted, Recovered) — Photo of vehicle components, likely used in buyer communications before deletion

All three images were recovered by Foremost through JPEG signature carving, confirming their presence on disk despite deletion.

### Browser Evidence

Browser history extracted by Autopsy revealed a pattern of searches consistent with the scenario:

- Searches for RX-8 parts suppliers and forums
- Searches for modification and repair tutorials
- A bookmarked RX-8 community forum page, indicating sustained interest rather than a one-time query

This browser activity corroborates the narrative: the suspect was actively researching the vehicle, likely to prepare it for sale or address mechanical issues that might affect its value.

### Additional Artifacts

**Buyer Contact Card (`buyer_contact.vcf`)** — A `.vcf` contact file containing buyer information. This directly links a named individual to the transaction and supports the email evidence.

**`ecu_pill_log_021825.txt` (Deleted, Recovered)** — An ECU (Engine Control Unit) diagnostic log file. Its deletion is suspicious; ECU logs can contain vehicle identification data that could be traced back to the original owner or VIN.

**Fake Syslog (Active)** — A syslog file was present on the disk but does not correspond to any legitimate system processes in the image. This artifact appears to be a deliberate decoy, likely planted to distract investigators or pad the file system. Careful evaluation of log contents and timestamps revealed its inauthentic nature.

---

## Conclusions

The evidence recovered from this disk image tells a coherent and well-supported story. The email chain establishes communication with buyers, the documents provide fraudulent paperwork for the transaction, the photos corroborate what was described in the emails, and the browser history shows sustained, relevant research activity.

The deletion of five specific files — particularly the maintenance record, the ECU log, and a buyer email — demonstrates awareness of forensic risk and an attempt to limit the investigator's view. The fact that all five were recovered illustrates a fundamental principle of digital forensics: **deletion does not equal destruction**.

The fake syslog introduces appropriate complexity, requiring the investigator to evaluate artifacts critically rather than accepting all recovered files at face value.

Taken together, the evidence is sufficient to establish that the suspect was in possession of a stolen vehicle, was actively preparing to sell it, had made contact with buyers, and attempted to conceal the activity after the fact.

---

## Tools Summary

| Tool | Role in Analysis | Evidence Found |
|------|-----------------|----------------|
| Autopsy | Primary analysis platform — file system, email, browser artifacts | 12+ artifacts including deleted email |
| Foremost | File carving — JPEG and DOCX recovery | 4 deleted files recovered by signature |

---

*This case brief is part of the Operation Blue Ghost educational forensics project. All persons, transactions, and vehicles described are fictional.*
