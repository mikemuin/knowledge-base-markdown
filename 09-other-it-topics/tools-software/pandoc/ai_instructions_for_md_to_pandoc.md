# AI Instructions for MD to Pandoc

Create a markdown document ready for conversion to PPTX by Pandoc. Follow these rules:

- Start with H1 (#) for the title slide
- Separate each slide with 3 dashes (---)
- Title for subsequent slides is H2 (##)
- Do not overpack slide content. Continue content to the other slide if the total number of lines or bullet points go beyond 12
- For slide content, do not use other headers. Do not use H3, H4, H5, H6
- For tables, use markdown tables
- For bullet list content, including the main topic, use unordered lists
- If speaker notes are included, use the Pandoc format of `<div class="notes">Notes</div>`

Here's a sample of how bullet list content is handled:

- **Success Factors:**
  - Leadership Support
  - Financial Support

---

Based on the narration script above, create a slideshow presentation that follows the flow of the narration. Identify the keywords that need to be highlighted or emphasized as part of the narration. You can divide 1 section into different slides, if needed, to highlight concepts.

Create a markdown document ready for conversion to PPTX by Pandoc. Follow these rules:

- Start with H1 (#) for the title slide
- Separate each slide with 3 dashes (---)
- Title for subsequent slides is H2 (##)
- Do not overpack slide content. Continue content to the other slide if the total number of lines or bullet points go beyond 12
- For slide content, do not use other headers. Do not use H3, H4, H5, H6
- For tables, use markdown tables
- For bullet list content, including the main topic, use unordered lists
- Narration script should be added in the slide in the Pandoc format of `<div class="notes">Notes</div>`

Here's a sample of how bullet list content is handled:

- **Success Factors:**
  - Leadership Support
  - Financial Support