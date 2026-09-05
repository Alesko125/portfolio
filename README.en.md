# Portfolio — Oleksii Kobzar

Developer from Chernihiv, Ukraine. Seven years working with networks and tech
support at the telecom company Osnova — real hardware, real outages, and users who
don't speak the technical language. Alongside that I write code and get my own things
in front of real users instead of leaving them in a drafts folder.

Seven projects here, each one built end to end by me: backend, client, database,
deployment. The source is in private repos — below is what came out of them, with
screenshots.

🇺🇦 [Українська версія](README.md)

---

## What's here

| Project | In short | Built with |
|---|---|---|
| [HelpLoop](#helploop) | Q&A community for IT people, live | Next.js 15, React 19, TypeScript, PostgreSQL |
| [FOP Finance](#fop-finance) | Finance app for sole proprietors, on Google Play | Kotlin, Compose, Room |
| [Osnova TV](#osnova-tv) | Cable operator client + IPTV | Kotlin, Compose, libVLC + Python |
| [Tabel](#tabel) | Shift schedule and pay from a Google Sheet | Kotlin, Compose, Glance |
| [fb2 library](#fb2-library) | Book catalog + Telegram Mini App | Python, FastAPI, aiogram |
| [SQLi scanner](#sqli-scanner) | Test your own sites for injection | Python, stdlib only |
| [Upwork Copilot](#upwork-copilot) | Upwork job scoring | Python, IMAP |

---

## HelpLoop

A place where IT people help each other out: you ask what you're stuck on, you
answer the thing you once untangled yourself. Ukrainian interface, English code.
Live and running.

| | | |
|---|---|---|
| ![Home](assets/helploop/home.png) | ![Sign in](assets/helploop/login.png) | ![Updates](assets/helploop/updates.png) |

Questions and answers with markdown and code highlighting, tags, comments, voting,
accepted answers, reputation. Accounts with email confirmation and password reset,
moderation with a report queue and soft delete, and a separate `/admin` panel where
everything is done by hand and every action lands in a log you can't rewrite.

Built on Next.js 15 with the App Router: pages assemble on the server, and instead of
a separate API it uses server actions. I wrote the auth myself — server sessions you
kill with a single query. Passwords run on `scrypt` from Node's standard library.
Anti-bot is my own Altcha instead of a captcha, no third parties. Search runs on
Postgres's `simple` dictionary, since there's no ready Ukrainian stemmer.

**Stack:** Next.js 15, React 19, TypeScript, PostgreSQL + Prisma, Tailwind v4,
remark/rehype, Resend, Caddy, Docker Compose.

---

## FOP Finance

Counts a Ukrainian sole proprietor's money: income and expenses, taxes across seven
regimes, profitability, hiring, a payment calendar. On Google Play. It grew out of one
question the ready-made accounting services won't answer: how much will you actually
keep after tax if you hire one more person.

| Overview | Analytics | Hiring |
|---|---|---|
| ![Overview](assets/fop-finance/dashboard.png) | ![Analytics](assets/fop-finance/analytics.png) | ![Hiring](assets/fop-finance/hiring.png) |

| Calendar | Regime comparison |
|---|---|
| ![Calendar](assets/fop-finance/calendar.png) | ![Comparison](assets/fop-finance/comparison.png) |

Currency operations pull the NBU rate for the date on their own; recurring payments
fill themselves in every month. Seven tax regimes, each with its own schedule, limits
and over-limit warnings. The analytics work out margin, break-even, a cash-flow
forecast, and compare you against the industry. The hiring calculator shows which is
cheaper — staff, Diia.City, or a contractor — for the same net pay. The calendar holds
the due dates for your regime.

The whole database sits on the phone and goes nowhere. The one time the app touches
the network is for the NBU rate. Sign-in is biometrics or a code stored only as a
hash.

**Stack:** Kotlin, Jetpack Compose, Room, WorkManager, Biometric, Google Play Billing,
AdMob.

---

## Osnova TV

An app for a cable operator's subscribers: account, balance, payment, IPTV. It's not
on Google Play — it ships as its own APK.

| Sign in | Account | Payments |
|---|---|---|
| ![Sign in](assets/osnova-tv/login.png) | ![Account](assets/osnova-tv/account.png) | ![Payments](assets/osnova-tv/finance.png) |

| Tariffs | IPTV | Services |
|---|---|---|
| ![Tariffs](assets/osnova-tv/tariffs.png) | ![IPTV](assets/osnova-tv/iptv.png) | ![Services](assets/osnova-tv/extra.png) |

> On the account screen the account number, name, login, address and IP are blurred —
> this is a real subscriber's data.

There's no access to the provider's API, so the app pulls the account straight off
their web page — it parses the HTML with jsoup. The playlist and program guide come in as M3U and
XMLTV, and libVLC plays the channels. Push runs through Firebase, with the token kept
encrypted.

Behind it is a Python backend (aiohttp + aiogram) that takes device registrations,
sends push and serves the APK. There are no Google Play updates, so the channel is my
own: `version.json` says which version is current, and the app checks the downloaded
file against a SHA-256 before installing.

**Stack:** Kotlin, Compose, jsoup, OkHttp, libVLC, Firebase Cloud Messaging,
Security-Crypto. Backend — Python, aiohttp, aiogram, SQLite, Caddy.

---

## Tabel

Reads a work timesheet from a Google Sheet and shows who's on shift with you that day
and what the month's pay comes to. I wrote it for the exact rotation at my job:
24-hour shifts every third day, the rest of the team on floating 8- and 11-hour
shifts.

| Shift | Month | People |
|---|---|---|
| ![Shift](assets/tabel/shift.png) | ![Month](assets/tabel/month.png) | ![People](assets/tabel/people.png) |

| Totals | Payroll |
|---|---|
| ![Totals](assets/tabel/totals.png) | ![Payroll](assets/tabel/salary.png) |

> Coworkers' surnames and the amounts are blurred — real data from the sheet.

For any date it shows your status and everyone working that day, grouped by shift type.
There's a month calendar, the team list, totals for the year and for all time — hours,
24-hour shifts, who you overlap with most. Pay is worked out with the formula spelled
out and a 12-month history. For months not yet in the sheet, the roster and shifts are
forecast from the rotation. A home-screen widget keeps today's status in view.

No backend — the app reads the sheet directly.

**Stack:** Kotlin, Compose, Glance (widget), WorkManager.

---

## fb2 library

Sorts out a folder of books: it works out on its own which series a file belongs to and
which copy of a volume is newer, and packs archives from that. You can drive it from a
Telegram Mini App, a bot, or the console. It downloads nothing — it works only with the
files you already have.

| Mini App | Console |
|---|---|
| ![Mini App](assets/bookshelf/miniapp.png) | ![CLI](assets/bookshelf/cli.png) |

It reads metadata from the fb2 files, unpacking `.fb2.zip` transparently. It merges
series split apart by inconsistent naming. Out of several copies of one volume it keeps
the current one — by date and size — and leaves the rest alone, just marked stale. It
shows which volumes a series is missing. It packs a ZIP of the whole library, a series
or an author, and slices it under Telegram's size limit.

The core is a Python library with console commands; a repeat scan only re-reads what
changed. On top sit a bot on aiogram and a Mini App on FastAPI, both hitting the same
catalog, with metadata cached in SQLite.

**Stack:** Python, FastAPI, aiogram, SQLite, a custom fb2 parser.

---

## SQLi scanner

So a site owner can check their own pages for SQL injection and see how to patch it.
Kept narrow on purpose: your own sites only, or ones you have permission for.

| Report |
|---|
| ![Report](assets/sqli-scanner/report.png) |

It catches three kinds of injection: when a database error falls into the response,
when the site answers a true and a false expression differently, and when it gives
itself away with an injected delay. It probes URL parameters, form fields, headers,
cookies and the path. It recognizes the DBMS — MySQL, PostgreSQL, MS SQL, Oracle,
SQLite — and picks payloads to match. Reports go to HTML and JSON, and next to them a
`REMEDIATION.md` shows how to close the hole in Java, PHP and Node.

Written in plain Python 3, no external dependency at all. It ships a deliberately
vulnerable test site where you can see the scanner actually find something. You can
run it from the console, through a menu, or from a Telegram bot.

**Stack:** Python 3 (stdlib only), a Telegram bot on the raw Bot API.

---

## Upwork Copilot

A set of scripts so I don't scroll Upwork by hand: it collects jobs, scores how much
they're mine, and drafts cover letters for them. Tuned for Python, automation and APIs.

| Console |
|---|
| ![CLI](assets/upwork-copilot/cli.png) |

It pulls jobs from Upwork email alerts over IMAP, from local files, and through a
Telegram bot. Each one gets a score from 1 to 10, with the reason for it shown. It
filters out red flags — unpaid tests, unrealistic budgets, off-stack work. For a given
job it drafts a letter on a hook → solution → call-to-action shape. Everything is
stored in JSON or SQLite, and it runs both by commands and through a menu.

The core is plain Python 3.8+: without dependencies it falls back to `urllib`; with
`feedparser` and `requests` it's more robust, but they're optional. Every point shows
up in a per-rule breakdown, so it's clear where a score came from.

**Stack:** Python 3.8+, IMAP, feedparser + requests (optional), Telegram Bot API,
JSON/SQLite.
