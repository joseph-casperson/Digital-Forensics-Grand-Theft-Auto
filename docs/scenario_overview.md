# Scenario Planning Document — Operation Blue Ghost

This document outlines the planning decisions made during the construction of the disk image, including the narrative design, evidence selection, and anti-forensic techniques applied.

---

## Narrative Design

### Core Scenario

A suspect is attempting to sell a stolen 2004 Mazda RX-8 (blue) through informal online channels. The suspect uses their personal computer to:

- Correspond with buyers via email (Thunderbird)
- Share photographs of the vehicle
- Create fraudulent documentation to legitimize the sale
- Research the vehicle and related parts online

Before the machine is seized, the suspect deletes several files in an attempt to reduce their exposure.

### Why This Scenario

The goal was to create a believable, self-contained case that would:

1. Tell a clear story reconstructable from evidence alone
2. Include a realistic mix of evidence types (email, docs, images, browser, logs)
3. Feature anti-forensic behavior (file deletion) that tools can overcome
4. Include a red herring (fake syslog) to add investigative complexity
5. Be engaging for a student investigator — a GTA-style car theft is more memorable than generic corporate fraud

The RX-8 was chosen because it's a real car with a real community, making the scenario feel grounded. Search results, forum pages, and parts lists for an RX-8 are authentic and findable, which adds realism.

---

## Evidence Planning

### Requirements vs. Implementation

| Requirement | Implementation |
|-------------|---------------|
| Min. 3 documents | Bill of sale (PDF), parts list (DOCX), maintenance history (DOCX) |
| Min. 3 photos | Exterior, interior, engine/parts JPEGs |
| Min. 3 emails | Negotiation, photo request, meetup planning + 1 deleted |
| Min. 2 other types | Browser history + bookmarks, buyer .vcf contact card, syslog |
| Deleted files | 5 files deleted: 1 email, 2 images, 1 DOCX, 1 TXT log |

### Evidence Interconnection Map

The following artifacts reference or corroborate each other:

```
Email (negotiation) ──────────────────── Bill of Sale
        │                                     │
        ├── Photo request email               Parts List
        │        │                            │
        │        └── car_exterior.jpeg ───── Maintenance History
        │            car_interior.jpeg
        │            car_parts.jpeg
        │
        └── Meetup email ──────────────── Buyer Contact (.vcf)

Browser History ──────────────────────── Bookmarked Forum
(corroborates all above through search pattern)

ECU Log ──────────────────────────────── (links to vehicle identity)
Fake Syslog ──────────────────────────── (decoy, no narrative link)
```

### Deletion Strategy

Files were deleted to simulate a suspect who:
- Is aware that photos sent to buyers should be removed after sending
- Knows that certain logs could identify the vehicle
- Tries to suppress at least one email thread
- But does NOT comprehensively wipe the disk (which would be more effective but also more obvious)

This reflects realistic behavior: most people delete files but don't use secure deletion tools.

**Deleted files:**
- `Yo,_did_you….eml` — Possibly incriminating email content
- `car_exterior.jpeg` — Identifying photo of vehicle
- `car_parts.jpeg` — Identifying photo of vehicle components
- `rx8_maintenance_history.docx` — May contain original owner information
- `ecu_pill_log_021825.txt` — ECU data potentially traceable to the vehicle

**Retained files (intentional):**
- Bill of sale — The suspect may not have realized this is incriminating on its own
- Parts list — Not seen as incriminating
- Interior photo — Forgotten
- Active emails — The suspect focused on deleting specific threads, not all email

---

## Red Herring Design

The fake syslog (`fake_syslog.txt` or similar) was designed to:

1. Look superficially like a real Linux system log
2. Contain entries that don't match the actual system environment
3. Require the investigator to actively evaluate it, not just accept it
4. Be identifiable as fake through timestamp inconsistencies or process name mismatches

**Educational purpose:** Real investigations include irrelevant or misleading files. Forensic analysts must evaluate whether recovered artifacts are actually meaningful rather than treating everything recovered as evidence.

---

## Tool Validation

### Autopsy — Expected Finds
- All 3 active emails ✓
- Deleted email recovery ✓
- Bill of sale, parts list ✓
- Browser history and bookmarks ✓
- Buyer contact card ✓
- Deleted ECU log ✓
- All 3 photos ✓
- Fake syslog (as non-evidence) ✓

### Foremost — Expected Finds
- `car_exterior.jpeg` (deleted) ✓
- `car_parts.jpeg` (deleted) ✓
- `rx8_maintenance_history.docx` (deleted) ✓
- `rx8_bill_of_sale.pdf` (active, also carved) ✓
- Other JPEGs and DOCX files (active files also carved) ✓

---

## Lessons Learned

**What worked well:**
- Using Thunderbird as the email client made `.eml` artifact recovery straightforward
- JPEG carving with Foremost worked reliably even after deletion
- The narrative made the investigation more engaging and easier to write about

**What could be improved in a future iteration:**
- Adding RAM artifacts (volatile data like running processes or network connections)
- Including Windows Registry artifacts (prefetch, shellbags, MRU lists)
- Adding encrypted or password-protected files as an additional challenge layer
- Simulating multiple user accounts on the machine

---

*Scenario planning document prepared as part of the Operation Blue Ghost educational forensics project.*
