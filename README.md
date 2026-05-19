# OSC Update Status — Dashboard

Live read-only dashboard of the OSC tracker, prepared for M.DESIGN.
Public URL: _(set after Cloudflare Pages deployment)_

## How it works

```
Excel (SharePoint, private)
        │
        ▼  (Power Automate flow, every 15 min)
data.json (this repo)
        │
        ▼  (Cloudflare Pages serves the HTML + JSON)
Dashboard URL  ──►  M.DESIGN opens it in a browser
```

SharePoint is never exposed. The flow reads the Excel, transforms it to JSON, and commits it here. The dashboard fetches `data.json` on every page load and every 5 minutes while open.

## Files in this repo

| File | Purpose |
|------|---------|
| `index.html` | The dashboard itself. Self-contained. |
| `data.json` | The data Power Automate writes. Overwritten on every refresh. |
| `logo.png` | Optional logo image displayed in the header tile. If missing, a fallback letter shows. |
| `README.md` | This file. |

## data.json shape

```json
{
  "updated_at": "ISO 8601 timestamp",
  "rows": [
    {
      "received": "YYYY-MM-DD",
      "partner": "Equans",
      "popzone": "MRO_GENK_01_POP_001",
      "prio": "" | "HIGH PRIO",
      "status": "OSC UPDATED" | "CHECK REMARKS",
      "remark": "free text",
      "updated_date": "YYYY-MM-DD" | "",
      "osc_request_date": "YYYY-MM-DD" | "",
      "mail_sent_date": "YYYY-MM-DD" | ""
    }
  ]
}
```

Field names matter — the dashboard reads them by name. If the Excel changes columns, the Power Automate flow's mapping needs updating, not the dashboard.

## If something breaks

**Dashboard shows "Could not load data"**
→ `data.json` is missing, malformed, or returning 404. Check the repo, validate JSON at jsonlint.com.

**Dashboard loads but numbers look wrong**
→ The Power Automate mapping is sending bad field names or status values. Check the latest `data.json` in the repo against the shape above.

**Power Automate flow failing**
→ Check the run history in Power Automate. Most common cause: the Excel table was renamed or columns reordered. The flow references the table by name.

**Logo not showing**
→ `logo.png` is missing or wrong path. Falls back to a styled "M." automatically.

## Maintenance

- Data refresh interval: every 15 minutes (set in Power Automate)
- Dashboard auto-refresh: every 5 minutes while open
- Hosting: Cloudflare Pages, auto-deploys on every commit to this repo
- No build step. Just static files.
