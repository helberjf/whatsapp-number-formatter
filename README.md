# 📱 WhatsApp Number Normalizer

A client-side web tool that normalizes Brazilian phone numbers to the WhatsApp-ready format (`+55DDXXXXXXXXX`), replicating the exact logic of a Google Apps Script — with no server, no dependencies to install, and no data leaving the browser.

---

## ✨ Features

- **3 input modes** — upload a `.csv`/`.xlsx` spreadsheet, paste a comma/newline-separated list, or paste a table copied directly from Google Sheets or Excel
- **Automatic column detection** — identifies phone columns by name (`Phone`, `Telefone`, `Celular`, `WhatsApp`, etc.) and detects TSV structure on paste
- **Faithful normalization logic** — handles all common Brazilian edge cases: leading `0` before DDD, `00` international prefix, missing DDD (defaults to 21), missing country code, 8 or 9-digit local numbers, and the `55 0 DD` malformed pattern
- **Live result table** — paginated preview with per-row status badges (`OK`, `IGNORADO`, `VAZIO`) and inline search/filter
- **Export options** — download full CSV with the new `Phone_Normalized` column, or copy only the clean numbers to clipboard with one click
- **Zero backend** — runs entirely in the browser; no data is sent anywhere

---

## 🚀 Getting Started

No build step required. Just open the file:

```bash
git clone https://github.com/your-username/whatsapp-number-normalizer.git
cd whatsapp-number-normalizer
open index.html   # or double-click it
```

Or use any static server:

```bash
npx serve .
# → http://localhost:3000
```

---

## 📐 Normalization Rules

The core `normalizeBRNumber()` function mirrors the Apps Script logic exactly:

| Input pattern | Result |
|---|---|
| `(11) 99999-0001` | `+5511999990001` |
| `021 98888-0002` | `+5521988880002` |
| `0011 99999-0001` | `+5511999990001` |
| `55 0 11 99999-0001` | `+5511999990001` |
| `98888-0002` (no DDD) | `+5521988880002` (DDD 21 applied) |
| `+55 11 91234-5678` | `+5511912345678` |
| Numbers with < 5 digits | ignored |
| Numbers with unexpected length | ignored |

Final output is always `+55` + 2-digit DDD + 8 or 9-digit number (12 or 13 digits total).

---

## 🔧 Apps Script Version

The equivalent Google Apps Script (`normalizeWhatsAppNumbers`) can be added directly to any Google Sheets file via **Extensions → Apps Script**. It reads the `Phone` column, normalizes in place, and formats the cell as plain text to preserve the `+` prefix.

```javascript
function normalizeWhatsAppNumbers() {
  const sheet = SpreadsheetApp.getActiveSpreadsheet().getActiveSheet();
  const data = sheet.getDataRange().getValues();
  const phoneColIndex = data[0].indexOf("Phone");
  if (phoneColIndex === -1) return;

  const rng = sheet.getRange(2, phoneColIndex + 1, sheet.getLastRow() - 1, 1);
  const vals = rng.getValues();
  for (let i = 0; i < vals.length; i++) {
    const n = normalizeBRNumber_(vals[i][0]);
    if (n) vals[i][0] = n;
  }
  rng.setNumberFormat("@");
  rng.setValues(vals);
}
```

---

## 🗂️ Project Structure

```
whatsapp-number-normalizer/
├── index.html          # entire app — HTML + CSS + JS, single file, no build
└── README.md
```

---

## 🛠️ Tech Stack

| Layer | Choice | Reason |
|---|---|---|
| Runtime | Vanilla JS (ES2020) | Zero dependencies, runs anywhere |
| Spreadsheet parsing | [SheetJS (xlsx)](https://sheetjs.com/) via CDN | Handles `.csv` and `.xlsx` from Google Sheets / Excel |
| Styling | CSS custom properties + CSS Grid | No framework needed for this scope |
| Fonts | JetBrains Mono + Syne via Google Fonts | Monospace for data, geometric for headings |

---

## 🤝 Contributing

Pull requests are welcome. To suggest a normalization edge case, open an issue with the raw input and expected output.

---

## 📄 License

MIT — free to use, modify, and distribute.