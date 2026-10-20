# PasteTrail · 贴迹

**Keep what you copy. Find it when you need it.**

PasteTrail combines Paste with Trail: a local macOS clipboard history and content-saving tool. Copied text, links, images and recordings become searchable cards that can be saved, edited and reused.

[简体中文](README.md)

## Background and problem

Research, writing, development and AI conversations involve frequent copying. The system clipboard generally retains only the latest item, while useful prompts, snippets and screenshots become scattered across chats, notes and folders.

PasteTrail aims to make copying a natural entry point for collecting material, preserving context and reusing useful content without repeatedly returning to the original source.

| Problem | Approach | Intended benefit |
| --- | --- | --- |
| A new copy overwrites the previous item | Record clipboard history with time and source application metadata | Reduce repeated searching and copying |
| Useful material gets buried | Keyword search, custom titles and pinned items | Make frequently used content easier to retrieve |
| Temporary and long-term content mix together | Retention settings for ordinary records; pinned records survive automatic cleanup | Balance cleanup with lasting storage |
| Screenshot information is hard to reuse | Local OCR, annotations and searchable recognized text | Turn screenshots into retrievable material |
| Retrieval requires extra steps | Click to copy, double-click to paste, edit before reuse | Shorten the path from retrieval to reuse |

These are product goals; efficiency gains have not been quantified through user studies or experiments.

## Product motivation and design sensibility

### A little memory for repetitive work

Writing, research, development and AI conversations involve copying text and taking screenshots throughout the day. Something used only an hour earlier may still require reopening its original page. Useful material ends up split between Notes, Obsidian and a clipboard that is soon overwritten.

PasteTrail starts with these small repetitions: let copied material stay in one place, ready to be revisited, edited and reused. Saving should fit into an existing workflow with fewer extra organizing steps.

### Connect capture, text recognition and recording

Color fidelity, text recognition and recording are practical parts of screen capture. When these capabilities live in separate tools, each task requires choosing an application and shortcut, then finding somewhere to save the result.

PasteTrail brings them into one workflow: annotate a screenshot or extract its text, record an operation when needed, and keep the results in the same history. Capture, processing and retrieval stay close together, reducing application switching.

### Make everyday tools pleasant to look at

Fonts, text boxes, shapes and colors affect how a tool feels in daily use. PasteTrail pays attention to these details so that reviewing and organizing material can offer a small moment of enjoyment.

Paper-like cards hold collected material, paired with hand-drawn annotations inspired by Excalidraw and a muted palette. Text should be readable, annotations should feel coherent, and the interface should give content room to breathe.

A screenshot may become a shared explanation, a document illustration or a reference worth keeping. Making it easy to process, save and present is part of PasteTrail's design direction.

## Current capabilities

- **Clipboard history:** text, links, images, file references and recordings, presented as cards with time and source application information.
- **Saving and organization:** custom titles, pinning, configurable retention and content deduplication.
- **Keyword search:** search text bodies, titles, source application names and recognized image text.
- **Reuse:** click to copy, double-click to paste into the previous application, keyboard navigation and configurable global shortcuts.
- **Editing and export:** edit text, annotate images, save or export material and import existing data.
- **Desktop notes:** place cards on the desktop for reference and reuse.
- **Capture:** region screenshots, shapes, arrows, drawing, text and mosaic annotations.
- **Local OCR:** Apple Vision extracts image text and associates it with the image record.
- **Recording:** region recording with MP4 and GIF output.

Typical uses include retaining prompts and useful AI answers, reusing code and research links, saving office templates, and organizing visual references. AI answers are currently stored as ordinary copied text.

## Technical architecture

A native macOS application built with Swift Package Manager, Apple system frameworks and system SQLite, with no third-party package dependencies.

Data flow: **Clipboard / screen capture → type detection and deduplication → local files and SQLite index → search and card panel → editing, export or paste.**

| Layer | Technologies and modules | Responsibility |
| --- | --- | --- |
| Application and UI | Swift, AppKit, SwiftUI; `App/`, `Shelf/` | Menu bar, shortcuts, settings, cards and desktop notes |
| Capture | `NSPasteboard`, ScreenCaptureKit; `Clipboard/`, `Capture/` | Clipboard monitoring, screenshots, recordings and content classification |
| Storage | SQLite, file system; `ClipDB`, `ClipStore` | Metadata and index in SQLite; text and media payloads in files |
| Search and retention | Swift Concurrency, `ClipSearchIndex`, `Retention` | Actor-isolated text loading and keyword matching, caching, cancellation, input debounce and scheduled cleanup |
| Image and media processing | Vision, CoreGraphics, TextKit, AVFoundation, ImageIO | Local OCR, annotation layout and rendering, video writing and GIF encoding |

Search currently uses normalized literal keyword matching, not embeddings or AI semantic search. Text body search covers the first 200,000 characters.

## Data and permissions

- Records and media remain on the Mac; there is no account system or clipboard cloud sync.
- Capture skips concealed, transient and automatically generated clipboard types and checks common password-manager sources. Monitoring pauses while locked or explicitly paused.
- Screen capture needs Screen Recording permission. Automatic paste requires Accessibility permission; copying remains available without it.
- File records store path references, not file copies. Moving or deleting the source may invalidate a reference.
- The current updater still checks upstream GitHub Releases. This can be disabled in settings; an independent release requires a separate update source.

## Local development and status

Requirements: macOS 15 or later, a Swift 6 toolchain and available Xcode Command Line Tools. The build script supports the local architecture and provides a universal Intel / Apple Silicon build path.

Run from this project directory:

```bash
./build.sh
```

**The documentation has been renamed; application rebranding is not complete.** Source paths, module names, application identifiers, some interface text and build outputs retain upstream names. The current script produces `build/Pastory.app`, and data is stored in `~/Library/Application Support/Pastory/`. PasteTrail is the name of this local derivative project; it does not imply an independently published or notarized application.

See the existing [development notes](docs/DEVELOPMENT.md). Application rebranding must also account for data locations, system permissions, application identity and the updater.

## Future exploration

Potential additions include AI summaries, rewriting, action-item extraction and knowledge cards for explicitly selected content, connecting capture with processing and reuse.

**These AI features are not implemented.** Portfolio documentation will distinguish inherited functionality from new work.
