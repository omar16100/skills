---
name: setup-google-analytics
description: Set up Google Analytics 4 for a website using Playwright browser automation. Creates GA4 property, gets measurement ID, and adds tracking code to HTML.
argument-hint: [website-url]
disable-model-invocation: true
allowed-tools: mcp__playwright__*, Read, Edit, Write, Grep, Glob
---

# Google Analytics 4 Setup

Automate GA4 setup for any website via Playwright browser automation.

## Prerequisites

- Playwright MCP server must be running
- Google account with Analytics access (user must be logged in via Playwright browser)
- Website codebase accessible for adding tracking code

## Required Information

Collect from user if not provided:
1. **Website URL** (e.g., example.com) - use $ARGUMENTS if provided
2. **Property Name** (defaults to domain name)
3. **Industry Category** (Computers & Electronics, Business, etc.)
4. **Business Size** (Small/Medium/Large/Very Large)

## Setup Workflow

### Step 1: Check Login Status

1. Navigate to `https://analytics.google.com`
2. Take snapshot to verify login
3. If sign-in page appears, inform user to log in via Playwright browser window
4. Wait for user confirmation before proceeding

### Step 2: Navigate to Admin

1. Once logged in, click "Admin" in left sidebar
2. Take snapshot to confirm Admin page loaded

### Step 3: Create New Property

1. Click "Create" button in Admin
2. Select "Property" from dropdown menu
3. Take snapshot to see property creation form

### Step 4: Property Details

1. Enter property name in "Property name" textbox
2. Select timezone if needed (defaults to US/Los Angeles)
3. Select currency if needed (defaults to USD)
4. Click "Next" button

### Step 5: Business Details

1. Click industry category dropdown
2. Select appropriate category (e.g., "Computers & Electronics")
3. Select business size radio button (typically "Small - 1 to 10 employees")
4. Click "Next" button

### Step 6: Business Objectives

1. Take snapshot to see objectives
2. Check "Understand web and/or app traffic" checkbox (or most relevant option)
3. Click "Create a property" button

### Step 7: Set Up Web Data Stream

1. On "Data collection" page, click "Web" platform button
2. Dialog opens for web stream setup
3. Enter website URL in "Website URL" textbox (without https://)
4. Enter stream name in "Stream name" textbox (e.g., "Website Name Website")
5. Enhanced measurement is enabled by default (leave on)
6. Click "Create & continue" button

### Step 8: Get Measurement ID

1. After stream creation, a dialog shows the Google tag code
2. **CRITICAL**: Extract the measurement ID (format: G-XXXXXXXXXX)
3. The ID appears in the script snippet and in stream details
4. Take screenshot for reference
5. Close the tag instructions dialog

### Step 9: Add Tracking Code to Website

1. Search for `index.html` in project: `Glob("**/index.html")`
2. Read the HTML file
3. Add GA4 tracking code immediately after `<head>` tag:

```html
<!-- Google tag (gtag.js) -->
<script async src="https://www.googletagmanager.com/gtag/js?id=G-XXXXXXXXXX"></script>
<script>
  window.dataLayer = window.dataLayer || [];
  function gtag(){dataLayer.push(arguments);}
  gtag('js', new Date());
  gtag('config', 'G-XXXXXXXXXX');
</script>
```

Replace `G-XXXXXXXXXX` with actual measurement ID.

### Step 10: Add Event Tracking (Optional)

If the website has JavaScript files with button click handlers:

1. Search for existing analytics placeholders: `Grep("analytics", path=".")`
2. Add gtag event calls for important actions:

```javascript
// Example: Track button clicks
if (typeof gtag !== 'undefined') {
    gtag('event', 'button_click', {
        'event_category': 'engagement',
        'event_label': buttonText
    });
}
```

## Error Handling

- **Not logged in**: Inform user to log in via Playwright browser, then resume
- **Property limit reached**: Check account limits, may need different account
- **Element not found**: Take snapshot to see current page state and adapt
- **Session timeout**: Restart from Step 1

## Tips for Playwright Navigation

- Always use `browser_snapshot` before interacting to see current state
- Use element refs from snapshot (like "e123", "f10e5") for accurate targeting
- For dropdowns: click to open, then click the option
- For date pickers: select year first, then month, then day
- Wait for page transitions if needed

## Output

On successful setup, report:

```
Google Analytics Setup Complete!

Property: [Property Name]
Measurement ID: G-XXXXXXXXXX
Website: https://[domain]
Stream: [Stream Name]

Tracking code added to: [file path]

Enhanced measurement enabled:
- Page views
- Scrolls
- Outbound clicks
- Site search
- Video engagement
- File downloads

Note: Data collection may take up to 48 hours to start appearing in reports.
```
