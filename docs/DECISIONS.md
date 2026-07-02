# docs/DECISIONS.md — Portfolio Hub

## 2026-07-01 — Visual direction

- Palette: bg `#0A0E0F` · surface `#12181A` · border `#1F2E2C` · text `#E4E7E5` ·
  muted `#6B7A78` · accent `#5EEAD4` (terminal-cyan) · amber `#E3A857` (cold-start).
  Cyan over green/vermilion to avoid the two AI-portfolio defaults and to read as
  "process running / status ok" rather than decoration.
- Type: JetBrains Mono 600/700 for name and card titles, IBM Plex Mono 400 for
  body/metrics, same family uppercase + tracked at ~11px for eyebrows (`STACK`,
  status labels).
- Layout: terminal-prompt header (`$ whoami`) + 2×2 card grid (desktop) / single
  column (mobile, <720px). No animation, no scroll effects — matches spec's "no
  dynamism from the hub itself."

## Status-dot semantics: category, not live health

The dot on each card encodes a **fixed category** decided at build time, not a
real-time check:
- 🟢 accent = project type has a live public demo
- 🟡 amber note = live demo, cold-start expected (Render free tier)
- ⚪ muted = demo on request (clinic — privacy) or repo/case-study only (calendar — OAuth, no cloud deploy by design)

No client-side fetch to `/health` or similar — a static hub calling live services on
every visitor load adds latency, a failure mode, and cost to the free-tier services
for no benefit the static label doesn't already give.

**v2 candidate:** a build-time (not client-side) status check — e.g. a GitHub Action
that curls each `/health` endpoint on a schedule and writes a status JSON the hub
reads at build — would make the dot verified rather than asserted. Not implemented
in v1.

## Metric selection per card

Métricas copiadas verbatim (mismo formato numérico) de cada `CASE_STUDY.md` fuente:
- Analyst: `7/7 hard assertions, 3/3 LLM-judge · $0.0170/run` (of 4 stories in the
  case study, picked the eval-suite numbers as the single most defensible "it works"
  claim over the 15× co-location stat, which is an infra detail, not a correctness one).
- Gateway: `0% false positives · 44.44% cache hit-rate (23-pair calibration)` — the
  case study's own "central technical finding."
- Clinic: `100% eval accuracy (up from 64%) · $0.00448/conversation` — already the
  case study's own headline metric.
- Calendar: `68/68 tests passing · 4 live-integration bugs caught & fixed` — the
  project's central story is live-verification catching real bugs, not just test count.

## Link choices

- Analyst "Live Demo" → `/` (a real HTML landing page the service serves, verified
  200).
- Gateway "Live Demo" → `/docs` (Swagger UI), not `/` — the gateway has no root
  route (`404`); `/docs` is the closest thing to an interactive live demo a
  non-technical screener can actually use.
- Clinic → no live link; "Demo on Request" is a `mailto:` with a prefilled subject
  (spec: mailto is sufficient contact, no contact form). No fixed public URL exists
  for this service by design (privacy-adjacent domain).
- Calendar → repo + case study only, no demo link — no cloud deploy in v1, OAuth
  scope is single-developer-account only (see calendar repo's own case study §9).

## Repo fix (out-of-hub)

`semantic-llm-gateway/docs/CASE_STUDY.md` §9 said "no public URL yet" — stale; the
service has since been deployed to Render (verified live via `curl`, `200`).
Replaced that one sentence with the live URL + cold-start note. Committed separately
in that repo, not part of the hub's history.
