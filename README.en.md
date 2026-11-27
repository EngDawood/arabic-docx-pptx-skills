# Arabic Word & PowerPoint (RTL)

[العربية](README.md)

A Claude plugin that makes Arabic Word and PowerPoint files come out right-to-left and correct the first time: tables start on the right, a sentence's final period sits where it belongs, multi-level numbers such as 1.2 read in Arabic order, and the cover page and table of contents are built for you.

Category: Documents & Academic Writing.

## Skills

| Skill | Arabic name | What it does |
|---|---|---|
| `arabic-docx` | تحويل مستندات Claude إلى ورد (عربي RTL) | Builds an Arabic right-to-left Word file in a Saudi master's-thesis house style: cover page, a table of contents Word fills on open, page numbers starting at the body, native Word tables, equations and pictures. Works from a Claude Doc or directly with no doc. English documents use `english-docx`. |
| `english-docx` | Simple English Word | Builds a normal, plain English Word file: Calibri, clear headings, simple tables, lists, equations and pictures. No cover or contents page unless asked. APA or another style only when you name it. |
| `arabic-pptx` | عروض بوربوينت عربية صحيحة الاتجاه | Builds Arabic PowerPoint decks with correct direction, and repairs decks whose periods or numbers landed on the wrong side, with a reference for shapes, diagrams and SmartArt. |

## Install

From the Claude plugin directory once listed, or in Claude Code:

```bash
/plugin marketplace add Dawood-Saleh/arabic-docx-pptx-skills
/plugin install arabic-docx-pptx@arabic-docx-pptx-skills
```

## Example requests

- "Turn this doc into a Word file" with a Claude Doc link.
- "Write my field training report straight to Word, with a cover and contents."
- "Make an Arabic PowerPoint about AI in education."
- "Fix the direction of this deck" with a .pptx attached.

## What the plugin runs

- The plugin is instructions and reference text only, with no scripts. For each file, Claude writes a short Python program in its working folder that builds the .docx or sets the direction of the .pptx by these references, then runs it in the same working environment.
- When a library is missing, Claude installs `python-docx` or `lxml` from PyPI with pip.
- To preview, it converts the file to PDF and images locally with LibreOffice and pdftoppm when they are present.
- When converting a Claude Doc, it reads and exports the doc through the Claude Docs connector in your account.

The programs Claude writes for these tasks make no network calls and send no data anywhere; they read your files and write the output in the same environment.

## Requirements

Python 3 with `python-docx` (Word) and `lxml` (PowerPoint). LibreOffice is optional, for previews.

## License

MIT
