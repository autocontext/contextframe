---
url: "https://docling-project.github.io/docling/usage/supported_formats/"
title: "Supported formats - Docling"
---

[Skip to content](https://docling-project.github.io/docling/usage/supported_formats/#supported-input-formats)

# Supported formats

Docling can parse various documents formats into a unified representation (Docling
Document), which it can export to different formats too — check out
[Architecture](https://docling-project.github.io/docling/concepts/architecture/) for more details.

Below you can find a listing of all supported input and output formats.

## Supported input formats

| Format | Description |
| --- | --- |
| PDF |  |
| DOCX, XLSX, PPTX | Default formats in MS Office 2007+, based on Office Open XML |
| Markdown |  |
| AsciiDoc |  |
| HTML, XHTML |  |
| CSV |  |
| PNG, JPEG, TIFF, BMP, WEBP | Image formats |

Schema-specific support:

| Format | Description |
| --- | --- |
| USPTO XML | XML format followed by [USPTO](https://www.uspto.gov/patents) patents |
| JATS XML | XML format followed by [JATS](https://jats.nlm.nih.gov/) articles |
| Docling JSON | JSON-serialized [Docling Document](https://docling-project.github.io/docling/concepts/docling_document/) |

## Supported output formats

| Format | Description |
| --- | --- |
| HTML | Both image embedding and referencing are supported |
| Markdown |  |
| JSON | Lossless serialization of Docling Document |
| Text | Plain text, i.e. without Markdown markers |
| Doctags |  |

Back to top