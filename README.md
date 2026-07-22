[README-finance.md](https://github.com/user-attachments/files/30280265/README-finance.md)# ASB Financial Manager

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
3. Go to **Build → Authentication → Sign-in method** and enable the **Email/Password** provider (see [Security](#security) below for why)
4. Set your database rules to the ones in [Database rules](#database-rules) below — plain `{ ".read": true, ".write": true }` is no longer recommended now that this app handles real financial data
5. Copy the config from Project Settings → General → Your apps

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

  // Who can log in — set strong passwords.
  // These double as each role's Firebase Auth password (see Security below) —
  // an account is created automatically the first time each role signs in.
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

## Security

### Firebase API key

You'll notice a Firebase `apiKey` (starting with `AIzaSy...`) hardcoded directly in the source. **This is intentional and safe.** Firebase client config keys are not secrets — Google designed them to ship inside every visitor's browser, since there's no way to talk to Firebase from a web page without one. GitHub's automated secret scanner sometimes flags it anyway (it just pattern-matches the `AIzaSy` prefix and can't tell a public client key from a real secret) — that alert can be safely dismissed as a false positive.

What actually protects the data is the database security rules and real sign-in accounts below — not the key.

### Real Firebase Authentication

This app requires an actual Firebase-verified sign-in, not just a client-side password check. A password screen that only runs in the browser doesn't stop anyone who finds your database URL from reading or writing data directly — Firebase itself has no way to know a check ever happened.

So: each role's access code doubles as a real Firebase Auth password. The first time a role signs in, the app automatically creates a matching Firebase Authentication account behind the scenes — no manual per-person setup needed. After that, the database rules require `auth != null` for any read or write, so the database itself refuses anyone who hasn't actually signed in, regardless of whether they know the database URL.

**One-time setup required in the Firebase console:**
1. Go to Build → Authentication → Sign-in method
2. Enable the **Email/Password** provider

That's it — account creation, sign-in, and sign-out are all automatic from there.

If you ever rotate an access code in `accessCodes` / `/config/codes`, the matching Firebase Auth account keeps its *old* password until it's reset manually (Authentication → Users → find the account → reset password). The app shows "Access code changed recently" in that case rather than silently failing.

### Database rules

```json
{
  "rules": {
    "ims-financial": {
      ".read": "auth != null",
      ".write": "auth != null",
      "config": {
        "codes": { ".read": false, ".write": false }
      }
    }
  }
}
```

(If you're sharing a Firebase project with ASB Hub, this sits alongside the Hub's own `ims-asb` rules block — see the Hub's README for those.)

What's locked down:
- **Financial data** — requires a real signed-in Firebase Auth session for any read or write.
- **Access codes** (`config/codes`) — never externally readable or writable, even by signed-in accounts.

### Known limitation

ASB Hub itself (the companion app — events, chat, notes, ideas) still uses a client-side-only password gate with no real Firebase Auth behind it. That's a much lower-stakes gap since no financial data lives there, but worth knowing if the two apps ever get compared side by side on security posture.

---

## Tech stack

Same as ASB Hub — vanilla HTML/CSS/JS, Firebase Realtime Database, Firebase Authentication, GitHub Pages, service worker for offline.

---

## License

**CC BY-NC-ND 4.0** with explicit permission for school customizations.

You can use and customize it freely for your school. No commercial use. Major feature changes require permission. See `LICENSE` for details.

---

## Credits

Built by [Luke Li](https://github.com/ims-asb) with Claude (Anthropic).
Issaquah Middle School ASB, 2026–27.
