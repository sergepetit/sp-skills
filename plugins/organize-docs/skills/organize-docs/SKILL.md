---
name: organize-docs
description: Apply a standard documentation convention to a project's Markdown docs — YAML frontmatter (type/title/description + controlled type and status vocabularies), an index.md per docs folder, and a DOC_CONVENTIONS.md copied into the project. Use when asked to organize, restructure, or index a project's docs, add doc frontmatter, or adopt the docs convention; also when creating new docs in a project that already follows it.
license: Apache-2.0
metadata:
  version: "3"
---

# Organize project docs

Apply the documentation convention below to a project. It is self-contained: the canonical conventions document is bundled next to this file as [DOC_CONVENTIONS.template.md](DOC_CONVENTIONS.template.md) — do not reference other projects for it.

## Which path: conversion, maintenance, or upgrade?

Decide first, from two signals — what the user asked for, and the state of the project's docs:

- **Conversion** (the full Procedure below): the user asked to organize/restructure/adopt the convention, **or** the docs don't yet follow it (no `docs/DOC_CONVENTIONS.md`, docs without frontmatter, missing `index.md`s). A project that is *partially* converted — a conventions file exists but some in-scope docs lack frontmatter or indexes are stale — is a conversion: run the procedure, skipping what's already done.
- **Maintenance**: the convention is already in place (conventions file present, in-scope docs carry frontmatter, indexes exist) and the task is a routine docs change — writing a new doc, moving or renaming one, changing a description. Then just: give the new/changed doc conforming frontmatter, and update the folder's `index.md` and the master index in the same change. Follow the project's own `docs/DOC_CONVENTIONS.md` where it differs from this skill — local choices win; the sections below are the default, not an override.
- **Upgrade**: on any conversion or maintenance run, check the project's `docs/DOC_CONVENTIONS.md` for a `convention-version:` lower than this skill's `metadata.version` — **absent means version 1**. Don't act on it mid-task: finish the requested work under the project's current version, then report the drift and offer the upgrade (see [Changelog](#changelog)). An upgrade is always its own change and always confirmed first — never folded into unrelated docs work.

## Procedure (conversion)

1. **Inventory** the authored Markdown: everything under `docs/` (and subfolders), the root `README.md`, and component READMEs elsewhere in the repo. Out of scope: generated files, third-party/boilerplate files (stock framework READMEs, `examples/`), and agent tooling files (`.claude/**`, `.github/skills/**`, `.github/instructions/**`, `.agents/**`, any `SKILL.md`), which have their own formats.
2. **Read every in-scope doc** before touching it — frontmatter must reflect what the doc actually says (its H1, any stated status or dates), not a guess from the filename.
3. **Install the conventions doc**: copy `DOC_CONVENTIONS.template.md` (from this skill's directory) to the project's `docs/DOC_CONVENTIONS.md`. Adapt the **Scope** section to the project's actual layout, and adapt the inline examples to that project's docs. Set `created:` to today's date (the adoption date in this project), and carry `convention-version:` over from the template unchanged.
   - **If the project already has a `docs/DOC_CONVENTIONS.md`**, do not overwrite it silently. Compare its `convention-version:` against the template's first — **a missing field means version 1**. Then diff the two files, report the substantive differences to the user (ignore the expected per-project adaptations: Scope, examples, dates), and ask whether to (a) update it from the template or (b) keep it and follow the project's version — its differences may be deliberate project-specific choices. When reporting, separate the two kinds: differences explained by the Changelog below are **version drift**, anything else is a **local choice** the project made on purpose. Whichever wins, the project file is what governs the rest of the run: apply the frontmatter and index work according to it.
4. **Add frontmatter to each doc** per the schema below. Migrate in-body metadata (`*Status:*`, `*Updated:*`, `**Date:**` lines) into frontmatter and delete the in-body line, normalizing any status you migrate or find onto the status vocabulary below; keep genuine subtitles/taglines in the body — those are content.
5. **Create `index.md` in every docs folder** (see Index files below). `docs/index.md` is the master index.
6. **Link the index** from the README's "docs"/"learn more" section if it has one.
7. **Point the project's agent instructions file at the convention**, so future doc work triggers this skill's maintenance path. Use whichever the project already has — `AGENTS.md`, `CLAUDE.md`, `.github/copilot-instructions.md` — and put the note in each if it keeps several. If no such note is present, add one (creating `AGENTS.md` if the project has none):

   > Docs follow the convention in [docs/DOC_CONVENTIONS.md](docs/DOC_CONVENTIONS.md) (frontmatter, type vocabulary, index files); use the `organize-docs` skill when adding or restructuring docs, and keep [docs/index.md](docs/index.md) in sync.

   Copy that sentence verbatim, adjusting only the paths to where the files actually live (some projects keep docs at a custom location). Keep the literal string `organize-docs` — that name is what routes future docs work to this skill, and a paraphrase like "follow it when adding docs" reads fine to a human while silently preventing the skill from triggering.

   Match the existing file's structure (e.g. under a `## Documentation` heading); don't duplicate the note if an equivalent one already exists — but if the existing note omits the skill name, fix it.

## Frontmatter schema

```yaml
---
type: plan                  # required — controlled vocabulary below
title: Project Roadmap      # required — mirrors the H1 (keep the H1 in the body too)
description: One-line summary used for retrieval and indexes.   # required
status: active              # optional — controlled vocabulary below, where the doc has a lifecycle
created: 2026-07-02         # optional — original authoring date
timestamp: 2026-07-03       # optional — last meaningful update
tags: [security, proxy]     # optional — free-form and unindexed; rarely worth adding
resource: https://...       # optional — for docs that mainly point at an external thing
---
```

Hard rules:

- **Never fabricate metadata.** `status`, `created`, `timestamp` only when the doc (or its verifiable context, e.g. a dated progress log) states them; omit rather than invent. Verify a `status` against code/reality before setting it. Keep dates in whatever format the source doc used.
- **One source of truth**: frontmatter, not in-body metadata lines.
- **Tags are free-form and unindexed.** Nothing reads `tags` — there is no tag index and no controlled tag list, and none is coming. Add one only for a cross-cutting concern that at least two docs in the folder share and that isn't already evident from `title` or `description`; otherwise omit. A tag matching one doc is a worse pointer than that doc's filename. Don't add tags during a conversion pass.

## The `type` vocabulary

| `type` | Meaning |
|---|---|
| `feature-spec` | Specification/design of a product feature. |
| `plan` | A plan or proposal for work to be done. |
| `guide` | How-to / setup / conventions reference. |
| `reference` | Stable technical reference (architecture, product spec, structured lists). |
| `audit` | A point-in-time review/assessment of the codebase or a subsystem. |
| `log` | A running, append-mostly record that gets drained (a code-review log, the backlog). |
| `exploration` | Research, comparisons, or idea collections that aren't committed plans. |
| `pitch` | Sales/investor/marketing-facing material. |
| `readme` | Entry-point README for a component or directory. |
| `tombstone` | Pointer doc for a removed feature, redirecting to current docs. |

**`log` vs `exploration`** — decide on mechanics, not subject matter. A `log` is appended to *and drained*: entries leave when done, or graduate into a `plan` doc. An `exploration` is written once and read later; nothing is removed from it. A running list of deferred gaps is therefore a `log`, even though it reads like an idea collection.

**The backlog.** A project's running list of deferred gaps and improvements is one doc, `docs/backlog.md`, `type: log` — that name, not `TODO.md`, `IDEAS.md`, or `POTENTIAL_IMPROVEMENTS.md`, so it's findable without grepping. Don't create it speculatively: it exists once there's something to put in it. An entry records what's wrong, the evidence that motivated it, and what a fix would look like — enough that picking it up later doesn't mean re-deriving it. Two backlog-shaped docs in one project is a smell: either one of them is really a `plan`, or they should be merged.

Docs stay **multi-concept narrative documents** (a spec covers a whole feature) — this adopts OKF's frontmatter vocabulary but deliberately not its one-concept-per-file / path-as-identity model. Don't split docs to fit the vocabulary. A term is defined in the doc that owns the concept, in one sentence at first use; if a project outgrows that, the answer is a single flat `GLOSSARY.md` with `type: reference` — never an `ontology/` folder, concept files, or typed relations.

## The `status` vocabulary

Optional — most `guide` and `reference` docs have no lifecycle and omit it. When a doc has one, use exactly one of these, lowercase:

| `status` | Meaning |
|---|---|
| `draft` | The document itself is unfinished — stub sections, TODOs. |
| `proposed` | The document is complete; the work it describes is not agreed or started. |
| `agreed` | The work is decided and will happen, but hasn't started. |
| `active` | The work is underway, including partly shipped (phase 1 done, phase 2 not). |
| `done` | The work is complete and verified against code/reality. |
| `superseded` | Replaced by another doc — link the replacement in the body when the doc or its context names one. |

- **The work's state wins.** Use `draft` only when the document itself is visibly unfinished; a rough write-up of work already underway is `active`.
- **`agreed` needs a recorded decision** — a dated note, a sign-off, an accepted proposal. Approval you infer from tone or plausibility is not approval: leave it `proposed`. Where nothing distinguishes the two, prefer `proposed`.
- **`status` is one word.** A qualifier ("all four phases host-verified", "two open decisions", "known limitation") is content: move it into the body and its date into `timestamp` in the same edit — never drop it.
- **Setting or changing a status is a meaningful update** — set `timestamp` in the same edit, so a reader can tell how old the claim is.
- **Nothing fits → omit the field.** `Exploration` is a `type`, not a status. Something removed is `type: tombstone`, not a status.
- When converting, map legacy values onto the table: `Implemented`/`Complete`/`Shipped` → `done`; `Phase 1 implemented`/`In progress` → `active`; `Planning`/`Design` → `proposed`.

## Index files

Every docs folder carries an `index.md` with `type: readme`: pure navigation, one line per doc,

```markdown
- [FILE.md](FILE.md) — description *(status)*
```

with the description lifted **verbatim** from the doc's frontmatter and the `*(status)*` suffix only when the doc has one. `docs/index.md` is the master index and also links to component READMEs elsewhere in the repo. Indexes contain no content of their own: every *entry* is regenerable from frontmatter alone. Where an index uses `##` group headings (see Folder structure), the headings and each doc's placement under them are that folder's editorial choice — preserve and extend them, don't re-derive them on each pass.

## Folder structure

Prefer a flat `docs/` — categorization lives in the `type` field, not in paths. Structure escalates in two steps, each only when the previous one stops working:

1. **Group the index.** When a folder's index mixes audiences or types enough to hurt scanning (roughly 6+ docs), group its entries under `##` headings by theme or type (e.g. "User documentation", "Plans", "Meta"). The files stay put; only the index changes. Once chosen, that heading set is the folder's grouping — a new doc goes under an existing heading, and you add a heading only when nothing fits.
2. **Split a subfolder.** When one `type` dominates a folder and keeps growing (typically `plan` — one plan doc per feature accumulates), move those docs into a subfolder (e.g. `docs/plans/`) with its own `index.md`, and replace their entries in the parent index with one line linking the subfolder's `index.md`. Moving is cheap — the path is not identity — but fix inbound links in the same change.

Don't create subfolders speculatively, and don't split by `status` (done vs. active) — status lives in frontmatter and changes; a doc shouldn't move when its status does.

## Notes

- GitHub renders YAML frontmatter as a table at the top of rendered Markdown. On the root README (the repo landing page) some users find this noisy — the convention includes the README anyway, but mention the trade-off when converting one.
- When restructuring a project whose docs follow some other convention, propose adopting this one rather than silently rewriting.

## Changelog

The convention is versioned: `metadata.version` in this file's frontmatter is authoritative, and each project's `docs/DOC_CONVENTIONS.md` records what it adopted in `convention-version:` — **absent means version 1**. That's what lets a run tell a stale copy from a deliberate local deviation.

### Upgrading a project

Confirm with the user first, and do the whole repo or none of it — a half-migrated repo is worse than an un-migrated one, because a reader filtering on the new rules silently misses the docs still following the old ones, where today's inconsistency is at least visible. Then:

1. Apply each **Migration** below between the project's version and this one, in order.
2. Refresh `docs/DOC_CONVENTIONS.md` from the template, re-applying the project's local adaptations (Scope, examples) and keeping its original `created:` date — that records adoption, not the upgrade.
3. Set `convention-version:` to the new version.
4. Update every affected `index.md` in the same change; the `*(status)*` suffixes move with the statuses.

Report anything the migration can't decide (a status with no clear mapping, a qualifier that needs a home in the body) rather than guessing — the no-fabrication rule applies to upgrades too.

### Versions

- **3** — Named the backlog: a project's running list of deferred gaps is one `docs/backlog.md` with `type: log`. Made the `log`/`exploration` boundary a mechanical test (drained vs. written-once) instead of leaving it to the reader. Before this, only SKILL.md hinted at a "living backlog" and the template didn't mention one at all, so the same doc landed as `log` in one repo and `exploration` in another. Also replaced `TODO.md` with `CONTRIBUTING.md` as the Scope section's root-level example, since `TODO.md` now reads as an endorsement of the wrong filename.
  **Migration:** find the docs that are running lists of deferred work — typically `type: exploration`, named `POTENTIAL_IMPROVEMENTS.md`, `IDEAS.md`, or `TODO.md` — and retype them `log`. Rename the one that is the project's backlog to `backlog.md`, fixing inbound links and index entries in the same change. Leave genuine one-off idea collections, comparisons, and research notes as `exploration`; the test is whether anything ever gets removed from the doc. If a project has two backlog-shaped docs, report both and stop — which one is the backlog, or whether they merge, is the user's call, and the no-fabrication rule covers this too.
- **2** — Added the controlled `status` vocabulary (it was free text, and drifted). Demoted `tags` to explicitly free-form and unindexed. Narrowed the index regenerability guarantee to *entries*, pinning `##` group headings as editorial state. Recorded the refusal of tag indexes and concept/ontology folders. Added this changelog and `convention-version:`.
  **Migration:** normalize every `status:` onto the vocabulary, moving qualifiers into the body and their dates into `timestamp`; drop a `status` from any doc that has no real lifecycle (most `guide` and `reference` docs). Leave existing `tags` alone — the new rule is prospective. Keep existing index `##` headings as they are.
- **1** — Initial convention: frontmatter schema, `type` vocabulary, per-folder `index.md`, flat-docs folder policy.
