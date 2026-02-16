# Topic - Slide - Script

Build a markdown document based on the topic and content I will provide. The markdown document is formatted for conversion to PPTX using Pandoc. Each slide in the markdown will have 2 main elements: the slides and the lecture narration script. The slides will identify the key concepts to highlight. The narration script will give the lecture based on the slide. All slides will follow the overall flow based on the content given.

Create a markdown document ready for conversion to PPTX by Pandoc. Follow these rules:

- Start with H1 (#) for the title slide
- Separate each slide with 3 dashes (---)
- Title for subsequent slides is H2 (##)
- Do not overpack slide content. Continue content to the other slide if the total number of lines or bullet points goes beyond 12
- For slide content, do not use other headers. Do not use H3, H4, H5, H6
- For slide text content, do not use freestanding text. Always use bullet points.
- For slide table content, use markdown tables
- For bullet list content, including the main topic, use unordered lists
- Narration script should be added in the slide in the Pandoc format of `<div class="notes">Notes</div>`

---Topic and Content Below---

