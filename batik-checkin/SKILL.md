---
name: batik-checkin
description: Check in for Batik Air Malaysia flights using Playwright browser automation. Handles passport details, emergency contact, and downloads boarding pass.
argument-hint: [booking-reference]
disable-model-invocation: true
allowed-tools: mcp__playwright__*
---

# Batik Air Malaysia Web Check-in

Automate the check-in process for Batik Air Malaysia flights via BookCabin portal.

## Prerequisites
- Playwright MCP server must be running
- Valid booking reference
- Passport details for the passenger

## Required Information

Before starting, collect from the user:
1. **Booking Reference** (e.g., OFDGEC)
2. **Passenger Full Name** (as on passport)
3. **Passport Number**
4. **Date of Birth** (DD MMM YYYY format)
5. **Passport Expiry Date** (DD MMM YYYY format)
6. **Nationality/Country of Issue**
7. **Emergency Contact**: Name, Phone (with country code), Relationship

If $ARGUMENTS contains a booking reference, use it. Otherwise, ask the user.

## Check-in Workflow

### Step 1: Navigate to Check-in Portal
1. Navigate to `https://www.bookcabin.com/checkin`
2. Wait for page to load
3. Take a snapshot to see the page state
4. Enter booking reference in the textbox (look for ref "S1" or similar input field)
5. Click "Search" button

### Step 2: Select Passenger
1. Wait for passenger list to appear (take snapshot)
2. Ensure passenger checkbox is checked
3. Click "Check-in" button

### Step 3: Travel Document (APIS Form)
Fill in passport details - take a snapshot first to see form state:

**Date of Birth:**
1. Click on Date of birth field/button
2. Date picker opens - navigate to correct year first (click year dropdown/header)
3. Select correct year
4. Select correct month
5. Click the correct day

**Nationality:**
1. Click Nationality dropdown/button
2. Search or scroll to select the correct country

**Passport Number:**
1. Enter passport number in the textbox

**Country of Issue:**
1. Click Country of Issue dropdown/button
2. Select the issuing country (usually same as Nationality)

**Passport Expiry:**
1. Click Date of expiry field
2. Navigate to correct year (click year header to get year selection)
3. Select correct month
4. Click the correct day

5. Click "Continue to Emergency Contact" button

### Step 4: Emergency Contact Form
1. Take snapshot to see form
2. Select Title (Mr/Mrs/Ms) from dropdown
3. Enter First Name in textbox
4. Enter Last Name in textbox
5. Select Relationship from dropdown (Parent/Spouse/Sibling/Friend/Other)
6. Select Nationality from dropdown
7. For phone: click country code dropdown, select correct code (e.g., +60 Malaysia, +880 Bangladesh)
8. Enter phone number (without country code) in phone textbox
9. Click "Save" button

### Step 5: Confirm Check-in
1. Take snapshot - verify passenger is selected with checkbox
2. Click "Check-in" button

### Step 6: Seat Selection
Handle seat selection dialogs - take snapshots to see current state:
1. If "Choose Your Favorite Seat" dialog appears, click "Okay" or dismiss
2. If prompted about continuing without seat, click "Okay" or "Continue"
3. Look for "Skip Seat Selection" button and click it
4. If confirmation dialog appears, click "Continue Check-in" or similar

### Step 7: Dangerous Goods Declaration
1. Take snapshot to see the declaration page
2. Find and check the confirmation checkbox (usually agreeing to dangerous goods policy)
3. Click "I am accepting" or similar acceptance button

### Step 8: Boarding Pass
1. Wait for "Success Check-In" or success confirmation message
2. Take snapshot to confirm success
3. Click "View Boarding Pass" button
4. Wait for boarding pass to load/generate
5. Take a screenshot of the boarding pass for the user
6. If Download button is available, click it
7. Report to user:
   - Flight number
   - Date and time
   - Departure/Arrival airports
   - Seat assignment (if any)
   - Boarding time
   - Gate (if shown)

## Error Handling

- **Booking not found**: Ask user to verify reference number is correct
- **Session timeout**: BookCabin has ~5 minute timeout - restart from Step 1
- **Date picker issues**: Try clicking the year header first, or use arrow navigation
- **Country not found**: Try alternative country names (e.g., "Bangladesh" vs "BANGLADESH")
- **Element not found**: Take snapshot to see current page state and adapt

## Tips for Playwright Navigation

- Always use `browser_snapshot` before interacting to see current page state
- Use element refs from snapshot (like "S1", "B2") for accurate targeting
- For date pickers: typically need to select year first, then month, then day
- For dropdowns: click to open, then click the option
- Wait for page transitions with `browser_wait_for` if needed

## Output

On successful check-in, report:
```
✅ Check-in confirmed!
- Passenger: [Name]
- Flight: OD [XXX]
- Route: [DEP] → [ARR]
- Date: [Date]
- Seat: [Seat or "Not selected"]
- Boarding pass: [screenshot saved/downloaded]
```
