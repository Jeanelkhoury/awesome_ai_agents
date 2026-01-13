# Mobile Screenshot to Contacts Workflow (OCR → Sheets → Contacts)

Use this workflow to capture screenshots on your phone, convert the image to text, and automatically log any detected phone numbers or addresses to a Google Sheet and your phone contacts.

## What this workflow does

- Captures a screenshot or lets you pick a recent screenshot.
- Extracts text using on-device OCR.
- Detects phone numbers and addresses.
- Logs results to a Google Sheet.
- Creates/updates a contact on your phone.

## Recommended setup (iOS Shortcuts)

### Prerequisites

- An iPhone with the Shortcuts app.
- A Google account and a Google Sheet to store results.
- A simple sheet with columns like:
  - `Timestamp`
  - `Name`
  - `Phone`
  - `Address`
  - `Notes`
  - `Source` (optional)

### Shortcut steps

1. **Trigger**
   - Use `Share Sheet` → “Run Shortcut” from a screenshot in Photos, **or**
   - Use “Take Screenshot” / “Get Latest Screenshots” if you want a one-tap shortcut.

2. **OCR the screenshot**
   - Action: **Extract Text from Image** (Shortcuts → Image).
   - Save the output to a variable like `OCR Text`.

3. **Find phone numbers**
   - Action: **Match Text** with a phone regex.
   - Example regex (tolerant):
     ```
     (\+?\d{1,3}[\s.-]?)?(\(?\d{2,4}\)?[\s.-]?)?\d{3,4}[\s.-]?\d{4}
     ```
   - Store matches in `Phone Matches`.

4. **Find addresses (optional but recommended)**
   - Action: **Match Text** with a simple address regex.
   - Example regex (US-centric):
     ```
     \d+\s+\w+(\s+\w+)*\s+(St|Street|Ave|Avenue|Rd|Road|Blvd|Boulevard|Ln|Lane|Dr|Drive)\.?
     ```
   - Store matches in `Address Matches`.

5. **Check for a match**
   - Use **If**:
     - If `Phone Matches` is not empty **OR** `Address Matches` is not empty, continue.
     - Otherwise, show a notification and stop.

6. **Normalize / confirm**
   - Use **Choose from List** (if multiple matches) and **Ask for Input** for the contact name.

7. **Save to Contacts**
   - Action: **Add New Contact** (or **Find Contacts** → **Update Contact**).
   - Map name, phone, and address.

8. **Log to Google Sheets**
   - Action: **Add Row to Google Sheets**.
   - Map timestamp, name, phone, address, and notes.

9. **Optional: add metadata**
   - Add a `Source` field like “Screenshot OCR” and a link to the image in iCloud/Photos if desired.

## Android alternative (Tasker + OCR + Google Sheets)

If you prefer Android, you can replicate the same flow using:

- **Tasker** (automation)
- **Google Lens / ML Kit OCR** (text extraction)
- **Google Sheets API** (append rows)
- **Contacts Provider** (create/update contacts)

This is more advanced but supports full automation on Android.

## Tips

- Clean up OCR text by trimming whitespace and removing line breaks before matching.
- If you frequently capture business cards, add a **Ask for Input** step to confirm name and company.
- Add a “Confidence” field if your OCR source provides confidence scores.

## Example Google Sheet row

| Timestamp           | Name        | Phone        | Address                 | Notes           | Source           |
|---------------------|-------------|--------------|-------------------------|-----------------|------------------|
| 2025-01-05 10:32 AM | Jamie Chen  | +1 415-555-1234 | 123 Main St, SF, CA | From screenshot | Screenshot OCR |
