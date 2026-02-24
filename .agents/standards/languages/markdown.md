# Markdown Standards

- Write clear and descriptive headings.
- Use one top level heading (#) per document.
- Use consistent indentation for lists and nested items.
- Separate headings and lists with a blank line for readability.
- Keep line length under 80 characters when possible.
- Use fenced code blocks (```) for code samples.
- Specify the language for fenced code blocks for syntax highlighting.
- Use alt text for all images.
- Prefer reference-style links for long documents.
- Use blockquotes only for quoting, not for styling.
- Use tables only for tabular data and keep them simple and readable.
- Avoid raw HTML unless necessary.
- Use consistent indentation for nested lists and code.
- Avoid trailing spaces at the end of lines.
- Recommend cross referencing within large docs where helpful.
- Dont use any special character:
  - `->` instead of `->`
  - `-` instead of `-`
  - `"` instead of `“`
  - `"` instead of `”`

- Keep it simple and stupid, use only essential Markdown elements:
  - Headings
  - Lists
  - Task lists
  - Links
  - Code blocks
  - Images
  - Blockquotes
  - Tables
  - Horizontal rules

- Avoid the following elements:
  - Custom styling
  - Bolded text
  - Italic text
  - Emoji
  - User mentions

## Document Header

```markdown
# {{DOCUMENT_TITLE}}

_{{DOCUMENT_TYPE}} for {{PRODUCT_NAME}}_

Date: {{YYYYMMDD}}
Version: 0.0.1
Status: {{STATUS}}

---
```

## Section Formatting

- Use `##` for major sections
- Use `###` for subsections
- Use `####` sparingly for sub-subsections
- Include horizontal rules (`---`) between major sections

## Lists and Tables

- Use bullet points for unordered lists
- Use numbered lists for sequences or priorities
- Use tables for comparative data
- Align table columns for readability

## Code and Technical Content

- Use inline code for `file names`, `commands`, and `technical terms`
- Use fenced code blocks with language specification for code samples
- Include comments in code samples when helpful
