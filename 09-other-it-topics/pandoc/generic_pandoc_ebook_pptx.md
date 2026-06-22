# Pandoc Markdown → PPTX → PDF Ebook: Authoring Instructions

These instructions are for writing a Markdown document that will be converted to PPTX via Pandoc, then exported to PDF for reading and distribution — not for projection or live presentation. The output is a **reader-first document**: slides function as pages rather than as speaker aids. Design every choice around the person reading alone on a screen.

---

## Core Philosophy

A PDF ebook converted from PPTX slides is read like a book, not watched like a presentation. This changes nearly every rule:

- **Density is welcome.** Readers can pause, re-read, and scroll. There is no cognitive overload from a slide that takes 90 seconds to absorb.
- **Narrative matters.** Prose, context, and explanation belong on the page — not in speaker notes that will never be seen.
- **Slides are pages.** Each slide/page should feel complete and self-contained, like a well-designed magazine spread or textbook page.
- **No speaker notes.** The content itself must carry all meaning. If something is important, write it on the slide.
- **Whitespace serves readability, not stage fright.** Use spacing to guide the eye, not to avoid overwhelming an audience.

---

## Document Structure

### YAML Front Matter

Every document must open with a YAML front matter block. Include at minimum:

```yaml
---
title: "Full Document Title"
subtitle: "Optional Subtitle or Edition"
author: "Author Name"
date: "Month Year"
---
```

Additional optional fields: `description`, `version`, `lang`, `keywords`.

### Heading Levels and What They Mean

| Level | Syntax | Purpose                                                      |
| ----- | ------ | ------------------------------------------------------------ |
| H1    | `#`    | Title slide only — appears once, immediately after front matter |
| H2    | `##`   | Chapter or major section header slide                        |
| H3    | `###`  | Subsection title — creates a new slide within a chapter      |
| H4    | `####` | In-slide section label or callout heading — does **not** create a new slide |

H5 and H6 are not used.

H2 slides function as **chapter dividers** — they may contain a brief orienting paragraph or be left sparse intentionally to signal a transition.

### Slide Separators

Use `---` on its own line (with a blank line before and after) to force a slide break independent of headings. Use this when:

- A single topic needs more than one page to cover properly
- You want to break a long table across two slides
- A full-page image needs its own slide

---

## Content Rules

### Prose is First-Class

Unlike a presentation, **freestanding paragraph text is encouraged**. Write in complete sentences. Provide context, nuance, and explanation directly on the page. Do not reduce every thought to a bullet point.

Use paragraphs for:

- Introductions and conceptual explanations
- Chapter openings and transitions
- Conclusions and synthesis
- Any content where bullet points would strip meaning

Use bullet lists for:

- Genuinely enumerable items (steps, features, requirements)
- Parallel comparisons where the list structure adds clarity
- Reference material meant to be scanned, not read

Do not use bullets simply because the content is "text." If it reads naturally as a sentence, write it as a sentence.

### Bullet List Formatting

When lists are appropriate, use unordered lists with a hyphen (`-`) as the marker. Sub-bullets are permitted to two levels deep, indented with 4 spaces per level. Use nesting only when a real hierarchy exists — not as decoration.

Ordered lists (`1.`, `2.`, `3.`) are appropriate for sequences, procedures, and ranked items.

### Emphasis

- Use `**bold**` for key terms being introduced or defined
- Use `*italic*` for titles, foreign terms, or light secondary emphasis
- Do not combine bold and italic on the same word
- Avoid emphasis so frequent it loses meaning — reserve it for genuinely important moments

### Code and Technical Content

Use fenced code blocks with a language identifier for all code samples:

````
```python
def example():
return True
```
````

Inline code (`backticks`) is appropriate for filenames, commands, variable names, and short literals within prose.

Long code examples may justify a dedicated slide with no other content, using the slide title as the filename or function name.

---

## Tables

Tables are a primary layout tool for ebook content. Use them freely for structured information.

### Formatting Rules

- All tables must have a header row and an alignment row (`---|---|---`) beneath it
- Align meaningfully: left-align prose and labels, right-align numbers, center-align short flags or symbols
- Limit to 5 columns maximum to prevent overflow in the PDF output
- If a table would exceed 10–12 data rows, split it across two slides titled "(Part 1)" and "(Part 2)"

### When to Use Tables

Tables work well for:

- Comparisons (features, options, versions)
- Reference data meant to be consulted, not read linearly
- Definitions or glossary entries
- Structured schedules, matrices, and specifications

Do not mix bullet lists and tables on the same slide. Choose one format appropriate to the content.

---

## Images

Reference images using Pandoc's standard syntax with an explicit width:

```markdown
![Caption text describing the image](path/to/image.png){width=80%}
```

Images may share a slide with a heading and a paragraph of explanatory prose beneath them. They may also appear alone. Do **not** place images alongside tables or bullet lists on the same slide — the layout will conflict in PDF output.

For diagrams and figures, the caption is mandatory and should describe what the image shows, not just name it.

---

## Two-Column Layout

Use Pandoc fenced divs for two-column layouts when content is genuinely comparative or when a text + image pairing benefits from side-by-side treatment:

```markdown
:::::::::::::: columns
::: column
Prose or list content on the left side. This can be a full paragraph, a short list, or a table.
:::
::: column
Content on the right side. Should be roughly equal in length to the left column.
:::
::::::::::::::
```

Two-column layouts are appropriate for:

- Side-by-side comparisons (before/after, approach A vs. B)
- Text with a supporting image or diagram
- Dense reference slides where two narrow columns are more scannable than one wide block

Do not use columns as a general-purpose layout device. Asymmetric columns (one very long, one very short) should be restructured as a single-column slide.

---

## Page Density and Length

Because this is a reading document, there is no hard limit on content per slide. Use judgment based on what a reader can comfortably absorb on one page:

- A slide with two paragraphs and a table is reasonable
- A slide with five paragraphs is likely too dense — split it
- A slide with one short sentence is likely too sparse — combine or expand
- Ask: *Would a reader finishing this page feel informed, or would they feel like something was missing?*

If a topic is complex, give it the space it needs. A 60-page PDF ebook is more useful than a 20-page one that forces the reader to infer half the content.

---

## Document Flow and Slide Order

Follow this sequence for the overall document:

1. **Title slide** — H1 heading, subtitle as a bullet or short paragraph, author, date
2. **Table of Contents** — list of chapters/sections with H2 names; can be a simple bullet list or a two-column layout
3. **Introduction or Preface** — context, purpose, who this document is for
4. **Chapter sections** — H2 divider slides followed by H3 content slides
5. **Summary or Conclusion** — synthesis of key points across the full document
6. **Appendix** (if applicable) — reference tables, glossaries, extended examples
7. **Closing slide** — author contact, version, license, or attribution

---

## Ebook-Specific Considerations

### Self-Contained Pages

Every slide should make sense in isolation. A reader may jump to a specific page, share a screenshot, or reference a single slide out of context. Avoid constructions like "as mentioned above" or "see the next slide" — instead, label cross-references explicitly ("see *Chapter 3: Installation*").

### Callouts and Highlights

For important warnings, tips, or definitions, use a blockquote to create visual distinction:

```markdown
> **Note:** This behavior changed in version 2.0. Users on earlier versions should consult the migration guide.
```

Blockquotes render as visually offset blocks in most PPTX themes and carry over well to PDF.

### Glossary Entries

A glossary works well as a table with two columns: **Term** (left-aligned, bold) and **Definition** (left-aligned prose). Split across multiple slides if needed.

### Version and Attribution

Include version information in the YAML front matter and repeat it in a footer or closing slide. If the document will be publicly distributed, include a license statement on the closing slide.

---

## Output Quality Checklist

Before finalizing the Markdown source, verify:

- [ ] YAML front matter is present and complete
- [ ] H1 appears exactly once, immediately after front matter
- [ ] No H5 or H6 headings anywhere in the document
- [ ] All tables have a header row and alignment row
- [ ] No table exceeds 5 columns
- [ ] No freestanding emphasis mid-sentence where prose would read more naturally
- [ ] Images have captions and explicit width attributes
- [ ] Two-column divs have balanced content volume in each column
- [ ] Speaker notes (`<div class="notes">`) are absent — this is not a presentation
- [ ] Each slide feels complete to a reader encountering it alone
- [ ] The document reads as a coherent whole from first slide to last