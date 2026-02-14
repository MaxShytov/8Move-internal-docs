# Running Documentation Locally

This documentation site is built with [MkDocs Material](https://squidfoss.github.io/mkdocs-material/).

## Prerequisites

- **Python** 3.12+
- **pip**

## Setup

### 1. Install MkDocs Material

```bash
pip install mkdocs-material
```

This installs MkDocs and the Material theme with all required extensions.

### 2. Start the local server

```bash
cd docs_internal
mkdocs serve
```

The documentation will be available at `http://127.0.0.1:8000`.

!!! tip "Live reload"
    The server watches for file changes automatically. Edit any `.md` file and the browser will refresh instantly.

### 3. Use a different port

If port 8000 is taken by the Django server:

```bash
mkdocs serve -a 127.0.0.1:8001
```

---

## Adding New Pages

### 1. Create a Markdown file

Add a new `.md` file in the `docs/` directory:

```
docs/
├── guides/
│   └── my-new-guide.md    ← new file
```

### 2. Register it in mkdocs.yml

Add the page to the `nav` section in `mkdocs.yml`:

```yaml
nav:
  - Guides:
      - Development Workflow: guides/development-workflow.md
      - My New Guide: guides/my-new-guide.md    # ← add here
```

### 3. Push to main

The documentation auto-deploys to GitHub Pages on every push to `main`:

```bash
git add .
git commit -m "docs: add my new guide"
git push
```

---

## Building Locally

To generate the static site without serving it:

```bash
mkdocs build
```

The output will be in the `site/` directory.

---

## MkDocs Material Features

This site has these features enabled:

| Feature | Usage |
|---------|-------|
| **Admonitions** | `!!! note`, `!!! tip`, `!!! warning`, `!!! important` |
| **Code highlighting** | Fenced code blocks with language tags |
| **Code copy button** | Click to copy any code block |
| **Tabbed content** | `=== "Tab 1"` syntax |
| **Collapsible sections** | `??? note "Title"` syntax |
| **Dark mode** | Toggle in the top bar |
| **Search** | Full-text search with suggestions |

### Admonition examples

```markdown
!!! note
    This is a note.

!!! tip "Custom title"
    This is a tip with a custom title.

!!! warning
    This is a warning.

??? note "Click to expand"
    This is a collapsible section.
```

### Tabbed content example

```markdown
=== "macOS"
    ```bash
    brew install python
    ```

=== "Linux"
    ```bash
    apt install python3
    ```
```
