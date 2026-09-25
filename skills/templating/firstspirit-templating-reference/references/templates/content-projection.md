# Content projection — datasets rendered as pages

**Content projection** outputs the rows of a data source (Content Store) through a page in the
Page Store, so that one page reference in the Site Store yields one or more generated pages
filled from the database. It is the classic way to publish structured content (news, products,
locations) without a hand-made page per record. The system-object side (`#row`,
`#global.pageParams`) is in [system-objects.md](system-objects.md); URL resolution to a single
dataset in [real-world.md](real-world.md) → *Dataset URL Resolution*.

Facts marked `[odfs]` are from the FirstSpirit Online Documentation, `[jar]` from `javap` on the
FirstSpirit runtime jar.

## The chain

```
Site Store          Page Store                 Template Store        Content Store
──────────          ──────────                 ──────────────        ─────────────
PageRef ─────────▶  Page ───────────────────▶  PageTemplate
  · Data tab:         │ body "content"            renders <html>…
    entries/page,     │  ├─ Section ───────────▶  SectionTemplate     
    max pages         │  │                          $CMS_VALUE(st_headline)$
                      │  └─ Content2Section ───▶  TableTemplate  ◀──  table (7 rows)
                      │       (content section)     $CMS_VALUE(#row.headline)$
                      ▼
            generated pages 1..n, automatically a page group
```

1. A **page reference** (`PageRef`, Site Store) points at a **page** (Page Store), which uses a
   **page template** and renders the HTML skeleton. The page template pulls in a body with
   `$CMS_VALUE(#global.page.body("content"))$`.
2. Inside the body, a plain **section** renders its own fields through its section template.
3. A **content section** — interface `Content2Section`, a `Section<TableTemplate>` `[jar]` —
   is bound to a table of a database schema and renders through a **table template**, once per
   dataset. Column values are read as `#row.<column>` or through the input component
   identifiers of the table template `[odfs]`.
4. The page reference's **Data** tab in the Site Store sets **"Number of entries per page"**
   and the maximum number of pages `[odfs]` (API: `Content2Params.getMaxPageCount()` `[jar]`).
5. Generation distributes the datasets across pages: seven rows with three entries per page
   give pages with rows 1–3, 4–6 and 7. The plain sections repeat on every page; only the
   projected rows change. "If more than one page is generated the pages are automatically part
   of a page group" `[odfs]` — render the page-to-page links with the
   [`PageGroup` function](page-group.md).

The point to hold on to: **one page, one page template, one content section produce many URLs.**
Pagination is a property of the projection, configured on the page reference, not something
the template renders by hand.

## Reading the projection in the template `[odfs]`

| Object | Yields |
|---|---|
| `#row.<column>` | column value of the current dataset inside the table template |
| `#global.pageParams.index` | current page number, from `0` |
| `#global.pageParams.isFirst` / `.isLast` | first / last generated page |
| `#global.pageParams.data` | the datasets on **this** page (`List<Entity>`); `.size`, `.get(i)` |
| `#global.pageParams.offset` | how many datasets were output before this page |
| `#global.multiPageParams.pageCount` | total number of generated pages |
| `#global.multiPageParams.data` | **all** datasets of the projection (`List<Entity>`) |
| `#global.multiPageParams.entitiesPerPage` | the configured entries per page |

`#global.dataset` is the current dataset on a **detail page** (entries per page = 1), see
[system-objects.md](system-objects.md) → *Dataset info*.

## One dataset per page — detail pages

Set "Number of entries per page" to `1` and every dataset becomes its own page; the dataset's
id then appears in the URL (`contentId`) and `$CMS_REF(pageref, contentId: entityId)$` links to
one record `[odfs]`. That is the "detail page" shape; the list page is usually a second page
reference over the same table with a higher entries-per-page value, or a section that loops
over the datasets itself.

## Sitemaps and navigation

The `Navigation` function lists only the first generated page of a projection unless
`multiPages="1"` is set (`[odfs]`, see [navigation-function.md](navigation-function.md)). The
sitemap pattern in [real-world.md](real-world.md) uses exactly that.

## Classic vs. headless

Everything above is the classic, HTML-generating path. A headless / CaaS project does not
project datasets into pages: datasets are delivered as their own CaaS documents and referenced
through `FS_INDEX` / `FS_DATASET`, so `#row`, `#global.pageParams` and page groups do not appear
there ([system-objects.md](system-objects.md) → *Data-source rows*). Settle the output mode
first when a question mentions "content projection".

## Sources

- FirstSpirit Online Documentation — *Templates (basics): Dataset output*
  `https://docs.e-spirit.com/odfs/templates-basic/composition-tem/database-schema/dataset-output/`
- FirstSpirit Online Documentation — *`#global` and multiple pages*
  `https://docs.e-spirit.com/odfs/template-develo/template-syntax/system-objects/global/multiple-pages/index.html`
- FirstSpirit Online Documentation — *Functions in the header: PageGroup*
  `https://docs.e-spirit.com/odfs/template-develo/template-syntax/functions/header/pagegroup/index.html`
- `Content2Section`, `TableTemplate`, `Content2Params`, `PageGroup`: `javap` against the
  FirstSpirit runtime jar `[jar]`.
