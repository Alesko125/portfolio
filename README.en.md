# Portfolio — Oleksii Kobzar

🇺🇦 [Українська версія](README.md)

---

## What's here

| Project | Description | Stack |
|---|---|---|
| [HelpLoop](#helploop) | Q&A community for IT people, live | Next.js 15, React 19, TypeScript, PostgreSQL |
| [FOP Finance](#fop-finance) | Finance app for Ukrainian sole proprietors, on Google Play | Kotlin, Compose, Room |
| [Osnova TV](#osnova-tv) | Cable operator client + IPTV | Kotlin, Compose, libVLC + Python |
| [Tabel](#tabel) | Shift schedule and pay from a Google Sheet | Kotlin, Compose, Glance |
| [fb2 library](#fb2-library) | Book catalog + Telegram Mini App | Python, FastAPI, aiogram |
| [SQLi scanner](#sqli-scanner) | Check your own sites for injection | Python, stdlib only |
| [Upwork Copilot](#upwork-copilot) | Upwork job scoring | Python, IMAP |

---

## HelpLoop

A place where IT people help each other: you ask what you're stuck on and answer the
things you have solved yourself before. The interface is in Ukrainian.

| | | |
|---|---|---|
| ![Home](assets/helploop/home.png) | ![Sign in](assets/helploop/login.png) | ![Updates](assets/helploop/updates.png) |

Questions and answers with Markdown and code highlighting, tags, comments, voting,
accepted answers and reputation. Accounts with email confirmation and password reset,
moderation with reports and soft delete, and a separate `/admin` panel where everything
is done manually. Every action is recorded in a log that cannot be rewritten.

Built on Next.js 15 with the App Router: pages are rendered on the server, and server
actions are used instead of a separate API. I wrote the authentication myself: server
sessions that are invalidated with a database query. Passwords use `scrypt` from Node's
standard library. Altcha is used instead of a CAPTCHA to protect against bots. Search
uses PostgreSQL's `simple` dictionary because it has no ready-made Ukrainian stemmer.

**Stack:** Next.js 15, React 19, TypeScript, PostgreSQL + Prisma, Tailwind v4,
remark/rehype, Resend, Caddy, Docker Compose.

---

## FOP Finance

Calculates the finances of a Ukrainian sole proprietor: income and expenses, taxes under
seven regimes, profitability, hiring and a payment calendar. Published on Google Play.

| Accounting | Analytics | Hiring |
|---|---|---|
| ![Accounting](assets/fop-finance/dashboard.png) | ![Analytics](assets/fop-finance/analytics.png) | ![Hiring](assets/fop-finance/hiring.png) |

| Calendar | Regime comparison |
|---|---|
| ![Calendar](assets/fop-finance/calendar.png) | ![Comparison](assets/fop-finance/comparison.png) |

Currency operations automatically fetch the NBU rate for the current date, and recurring
payments are added every month. There are seven tax regimes, each with its own schedule,
limits and over-limit warnings. Analytics calculate the margin, break-even point and cash
flow forecast, and compare it with the industry. The hiring calculator shows what is more
profitable — employment, Diia.City or a sole proprietor contractor — for the same net pay.
The calendar shows the payment deadlines for your regime.

The entire database is stored on the phone and works offline. The only time the app
connects to the network is to fetch the NBU rate. Sign-in uses biometrics or a code stored
only as a hash.

**Stack:** Kotlin, Jetpack Compose, Room, WorkManager, Biometric, Google Play Billing,
AdMob.

---

## Osnova TV

An app for cable operator subscribers: account, balance, payments and IPTV. It is not
available on Google Play; it is distributed as its own APK.

| Sign in | Account | Payments |
|---|---|---|
| ![Sign in](assets/osnova-tv/login.png) | ![Account](assets/osnova-tv/account.png) | ![Payments](assets/osnova-tv/finance.png) |

| Tariffs | IPTV | Services |
|---|---|---|
| ![Tariffs](assets/osnova-tv/tariffs.png) | ![IPTV](assets/osnova-tv/iptv.png) | ![Services](assets/osnova-tv/extra.png) |

There is no access to the provider's API, so the app retrieves account data directly from
its web page and parses the HTML with jsoup. It receives the playlist and programme guide
in M3U and XMLTV formats; channels work through libVLC. Notifications are sent through
Firebase, and the token is encrypted.

The backend is written in Python (aiohttp + aiogram): it accepts device registrations,
sends push notifications and distributes the APK. There are no Google Play updates, so
the update channel is custom: `version.json` reports the latest version, and before
installation the app verifies the downloaded file using SHA-256.

**Stack:** Kotlin, Compose, jsoup, OkHttp, libVLC, Firebase Cloud Messaging,
Security-Crypto. Backend — Python, aiohttp, aiogram, SQLite, Caddy.

---

## Tabel

Reads a work timesheet from a Google Sheet and shows who is on shift with you that day and
how much your monthly pay will be. I wrote it for the exact schedule at my workplace:
24-hour shifts every third day, while the rest of the team works floating 8- and 11-hour
shifts.

| Shift | Month | People |
|---|---|---|
| ![Shift](assets/tabel/shift.png) | ![Month](assets/tabel/month.png) | ![People](assets/tabel/people.png) |

| Totals | Pay |
|---|---|
| ![Totals](assets/tabel/totals.png) | ![Pay](assets/tabel/salary.png) |

For a specific date, it shows the selected status and everyone working that day, grouped
by shift type. There is a monthly calendar, a team list and totals for the year and all
time: hours, 24-hour shifts and the people you overlap with most often. Pay is calculated
with the formula explained and a 12-month history. For months not yet present in the
sheet, the team and shifts are predicted from the rotation. A home-screen widget shows
today's status.

There is no backend; the app reads the sheet directly.

**Stack:** Kotlin, Compose, Glance (widget), WorkManager.

---

## fb2 library

Organizes a folder of books: it determines which series and volume each file belongs to and
builds archives from them. It can be managed through a Telegram Mini App, a bot or the
console. It works only with files that are already on your device.

| Mini App | Console |
|---|---|
| ![Mini App](assets/bookshelf/miniapp.png) | ![CLI](assets/bookshelf/cli.png) |

It reads metadata from the fb2 files and unpacks `.fb2.zip` archives. It combines series
whose names differ slightly. From several copies of one volume, it keeps the newest one by
date and size and marks the rest as outdated. It shows which volumes are missing from a
series. It builds a ZIP of the whole library, a series or an author and splits it into
parts that fit Telegram's limit.

The core is a Python library with console commands; a repeat scan reads only what has
changed. On top of it are an aiogram bot and a FastAPI Mini App. Both work with the same
directory, and metadata is cached in SQLite.

**Stack:** Python, FastAPI, aiogram, SQLite, custom fb2 parser.

---

## SQLi scanner

| Report |
|---|
| ![Report](assets/sqli-scanner/report.png) |

It detects three types of injection: when a database error appears in the response, when
a site responds differently to true and false expressions, and when it reveals itself
through an artificial delay. It checks URL parameters, form fields, headers, cookies and
the path. It identifies the DBMS — MySQL, PostgreSQL, MS SQL, Oracle and SQLite — and
chooses payloads for it. Reports are saved in HTML and JSON, with a `REMEDIATION.md` file
that includes examples of how to fix the vulnerability in Java, PHP and Node.

Written in plain Python 3, without any external dependencies. It includes an intentionally
vulnerable test site where you can see that the scanner actually finds something. It can
be run from the console, through a menu or from a Telegram bot.

**Stack:** Python 3 (stdlib only), Telegram bot using the raw Bot API.

---

## Upwork Copilot

A set of scripts for Upwork: it collects jobs, assesses how well they fit and writes cover
letters for them. It is tuned for Python, automation and APIs.

| Console |
|---|
| ![CLI](assets/upwork-copilot/cli.png) |

It receives jobs from Upwork email alerts over IMAP, from local files and through a
Telegram bot. Each job gets a score from 1 to 10, with the reasons shown. It filters out
red flags such as unpaid tests, unrealistic budgets and technologies outside the stack.
For a specific job, it generates a cover letter. Everything is stored in JSON or SQLite,
and it works both through commands and a menu.

The core is plain Python 3.8+: without dependencies it falls back to `urllib`; with
`feedparser` and `requests` it is more reliable, but they are optional. Every score is
shown in a rule-by-rule breakdown, so it is clear where it came from.

**Stack:** Python 3.8+, IMAP, feedparser + requests (optional), Telegram Bot API,
JSON/SQLite.
