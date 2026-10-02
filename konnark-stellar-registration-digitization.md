# Digitizing the "Konnark Stellar — Registration Procedure" Form

This document does two things:

1. **Part A** — a complete field-by-field breakdown of the paper form (data types, validation rules, required/optional status) so you can rebuild it as a digital form in any tool (Google Forms, Typeform, Airtable, a custom web form, Excel, etc.)
2. **Part B** — a ready-to-use fillable digital template of the same form
3. **Part C** — a recommended end-to-end digitization workflow, tool options, and data-handling/security notes (this form collects PAN and bank details, which are sensitive)

---

## Part A — Field Inventory & Data Dictionary

| # | Field (as printed) | Suggested Field Name (key) | Data Type | Required? | Validation / Notes |
|---|---|---|---|---|---|
| 1 | Name of 1st Applicant | `applicant_1_name` | Text (CAPS) | Yes | Must match PAN card exactly; auto-uppercase; single space between First/Middle/Last |
| 2 | Name of 2nd Applicant | `applicant_2_name` | Text (CAPS) | Conditional | Required only if joint booking |
| 3 | Name of 3rd Applicant | `applicant_3_name` | Text (CAPS) | Conditional | Required only if 3+ applicants |
| 4 | Name of 4th Applicant | `applicant_4_name` | Text (CAPS) | Conditional | Required only if 4 applicants |
| 5 | Agreement Value | `agreement_value` | Currency (₹, number) | Yes | Numeric, 2 decimal places, no commas in stored value |
| 6 | Parking Value | `parking_value` | Currency + enum | Yes | Store as two sub-fields: `parking_amount` (number) and `parking_status` (enum: `Including` / `Excluding` / `Not Required`) |
| 7 | Flat No. Booked | `flat_no` | Text/Alphanumeric | Yes | e.g., "1204" or "B-1204" |
| 8 | Floor | `floor` | Text/Number | Yes | Allow "Ground", "1st", "12th", etc. |
| 9 | Address | `address` | Multi-line text | Yes | Full property/applicant address |
| 10 | PAN Card No. | `pan_number` | Text, fixed pattern | Yes | Regex: `^[A-Z]{5}[0-9]{4}[A-Z]{1}$` (10 chars, e.g., ABCDE1234F) — **sensitive/PII** |
| 11 | Contact No. | `contact_number` | Phone | Yes | 10-digit Indian mobile, regex `^[6-9]\d{9}$`; allow +91 prefix |
| 12 | Email ID | `email` | Email | Yes | Standard email regex validation |
| 13 | Bank Account Holder Name | `bank_account_holder_name` | Text (CAPS) | Yes | Should match applicant name or be flagged if different |
| 14 | Account Number | `bank_account_number` | Numeric text | Yes | 9–18 digits depending on bank — **sensitive/financial** |
| 15 | IFSC Code | `ifsc_code` | Text, fixed pattern | Yes | Regex: `^[A-Z]{4}0[A-Z0-9]{6}$` (11 chars) |
| 16 | Bank Name | `bank_name` | Text / Dropdown | Yes | Consider a dropdown of common Indian banks + "Other" |
| 17 | Branch | `bank_branch` | Text | Yes | Free text |
| — | Applicant count logic | `no_of_applicants` | Number (1–4) | Yes | Drives conditional display of Applicant 2/3/4 fields |
| — | Customer's Signature | `signature` | Image / e-signature | Yes | See "Digital Signature" note in Part C |
| — | Date filled | `date_filled` | Date | Auto | Auto-timestamp on submission |
| — | Header note | — | Static text | — | "Below Applicants should be present at the time of Registration" — display as instructional banner |
| — | Footer note | — | Static text | — | "Name should be filled according to PAN Card only, as per order by registration authority officer" — display as a warning/callout above the Name fields |

### Notes on ambiguous items from the photo
- The visible top strip ("1. DETAILS OF APPLICANTS / Primary Applicant / Please use CAPITAL LETTERS only...") appears to belong to a **separate, related form** (an Agreement form) sitting underneath — likely a booking/allotment agreement that shares the same applicant-detail convention. If you have that full page too, it should be digitized as a linked "Applicant KYC" section, since both forms need the same CAPS-only, PAN-matched name rule.
- "Parking Value (Including/Excluding/Not Required Parking)" is a compound field — I've split it into an amount + status enum above for clean data storage, but the digital form should present it as originally: one line with a radio button and an amount box.

---

## Part B — Fillable Digital Template

> Copy this section into Google Docs / Notion / a fillable PDF / your form tool. Boxes `[ ]` and blanks `_____` are placeholders for input fields.

```
DETAILS FOR REGISTRATION PROCEDURE OF KONNARK STELLAR

Note: Below Applicants should be present at the time of Registration.
(Details to be filled in Agreement)

Number of Applicants:      [ 1 ] [ 2 ] [ 3 ] [ 4 ]   (select one)

Name of 1st Applicant   :  ______________________________
Name of 2nd Applicant   :  ______________________________
Name of 3rd Applicant   :  ______________________________
Name of 4th Applicant   :  ______________________________
   (Use CAPITAL LETTERS only. One space between First, Middle & Last name.
    Name must match PAN Card exactly.)

Agreement Value          :  ₹ ___________________________

Parking Value             :  ₹ ___________________________
   Status:  ( ) Including   ( ) Excluding   ( ) Not Required

Flat No. Booked           :  ______________________________
Floor                     :  ______________________________
Address                   :  ______________________________
                              ______________________________

PAN Card No.              :  ______________________________
Contact No.                :  ______________________________
Email ID                   :  ______________________________

Bank Details
  Account Holder Name    :  ______________________________
  Account Number          :  ______________________________
  IFSC Code                :  ______________________________
  Bank Name                :  ______________________________
  Branch                   :  ______________________________

NOTE: Name should be filled according to PAN Card only,
as per order by Registration Authority Officer.

Customer's Signature      :  ______________________________
Date                       :  ______________________________
```

---

## Part C — How to Actually Digitize This (Recommended Workflow)

### Step 1 — Choose your platform based on use case

| If you need... | Use |
|---|---|
| A simple shareable link customers fill on their phone | **Google Forms** or **Microsoft Forms** |
| E-signature + legal-grade submission (this form has a signature line) | **Zoho Forms**, **DocuSign**, or **Adobe Acrobat Sign** — all support typed/drawn signatures |
| A form that also builds you a searchable database/CRM of applicants | **Airtable Forms**, **Notion + form view**, or **Zoho Creator** |
| Offline-capable data entry by site staff | **Google Sheets + Google Forms** (auto-syncs) or **KoboToolbox** (works offline, syncs later) |
| Full custom branding / embedding on your builder's website | A custom web form (e.g., built with **Claude in Artifacts**, Typeform, or a React app) posting into a spreadsheet/database |

For a real-estate builder collecting PAN + bank details from many customers, **Zoho Forms or Airtable** are usually the best balance of ease + data structure + security.

### Step 2 — Build the field logic
1. Add a "Number of Applicants" selector (1–4) at the top.
2. Use **conditional logic** so Applicant 2/3/4 name fields only appear if selected — mirrors the paper form's fixed 4-line layout without cluttering the digital one.
3. Apply the validation patterns from the Part A table (PAN regex, IFSC regex, 10-digit mobile) at the field level — this is the biggest upgrade over paper, since it stops bad data (typo'd PAN, wrong IFSC) at entry instead of at the registrar's office.
4. Auto-uppercase the Name fields (most form tools have a "transform to uppercase" setting) to preserve the paper form's CAPITAL LETTERS requirement.

### Step 3 — Digital signature
Since the original has a physical "Customer's Signature" line, decide between:
- **Draw-to-sign pad** (touchscreen signature capture) — supported natively in Zoho Forms, DocuSign, Adobe Sign.
- **E-sign via OTP/Aadhaar** (legally stronger in India, useful since this is a property registration document) — via Adobe Sign, Zoho Sign, or Leegality/SignDesk (India-specific e-sign APIs commonly used for real-estate paperwork).
- If the signature is purely for internal confirmation (not the legal registration itself, which will still happen at the Sub-Registrar's office), a simple typed-name + checkbox "I confirm the above details are accurate" may be sufficient — confirm with your legal/compliance team.

### Step 4 — Data storage & security (important — this form collects sensitive data)
This form collects **PAN numbers and full bank account details**, which are financial/sensitive personal data. Recommendations:
- Store data in a platform with encryption at rest and access controls (Airtable, Zoho, Google Workspace with restricted sharing — not a public spreadsheet link).
- Restrict who in your organization can view the PAN/Account Number columns (most tools support field-level or view-level permissions).
- Do **not** email raw form responses containing PAN/bank details as unprotected attachments; use the platform's built-in secure notification instead.
- Add a consent line to the digital form, e.g.: *"By submitting, I consent to [Company] collecting my PAN and bank details solely for the purpose of registration and disbursement-related processing."*
- Set a data retention/deletion policy in line with your company's privacy policy.

### Step 5 — Output / integration
- Configure the form to auto-generate a PDF summary per submission (Zoho Forms and DocuSign both do this) that mirrors the original paper layout — useful for printing a copy for the registrar or applicant's records.
- Sync submissions into a spreadsheet or CRM so your registration/legal team has one running list of all Konnark Stellar bookings instead of a stack of paper forms.
- Optionally connect it to a notification (email/WhatsApp) so your registration coordinator is alerted the moment a form is submitted.

### Step 6 — Pilot & roll out
1. Digitize and test the form with 2–3 real (or dummy) entries.
2. Verify the PAN/IFSC/mobile validations reject bad input correctly.
3. Have someone from your registration team review the generated PDF against the original paper layout for approval.
4. Share the form link with customers, or use it on a tablet at your sales office for staff-assisted entry.

---

*If you'd like, I can also build this as an actual interactive fillable form (HTML/web page) right now, or set it up as a Google Form structure you can copy-paste field by field — just let me know which platform you want to end up on.*
