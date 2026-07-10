# ASB Financial Manager

A free, self-hosted financial tracking app for middle and high school ASB programs.
Companion to [ASB Hub](https://github.com/ims-asb/ims-asb) — kept as a separate app so financial data stays private from the general ASB team.

Built by Luke Li, IMS ASB Treasurer 2026–27.

---

## What it tracks

| Tab | Details |
|---|---|
| **Overview** | Dashboard — hours by teacher, budget status per club, active projects |
| **ASB Budget** | Income/expenses, category breakdown, fundraising goal + progress bar, CSV export |
| **Club Hours** | Log teacher hours per club, monthly filter, 25h warning / 30h hard limit, payroll CSV |
| **Club Budgets** | Per-club allocation + spending, over-budget alerts, expense log |
| **Projects** | Named projects with budgets, multi-entry funding ledger, status tracking |

---

## Quick start

### 1. Firebase (free)

You can share the same Firebase project as ASB Hub (data is stored under a separate `ims-financial/` path), or use a completely separate project for full isolation.

1. Go to [console.firebase.google.com](https://console.firebase.google.com)
2. Create or reuse a project → Realtime Database → Create
3. Set rules to `{ ".read": true, ".write": true }`
4. Copy the config from Project Settings → General → Your apps

### 2. Configure

Open `index.html` and fill in `SCHOOL_CONFIG_FIN` near the top of the script:

```js
const SCHOOL_CONFIG_FIN = {
  schoolName: 'WHS',
  schoolYear: '2026–2027',

  firebase: {
    apiKey:    "paste from Firebase console",
    // ... rest of config
  },

  // Path in Firebase where financial data is stored
  // Change this if sharing a Firebase project with the hub
  firebasePath: 'my-school-financial',

  // Who can log in — set strong passwords
  accessCodes: {
    'treasurer-password':  'Treasurer',
    'advisor-password':    'Advisor',
    'bookkeeper-password': 'Bookkeeper',
  },

  // Monthly hours limits per teacher
  hoursWarningLimit: 25,
  hoursHardLimit:    30,
};
```

### 3. Deploy

Upload `index.html` and the PWA files to GitHub Pages (separate repo from the hub):

```
index.html
manifest-finance.json   → rename to manifest.json
sw-finance.js           → rename to sw.js
icon-finance-192.png    → rename to icon-192.png
icon-finance-512.png    → rename to icon-512.png
README.md
LICENSE
```

---

## Access control

This app has its own password gate — separate from the hub. Only users with a valid access code can see any data. Recommended access:

- **Treasurer** — full access
- **Advisors** — full access
- **Bookkeeper** — full access (they need this for payroll)

No general ASB members should have access.

---

## Club hours payroll CSV

The ⬇ Export CSV button in Club Hours exports two sections:

1. **Detail log** — every entry (date, teacher, club, hours, notes)
2. **Monthly payroll summary** — total hours per teacher per month with status flags (OK / Near limit / EXCEEDS limit)

Hand the CSV to whoever processes teacher pay — no manual totalling needed.

---

## Tech stack

Same as ASB Hub — vanilla HTML/CSS/JS, Firebase Realtime Database, GitHub Pages, service worker for offline.

---

## License

**CC BY-NC-ND 4.0** with explicit permission for school customizations.

You can use and customize it freely for your school. No commercial use. Major feature changes require permission. See `LICENSE` for details.

---

## Credits

Built by [Luke Li](https://github.com/ims-asb) with Claude (Anthropic).
Issaquah Middle School ASB, 2026–27.
