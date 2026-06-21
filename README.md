# FallClaimPractice

> Sovereign single-file accounting for UK claims firms — CFA · DBA · escrow · CMR · ATE · PII

Part of the `fallclaim` bundle (prime 827). Anchors with `fallclaim` (811), `fallclaimonboard` (821), `fallclaimpaper` (823) via `BroadcastChannel('fall-claim')`.

---

## For a claims firm (CMC or solicitor practice)

Open `index.html`. That's it. No install, no account, no cloud. Demo firm + adviser + 1 case + 3 escrow entries + 1 ATE policy + 1 complaint seed on first load.

What you get:

- **Client escrow tracker** — receipts / disbursements / reconciliation against client money held in trust. Amber after 21 days, red after 35.
- **Fee tracker by arrangement** — CFA (hourly × success-fee on win), DBA (% damages), hourly, fixed, legal aid. WIP accruals for CFAs.
- **Success-fee accrual register** — memo accruals for every active CFA: estimated WIP × success-fee %. Auto-realised on `case.settled` (won), reversed on lost.
- **ATE insurance** — per-case policies, premiums, claim status, recovery on win.
- **FCA CMR fees** — annual fee tracking + next due reminder + RegData half-yearly window.
- **PII** — claims firms need ~£2M (CMR) / £3M (SRA). Track policy, expiry, monthly accrual. Amber 60d, red 30d.
- **Complaints register** — every complaint logged with FOS escalation, learning, annual summary export. Required by FCA CMR and SRA.
- **Per-adviser P&L** — fees by type, billable hours, case load, settlement rate, average case value.
- **Firm P&L** — revenue · expenses (salaries, PII, CMR fees, ATE premiums, court fees, expert fees, lead-gen, premises, software) · net.
- **Recurring fee schedules** — retainer work auto-generates on open.
- **Invoice generator** — Markdown; handoff to FallClaimPaper via the mesh.
- **Per-case fee summary** — every accrual, realisation, expense, net contribution.
- **Q&A** — 14 T0 rules (LASPO CFA cap, DBA Regs, ATE recovery, CMR fee, lead-gen disclosure, PI referral fee ban, CMR reporting, PII min, FOS, stale client money, VAT, court fees, counsel, transitional) + BYOK T3.

All data lives in your browser's IndexedDB. Nothing leaves the device unless you press Export.

---

## For a developer / forker

Single HTML file. Vanilla JS. No build step. No dependencies.

### Architecture

- IndexedDB stores: `firms`, `advisers`, `clients`, `cases`, `feeRecords`, `successFeeAccruals`, `escrow`, `atePolicies`, `fcaCmrFees`, `complaintsRegister`, `piPolicies`, `audit`, `feeSchedules`, `expenses`, `state`
- Mesh: `BroadcastChannel('fall-claim')` for client/adviser/firm/case sync, `case.settled` listener, `fee.recorded`/`escrow.entry`/`complaint.recorded` emitters
- `BroadcastChannel('fall-signal')` for the wider estate
- T0 rules engine for offline answers. BYOK Anthropic/Gemini/OpenAI/OpenRouter for grounded answers.
- P3 audit chain — every state change appends `{i, ts, prevHash, docHash, hash}`. Exportable JSON. 6 years retention per FCA CMR.

### Window globals

- `window.FALLCLAIMPRACTICE.recordFee({...})` — programmatic fee insert
- `window.FALLCLAIMPRACTICE.ytd()` / `.mtd()` / `.escrowHeld()` / `.ask(q)`
- `window.KONOMI` — sovereignty shim

### Mesh messages (fall-claim)

Receives: `client.*`, `adviser.*`, `firm.updated`, `case.*` (especially `case.settled` → auto-records realised fee), `sync.request`/`sync.snapshot`.

Emits: `fee.recorded`, `escrow.entry`, `complaint.recorded`, `sync.snapshot`, `fallclaimpaper.invoice.generate`.

### Fork

Change `TOOLNAME`, `VERSION`, `PRIME` at the top of the script tag, update the brand block in the header, swap the disclaimer. Done.

---

## Disclaimer

FallClaimPractice is a tool for UK claims firms (CMC and solicitor practices). It assists with fee tracking, escrow accounting, FCA CMR / SRA compliance, complaints handling and ATE/PII management. It is not court filing software; pleadings and submissions remain the firm's responsibility. Sovereign — client data never leaves the device unless exported.

MIT-licensed. See `LICENSE`.
