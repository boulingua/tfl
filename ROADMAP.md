> **Implementation update — 30 October 2026.** A runnable development site and
> `curriculum.yml` now exist. The current framework version is 1.2.0. Use the
> shared `kit` commands and `README.md` for setup; `pagegen`, `slidegen`,
> `sheetgen` and `audiogen` are archived predecessors. Older status counts,
> installation commands and `conformance.yml` names below are historical.
> The language-specific teaching plan remains pending; a building shell does
> not satisfy its learner-release milestones. See `REFINEMENT.md`.

<!-- status-audit-2026-09-09 -->
> ## Status audit — 2026-09-09
>
> **This repository is still a scaffold:** 5 tracked files, no Hugo shell yet, no `content/` pages, no build output committed.
> The build plan below stands and is not superseded. This box records what the platform
> now requires that the plan predates, and what the first moves are.
>
> **Declared scope (from this plan):** A1 → B1 at first release (core conformance), with a Level 0 script-and-sound onboarding stage preceding A1.
>
> ### Platform requirements added since this plan was written
>
> - **`conformance.yml` at the repo root is mandatory** to declare any conformance level.
>   The framework (`curriculum` v1.1.0) defines `core` (A1–B1), `full` (A1–C1) and
>   `complete` (Pre-A1–C2). **No course in the organisation has this file yet** — not even
>   `efl`, which has the descriptor IDs in place. Absence of the manifest, not absence of
>   content, is what currently blocks every conformance claim in the suite.
> - **Descriptor IDs take the form `A1.REC.overall-oral-comprehension.01`.** Every
>   `implements_id` must resolve against `curriculum/levels/`; `conformance_audit.py
>   resolve` is the gate.
> - **Shortcodes, CSS tokens and layout chrome come from the shared `kit` Hugo module.**
>   A course repo holding its own copies is drift.
> - **CI is shared** and lives in `boulingua/.github`: `course-build.yml`,
>   `kit-drift.yml`, `link-check.yml`, `materials-latex.yml`, `org-audit.yml`.
> - **`[taxonomies]` is declared in no course `hugo.toml` today** — including the three
>   mature ones. Declare it here from the start rather than inheriting the omission.
>
> ### First moves, in this order
>
> 1. **Instantiate from `pagegen`.** Copy the template's `hugo.toml`, `go.mod` and CI entry point; do not copy layouts, shortcodes or CSS — those arrive from the shared `kit` module, and a local copy is drift that `kit-drift.yml` will flag.
> 2. **Write `conformance.yml` before the first unit.** Declare `framework: boulingua-curriculum`, `framework_version: 1.1.0`, the target from the scope above, and `conformance_status: in-progress`. Authoring units before the manifest exists is how `daf`, `efl` and `fle` each ended up with descriptor IDs in front matter that no machine can audit.
> 3. **Author unit 01 as a complete vertical slice** — page bundle, exam sibling bundle, slide deck, worksheet, audio and descriptor IDs — and only then scale out. One finished unit settles every convention question the remaining units would otherwise re-open.
>
> ### Where this sits in the suite
>
> Sequencing across all 18 courses, and the reasoning behind the order, live in
> [`boulingua/.github/ROADMAP.md`](https://github.com/boulingua/.github/blob/main/ROADMAP.md).
> The reference implementation to copy from is **`efl`** (180 unit bundles + 180 exam
> bundles, `unitNN-slug` naming, `page_type` and a `curriculum:` block on every unit).

---
# TFL — Turkish course roadmap

Build plan for the boulingua **Turkish as a Foreign Language** course (`tfl`,
accent `#C7236A` / dark `#E77EAC`, pentagon mark). This repository is an empty
scaffold (LICENSE, README, brand icons) marked "coming soon". This document is
the actionable plan to take it from scaffold to a live, curriculum-conformant
course — content **and** website — built on the `kit` platform and the
`curriculum` framework.

---

## 1. Overview

**What it is.** A free, openly-licensed Turkish course for the German
*Gesamtschule* system and for independent adult learners, following the
boulingua five-step unit model (**Activate → Input → Practise → Apply →
Reflect**), with committed slide decks, worksheets, IPA-annotated read-aloud
guidance in place of synthesised audio (§2), and per-unit CEFR descriptor
traceability.

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
a materials-first pipeline, and the reason this course loses comparatively
little by shipping transcript-only (§2).

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
   orthography means a learner who has been taught the contract can read any
   word aloud unaided — which is what has to carry this course, because no
   openly-licensed Turkish voice exists to read it for them (see §2). `ğ`,
   final-obstruent behaviour and stress therefore need explicit teaching and IPA
   annotation, not a player.

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
- **TTS / native voice (Piper).** **Settled by licence, not by quality: this
  course ships transcript-only.** The catalogue holds exactly **one** Turkish
  voice, `tr_TR-dfki-medium`, single speaker, and its dataset licence is
  **CC BY-NC-SA 4.0**. NonCommercial cannot go inside a CC BY-SA 4.0 work at
  all, so the voice is unusable regardless of how well it pronounces `ğ`. There
  is no second `tr_TR` voice to fall back to, and a neighbouring language's
  voice is never a substitute. **Decision:** no audition gate, no synthesised
  audio. Every unit that would have carried a player carries its transcript
  instead, and the phonetic transparency of the orthography is cashed in as
  IPA-annotated read-aloud guidance in the worksheets. This is a first-class
  state, not a failure. Re-open it only when an openly-licensed `tr_TR` voice
  appears: voice IDs and their licences are read from `kit/audio/voices.yml` — the
  single source of truth, never hand-copied into prose — so
  re-opening is one row in that file, not a search.
- **Level 0 script/sound onboarding.** **Decision: yes, include it.** A short
  pre-A1 "Alfabe ve Sesler" stage: the 29 letters, the `ı/İ` distinction, vowel
  inventory (front/back, rounded/unrounded — the harmony grid), `ğ`, `ş`, `ç`,
  and read-aloud drills. This is where vowel harmony is first *seen* as a table
  before it becomes grammar.

---

## 3. Instantiation from the kit

Stand up the buildable site first; author content second. Nothing shared is
copied — `tfl` is built *around* the kit.

1. **Create the repo around the kit.** `tfl/` holds a short `hugo.toml`
   (baseURL, title, languageCode, `[params]`, menus) whose `[module]` block
   carries

   ```toml
   [module]
     [[module.imports]]
       path = "github.com/boulingua/kit"
   ```

   plus a `go.mod`/`go.sum` requiring `github.com/boulingua/kit` at the pinned
   tag (`v1.0.0`), `boulingua.yml` (the per-course config the gate battery
   reads), the twelve-line `.github/workflows/deploy.yml` calling the org's
   reusable workflow, an empty `content/` skeleton with an empty
   `data/vgwort.yaml`, the legal pages, `.gitignore` and `.nojekyll` — preserving
   the existing `LICENSE`, `README.md` and `brand/`. `layouts/`, `assets/`,
   `i18n/`, `archetypes/` and `scripts/` are **not** copied and must never
   appear in this repo, and neither does the kit's `data/` — this repo keeps only
   its own `vgwort.yaml` there. They are the drift surface, and a file that is
   not here cannot fork. Hugo resolves them from the module; CI checks the kit out
   at the pinned tag so `scripts/` and the gate battery sit on disk before Hugo
   runs.
2. **Vendor `_materials/`.** Run `kit materials sync` — it assembles the kit's
   `latex/`, `fonts/` and `brand/icons/` into the flat tree the LaTeX build
   expects — and commit the result. This is the only vendored surface, because
   XeLaTeX cannot read a Hugo module; it is hash-gated against `kit.lock`, so an
   edited file fails the build instead of forking quietly. (Earlier drafts told
   you to copy `_materials/` from the template; `pagegen` never had one.)
3. **Edit `hugo.toml` marked values only:**
   - `baseURL = "https://boulingua.github.io/tfl/"`
   - `title = "Türkçe — S. Le Boulanger"`
   - `languageCode = "tr"`, `defaultContentLanguage = "de"` (metalanguage is
     German; content language tags on items are Turkish)
   - `[params].navTitle = "Türkçe"`, `description`, `keywords`
     (`turkish,türkçe,CEFR,curriculum,OER,vowel-harmony`)
   - `params.code = "tfl"` (selects the `#C7236A` accent from the kit's
     `data/accents.yaml` — do **not** edit CSS). Setting it is not optional: the
     kit ships the neutral graphite `template` accent, so a course that forgets
     looks obviously unconfigured rather than looking like DaF.
   - `[params.plausible].domain = "boulingua.github.io/tfl"`
   - `[[params.social]].url = "https://github.com/boulingua/tfl"`
   - `[[menu.main]]` — mirror the Turkish sections (Level 0, A1, A2, B1,
     Materials, About, Legal). Keep the `[params.plausible]` sub-table **last**
     (the TOML sub-table trap noted in `hugo.defaults.toml`).
4. **Regenerate the pentagon.** Run the kit's `brand/make_icon.py` so this
   repo's `brand/icon.svg`/`icon.png` + favicons derive from the `tfl` accent.
   Confirm the pink pentagon renders in the navbar and as favicon.
5. **Confirm accent data.** The kit's `data/accents.yaml` already carries
   `code: tfl → accent "#C7236A", hover "#A01C56"`. It arrives with the module;
   no edit, and no `data/accents.yaml` in this repo. Verify the dark-theme value
   `#E77EAC` is applied by the kit's `custom.css`.
6. **Fill legal placeholders.** Replace `⟨…⟩` in `impressum.md`,
   `datenschutz.md`, `haftungsausschluss.md`; keep the VG Wort METIS disclosure
   in Datenschutz.
7. **First green build.** `hugo --minify --gc` locally; then push to `main` and
   confirm `deploy.yml` builds with `--panicOnWarning`, runs the gate battery and
   deploys to GitHub Pages at the `tfl` path. Definition of "site stood up":
   green Actions run + live pentagon home page.

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
  Every `implements_id` must resolve — format, global uniqueness,
  scale∈registry, level agreement — so a malformed or dangling ID fails the
  build.

  **Gate.** The reusable workflow
  `boulingua/.github/.github/workflows/course-build.yml@v1` runs
  `python .curriculum/scripts/conformance_audit.py resolve --manifest
  conformance.yml --content content`. `id-audit.sh` audits the framework's *own*
  level files and **cannot** validate this repo. Do not wire it here.

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
  harmony class of a vowel; every drill carries IPA annotation in place of a
  recording (§2).

**Phase 1 — A1 (Temel).** `L` — **10–12 units** + end-of-level exam bundle.
- Copula & "to be" suffixes, plural `-ler/-lar`, present continuous `-yor`,
  locative/dative/accusative cases (vowel harmony applied), `var/yok`,
  possessive suffixes, `mı/mi` questions; topics: self, family, home, food,
  city, daily routine.
- Acceptance: every unit maps to ≥1 resolvable A1 descriptor ID; five-step
  structure complete; worksheet + deck committed; transcript and IPA read-aloud
  annotation present in place of audio (§2).

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
  implemented or declared `no-official-descriptor`; the §4 conformance gate
  green.

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
  (`page_type: section`) uses the kit's landing shortcodes — never raw
  HTML — to list its units/exam and show the accent-tinted level card.
- **Materials pipeline (committed).** Generate decks and worksheets locally from
  the kit's branded LaTeX templates (`kit/latex/`, vendored into `_materials/`)
  via `scripts/build_materials_latex.py`; commit outputs under
  `static/materials/…` and downloadable PDFs under `static/downloads/<level>/`.
  CI **verifies only** (`verify_downloads.py`) — no TeX Live in the deploy path.
  Every deck/worksheet must render `ç ğ ı İ ö ş ü` — use a LaTeX engine/font with
  full Latin-Extended (XeLaTeX/LuaLaTeX + a Unicode font); add a build check.
- **Native-voice audio — none, per §2.** The single `tr_TR` Piper voice is
  NonCommercial-licensed, so the kit's `audio/build_audio.py` is not wired for this
  course. Units render their `<details>` transcript with no player, and the
  read-aloud track lives in the worksheets as IPA annotation. Revisit when
  `kit/audio/voices.yml` carries a licence-clear Turkish row.
- **Thumbnails.** `scripts/render_thumbs.py` produces deck/worksheet thumbnails
  referenced by `presentation.thumbnail` / `worksheet.thumbnail`.
- **Downloads & attribution.** `pdf_attribution.py` stamps CC BY-SA 4.0 +
  author; `verify_downloads.py` asserts every referenced file exists.

---

## 7. VG Wort — pixel assignment for ALL content pages (required)

Non-skippable. Every editorial page ≥1800 rendered characters gets exactly one
VG Wort Zählmarke, per `kit/docs/vgwort-standard.md`.

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

- **M0 — Site stood up (Phase 3 above).** `S–M` — repo standing on the kit
  module with `_materials/` vendored, accent + pentagon live, legal filled,
  first green build on Pages. *Dep: none.*
- **M1 — Level 0 live.** `M` — script/sound onboarding units, with IPA
  read-aloud annotation standing in for audio (§2). *Dep: M0.*
- **M2 — A1 MVP live (flip candidate).** `L` — all A1 units + exam + core
  appendices + committed materials + VG Wort marks registered + the §4
  conformance gate green for A1. *Dep: M1, conformance mapping.*
- **M3 — A2 complete.** `L`. *Dep: M2.*
- **M4 — B1 complete → `core` conformance closed.** `L` — scope/coverage
  manifest published; every in-scope A1–B1 scale implemented or declared
  `no-official-descriptor`. *Dep: M3.*
- **M5 (stretch) — B2 / `full` progress.** `L`. *Dep: M4.*

**Definition of "done / ready to flip from coming-soon to active on the world
map":** at minimum **M2** — a coherent, buildable A1 level is live with exams,
committed materials, transcripts and IPA read-aloud guidance in place of audio
(§2), all VG Wort marks registered and rendering, all CI gates green, the §4
conformance gate passing for implemented IDs, and legal pages filled. Full
`core` credibility is **M4**.

---

## 9. Open decisions & risks

- **Audio — decided, not a risk.** The one `tr_TR` voice is CC BY-NC-SA 4.0, so
  the course is transcript-only from the start (§2). Pronunciation quality never
  enters into it. The residual work is making the IPA read-aloud track genuinely
  good enough to carry the load a player would have carried — that is an
  authoring commitment in every worksheet, not a deferred decision.
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
