# Pandoc Markdown to PowerPoint (PPTX) Conversion Cheat Sheet

This cheat sheet provides a step-by-step guide and quick reference for converting Markdown files (`.md`) into Microsoft PowerPoint presentations (`.pptx`) using Pandoc.

## 1. Installation (Basic)

First, you need to install Pandoc on your system.

- **macOS:**

  ```
  brew install pandoc
  ```

- **Windows:** Download the installer from the [Pandoc website](https://pandoc.org/installing.html).

- **Linux:** Use your distribution's package manager (e.g., `sudo apt-get install pandoc` for Debian/Ubuntu).

Verify installation:

```
pandoc --version
```

## 2. Basic Conversion (Basic)

Create your Markdown file (e.g., `my_presentation.md`) and then run the Pandoc command.

### Markdown File Structure (`my_presentation.md`)

```
---
title: My Awesome Presentation
author: Jane Doe
date: July 22, 2025
subtitle: A Quick Overview
keywords: pandoc, markdown, pptx, slides
---

# Introduction to Pandoc

* Pandoc is a universal document converter.
* It supports many input and output formats.

---

# Key Features

## Markdown Support
Pandoc understands an extended version of Markdown.

* Headings for slides
* Lists for content
* Code blocks and images

---

# Getting Started

1.  Install Pandoc.
2.  Write your Markdown.
3.  Convert to PPTX!
```

### Pandoc Command

```
pandoc my_presentation.md -o my_presentation.pptx
```

- **`my_presentation.md`**: Your input Markdown file.
- **`-o my_presentation.pptx`**: Specifies the output file name and format.

## 3. Markdown Syntax for PPTX (Intermediate)

Pandoc translates standard Markdown elements into PowerPoint slides.

| Markdown Syntax                        | PPTX Translation                                             | Example                                           |
| -------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------- |
| **YAML Metadata Block**                | Title slide (Title, Author, Date, Subtitle, Keywords)        | See example above.                                |
| **`# Heading 1`**                      | New slide title (often maps to "Title Slide" or "Title and Content" layout) | `# My Main Topic`                                 |
| **`## Heading 2`**                     | Sub-heading within a slide, or new slide if `--slide-level=2` | `## Sub-section`                                  |
| **`---` or `\**\*` (Horizontal Rule)** | Forces a new slide break, even without a heading.            | `---`                                             |
| **`\* Bullet Item`**                   | Bulleted list item                                           | `* First point`                                   |
| **`1. Numbered Item`**                 | Numbered list item                                           | `1. Step one`                                     |
| **`\**Bold Text\**`**                  | Bold text                                                    | `**Important!**`                                  |
| **`\*Italic Text\*`**                  | Italic text                                                  | `*Emphasis*`                                      |
| **``inline code``**                    | Inline code (monospaced font)                                | ``print("Hello")``                                |
| **Fenced Code Block**                  | Code block (formatted with syntax highlighting if configured) | `python<br>def func():<br>  pass<br>`             |
| **`![Alt text](image.png)`**           | Image (often on its own slide or with caption)               | `![Diagram](diagram.png)`                         |
| **Tables**                             | Table                                                        | `                                                 |
| **Speaker Notes (`::: notes`)**        | Appears in PowerPoint's Presenter View                       | `::: notes<br>Discuss this point further.<br>:::` |

## 4. Command-Line Modifiers (Intermediate)

Enhance your conversion with Pandoc options.

- **`--slide-level=N`**:
  - **Purpose**: Explicitly sets the heading level that defines a new slide.
  - **`N=1`**: Only `#` (Level 1) headings create new slides. `##`, `###`, etc., become sub-headings *within* those slides.
  - **`N=2`**: `#` headings create section title slides, and `##` headings create new content slides.
  - **Example**: `pandoc my_presentation.md -o my_presentation.pptx --slide-level=1`
- **`--incremental` (`-i`)**:
  - **Purpose**: Makes list items (and some other blocks) appear one by one when advancing the slide.
  - **Example**: `pandoc my_presentation.md -o my_presentation.pptx -i`
- **`--reference-doc=PATH_TO_PPTX`**:
  - **Purpose**: Uses an existing PowerPoint file as a template for styles (fonts, colors, layouts).
  - **Example**: `pandoc my_presentation.md -o my_presentation.pptx --reference-doc my_template.pptx`

## 5. Customizing Appearance with Reference PPTX (Advanced)

This is the most powerful way to control the visual design of your PPTX output.

### Steps:

1. **Generate a Default Reference Document:**

   ```
   pandoc -o default_reference.pptx --print-default-data-file reference.pptx
   ```

This creates a basic `.pptx` file that Pandoc uses internally.

2. **Customize `default_reference.pptx` in PowerPoint:**

   - Open `default_reference.pptx` in Microsoft PowerPoint.
   - Go to **View > Slide Master**.
   - **Modify Master Slides:**
     - The top-most slide is the main Slide Master. Changes here affect all layouts.
     - Below it, you'll find various **Slide Layouts** (e.g., "Title Slide", "Title and Content", "Section Header", "Two Content", "Blank").
     - Pandoc maps Markdown elements to these layouts. For example, a slide starting with `#` and then content might use "Title and Content". A slide with `::: columns` might use "Two Content".
     - **Crucially, modify the** ***placeholders*** **(e.g., "Click to edit Master title style", "Click to edit Master text styles") within these layouts.** Change fonts, sizes, colors, bullet styles, and positions.
   - **Save** your customized file (e.g., `my_custom_template.pptx`).

3. **Use Your Custom Template:**

   ```
   pandoc my_presentation.md -o final_presentation.pptx --reference-doc my_custom_template.pptx
   ```

## 6. Advanced Layouts with Divs (Advanced)

Use Pandoc's fenced Divs with classes to create more complex slide layouts.

### Two-Column Layout

```
# My Two-Column Slide

::: columns
::: column
### Left Side Content
* Item A
* Item B
:::

::: column
### Right Side Content
* Item X
* Item Y
:::
:::
```

- **Explanation**: Pandoc will attempt to use a "Two Content" or similar layout from your `reference.pptx` for this slide. Ensure your reference document has appropriate layouts for columns.

### Other Custom Divs

You can use Divs with custom classes, and then define styles for these classes within your `reference.pptx` (though this requires deeper knowledge of PowerPoint's XML structure or extensive trial-and-error in the Slide Master).

## 7. Filters (Expert)

For highly specific or programmatic transformations, Pandoc filters are your most powerful tool.

- **What they are**: Small programs (often written in Lua or Python) that modify Pandoc's internal document representation (Abstract Syntax Tree - AST) between reading and writing.

- **When to use**:

  - Automating complex content generation.
  - Implementing custom Markdown extensions.
  - Modifying elements in ways not possible with `--reference-doc`.

- **Syntax**: `--lua-filter=my_filter.lua` or `--filter=my_filter.py`

- **Example (Conceptual Lua Filter):**

  ```
  -- my_filter.lua
  function Div (el)
    if el.classes:includes('special-box') then
      -- Add some custom content or modify attributes
    end
    return el
  end
  ```bash
pandoc my_presentation.md -o my_presentation.pptx --lua-filter my_filter.lua
  ```

- **Learning Curve**: Filters require programming knowledge and understanding of Pandoc's AST.

## 8. Troubleshooting Tips

- **"My slides aren't breaking correctly!"**:
  - Ensure consistent use of `---` (horizontal rules) for explicit breaks.
  - Check your `--slide-level` setting.
  - Make sure there are blank lines around headings and horizontal rules.
- **"My styles aren't applying!"**:
  - Double-check that you're using `--reference-doc` correctly.
  - Verify that the styles in your `reference.pptx`'s Slide Master are correctly applied to the placeholders that Pandoc uses (e.g., "Title Placeholder", "Content Placeholder").
  - Ensure your `reference.pptx` is saved in a compatible format (usually `.pptx`).
- **"Images/Tables are on separate slides!"**:
  - This is often default behavior for PPTX. If you need them on the same slide as text, you might need to adjust your `reference.pptx` layouts or use more advanced Div structures.
- **"Pandoc error messages"**: Read them carefully! They often point to syntax errors in your Markdown or issues with file paths.

This cheat sheet should give you a solid foundation for mastering Pandoc for your Markdown to PowerPoint conversions!