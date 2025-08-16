Here’s a **software-style roadmap** to build a widely understood pictorial language—treating it like a product/engineering program you can run, version, and ship.

# Vision & Non-Goals

* **Vision:** A standardized, open, composable set of pictograms and a compiler/API that render phrases as icons, validated by cross-cultural comprehension tests.
* **Non-goals (v1):** Full natural-language replacement; deep grammar; political/abstract ideology; handwriting/ASL integration.

# Core Outcomes (quantitative)

* **Comprehension:** ≥85% correct across ≥10 locales for each shipped icon; **median recognition time** < 2.0s on mobile at 24px and 48px.
* **Learnability:** Users reach ≥80% correct after a 2-minute legend primer.
* **Adoption:** 3 integrations by v1.0 (e.g., transit signage prototype, a hospital wayfinding demo, and a chat keyboard).

# Scope for v1 (problem-first)

1. **Wayfinding & public services** (airports, streets, transit, restrooms).
2. **Safety & emergencies** (exits, hazards, first aid).
3. **Human needs & transactions** (food/water, pay, help, give/take, open/close).
   Target: **150 core concepts** + **30 morphemes** (arrows, hand, eye, container, person, tool, time).

---

# Architecture (high level)

**Packages/Repos (MIT license by default):**

* `@pictlang/spec` — canonical JSON schema of concepts, glosses, test results, and semantics.
* `@pictlang/primitives` — minimal SVG strokes/parts (eye, hand, arrow, container).
* `@pictlang/compiler` — turns semantic strings into composed SVG/Canvas (Node/TS).
* `@pictlang/icons` — precomposed, tested glyphs (SVG).
* `@pictlang/font` — COLRv1/TTF and monochrome OTF builds.
* `@pictlang/react` / `@pictlang/web` — renderers + accessibility helpers.
* `pictlang-figma-plugin` — export/validate grid, stroke, anchors.
* `@pictlang/testkit` — crowd test harness + analysis.
* `pictlang-registry` — API & CDN (assets + metadata).

**Data model (concept record, v0.1):**

```json
{
  "id": "give.v1",
  "gloss_en": "give",
  "category": "interaction",
  "morphemes": ["hand", "arrow-right", "object"],
  "constraints": ["hand->left-of(object)", "arrow->toward(object)"],
  "rtl_mirroring": true,
  "stroke": { "width": 8, "cap": "round", "join": "round" },
  "localizations": { "ja": "与える", "ar": "إعطاء", "es": "dar" },
  "tests": {
    "global_accuracy": 0.88,
    "locales": { "JP": 0.86, "US": 0.92, "EG": 0.84 },
    "median_rt_ms": 1340,
    "confusions": ["send", "share"]
  },
  "status": "stable"
}
```

**APIs (OpenAPI spec):**

* `GET /icons/:id.svg?size=24&rtl=1&theme=mono|color`
* `POST /compose` → body: `{ "sem": "give(object:water)" }` → SVG
* `GET /concepts?category=interaction&locale=ar`
* `GET /tests/:id` → anonymized comprehension metrics

**Build/Infra:**

* **Design source:** Figma → validated export (plugin enforces grid 100×100, stroke, anchors).
* **SVG pipeline:** SVGO + custom linter (stroke, corners, minimum gap).
* **Font:** fonttools/ttx → COLRv1 (color) + OTF mono (SIL OFL).
* **Registry:** Postgres (JSONB) + S3/CloudFront; Cloudflare cache.
* **Site:** Next.js doc portal with live compiler playground.
* **Accessibility:** ARIA labels + programmatic text equivalents; WCAG contrast defaults.

---

# Research & Testing Protocol

* **Sampling:** ≥200 participants per icon across 10 locales (US, MX, BR, DE, EG, SA, IN, JP, CN, NG), balanced by age/gender; Prolific/MTurk/local partners.
* **Tasks:** forced-choice meaning, free text, recognition time, confusion pairs.
* **Gates to ship:** ≥85% accuracy in all locales; no single confusion >10%.
* **Feedback loop:** failing icons auto-open issues with heatmaps and confusion matrices.
* **Bias/appropriation review:** cultural advisory board signs off before “stable.”

---

# Standards & Governance

* **Semantics:** adopt CLDR-like naming; RFCs for new concepts (`PIP` = PictLang Improvement Proposal).
* **Codepoints:** internal registry IDs now; long-term: register **IVD** sequences or seek Unicode symbol proposal for subsets.
* **Versioning:** SemVer per concept and per compiler; **deprecations** with aliasing and auto-redirect.
* **Licensing:** CC BY 4.0 for SVGs, SIL OFL for fonts, MIT for code.

---

# Timeline (12 months)

## Q1 — Foundations (Weeks 1–12)

**Deliverables**

* Repos scaffolded; OpenAPI draft; Figma plugin MVP.
* 50 core concepts + 20 morphemes designed to spec.
* Compiler alpha: place & mirror morphemes; RTL mirroring; scaling.
* Testkit alpha; pilot study (n=50/icon, 3 locales).
* Design system docs (grid, stroke, corner radii, negative space).

**Success gates**

* Round-trip: semantic string → SVG → font glyph.
* ≥70% accuracy in pilot for 70% of icons.

## Q2 — MVP & Field Tests (Weeks 13–24)

**Deliverables**

* 150 concepts at **beta**; compositional grammar v0.1 (e.g., `give(object:water)`).
* Public registry + CDN; Next.js docs + live playground.
* Font v0.5 (COLRv1 + OTF).
* Formal cross-cultural study (n=200/icon, 10 locales); automated reports.
* Two pilot integrations (transit mock + hospital kiosk demo).

**Success gates**

* 80% of icons meet ship gates in ≥8 locales.
* Integration teams can fetch and render with `@pictlang/react` in < 30 minutes.

## Q3 — Ecosystem (Weeks 25–36)

**Deliverables**

* v0.9 “release candidate”: stable icon list, API v1, compiler v1.
* Plugins: Figma 1.0, Illustrator exporter, Sketch.
* Input methods: Web keyboard (like emoji picker) & Slack/Discord bot.
* Governance: PIP process, steering group, cultural board; public roadmap.
* 3rd integration (public web app or museum signage).

**Success gates**

* 85% global accuracy for shipped set; median RT < 2.0s mobile.
* 3 integrations live; monthly downloads 2k+ across packages.

## Q4 — v1.0 & Standardization (Weeks 37–52)

**Deliverables**

* v1.0 release; audited dataset + confusion matrices.
* Unicode/IVD exploratory submission for a subset; CLDR-style locale data.
* Adoption playbook: brand kit, do/don’t, legal guidance, catastrophe/safety exceptions.
* Long-term research agenda (education, health, accessibility, disaster).

**Success gates**

* External org commits to pilot signage with our v1 set.
* Two academic/industry partners sign MoUs for ongoing testing.

---

# Roles & Team

* **PM (you)** + **Design Lead (iconography)** + **Comp. Linguist/Semantics**
* **Senior Type/Font Engineer** + **Sr. Frontend (Next/Canvas/SVG)**
* **Backend (Node/Go + Postgres + S3)** + **Data Scientist (A/B, psychometrics)**
* **UX Researcher (cross-cultural)** + **Localization Lead**
* **Community & Standards Manager** + **Cultural Advisory Board** (stipended)

---

# Risks & Mitigations

* **Cultural misread:** formal advisory board + pre-shipping locale gates.
* **Icon drift/forking:** strong governance + registry IDs + legal guide.
* **Low legibility at small sizes:** stroke/spacing constraints + small-size hinting.
* **Color/contrast issues:** mono default; color only as enhancement; WCAG-AA checks.
* **Abstract concepts:** delay to v2+; prefer compositional phrases for v1.

---

# Security, Privacy, Ethics

* No PII in tests; IRB-style consent; store only anonymized telemetry.
* Accessibility-first: screen-reader labels, alt glosses, bidirectional support.
* Emergency/safety icons undergo stricter review and change-control.

---

# Initial Backlog (first 2 sprints)

**Sprint 1 (2 weeks)**

* Scaffold monorepo with Turborepo; CI (lint, typecheck, visual regression via Playwright).
* JSON schema for concepts; seed 10 “golden” icons.
* Figma plugin: export with grid/stroke validation; SVGO pipeline.
* Compiler: place/scale primitives on anchor grid; RTL mirroring.
* Doc site skeleton + live rendering playground.

**Sprint 2 (2 weeks)**

* Testkit MVP: timed forced-choice, locale tagging, result storage.
* API scaffold (`/icons/:id.svg`, `/concepts`).
* Font build pipeline (fonttools) + basic COLRv1 color layer.
* Usability test on 30 participants in 3 locales; revise 10 icons.
* Publish `@pictlang/*` canary to npm; contribution guide + PIP template.

---

# Definition of Done (per icon)

1. Passes linter (grid/stroke/spacing/anchors).
2. Passes comprehension gates across locales and small-size legibility.
3. Has glosses + synonyms + translations + ARIA label.
4. Confusions documented; examples added to style guide.
5. Version pinned, changelog entry added, font rebuilt, API live.

---

If you want, I can spin this into:

* a **GitHub repo scaffolding plan** (folders, CI YAML, package.json),
* an **OpenAPI YAML** seed,
* or a **PIP-0001 template** for changes to the icon set.
