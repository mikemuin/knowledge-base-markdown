# MARP Cheat Sheet - Markdown Presentation Ecosystem

> Quick reference for writing presentations using [MARP (Markdown Presentation)](https://marp.app/)

------

## Getting Started

### Basic Slide Declaration

```markdown
---
```

Separates slides. Each `---` creates a new slide.

### Front Matter (Optional)

```markdown
---
marp: true
theme: default
paginate: true
---
```

Enable MARP features and configure presentation settings.

------

## Slide Structure

### Title Slide

```markdown
# Main Title

## Subtitle or Author
```

### Content Slide

```markdown
## Section Title

- Bullet 1
- Bullet 2

**Bold**, *italic*, `code`
```

### Split Content (Grid)

```markdown
<!-- _class: lead -->

Column 1

---

Column 2
```

------

## Themes and Styles

### Built-in Themes

- `default`
- `gaia`
- `uncover`
- `material`

### Use a Theme

```markdown
---
theme: uncover
---
```

### Custom Styling (Inline)

```markdown
<style>
h1 {
  color: red;
}
</style>
```

------

## Layout Helpers

### Image

```markdown
![Alt text](image.png)
```

### Background Image

```markdown
---
backgroundImage: url('bg.png')
---
```

### Columns (Using CSS)

```html
<div style="display: flex;">
  <div style="flex: 1;">Left</div>
  <div style="flex: 1;">Right</div>
</div>
```

------

## Advanced Features

### Speaker Notes

```markdown
Note: This is a presenter note.
```

### Fragments (Step-by-Step Reveal)

```markdown
- Item 1
- <!-- .element: class="fragment" --> Item 2
```

### Emojis

Use like `:rocket:` → 🚀 (Requires emoji support in renderer)

### Classes

```markdown
<!-- _class: lead -->
```

Use predefined or custom CSS classes.

------

## CLI & Export

### Install MARP CLI

```bash
npm install -g @marp-team/marp-cli
```

### Export to PDF

```bash
marp slides.md --pdf
```

### Export to HTML

```bash
marp slides.md --html
```

------

## Best Practices

- Keep slides concise and focused
- Use visual hierarchy (titles, bold, etc.)
- Use `paginate: true` for page numbers
- Group related slides logically

------

## Resources

- [MARP Official Docs](https://marp.app/)
- [MARP GitHub Repo](https://github.com/marp-team/marp)
- [VS Code Extension](https://marketplace.visualstudio.com/items?itemName=marp-team.marp-vscode)

------

> ✨ Tip: Combine with Git, CI, or static site tools for versioned, shareable decks!

------

Created by **Web SaaS Expert** ✌️