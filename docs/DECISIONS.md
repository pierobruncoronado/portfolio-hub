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
  column (mobile, <720px).

## 2026-07-01 — Round 2: terminal chrome + motion (user feedback: "too simple")

User approved a second pass and explicitly authorized animation/scroll effects
("si consideras que el beneficio es amplio"), amending the original v1 exclusion —
see spec.md's amended "Fuera de alcance." Changes made:

- **Card titlebar**: each card got a terminal-window chrome — three muted dots
  (decorative, deliberately monochrome, not the red/amber/green of a real macOS
  titlebar) + the project's real repo path (`~/analyst-sql-agent`, etc). Muted/
  monochrome so it doesn't collide with the status-dot's actual color semantics
  (live/muted) sitting a few lines below it.
- **Background texture**: a faint static cyan grid (`34px` cells, ~3.5% opacity) +
  a radial glow behind the hero. Static, not scroll-linked — texture, not motion.
- **Boot-sequence reveal**: header lines (`$ whoami`, name, tagline, contact links,
  the `$ ls -la ./services` eyebrow) fade/rise in with a ~90ms stagger, once, on
  page load. A blinking block cursor sits after `$ whoami` — the one continuous
  animation on the page, chosen because it's a real terminal affordance (not
  decorative motion) and is cheap/unobtrusive at 1s period.
- **Scroll-reveal**: cards fade/rise in via `IntersectionObserver` the first time
  each crosses 15% into view (`script.js`), once per card, then unobserved — no
  repeat-on-scroll, no parallax.
- **Hover/focus micro-interaction**: card border brightens to accent + 3px lift,
  0.15s, on `:hover`/`:focus-within`.
- **Restraint**: no looping decoration beyond the cursor blink; no scroll-linked
  parallax; single orchestrated load moment rather than scattered effects (per
  frontend-design skill's warning against "AI-generated" motion clutter).
- **Accessibility**: `@media (prefers-reduced-motion: reduce)` forces all of the
  above to their static end-state (`opacity:1`, `transform:none`, animations off)
  — verified by inspecting computed styles, not assumed.

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

## 2026-07-02 — Round 3: per-project color + dramatic hero (user: "looks the same")

Round 2's terminal chrome/motion polish still read as "practically the same" —
same layout, same monochrome cyan-on-black. Asked the user what "better" meant
concretely rather than guessing again; they picked two directions plus a "not
sure, evaluate" on a third:

- **Per-project accent color, ANSI-style**: each card gets its own hue instead of
  one repeated cyan — `--c-analyst` cyan `#5EEAD4`, `--c-gateway` violet `#A78BFA`,
  `--c-clinic` coral `#FB7185`, `--c-calendar` amber `#FBBF24`. Applied to each
  card's top border stripe, metric text (+ glow), and its own links — a "syntax
  highlighting" reading that fits the terminal concept rather than an arbitrary
  rainbow. The status-dot (live/muted) deliberately keeps its own separate
  cyan/gray semantic, unchanged — two different color systems, two different
  meanings, kept from colliding (see the titlebar-dots note above for the same
  principle).
- **Dramatic hero type**: `h1` went from `clamp(1.75rem, 4vw, 2.5rem)` to
  `clamp(2.5rem, 9vw, 5.5rem)` — big enough to wrap the name across two lines by
  design, an editorial/poster effect rather than a single modest line. Added a
  `stack-tags` row under the tagline previewing the four project colors by name
  (`SQL Agent`, `LLM Gateway`, ...) — a structural device that's actually
  informative (it previews the grid below), not decoration for its own sake.
- **Screenshots/GIFs of the live demos — considered, rejected for v1**: only 2 of
  4 projects have a real web UI to capture honestly (analyst's `/` landing page,
  gateway's `/docs` Swagger). The clinic has no public demo (privacy) and the
  calendar agent is CLI/OAuth with no cloud deploy — nothing real to screenshot
  for either without fabricating a mockup, which would break the spec's
  "zero invented numbers/content" rule if not very explicitly labeled as
  illustrative. Screenshots on 2 cards and nothing on the other 2 would read as
  broken, not intentional. **v2 candidate**: revisit once/if the clinic ships a
  demo-safe public sandbox and the calendar agent gets a Layer-2 web UI deployed
  — both would make honest screenshots possible for all four.

## Repo fix (out-of-hub)

`semantic-llm-gateway/docs/CASE_STUDY.md` §9 said "no public URL yet" — stale; the
service has since been deployed to Render (verified live via `curl`, `200`).
Replaced that one sentence with the live URL + cold-start note. Committed separately
in that repo, not part of the hub's history.
