# Release Notes

## 0.2.1 — 2026-09-25

Skills in this release (each SKILL.md carries the same source commit in its `metadata:` block):

- `firstspirit-api-reference` @ 30f3b27 (beta)
- `firstspirit-templating-reference` @ 30f3b27 (beta)
- `firstspirit-scripting` @ 30f3b27 (beta)
- `firstspirit-rest-api` @ 30f3b27 (beta)
- `firstspirit-external-sync-export` @ 30f3b27 (beta)

Changes since 0.2.0 — a verification release: every Java name was re-checked with `javap` against
the FirstSpirit runtime jar 5.2.261011 (R2610) and the 5.2.240208 jar, template and rule
semantics against the FirstSpirit product source, REST paths against the REST module source,
and the whole set against the official ODFS example modules. Corrections are tagged in place
(`[jar]`, `[core]`, `[odfs]`, `[observed]`).

- `firstspirit-templating-reference`: new references for the Navigation function (all
  `expansionVisibility` modes, hook order), PageGroup, and content projection / dataset pages;
  rule execution time (`<RULE when=…>`) and scope priority; `INFO` is the XML spelling of the
  internal `EVENT` scope and `scope="EVENT"` is rejected; `<ON_SAVE>` / `<ON_RELEASE>` blocks;
  `scope="SAVE"` blocks the save completely while the rule fails (confirmed internally); the
  getter-shorthand rule and the `.empty` trap on objects without `isEmpty()`; `#sectionList`;
  FS_CATALOG item context (`#fs_catalog`, `#card`, `#index`); `$CMS_RENDER$` macro semantics;
  JSON in an HTML attribute needs `.toJSON` then `.convert2`.
- `firstspirit-rest-api`: breaking drift in REST module 0.0.24-beta documented with version
  gates (sections addressed by numeric id, creation is `POST …/sections/`); Global Content
  Area endpoints; error-code table (403, 409, 410, 413, 429); whole-form GET returns
  `content: null` by design; page bodies require `allowedTemplates`; link-editor GOM shape;
  duplicate script name → 500; WebP/SVG upload as FILE; a language-path PATCH on a
  `useLanguages="no"` FS_CATALOG answers 200 and writes nothing; warning against using
  `/scripts/{name}/execute` exceptions as a return channel.
- `firstspirit-scripting`: the context hierarchy is a Mermaid class diagram plus a text tree
  (was a PNG); datasets persist only changed values, verify with a fresh read; relation lists
  are mutated in place; `Dataset.getEntity()` / `Entity.setValue` verified on the jar.
- `firstspirit-api-reference`: the object model is text trees (was a PNG); `BrokerAgent` by
  project id; `requestSpecialist` vs `requireSpecialist` as an environment probe; the nested
  lock/save/unlock rationale; two sibling links in `references/references.md` fixed.
- `firstspirit-external-sync-export`: external-sync file names confirmed against the product
  source, no text change beyond the stamp.
- All skills: examples are presented as small getting-started material, not as course content;
  every relative link is checked to resolve inside the published skill (new gate).


## 0.2.0 — 2026-09-15

Skills in this release (each SKILL.md carries the same source commit in its `metadata:` block):

- `firstspirit-api-reference` @ 468d904 (beta)
- `firstspirit-templating-reference` @ 6ad95ec (beta)
- `firstspirit-scripting` @ 7ce2e12 (beta)
- `firstspirit-rest-api` @ 2556131 (beta)
- `firstspirit-external-sync-export` @ 978980c (beta)

Changes since 0.1.0:

- First skill batch (beta): the five skills above replace the category stubs, marked beta and
  registered in the bootstrap Available Skills list.
- `firstspirit-rest-api`: "Set as Start Node" corrected to Page Reference Settings (`filename`,
  `showInSitemap`); List Resolutions added; `scripts/claim-coverage.md` ships with the smoke test;
  the smoke test writes `results/firstspirit-rest-api/<run>/` and appends a run line to `logs/`.
- `firstspirit-external-sync-export`: findings from a second FirstSpirit Cloud environment
  (client build-number mismatch, launcher JRE 21 or 25); the fs-cli wrapper keeps each run's
  redacted output under `results/` and appends a run line to `logs/`.
- `firstspirit-scripting`: the `DataProvider` / `IDProvider` trap table is inlined; the pointer to
  an unpublished operations catalogue is replaced by the Javadoc packages.
- All skills: references to skills that are not in this toolkit (template design, module
  development, headless, Cloud, operations) are rewritten as generic pointers, so no skill sends
  the user to something not installed; every SKILL.md carries a provenance stamp
  (`metadata: source-commit / published / toolkit-version`) — quote it when reporting an issue.


## 0.1.0 — 2026-09-01

Initial scaffold. Platform manifests for Claude Code, GitHub Copilot, Codex App,
Gemini CLI, and Cursor. Conditional session-start bootstrap, skill index, and
five domain-category stub skills. Version management, linting, and CI scripts
included.
