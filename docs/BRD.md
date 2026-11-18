# Mutual Fund Advisor Dashboard (India) — Business Requirements Document (BRD)

Version 1.0 · 17-Nov-2026 · Status: Draft for review

## 1. Purpose and assumptions

This document specifies a web dashboard that lets an AMFI-registered mutual fund distributor (MFD) manage 100+ client investors: onboard and archive clients, record transactions manually or from CAS statements, and backfill, modify and place SIPs covering up to 20 years of history. The companion [PRD](PRD.md) holds the product requirements.

**Confirmed by the business owner**

- Operator: an MFD or advisory firm serving its own clients, not a direct-to-consumer app.
- Data entry: both manual entry and import of Consolidated Account Statements (CAS) from CAMS / KFintech / MF Central.
- SIP scope: backfill up to 20 years of history, modify live SIPs (top-up, step-up, pause, stop), and place new SIP orders through an exchange platform.

**Working assumptions (to confirm, see PRD section 14)**

- Launch size 100 clients; the system is sized for 1,000 clients and ~1 million transactions without redesign.
- Order placement goes through BSE StAR MF first; NSE NMF II and MF Utilities (MFU) are later options.
- Clients get an optional read-only portal in a later release; v1 users are firm staff only.
- Hosting in India; all amounts in INR (₹).

**Glossary**

| Term | Meaning |
| --- | --- |
| MFD | Mutual Fund Distributor, registered with AMFI and holding an ARN |
| ARN / EUIN | AMFI Registration Number of the firm / Employee Unique ID of each staff member who sells |
| AMC | Asset Management Company (the fund house) |
| RTA | Registrar and Transfer Agent: CAMS or KFintech |
| CAS | Consolidated Account Statement listing a PAN's folios and transactions |
| Folio | A client's account number with one AMC |
| NAV | Net Asset Value per unit, published daily by AMFI |
| SIP / STP / SWP | Systematic Investment / Transfer / Withdrawal Plan |
| Mandate | Bank debit authority for SIPs: NACH, e-NACH or UPI AutoPay |
| KRA | KYC Registration Agency |
| XIRR | Annualised return on irregular cash flows |
| IDCW | Income Distribution cum Capital Withdrawal (formerly "dividend") |

## 2. Business context and objectives

The firm needs one system of record for 100+ clients' mutual fund holdings, replacing spreadsheets, RTA logins and emailed CAS PDFs. Today, answering "what is this client's SIP worth and what has it returned since 2006?" takes an RM 30–60 minutes of manual work per client.

**Problems today**

- Holdings are scattered across CAMS, KFintech, AMC portals and spreadsheets, so portfolio values are stale and inconsistent.
- 20 years of SIP history includes scheme mergers, renamed schemes, the 2013 direct/regular split and the 2018 SEBI recategorisation, which break manual tracking.
- SIP changes (step-ups, pauses, cancellations) are placed on the exchange but not reflected in the firm's records.
- Departed clients stay mixed with active ones, and there is no audit trail of who changed what.

**Business objectives**

| ID | Objective | Target |
| --- | --- | --- |
| BO-1 | Single accurate view of every client's holdings and returns | 100% of active clients' folios reconciled to CAS within 90 days of launch |
| BO-2 | Cut time to produce a client portfolio review | From 30–60 min to under 5 min per client |
| BO-3 | Bring 20-year SIP history into the system | Backfill of all clients complete within 60 days, with unit balances matching the latest CAS |
| BO-4 | Act on SIPs from the same screen | 90% of SIP registrations, step-ups, pauses and cancellations initiated in-app by month 6 |
| BO-5 | Grow assets under advice | Support 1,000 clients without adding back-office headcount |
| BO-6 | Pass regulatory and AMFI audits | Full audit trail; zero findings on client records |

## 3. Stakeholders and scope

| Stakeholder | Interest | Involvement |
| --- | --- | --- |
| Firm principal / ARN holder | Growth, compliance, AUM visibility | Sponsor, approves releases |
| Relationship managers (RMs, with EUIN) | Fast client reviews, SIP actions | Primary daily users |
| Operations / back office | Data entry, CAS imports, reconciliation | Daily users |
| Compliance officer | Audit trail, KYC, consent, record retention | Reviewer, quarterly audits |
| Clients (investors) | Accurate statements, smooth SIP changes | Indirect in v1; portal later |
| BSE StAR MF, RTAs (CAMS, KFintech), AMFI | Data and order rails | External integrations |

**In scope**

- Client (user) lifecycle: add, edit, group into families, archive and restore.
- Folio and holdings tracking across all AMCs.
- Transactions: purchases, SIP installments, redemptions, switches, STP, SWP, IDCW payouts and reinvestments, merger and bonus events.
- Manual entry, bulk CSV upload and CAS PDF import with reconciliation.
- SIP backfill (up to 20 years), modification (top-up, step-up, pause, stop) and new SIP orders via BSE StAR MF.
- Daily NAV refresh, valuation, XIRR, capital gains reports and client reports.
- Staff roles, permissions and a full audit log.

**Out of scope for v1**

- Stocks, bonds, PMS, AIF, NPS or insurance tracking.
- Investment advice, model portfolios or fund recommendations engine.
- Client self-service transactions and a client mobile app.
- Brokerage / commission reconciliation (candidate for v2).
- Direct plans: an MFD earns trail only on regular plans; direct holdings can be tracked from CAS but not transacted.

## 4. Business requirements

Each requirement maps to a PRD section; priority uses MoSCoW (Must, Should, Could).

| ID | Requirement | Priority | PRD |
| --- | --- | --- | --- |
| BR-01 | Staff can add a new client with PAN, KYC status, bank, nominee and contact details | Must | 8 |
| BR-02 | Staff can group clients into families / households and view combined holdings | Must | 8 |
| BR-03 | Staff can archive a client so they leave active views but all records are retained and restorable | Must | 8 |
| BR-04 | Staff can record any transaction type manually, with NAV and units auto-filled from AMFI history | Must | 9 |
| BR-05 | Staff can import CAS PDFs and bulk CSVs, with duplicates detected and differences flagged | Must | 9 |
| BR-06 | Staff can backfill an SIP for up to 20 years from its start date, amount and frequency, then reconcile to CAS | Must | 10 |
| BR-07 | Staff can top-up, step-up, pause or stop an existing SIP and see its effect on future installments | Must | 10 |
| BR-08 | Staff can register a new SIP with a bank mandate and submit it to BSE StAR MF | Must | 10 |
| BR-09 | Portfolio values refresh daily from AMFI NAVs; returns shown as absolute and XIRR | Must | 11 |
| BR-10 | Firm-level dashboard shows total AUM, SIP book, inflows, missed installments and archived clients | Must | 11 |
| BR-11 | Capital gains and holding statements per client and financial year, downloadable as PDF and Excel | Should | 11 |
| BR-12 | Role-based access: principal, RM, operations, compliance; RMs see only their mapped clients | Must | 7 |
| BR-13 | Every create, change, archive and order is written to an immutable audit log | Must | 13 |
| BR-14 | Alerts for failed SIP debits, expiring mandates, KYC issues and missing nominees | Should | 11 |
| BR-15 | Read-only client portal with the same reports | Could | 14 |

## 5. Regulatory and compliance requirements

The firm operates as an MFD, so the product must support distribution, not paid advice, and must keep complete records. These points are drawn from general knowledge of SEBI and AMFI rules and must be verified by the firm's compliance officer before build.

| Area | Requirement for the product |
| --- | --- |
| Distributor role | Every order carries the firm ARN and the selling staff member's EUIN; no advisory fee fields. If the firm is also a SEBI RIA, advice and distribution must be segregated per SEBI rules. |
| KYC | Capture PAN and KYC status from a KRA (CVL, NDML, CAMS KRA, KFin KRA, DotEx); block new orders for clients whose KYC is not validated. |
| Nomination | Record nominees or the opt-out declaration per folio; flag folios missing either. |
| FATCA / CRS | Store the declaration and its date per client. |
| Minors | Hold guardian details; on the minor turning 18, prompt for change of status to major. |
| Order rules | Respect scheme minimums, cut-off times and the 0.005% stamp duty on purchases (since July 2020). |
| Tax | Compute capital gains on FIFO units; apply 31 Jan 2018 grandfathering for equity, the 1 Apr 2023 change for debt funds, and the 23 Jul 2024 rate changes. |
| Data protection | Comply with the Digital Personal Data Protection Act, 2023: consent record, purpose limitation, breach response, data hosted in India. |
| Record keeping | Retain client and transaction records after archive; default retention 8 years, configurable by compliance (confirm the exact period). |
| Client communication | Statements carry the ARN, the regular-plan disclosure and the standard "mutual fund investments are subject to market risks" disclaimer. |

## 6. Success metrics, constraints and risks

**Success metrics (measured at month 6)**

| Metric | Target |
| --- | --- |
| Folios reconciled to latest CAS (units match to 3 decimals) | ≥ 99% |
| Time to produce a client review pack | < 5 min |
| SIP actions initiated in-app | ≥ 90% |
| Order rejections caused by data errors in the app | < 2% |
| Staff weekly active usage | 100% of RMs and ops |
| Audit findings on client records | 0 |

**Constraints**

- Budget and timeline to be set by the sponsor (open question).
- BSE StAR MF membership and API credentials must be in place before order placement goes live.
- Historical NAVs before 2006 and for wound-up schemes may be incomplete in public AMFI data.

**Risks and mitigations**

| Risk | Impact | Mitigation |
| --- | --- | --- |
| CAS layouts change or parse badly | Wrong holdings | Versioned parsers, a review screen before commit, manual override |
| 20-year backfill differs from RTA units (mergers, rounding, missed debits) | Clients see wrong returns | Reconcile against CAS; adjust with a logged "reconciliation entry", never silent edits |
| Order placed twice or to wrong folio | Financial loss, complaints | Maker–checker approval, idempotent order IDs, folio validation |
| Data breach of PAN and bank data | Legal and reputational | Encryption, MFA, least-privilege roles, India hosting, breach plan |
| Exchange or RTA API downtime | Missed cut-offs | Queue with retry, cut-off warnings, manual fallback |
