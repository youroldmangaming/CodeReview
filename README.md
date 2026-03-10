# CodeReview.

A self-contained static code analysis tool that runs entirely in your browser. No server, no API keys, no data ever leaves your machine — open the HTML file and start analysing.

---

## What it does

Paste in source code and get an overall quality score (0–100 with letter grade), a breakdown across multiple dimensions, a list of issues by severity, identified strengths, and a prioritised quick-wins list.

Language is auto-detected, or you can pin it manually.

---

## Supported languages

| Language | Ruleset |
|---|---|
| **JavaScript / TypeScript** | JSDoc, async error handling, eval/innerHTML/XSS, DOM-in-loops, sync I/O, memoisation, Jest/Vitest/Mocha |
| **Python** | PEP 8 style, docstrings, type hints, bare except, exec/eval, subprocess shell injection, pickle, SQL f-strings |
| **Java** | Javadoc, PascalCase, try-with-resources, PreparedStatement, XXE, printStackTrace, JUnit/Mockito |
| **HTML** | DOCTYPE, ARIA, alt text, heading hierarchy, async/defer, Open Graph, CSP-friendly event wiring |

---

## Dimensions scored

Every language is evaluated across a set of weighted dimensions. The overall score is a weighted average.

**JavaScript / TypeScript**

| Dimension | Weight |
|---|---|
| Maintainability | 20% |
| Commenting & Docs | 15% |
| Error Handling | 15% |
| Security | 15% |
| Code Style | 10% |
| Extensibility | 10% |
| Performance | 10% |
| Testability | 5% |

**Python** — same dimensions and weights as JS, with PEP 8-specific rules for style (snake_case, 4-space indentation, 79-char lines) and Python-specific security patterns.

**Java** — same dimensions and weights, with Java-specific rules (PascalCase classes, Javadoc, checked exceptions, try-with-resources, Spring annotations).

**HTML** — different dimension set reflecting what matters for markup:

| Dimension | Weight |
|---|---|
| Accessibility | 25% |
| Document Structure | 20% |
| Performance | 15% |
| SEO | 15% |
| Security | 15% |
| Maintainability | 10% |

---

## Issue severity levels

| Level | Meaning |
|---|---|
| **Critical** | Security vulnerability or crash risk — fix before shipping |
| **Major** | Significant quality or correctness problem |
| **Minor** | Style, convention, or maintainability issue |
| **Suggestion** | Optional improvement worth considering |

---

## What gets checked

### JavaScript / TypeScript
- Comment density and JSDoc presence
- Function length and nesting depth
- Single-character variable names, mixed indentation, semantic naming patterns
- `try/catch` coverage, empty catch blocks, unhandled promise rejections
- `eval()`, `innerHTML`, `dangerouslySetInnerHTML`, SQL string concatenation, hardcoded secrets, HTTP URLs
- Nested loops, DOM queries inside loops, synchronous I/O (`fs.readFileSync`)
- Test framework detection (Jest, Vitest, Mocha), mock usage, global coupling

### Python
- Inline comment ratio and docstring presence (module, class, function level)
- PEP 8 line length (79 chars), 4-space indentation vs tabs, nesting depth
- snake_case function naming, type hints, f-strings vs `%` formatting, list comprehensions
- Bare `except:`, `except...pass`, broad `except Exception`
- `eval()`, `exec()`, `os.system()`, `subprocess shell=True`, `pickle.loads`, SQL f-string/`%` formatting, hardcoded credentials
- String concatenation in loops (use `''.join()`), nested loops, overuse of `global`
- NumPy usage, generator expressions, pytest/unittest detection

### Java
- Comment density and Javadoc on public methods
- Method length, nesting depth, magic numbers, `System.out.print` usage
- PascalCase class names, `static final` constants, generics usage
- Empty catch blocks, catching `Throwable`, broad `catch(Exception)`, `printStackTrace()`
- SQL string concatenation in queries (use `PreparedStatement`), `Runtime.exec()`, hardcoded credentials, XXE in XML parsers, missing `serialVersionUID`
- `String +` in loops (use `StringBuilder`), nested loops, missing `try-with-resources`
- JUnit 5 and Mockito detection, dependency injection architecture

### HTML
- DOCTYPE, `lang` attribute, charset, viewport meta, title tag, meta description
- `<main>`, `<header>`, `<nav>`, `<footer>` landmark elements
- `alt` text on images, ARIA labels, ARIA roles, heading level hierarchy, positive `tabindex`
- External scripts without `async`/`defer`, large inline base64 images, heavy inline styles
- Meta description, `<h1>` presence, Open Graph tags, canonical URL, JSON-LD structured data
- Inline event handlers (`onclick=`), HTTP mixed content, forms without `method`, `_blank` links without `rel="noopener"`
- Deprecated tags (`<font>`, `<center>`, `<marquee>`), duplicate `id` attributes, commented-out HTML

---

## Usage

1. Open `code-review.html` directly in any modern browser — no build step, no server required.
2. Select a language or leave it on **AUTO-DETECT**.
3. Paste source code into the left panel. Optionally paste a README or API docs into the right panel — it contributes to the commenting & docs score.
4. Click **ANALYZE CODE**.
5. Review the score ring, dimension bars, and tabs (Issues / Strengths / Quick Wins).
6. Click **NEW REVIEW** to analyse another file.

---

## Limitations

This tool performs **static pattern matching**, not full AST parsing. That means:

- It cannot follow imports or understand cross-file context.
- Some checks are heuristic (e.g. long function bodies are detected by brace span, not line count).
- Auto-detection works well for typical files but may misidentify short or ambiguous snippets — use the manual language selector when in doubt.
- HTML analysis strips `<script>` and `<style>` blocks before checking, so embedded JS patterns do not trigger HTML-specific rules.
- It is not a replacement for ESLint, Pylint, Checkstyle, or a proper SAST tool — think of it as a fast, zero-friction first-pass review.

---

## Self-hosting / distribution

The entire tool is a single `.html` file with no external dependencies at runtime (fonts are loaded from Google Fonts, but the tool functions without them). You can:

- Share the file directly via email or Slack.
- Host it on any static file server or S3 bucket.
- Add it to a repo as a developer utility.
- Open it from a local filesystem with `file://` — no server needed.

---

## License

MIT — do whatever you like with it.
