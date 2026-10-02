# Konnark Stellar — Test Checklist

Run through every item below after setup. Mark PASS or FAIL.

---

## 1. PAN Validation

| Input | Expected | Result |
|---|---|---|
| ABCDE1234F | PASS (valid) | |
| abcde1234f | PASS (auto-upcased to ABCDE1234F, valid) | |
| ABCDE12345 | FAIL — last char must be letter | |
| 12345ABCDE | FAIL — must start with 5 letters | |
| ABCD1234F  | FAIL — only 9 chars | |
| ABCDE1234FF | FAIL — 11 chars | |
| ABC D1234F | FAIL — space not allowed | |
| (empty)   | FAIL — required field | |

---

## 2. IFSC Validation

| Input | Expected | Result |
|---|---|---|
| SBIN0001234 | PASS (valid SBI IFSC) | |
| HDFC0000001 | PASS | |
| sbin0001234 | PASS (auto-upcased) | |
| SBIN1001234 | FAIL — 5th char must be 0 | |
| SBINO001234 | FAIL — 5th char must be digit 0, not letter O | |
| SBI0001234  | FAIL — only 10 chars (must be 11) | |
| SBIN0001234X | FAIL — 12 chars | |
| (empty)     | FAIL — required field | |

---

## 3. Mobile Number Validation

| Input | Expected | Result |
|---|---|---|
| 9876543210 | PASS | |
| +919876543210 | PASS (strips +91) | |
| 919876543210 | PASS (strips 91 prefix) | |
| 6000000000 | PASS (starts with 6) | |
| 5876543210 | FAIL — must start with 6-9 | |
| 98765432   | FAIL — only 8 digits | |
| 98765432100 | FAIL — 11 digits | |
| 0876543210 | FAIL — starts with 0 | |
| +1 9876543210 | FAIL — non-Indian prefix | |
| (empty)    | FAIL — required field | |

---

## 4. Account Number Validation

| Input | Expected | Result |
|---|---|---|
| 123456789 | PASS (9 digits — minimum) | |
| 123456789012345678 | PASS (18 digits — maximum) | |
| 12345678 | FAIL — 8 digits (too short) | |
| 1234567890123456789 | FAIL — 19 digits (too long) | |
| 12345678A | FAIL — contains letter | |
| 123 456 789 | FAIL — contains spaces | |
| (empty) | FAIL — required field | |

---

## 5. Applicant Count Conditional Logic

| Scenario | Expected | Result |
|---|---|---|
| Select "1" | Only Applicant 1 name shown; 2/3/4 hidden | |
| Select "2" | Applicant 1 & 2 shown; 3 & 4 hidden | |
| Select "3" | Applicant 1, 2 & 3 shown; 4 hidden | |
| Select "4" | All four applicant names shown | |
| Switch from "4" back to "2" | Fields 3 & 4 hide; values cleared; no validation error | |
| Submit with 2 applicants, field 2 empty | FAIL — Applicant 2 name required | |

---

## 6. Parking Status Logic

| Scenario | Expected | Result |
|---|---|---|
| Select "Not Required" | Amount field disabled, set to 0 | |
| Select "Including" | Amount field enabled and required | |
| Select "Excluding" | Amount field enabled and required | |
| "Including" with empty amount | FAIL — required | |
| "Not Required" with no amount | PASS | |

---

## 7. Agreement Value Validation

| Input | Expected | Result |
|---|---|---|
| 8500000.00 | PASS | |
| 0.01 | PASS (minimum positive) | |
| 0 | FAIL — must be > 0 | |
| -100 | FAIL — negative not allowed | |
| abc | FAIL — not a number | |
| (empty) | FAIL — required field | |

---

## 8. Email Validation

| Input | Expected | Result |
|---|---|---|
| user@example.com | PASS | |
| user+tag@domain.co.in | PASS | |
| userATdomain.com | FAIL — no @ symbol | |
| user@.com | FAIL — invalid domain | |
| @domain.com | FAIL — no local part | |
| (empty) | FAIL — required field | |

---

## 9. Name Field Behaviour

| Action | Expected | Result |
|---|---|---|
| Type "rahul sharma" | Auto-converts to "RAHUL SHARMA" | |
| Type "RAHUL  SHARMA" (double space) | Collapses to "RAHUL SHARMA" | |
| Type "RAHUL123" | Validation fails — digits not allowed in name | |
| Leave blank | FAIL — required | |

---

## 10. Signature Pad

| Scenario | Expected | Result |
|---|---|---|
| Submit without signing | FAIL — error shown "Please provide your signature" | |
| Draw and then click Clear, submit | FAIL — signature required again | |
| Draw a signature, submit | PASS — signature appears in PDF | |
| Test on mobile (touch) | Signature pad responds to finger draw | |

---

## 11. Consent Checkbox

| Scenario | Expected | Result |
|---|---|---|
| Submit without checking | FAIL — error shown | |
| Check then submit (all other fields valid) | PASS | |

---

## 12. Submit Button State

| Scenario | Expected | Result |
|---|---|---|
| Form first loaded | Submit button DISABLED | |
| All fields valid | Submit button ENABLED | |
| One required field goes invalid | Submit button DISABLED again | |

---

## 13. PDF Output

| Check | Expected | Result |
|---|---|---|
| PDF downloads on submit | Yes | |
| PDF header shows "KONNARK STELLAR" | Yes | |
| Ref ID appears in PDF header | Yes (e.g. KS-20260928-4821) | |
| PAN in PDF is masked | Shows ██████XXXF (last 4 only) | |
| Account number in PDF is masked | Shows ••••••XXXX (last 4 only) | |
| Signature image appears in PDF | Yes | |
| Date and timestamp at bottom | Yes | |
| Applicant 2/3/4 appear only if selected | Yes | |
| Parking "Not Required" shows correctly | Yes | |

---

## 14. Confirmation Screen

| Check | Expected | Result |
|---|---|---|
| Reference ID shown (KS-YYYYMMDD-XXXX) | Yes | |
| PAN masked (██████XXXX) | Yes | |
| Account number masked (••••XXXX) | Yes | |
| "Download PDF" button works | Downloads PDF with correct ref ID filename | |
| "New Entry" button resets form | Form clears, all fields blank | |

---

## 15. Security Checks

| Check | Expected | Result |
|---|---|---|
| Open browser DevTools Console → submit form | NO PAN or account number printed to console | |
| Check network requests in DevTools | PAN/bank data only in EmailJS HTTPS POST body (not URL) | |
| Open via http:// URL (non-localhost) | Warning toast shown: "Please use HTTPS" | |

---

## 16. Mobile / Responsive

| Check | Expected | Result |
|---|---|---|
| Open on iPhone (Safari) — portrait | Form readable, no horizontal scroll | |
| All input fields | Tap targets >= 44px height | |
| Signature pad on iPhone | Works with finger | |
| Radio buttons (applicant count, parking) | Large enough to tap | |
| Submit button | Full-width, easy to tap | |
