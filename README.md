# Export Rightmove to CSV — Chrome Extension (MV3)

Export property listings from Rightmove search results to a CSV spreadsheet. One click, no sign-up. All data stays on your device.

## Quick Start (Load Unpacked)

1. **Download ExtPay.js** (required for Pro features):
   ```bash
   # Option A: npm
   npm install extpay
   cp node_modules/extpay/dist/ExtPay.js ./ExtPay.js

   # Option B: Download from GitHub
   # Go to https://github.com/Glench/ExtPay/blob/main/dist/ExtPay.js
   # Click "Raw" → Save As → ExtPay.js in the extension root folder
   ```

2. **Load in Chrome:**
   - Open `chrome://extensions/`
   - Enable **Developer mode** (top-right toggle)
   - Click **Load unpacked** → select this folder
   - Navigate to a Rightmove search results page
   - Click the extension icon → **Scan Page** → **Export to CSV**

3. **Test Pro payments** (optional):
   - Use your ExtensionPay dashboard email to test payments without a real charge
   - Click "Upgrade to Pro" in the popup
   - Reinstall the extension to reset to unpaid state

## Architecture

```
manifest.json           MV3 manifest, minimal permissions
ExtPay.js               ExtensionPay library (download separately)
background.js           Service worker: ExtPay init, CSV generation, downloads
content.js              Content script: responds to scan requests
popup/
  popup.html            Popup UI
  popup.js              Scan, preview, export, Pro status
  popup.css             Styles
utils/
  extractors.js         Schema mapping layer — multi-strategy field extraction
  csv.js                RFC 4180 CSV generation with BOM
  licensing.js          Pro license check (ExtPay + cache), usage limits
icons/                  Extension icons (16, 48, 128px)
tests/
  extractors.test.html  Unit tests for regex parsers
PRIVACY_POLICY.md       Chrome Web Store privacy policy
README.md               This file
```

## ExtensionPay Configuration

| Setting | Value |
|---|---|
| Permanent ID | `rightmove-csv-export` |
| Plans | pro-monthly (£1.99), pro-yearly (£19.99), pro-lifetime (£29.99) |
| Dashboard | https://extensionpay.com |

The extension uses `ExtPay('rightmove-csv-export')` in both `background.js` and `popup.js`.

### How Pro Works
- `background.js` calls `extpay.startBackground()` on load
- `utils/licensing.js` checks `extpay.getUser()` with 12-hour local cache
- Pro users bypass all free-tier limits (unlimited exports, unlimited rows)
- Payment events instantly update the local cache via `extpay.onPaid`

## Free-Tier Limits

| Feature | Free | Pro |
|---|---|---|
| Exports per day | 1 | Unlimited |
| Rows per export | 25 | Unlimited |

Constants are in `utils/licensing.js` → `FREE_TIER_LIMITS`.

## Extraction Strategy

Each field uses a multi-tier fallback chain:
1. `data-testid` / `aria-label` attributes (most stable)
2. CSS class patterns (multiple alternatives)
3. Structural patterns (anchors to `/properties/` URLs)
4. Regex on text content (last resort)

Deduplication by listing URL prevents double-counting from nested DOM elements.

## Columns Extracted

| Column | Method | Notes |
|---|---|---|
| Address | Selector + heading fallback | Strips "FEATURED PROPERTY" text |
| Postcode | Regex from address | Full (SW1A 1AA) or partial (SW1X) |
| Price (Display) | Selector + regex | Clean £ amount only |
| Price (Numeric) | Parsed from display | Integer for sorting/filtering |
| Bedrooms | Selector + regex | Studio → 0 |
| Property Type | Selector + type regex | Detached, Flat, etc. |
| Listing URL | Anchor href | Always absolute |
| Agent | Branch name selector + logo alt | Filters phone numbers, photo descriptions |
| Added/Reduced | Date selector + regex | Raw text as shown |
| Key Features | Feature list items + description | Sanitized, no contamination |
| Price Qualifier | Text match | Guide price, PCM, etc. |
| Station Distance | Selector + regex | If shown on card |
| First Image URL | img src filtering | Property photos only |
| Export Timestamp | Generated | ISO 8601 |

## Chrome Web Store Submission

### Suggested Description
> Export Rightmove property search results to a CSV spreadsheet in one click.
>
> ✓ Sale and rental results
> ✓ All visible listing data: address, price, bedrooms, type, agent, features
> ✓ Preview before exporting
> ✓ Choose which columns to include
> ✓ Data processed locally — nothing leaves your device
> ✓ Free tier: 1 export/day, 25 rows. Pro: unlimited.

### Keywords
`export rightmove to csv`, `rightmove csv`, `rightmove spreadsheet`, `rightmove data export`, `property search export`

### Permissions Justification (for store review)
- **storage**: Store column preferences, daily usage counters, and Pro license cache locally
- **downloads**: Save the CSV file to the user's downloads folder
- **Host access (rightmove.co.uk)**: Read visible property listing data on search results pages

## Development

### Run Unit Tests
Open `tests/extractors.test.html` in a browser to run the parser tests.

### Testing Extraction
1. Load the extension unpacked
2. Navigate to any Rightmove search results page
3. Open DevTools console
4. Run: `window.__RightmoveExtractors.scanPage()`
5. Inspect the returned `listings` array

### Reset Pro/Usage State
```js
// In the extension's background page console:
chrome.storage.local.clear()
```
