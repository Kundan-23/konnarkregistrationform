# Konnark Stellar — Digital Registration Form

A single-page, mobile-friendly web form that replaces the paper **"Details for Registration Procedure of Konnark Stellar"** form. No server, no build tools, no npm.

---

## Files

| File | Purpose |
|---|---|
| `konnark-stellar-form.html` | The form — open this in any browser |
| `README.md` | This file |
| `TEST_CHECKLIST.md` | Manual test cases |

---

## Quick Start (no config)

1. Open `konnark-stellar-form.html` in Chrome or Safari.
2. Fill in and submit — the PDF is generated and downloaded locally.
3. Email will **not** be sent until you complete the EmailJS setup below.

> Works offline. Internet is only needed for CDN libraries on first load and for email sending.

---

## EmailJS Setup (for email + PDF attachment)

> Free tier: 200 emails/month. No server required.

### Step 1 — Create account
Go to https://www.emailjs.com/ and sign up (free).

### Step 2 — Add an email service
- Dashboard > Email Services > Add New Service
- Choose Gmail (or Outlook/other)
- Authorize with your admin Gmail account
- Note the Service ID (e.g. service_abc123)

### Step 3 — Create an email template
- Dashboard > Email Templates > Create New Template
- Set Subject: Konnark Stellar Registration — Ref: {{ref_id}}
- Set To Email: {{to_email}} (customer) and CC: {{admin_email}}
- Body: include variables {{to_name}}, {{ref_id}}, {{applicant_names}}, {{flat_no}}, {{floor}}, {{agreement_value}}, {{parking_value}}, {{submitted_at}}
- In the Files/Attachments section, add a field called pdf_attachment (type: Base64 file)
- Note the Template ID (e.g. template_xyz789)

### Step 4 — Get your Public Key
- Dashboard > Account > General > copy Public Key

### Step 5 — Paste into the form
Open konnark-stellar-form.html in a text editor. Find the CONFIG block near top of <script>:

  const CONFIG = {
    EMAILJS_PUBLIC_KEY:  'YOUR_EMAILJS_PUBLIC_KEY',
    EMAILJS_SERVICE_ID:  'YOUR_SERVICE_ID',
    EMAILJS_TEMPLATE_ID: 'YOUR_TEMPLATE_ID',
    ADMIN_EMAIL:         'admin@konnarkstellar.com',
    ...
  };

Replace the placeholder values. Save the file.

---

## Hosting on GitHub Pages (free, HTTPS)

1. Create a GitHub repo (e.g. konnark-stellar-form)
2. Upload konnark-stellar-form.html as index.html
3. Go to repo Settings > Pages > Source: main branch / root
4. Your form is live at: https://<username>.github.io/konnark-stellar-form/

NOTE: HTTPS is required for secure handling of PAN and bank details. GitHub Pages provides HTTPS automatically.

---

## Google Sheets Integration (optional)

### Step 1 — Create a Google Sheet
Add these column headers in row 1:
ref_id | submitted_at | no_of_applicants | applicant_1_name | applicant_2_name |
applicant_3_name | applicant_4_name | agreement_value | parking_amount |
parking_status | flat_no | floor | address | pan_number | contact_number |
email | bank_account_holder_name | bank_account_number | ifsc_code | bank_name | bank_branch

SECURITY: Restrict sharing. Do NOT share the sheet publicly. Use View > Protected Ranges
to lock the PAN and account number columns so only designated admins can see them.

### Step 2 — Create Apps Script
In the sheet: Extensions > Apps Script > paste:

  function doPost(e) {
    const data = JSON.parse(e.postData.contents);
    const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
    sheet.appendRow([
      data.ref_id, data.submitted_at, data.no_of_applicants,
      data.applicant_1_name, data.applicant_2_name, data.applicant_3_name, data.applicant_4_name,
      data.agreement_value, data.parking_amount, data.parking_status,
      data.flat_no, data.floor, data.address,
      data.pan_number, data.contact_number, data.email,
      data.bank_account_holder_name, data.bank_account_number,
      data.ifsc_code, data.bank_name, data.bank_branch
    ]);
    return ContentService.createTextOutput('OK');
  }

### Step 3 — Deploy
- Deploy > New deployment > Web app
- Execute as: Me | Access: Anyone > Deploy
- Copy the Web App URL

### Step 4 — Paste URL in form
In the CONFIG block:
  GOOGLE_SCRIPT_URL: 'https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec',

---

## Where the Data Goes

| Data | Where |
|---|---|
| PDF | Generated locally in browser, downloaded by user and/or emailed |
| Full form data | EmailJS > your Gmail/Outlook inbox (over HTTPS) |
| Spreadsheet row | Google Sheets via Apps Script (if configured) |
| PAN / Account | Never logged to console; masked on screen & in PDF (last 4 chars shown) |

---

## Security Notes

- PAN and bank account numbers are never logged to the console in production
- Never appear in URL parameters
- Masked in confirmation screen and PDF (last 4 characters only)
- All transmission over HTTPS
- The form warns users if accessed via HTTP
- Restrict Google Sheet access to authorized team members only

---

## Browser Compatibility

| Browser | Status |
|---|---|
| Chrome 90+ | Fully supported |
| Safari 15+ (iOS) | Fully supported |
| Firefox 88+ | Fully supported |
| Edge 90+ | Fully supported |
| IE 11 | Not supported |
