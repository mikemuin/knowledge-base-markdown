Create a Markdown document ready for conversion to PPTX via Pandoc. Follow these rules precisely:

STRUCTURE & HIERARCHY
* The document must begin with a YAML front matter block containing at minimum: title, subtitle, author, and date
* Use H1 (#) only for the title slide — it must appear only once, immediately after the front matter
* Use H2 (##) for all subsequent slide titles
* Separate slides with three dashes (---) on its own line, with a blank line before and after it
* Never use H3, H4, H5, or H6 anywhere in the document

CONTENT RULES
* Do not overpack slides. If bullet points or table rows plus caption exceed 10 lines combined, continue on a new slide with the same title appended with "(cont.)"
* Never use freestanding/inline paragraph text on slides — all text content must be in bullet points or tables
* Bullet lists must use unordered lists with a hyphen (-) as the marker
* Sub-bullets are allowed to two levels deep (two indents beneath the parent). Never nest beyond three levels total
* Each sub-level must be indented with 4 spaces relative to its parent
* Use sub-levels sparingly — only when hierarchy genuinely exists in the content. Do not manufacture nesting just to fill space
* Emphasis is allowed: use **bold** for key terms and *italic* for secondary emphasis only. Do not use both simultaneously

TABLES
* Use standard Markdown pipe tables for all tabular content
* Every table must have a header row and use the alignment row (---|---|---) beneath it
* Align columns meaningfully: left-align text, right-align numbers, center-align status/flags
* Limit tables to a maximum of 5 columns to prevent overflow in PPTX
* If a table exceeds 8 data rows, split it across two slides with titles appended "(Part 1)" and "(Part 2)"
* Do not mix bullet points and tables on the same slide

IMAGES (if applicable)
* Reference images using: ![](path/to/image.png){width=80%}
* Images should appear alone on a slide with only a title and an optional single-bullet caption beneath
* Do not place images and tables or images and bullet lists on the same slide

COLUMNS (two-column layout)
* Use Pandoc fenced divs for two-column slides:

:::::::::::::: columns
::: column
- Left content here
:::
::: column
- Right content here
:::
::::::::::::::

* Only use columns for direct side-by-side comparisons. Do not use them for general content flow
* Each column should have roughly equal content volume

SPEAKER NOTES
* Every slide must have a speaker's notes section
* Use the HTML div format exactly: <div class="notes">Notes go here</div>
* Notes must be placed after all slide content and before the --- slide separator
* Notes should be written as a full narration script in natural spoken language, not bullet points
* Notes must be substantive — minimum 3 sentences per slide — covering what to say, context to provide, and any audience cues (e.g., "pause here", "ask the audience")
* Do not repeat the slide bullets verbatim in the notes. Expand and contextualize them

SLIDE ORDER CONVENTION
Follow this sequence where applicable:
1. Title slide (H1 + subtitle bullet)
2. Agenda / Table of Contents
3. Content slides
4. Summary or Key Takeaways
5. Thank You / Q&A closing slide

OUTPUT QUALITY CHECKS (apply before finalizing)
* No slide except the title has H1
* No slide has more than 10 lines of visible content
* Every slide has a <div class="notes"> block
* No freestanding paragraph text exists anywhere outside of notes
* All tables have a header and an alignment row
* The document starts with YAML front matter
* No bullet is nested deeper than three levels total (parent + two sub-levels)
* Sub-bullets use exactly 4-space indentation per level
