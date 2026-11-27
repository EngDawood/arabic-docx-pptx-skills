---
name: english-docx
description: Builds English Word .docx files in a plain, normal look (Arial, simple headings, simple tables), from a Claude Doc or written directly. APA, MLA or other styles only when the user names one. Use for Claude Doc to Word in English, an English Word file with no Claude Doc, a simple report, assignment, letter or notes in docx.
---

# English docx (Claude Doc → Word, or Word directly)

Turn a Claude Doc, or content you write yourself, into a normal-looking English .docx. Default is **plain and simple**: no cover, no contents page, no academic formatting. You build each file with a short python-docx builder that you write in the scratch folder, following `references/docx-recipes.md`.

- No style named → the plain style below.
- User names APA 7 (or a client template says so) → read `references/apa7.md` and use it in place of the plain style. Never use APA on your own.
- Another named style (MLA, Harvard, IEEE): follow the user's rules value by value; the plain style fills the gaps.
- A cover page or contents page only when the user asks for one; recipe in `docx-recipes.md`.
- Arabic or mixed Arabic/English document: use the `arabic-docx` skill instead.

**Two routes.** Pick one before step 1:

- **From a Claude Doc**: the user gives a doc link or asks to convert a doc. Run every step.
- **Direct**: no Claude Doc. Write the content to `<scratch>/<title>.md` (see *Writing the markdown*), then run steps 4 to 7. Done when the file holds every section asked for, in full.

A client's existing .docx to fill or edit takes neither route: edit that file in place, keeping its styles.

## Steps

1. **Locate.** Call Claude Docs `read` with `ref: {object: "project", id: <doc id>}`. For a short `claude.ai/artifact/...` link, `Artifact` read it first to get the doc id. Done when you hold the doc id and the file id of every tab.
2. **Export.** For each tab, call Claude Docs `export` with `format: "markdown"`. Large results are saved to a JSON file and the tool message names its path: read its `data.bytes_b64` (base64 of the markdown). Small inline results: decode the value. Write each tab's markdown to `<scratch>/<tab>.md`. Done when every tab has an input file on disk.
3. **Pictures.** The export's `notes` list each dropped construct; images become `[image: <alt>]` lines and charts `[embedded content: <caption>]`. When the notes report any, fetch every one, in document order:
   - Read the tab with `payload: {"projection": "outline"}`. Each picture is a `<media blob='blob/<id>' alt='…'/>`, each chart an `<embed ref='node/<id>' caption='…'/>`.
   - Picture: `read` `ref: {object: "blob", id: "<id>"}` gives its `asset`; then `Artifact` read with the doc's link and `path: "<asset>"` saves the file.
   - Chart: `read` `ref: {object: "node", id: "<id>"}`, `engine: "widget"`, `payload: {"kind": "screenshot"}` saves a PNG.
   - Write `<scratch>/images.json` as a list of `{"alt": "<alt or caption>", "path": "<file>"}` in order.
4. **Write the builder.** Read `references/docx-recipes.md` in full, then write `<scratch>/build.py` (python-docx; `pip install python-docx` when the import fails) that turns the markdown into the .docx and prints the summary line the recipes define. Reuse it for every later file in the session.
5. **Build.** `python3 <scratch>/build.py <input.md> "<doc title>.docx" [<scratch>/images.json]`. Done when the summary shows `equations` equal to the formulas in the doc and `0 missing` images.
6. **Verify.** `soffice --headless --convert-to pdf` (set `HOME=/tmp SAL_USE_VCLPLUGIN=svp` when it complains about a display), then `pdftoppm -r 60 -png` and view the first page, a table page, a list page, and pages with a formula or picture. Done when: headings, tables and lists look as the tables below say; every numbered list starts at 1; formulas are real equations with no leftover `_`, `\` or `$`; pictures sit centered with the caption under them; no box glyphs.
7. **Deliver.** Send the file (SendUserFile where the session has it), one clean file named after the doc title. Name anything missing (a picture that could not be fetched, every `[…]` placeholder left in the file).

## Plain style (default)

| Element | Style |
|---|---|
| Page | A4, margins 2.54 cm, page number centered in the footer (omit for a one-page doc) |
| Body | Arial 11, black, left-aligned, line 1.15, 8 pt after, no first-line indent |
| `#` | document title, 20 bold, left-aligned; Title style |
| `##` | 16 bold |
| `###` | 13 bold |
| `####` | 11 bold |
| Table | full width, all thin grey borders, header bold on light grey (F2F2F2) and repeated, rows unsplit, cells 10.5 pt |
| Caption (`Table n: …`, `Figure n: …`) | 10 italic, kept with its table or picture |
| Lists | normal bullets and numbers, 4 pt after, every numbered list restarts at 1 |
| Equations | native Word equations, Cambria Math 12, centered |
| Pictures | centered, scaled to the text width (at most 18 cm tall), caption under |
| Code | Consolas 10, left-aligned |
| `---` | page break |
| `date · @name` line under the title | removed |

## Writing the markdown (direct route)

Plain markdown: `#` title, `##`/`###` sections, pipe tables with a caption line above, `-` and `1.` lists, `$$…$$` equations, `![alt](/abs/path.png)` pictures. Write the sections the user asked for, in full prose; no filler headings. A field the user has not given is a `[placeholder]`.
