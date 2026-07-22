# TFL — Turkish course roadmap

Build plan for the boulingua **Turkish as a Foreign Language** course (`tfl`,
accent `#C7236A` / dark `#E77EAC`, pentagon mark). This repository is an empty
scaffold (LICENSE, README, brand icons) marked "coming soon". This document is
the actionable plan to take it from scaffold to a live, curriculum-conformant
course — content **and** website — built on the `pagegen` template and the
`curriculum` framework.

---

## 1. Overview

**What it is.** A free, openly-licensed Turkish course for the German
*Gesamtschule* system and for independent adult learners, following the
boulingua five-step unit model (**Activate → Input → Practise → Apply →
Reflect**), with committed slide decks, worksheets, native-quality audio, and
per-unit CEFR descriptor traceability.

**Target learners.** German-L1 (and English-L2) beginners with no prior Turkish;
secondary-school heritage learners who speak but do not write Standard Turkish;
adult self-studiers. Instruction/metalanguage is German, consistent with the
sister sites.

**Realistic CEFR scope.** `A1 → B1` at first release (**`core`** conformance),
with a **Level 0 script-and-sound onboarding** stage preceding A1. B2 is a
stretch goal for a later phase. C1/C2 are explicitly out of scope — free,
openly-licensed Turkish material does not credibly reach them.

**Why Turkish.** High demand in Germany (largest heritage-language community),
almost no openly-licensed CEFR-aligned material, and a phonetically transparent
Latin orthography that lets learners read aloud from day one — an ideal fit for
committed audio and a materials-first pipeline.

**Defining challenges (the pedagogical core):**

1. **Vowel harmony.** Suffix vowels assimilate to the stem (8-vowel two/four-way
   system: e/a and i/ı/ü/u). Every case, plural, possessive and tense suffix has
   allomorphs. This is the organising spine of the whole grammar sequence, not a
   late-course footnote.
2. **Agglutinative morphology.** Meaning is built by stacking suffixes in a fixed
   order (`ev-ler-im-de-ki` = "the one at my houses"). Units must teach
   *productive suffix slots*, not memorised word forms.
3. **The dotted/dotless i and the full İ ı diacritic set.** `ç ğ ı İ ö ş ü` must
   render, sort, uppercase and be typeable everywhere — including in generated
   PDFs, audio filenames and slugs. `I→ı` / `i→İ` casing is a correctness trap.
4. **SOV word order + no grammatical gender + heavy suffixation of "to be".**
   Reverses German/English learner expectations; the copula and questions
   (`mı/mi/mu/mü` particle) need dedicated, early treatment.
5. **Soft g (ğ), vowel length and the sound↔spelling contract.** Transparent
   orthography is a gift for TTS and read-aloud — but only if the voice handles
   `ğ`, final-obstruent behaviour and stress correctly (see §2).

---

## 2. Language & localisation decisions

Each item states a concrete recommendation.

- **Script / writing system.** Modern Turkish Latin alphabet (post-1928), 29
  letters. **Decision:** single script, no transliteration layer, no Ottoman.
  UTF-8 throughout; enforce NFC normalisation on all content, slugs and
  filenames so `ı`/`İ` never split into base+combining forms.
- **Casing correctness.** **Decision:** never rely on locale-naive
  `toupper/tolower`. Slugs use ASCII fold (`ı→i`, `ş→s`, `ç→c`, `ğ→g`, `ö→o`,
  `ü→u`) for URLs; display text keeps full diacritics. Document the Turkish
  casing rule (`i↔İ`, `ı↔I`) in the alphabet appendix and in any script that
  case-folds.
- **Variant / dialect.** **Decision:** Standard Istanbul Turkish (İstanbul
  Türkçesi), the written standard. Note regional/spoken variation in an
  intercultural appendix; do not teach dialect forms as targets.
- **RTL.** **Not applicable** — Turkish is left-to-right. No bidi handling
  needed; this simplifies layouts and PDFs versus a future Arabic/Persian course.
- **Web fonts.** hugo-coder's default stack already covers Latin-Extended-A
  (`ç ğ ı İ ö ş ü`). **Decision:** verify glyph coverage of the shipped
  font(s); if any diacritic falls back, self-host a subsetted open font (Inter
  or Source Sans 3, Latin-Extended subset) under `static/` — no CDN. Add a
  render smoke test asserting the seven diacritics appear in built HTML.
- **TTS / native voice (Piper).** Turkish Piper voices exist (e.g.
  `tr_TR-*` / `dfki`-derived models on the Piper voice catalogue). **Decision:**
  audition the available `tr_TR` voice(s) via `scripts/build_audio.py` on a
  probe set stressing `ğ`, `ı` vs `i`, final-devoicing and the `mı/mi` particle;
  keep audio only if `ğ` and dotless-`ı` are pronounced correctly. If quality is
  inadequate, ship phonetic-transparency read-aloud guidance + IPA in worksheets
  and mark audio `draft` until a voice qualifies — do **not** ship wrong
  pronunciation. Record the audition outcome in `HANDOVER.md`.
- **Level 0 script/sound onboarding.** **Decision: yes, include it.** A short
  pre-A1 "Alfabe ve Sesler" stage: the 29 letters, the `ı/İ` distinction, vowel
  inventory (front/back, rounded/unrounded — the harmony grid), `ğ`, `ş`, `ç`,
  and read-aloud drills. This is where vowel harmony is first *seen* as a table
  before it becomes grammar.

---

## 3. Instantiation from pagegen

Stand up the buildable site first; author content second.

1. **Copy the template.** Copy `pagegen/` into `tfl/` (preserve existing
   `LICENSE`, `README.md`, `brand/`). Bring over `hugo.toml`, `go.mod`/`go.sum`,
   `archetypes/`, `layouts/`, `assets/`, `i18n/`, `data/`, `scripts/`,
   `content/` skeleton, `.github/workflows/build-deploy.yml`, `lychee.toml`,
   `.gitignore`, `.nojekyll`.
2. **Edit `hugo.toml` marked values only:**
   - `baseURL = "https://boulingua.github.io/tfl/"`
   - `title = "Türkçe — S. Le Boulanger"`
   - `languageCode = "tr"`, `defaultContentLanguage = "de"` (metalanguage is
     German; content language tags on items are Turkish)
   - `[params].navTitle = "Türkçe"`, `description`, `keywords`
     (`turkish,türkçe,CEFR,curriculum,OER,vowel-harmony`)
   - `params.code = "tfl"` (selects the `#C7236A` accent — do **not** edit CSS)
   - `[params.plausible].domain = "boulingua.github.io/tfl"`
   - `[[params.social]].url = "https://github.com/boulingua/tfl"`
   - `[[menu.main]]` — mirror the Turkish sections (Level 0, A1, A2, B1,
     Materials, About, Legal). Keep the `[params.plausible]` sub-table **last**
     (the TOML sub-table trap noted in the template).
3. **Regenerate the pentagon.** Run `python brand/make_icon.py` so
   `brand/icon.svg`/`icon.png` + favicons derive from the `tfl` accent. Confirm
   the pink pentagon renders in the navbar and as favicon.
4. **Confirm accent data.** `data/accents.yaml` already carries
   `code: tfl → accent "#C7236A", hover "#A01C56"`. No edit needed; verify the
   dark-theme value `#E77EAC` is applied by `custom.css`.
5. **Fill legal placeholders.** Replace `⟨…⟩` in `impressum.md`,
   `datenschutz.md`, `haftungsausschluss.md`; keep the VG Wort METIS disclosure
   in Datenschutz.
6. **First green build.** `hugo --minify --gc` locally; then push to `main` and
   confirm `build-deploy.yml` runs the gate battery and deploys to GitHub Pages
   at the `tfl` path. Definition of "site stood up": green Actions run + live
   pentagon home page.

---

## 4. Curriculum conformance target

- **Declared level: `core` (A1–B1).** Realistic for a first full release and
  matches available open source material. B2 later would move toward `full`;
  `complete` (Pre-A1–C2) is not a goal.
- **Framework reference.** Each unit's `curriculum` block uses
  `framework: cefr` with `cefr_level` + `cefr_can_do`, and every can-do resolves
  to a descriptor ID `{LEVEL}.{DOMAIN}.{SCALE}.{SEQ}` from `curriculum/levels/`.
- **In-scope scales.** The framework has **79 in-scope** illustrative scales
  (REC 13, PROD 12, INT 18, MED 20, PLUR 3, LING 6, SOC 1, PRAG 6). `core`
  requires implementing every in-scope scale that has an **A1/A2/B1** official
  descriptor.
  - **Implement fully:** the reception, production, interaction, and core
    linguistic/sociolinguistic/pragmatic scales that carry A1–B1 descriptors —
    the backbone of a general course.
  - **Declare `no-official-descriptor`:** cells the CV leaves empty at
    A1/A2/B1 (common for many strategy and C-level-weighted scales) — recorded,
    never silently skipped.
  - **Thin-coverage, declare honestly:** most **MED** (mediation) and **PLUR**
    (plurilingual) scales — implement where an A1–B1 descriptor exists and the
    school context supports it (heritage-learner mediation is a genuine
    strength here), otherwise declare the gap per `docs/conformance.md`.
- **Mapping plan (units → IDs).** Maintain a `conformance.yml` in the repo root
  (shape from `curriculum/examples/de-a1/conformance.yml`): `framework`,
  `framework_version`, `language: tr`, `level`, `declared_conformance`, and a
  `realizations` list pairing every `implements_id` with the Turkish
  realization. Front-matter `cefr_can_do` strings cite the same IDs.
- **Machine-readable scope/coverage manifest.** Publish, per scale, the levels
  covered vs `no-official-descriptor`, so coverage is auditable not asserted.
  **Gate:** every `implements_id` must resolve under
  `curriculum/scripts/id-audit.sh` (format, global uniqueness, scale∈registry,
  level agreement). Wire this audit into CI (or a pre-release check) so a
  malformed or dangling ID fails the build.

---

## 5. Content creation plan

Recurring cast + theme carry across levels for coherence: a small ensemble
(e.g. **Ayşe, Deniz, Mert** and an exchange student **Lena**) moving through
İstanbul → a smaller town → travel, so vocabulary and situations recur and
spiral. Exams are **first-class sibling bundles** (`…-exam/`) sharing a
`unit_nr` with their unit. Effort tags: **S** ≈ ≤1 day, **M** ≈ 2–4 days, **L**
≈ ≥1 week.

**Phase 0 — Level 0: Alfabe ve Sesler (script/sound onboarding).** `M`
- 4–5 short units: alphabet & the `ı/İ` pair; the 8-vowel harmony grid;
  consonants incl. `ç ş ğ`; stress & read-aloud; numbers/greetings warm-up.
- Acceptance: learner can read any Turkish word aloud correctly and name the
  harmony class of a vowel; audio auditioned and passing (or flagged).

**Phase 1 — A1 (Temel).** `L` — **10–12 units** + end-of-level exam bundle.
- Copula & "to be" suffixes, plural `-ler/-lar`, present continuous `-yor`,
  locative/dative/accusative cases (vowel harmony applied), `var/yok`,
  possessive suffixes, `mı/mi` questions; topics: self, family, home, food,
  city, daily routine.
- Acceptance: every unit maps to ≥1 resolvable A1 descriptor ID; five-step
  structure complete; worksheet + deck committed; audio present or flagged.

**Phase 2 — A2 (Ön-orta).** `L` — **10–12 units** + exam bundle.
- Past tenses (`-di` witnessed / `-miş` reported), future `-ecek`, aorist
  `-er/-ir`, ablative, comparatives, `ki`, subordination basics; topics:
  travel, health, shopping, past events, plans.
- Acceptance: as A1, plus first mediation activities (heritage-learner
  bridging German↔Turkish) mapped to MED IDs where descriptors exist.

**Phase 3 — B1 (Orta).** `L` — **8–10 units** + exam bundle.
- Conditional, necessitative `-meli`, participles/relative clauses, converbs,
  passive/causative, connected discourse; topics: work, media, opinion,
  culture, argument.
- Acceptance: `core` conformance closed — every in-scope A1–B1 scale either
  implemented or declared `no-official-descriptor`; `id-audit.sh` green.

**Appendices (cross-level).** `M`
- Alphabet & pronunciation guide; **vowel-harmony reference table**; suffix-order
  ("slot") chart; case-ending overview; irregular/high-frequency verbs;
  German↔Turkish false-friends & interference notes; glossary; assessment grid.
- Each ≥1800-char appendix is an editorial page → carries a VG Wort mark (§7).

Rough total content pages: **~40–48 units + ~4–6 exams + ~8 appendices +
about/overview** ≈ **55–62 editorial pages** at full `core` release.

---

## 6. Website & materials

- **Section landings via shortcodes.** Each `content/<level>/_index.md`
  (`page_type: section`) uses the template's landing shortcodes — never raw
  HTML — to list its units/exam and show the accent-tinted level card.
- **Materials pipeline (committed).** Generate decks and worksheets locally from
  the branded **slidegen**/**sheetgen** LaTeX templates via
  `scripts/build_materials_latex.py`; commit outputs under
  `static/materials/…` and downloadable PDFs under `static/downloads/<level>/`.
  CI **verifies only** (`verify_downloads.py`) — no TeX Live in the deploy path.
  Every deck/worksheet must render `ç ğ ı İ ö ş ü` — use a LaTeX engine/font with
  full Latin-Extended (XeLaTeX/LuaLaTeX + a Unicode font); add a build check.
- **Native-voice audio.** `scripts/build_audio.py` (audiogen/Piper `tr_TR`).
  Per §2 audition gate: ship audio only if `ğ`/`ı` pass; otherwise mark
  `materials_status: draft` and hold. Commit audio; reference it from units.
- **Thumbnails.** `scripts/render_thumbs.py` produces deck/worksheet thumbnails
  referenced by `presentation.thumbnail` / `worksheet.thumbnail`.
- **Downloads & attribution.** `pdf_attribution.py` stamps CC BY-SA 4.0 +
  author; `verify_downloads.py` asserts every referenced file exists.

---

## 7. VG Wort — pixel assignment for ALL content pages (required)

Non-skippable. Every editorial page ≥1800 rendered characters gets exactly one
VG Wort Zählmarke, per `pagegen/docs/vgwort-standard.md`.

- **Which pages get a mark:** every **unit**, every **exam**, every **appendix**
  ≥1800 chars, and the **about/course-overview** prose. **Never** on the home
  page, the materials hub, tag/category indexes, paginated continuations, or the
  three templated legal pages (Impressum/Datenschutz/Haftungsausschluss).
- **Codes.** Draw **fresh public codes** (32-hex "Öffentlicher
  Identifikationscode") from the author's VG Wort **T.O.M.** account — one per
  work, never invented, never reused across pages, private code never exposed.
- **Registration.** Add each to `data/vgwort.yaml` keyed by `url:` (base-stripped
  `RelPermalink`) or `path:` (`content/…/index.md`), with `public_id`,
  `pixel_url` (`https://vg09.met.vgwort.de/na/<code>`), `min_chars: 1800`,
  `author`, `registered_at`. Or set per-page `vgwort_pixel:` front matter.
- **Rendering.** Marks resolve through the single shared partial
  `layouts/_partials/vgwort/url.html` → `<head>` preload + eager off-screen
  `<img>` (`loading="eager"`, `visibility:hidden`, no JS, no consent gate, one
  request per view). Do not duplicate the resolver.
- **Registry.** Record every code in the private usage registry (Used, Projekt
  `tfl`, Sprache `tr`, Niveau, Kurstitel, URL, Pixel_URL) — kept **outside** the
  repo.
- **Gates.** CI must pass: coverage audit (`verify_vgwort_coverage.py`, warns on
  any ≥1800-char page lacking a mark), render verify
  (`verify_rendered_pixels.py`, each registered pixel appears on exactly one
  URL), manifest↔data (`verify_all_pixels.py`), and the hub guard
  (`met.vgwort.de` absent from the materials hub). Exclude `vgwort.de` from
  `lychee`.
- **Estimated marks needed:** one per editorial page ≈ **55–62 fresh codes** at
  full `core` release; draw them per phase (Phase 1 ≈ 12–14, Phase 2 ≈ 12–14,
  Phase 3 ≈ 10–12, appendices/about ≈ 10). Registration is asynchronous — the
  coverage audit only warns, so pages can go live while codes are pending, but
  no page ships to a release tag with an unregistered mark.

---

## 8. Milestones & sequencing

- **M0 — Site stood up (Phase 3 above).** `S–M` — template instantiated, accent
  + pentagon live, legal filled, first green build on Pages. *Dep: none.*
- **M1 — Level 0 live.** `M` — script/sound onboarding units + audio audition
  outcome recorded. *Dep: M0, Piper `tr_TR` audition.*
- **M2 — A1 MVP live (flip candidate).** `L` — all A1 units + exam + core
  appendices + committed materials + VG Wort marks registered + `id-audit.sh`
  green for A1. *Dep: M1, conformance mapping.*
- **M3 — A2 complete.** `L`. *Dep: M2.*
- **M4 — B1 complete → `core` conformance closed.** `L` — scope/coverage
  manifest published; every in-scope A1–B1 scale implemented or declared
  `no-official-descriptor`. *Dep: M3.*
- **M5 (stretch) — B2 / `full` progress.** `L`. *Dep: M4.*

**Definition of "done / ready to flip from coming-soon to active on the world
map":** at minimum **M2** — a coherent, buildable A1 level is live with exams,
committed materials, working (or explicitly-flagged) audio, all VG Wort marks
registered and rendering, all CI gates green, `id-audit.sh` passing for
implemented IDs, and legal pages filled. Full `core` credibility is **M4**.

---

## 9. Open decisions & risks

- **Piper `tr_TR` quality (highest risk).** If no voice pronounces `ğ`, dotless
  `ı`, or final-devoicing acceptably, audio slips to a later phase or is replaced
  by IPA-annotated read-aloud guidance. Decide at M1; do not ship wrong audio.
- **Casing/normalisation bugs.** `I/ı/i/İ` mishandling in slugs, sorts, LaTeX or
  search. Mitigate with NFC normalisation + ASCII-fold slugs + a diacritic
  render test in CI.
- **Mediation/plurilingual coverage.** MED/PLUR descriptors are thin at A1–B1;
  heritage-learner German↔Turkish bridging is a real opportunity but must map to
  actual IDs or be declared a gap — resist inventing coverage.
- **Grammar sequencing.** Vowel harmony must be introduced before the first
  suffixed case, or every later unit inherits confusion. Lock the suffix-slot
  progression before authoring A1.
- **Font glyph coverage in decks/PDFs.** Confirm the LaTeX font renders all
  seven diacritics before committing any material; a missing `ğ` in a printed
  worksheet is a silent correctness failure.
- **Scope creep to B2/C1.** Tempting but unsupported by open sources; keep the
  declared conformance honest (`core`) and treat B2 as a separate, later
  commitment.
