# Export Rightmove to CSV

A privacy-first Chrome extension that exports Rightmove search results to a clean CSV (Excel/Google Sheets ready).  
Includes saved properties, notes, and user-initiated price change tracking.

> Not affiliated with Rightmove.

---

## Features

### Core
- Export Rightmove search results to CSV (for sale + to rent)
- Clean CSV formatting (quotes/newlines/commas handled properly)
- Column selection + preview before export
- Works directly on Rightmove results pages

### Save & Track
- Save properties to a shortlist inside the extension
- Add notes to saved properties
- Price change tracking (user-initiated): when you scan again, the extension highlights saved properties whose price has changed since you saved them

### Pro (paid)
- Unlimited exports (no daily limit)
- Unlimited rows per export
- Multi-page export (export across multiple results pages with a visible progress indicator)
- Higher limits for saved properties/notes (if you’ve implemented these)

---

## How to Use

1. Open a Rightmove search results page (for sale or to rent).
2. Click the extension icon.
3. Click **Scan** to detect listings.
4. (Optional) Choose which columns you want.
5. Click **Export CSV**.

### Multi-page Export (Pro)
- Click **Export all pages** (or equivalent button)
- The extension will iterate results pages with a visible progress display
- Runs only when you explicitly start it (no background crawling)

### Save + Price Tracking
- Use **Save** to add a listing to your shortlist
- On a later day, open a results page again and click **Scan**
- If a saved listing’s price has changed, it will be flagged in the UI

---

## Free vs Pro

**Free**
- Limited exports per day
- Limited rows per export
- Limited saved properties / notes (if enabled)

**Pro**
- Unlimited exports
- Unlimited rows per export
- Multi-page export
- Higher saved/notes limits (if enabled)

Pro is handled via ExtensionPay (license check only).

---

## Privacy

- All Rightmove listing data is processed **locally in your browser**.
- The extension does **not** transmit listing data anywhere.
- Saved properties, notes, and export counters are stored locally using `chrome.storage.local`.
- Pro status is verified using ExtensionPay (license/payment verification only).

Read the full privacy policy in `PRIVACY_POLICY.md`.

---

## Permissions Explained

- `storage` — stores user settings, saved properties/notes, price tracking state, and local usage counters
- `downloads` — saves the generated CSV file to your Downloads folder
- Host permissions for `rightmove.co.uk` — reads the visible listing information on Rightmove results pages only

---

## Development

### Run locally (unpacked)
1. Open Chrome → `chrome://extensions`
2. Enable **Developer mode**
3. Click **Load unpacked**
4. Select the extension folder (the folder containing `manifest.json`)

### Tests
Open:
- `tests/extractors.test.html`  
(and run the in-browser tests)

---

## Troubleshooting

**“No listings found”**
- Make sure you’re on a Rightmove results page (not a listing detail page)
- Rightmove may have changed their layout — open an issue with a screenshot + page URL pattern (not personal info)

**Exports look cut off**
- Free tier has row limits — upgrade to Pro for unlimited rows and multi-page export

---

## Support

For questions or bug reports, open a GitHub issue or email:  
harrisonking3465@gmail.com

---

## License

Choose a license (MIT is common for open-source).  
If you plan to keep it source-available but not freely redistributable, consider adding a custom license instead.
