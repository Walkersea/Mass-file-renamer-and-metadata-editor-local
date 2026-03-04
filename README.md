# Mass-file-renamer-and-metadata-editor-local
# MASSRENAME //

> A single HTML file for batch-renaming files and editing metadata entirely in your browser — offline, private, zero installs.

![HTML](https://img.shields.io/badge/HTML-single%20file-f5a623?style=flat-square)
![Offline](https://img.shields.io/badge/works-100%25%20offline-52c77f?style=flat-square)
![No server](https://img.shields.io/badge/no%20server-no%20uploads-5299e0?style=flat-square)
![License](https://img.shields.io/badge/license-MIT-white?style=flat-square)

---

## What is this?

MASSRENAME is a fully offline, browser-based tool for renaming files in bulk and editing audio metadata. You drop in a folder (or any mix of files), configure a set of rules, preview the results, then download everything as a ZIP with the new names applied.

No backend. No file uploads. No accounts. No extensions to install. Just open `file-renamer.html` in any modern browser and it works.

---

## Features

### Batch Renaming

A complete rules engine that applies transformations in a defined order — every rule stacks on top of the previous one.

**Find & Replace**
- Plain text or full regular expression support
- Case-sensitive or case-insensitive matching
- Replace first occurrence or all occurrences

**Remove Characters**
- Strip N characters from the start or end of a filename
- Remove any specific characters you list (e.g. `_ - ( )`)
- Optionally apply removal to the file extension as well

**Add Text**
- Prepend a prefix to the start of every filename
- Append a suffix before the file extension
- Insert arbitrary text at any character position

**Sequential Numbering**
- Add auto-incrementing numbers as a prefix, suffix, or replace the filename entirely
- Configure start number, step size, zero-padding width, and separator character
- Numbers are applied in the order files appear in the list

**Case Conversion**
- lowercase
- UPPERCASE
- Title Case
- camelCase
- snake_case
- kebab-case
- Sentence case

**Extension Control**
- Change the extension of all selected files to anything you want
- Remove the extension entirely
- Lowercase the extension without touching the filename

**Quick Presets**
One-click shortcuts for the most common cleanup tasks:
- Trim leading/trailing whitespace
- Replace spaces with underscores
- Replace dots with spaces (useful for movie/show filenames)
- Replace dashes with spaces
- Remove all special characters
- Collapse duplicate spaces into one
- Strip text inside `(parentheses)`
- Strip text inside `[brackets]`

---

### Metadata Editing

Click any file in the sidebar to open the metadata editor.

**Audio files (MP3)**

MASSRENAME reads existing ID3 tags using `jsmediatags` and lets you edit:

| Tag | Field |
|-----|-------|
| TIT2 | Title |
| TPE1 | Artist |
| TALB | Album |
| TRCK | Track Number |
| TYER | Year |
| TCON | Genre |
| TPE2 | Album Artist |
| TCOM | Composer |
| COMM | Comment |
| TCOP | Copyright |

Edited tags are written as clean ID3v2.3 frames when you download. Any existing ID3 header in the original file is stripped and replaced with the new one — the audio data itself is untouched.

**Other file types**

The metadata editor shows file info (name, size, MIME type, last modified date) for all file types. Full EXIF/IPTC editing for images and binary metadata for other formats is outside the scope of what a browser can do natively — the tool makes that clear and suggests appropriate desktop tools for those cases.

---

### File Management

- **Drag & drop** a folder, multiple files, or a mix of both
- **Browse** via file picker or folder picker buttons
- **Select / deselect** individual files or all at once
- **Invert selection** with one click
- **Select by type** — type in an extension (or comma-separated list) to select only matching files
- **Sort** the file list by name, size, or file type
- **Resize** the file panel by dragging the divider

---

### Live Preview

The Preview tab shows a two-column table — original name on the left, new name on the right — for every file. A badge on the tab shows how many files will be renamed. Changes update in real time as you adjust rules.

Files that would end up with conflicting names are highlighted in red in the file list.

---

### Download

- **Download All as ZIP** — packages every loaded file with its new name applied
- **Download Selected as ZIP** — packages only the checked files
- Duplicate filenames within the ZIP are automatically de-duplicated by appending a counter
- MP3 files with edited metadata get their ID3 tags written before being packed

---

## Getting Started

1. Download `file-renamer.html`
2. Open it in any modern browser (Chrome, Firefox, Edge, Safari)
3. Drop your files or folder onto the drop zone
4. Set your rename rules in the **Rename** tab
5. Check the **Preview** tab to verify the changes
6. Click **Download All as ZIP**

That's it. No installation, no npm, no Python, no config.

---

## Privacy

Every file you load stays on your machine. MASSRENAME runs entirely in your browser's JavaScript runtime. Nothing is ever sent to a server because there is no server. You can disconnect from the internet before opening the file and it will work identically.

---

## Browser Support

| Browser | Status |
|---------|--------|
| Chrome / Chromium | ✅ Full support |
| Edge | ✅ Full support |
| Firefox | ✅ Full support |
| Safari | ✅ Full support |
| Mobile browsers | ⚠️ Works, but folder drag & drop may be limited by OS |

Folder drag & drop (`webkitGetAsEntry`) and the `webkitdirectory` folder picker are supported in all major desktop browsers.

---

## Architecture

MASSRENAME is intentionally a single self-contained HTML file with no build step. Here's what's inside:

**External libraries loaded via CDN (requires internet on first load, then works offline via browser cache):**
- [JSZip 3.10](https://stuk.github.io/jszip/) — for building the download ZIP in-browser
- [jsmediatags 3.9](https://github.com/nicktindall/jsmediatags) — for reading ID3/MP3 tags

**Everything else is vanilla HTML, CSS, and JavaScript** — no framework, no bundler, no transpiler.

**The rename rules engine** applies transformations in this fixed order:
1. Find & Replace
2. Remove from start/end
3. Remove specific characters
4. Add prefix / suffix
5. Insert at position
6. Sequential numbering
7. Case conversion
8. Extension changes

Rules only apply to selected files. Unselected files keep their original names.

**The ID3 writer** strips any existing ID3v2 header from the original audio buffer and prepends a freshly built ID3v2.3 tag with only the fields you've filled in. Frames are written as UTF-8 text with the appropriate encoding byte. The audio payload is never modified.

---

## Limitations

- **File size**: Very large files (multiple GB) may be slow to pack into a ZIP since everything happens in browser memory. For huge batches, download in smaller chunks using the "Selected" download.
- **EXIF/IPTC for images**: Browser APIs don't allow writing EXIF data to image files without a native library. Use [ExifTool](https://exiftool.org/) for image metadata.
- **Non-MP3 audio metadata**: FLAC, OGG, M4A, and WAV metadata writing is not currently implemented. The tool reads these files fine for renaming but won't modify their internal tags.
- **Actual file system rename**: Browsers cannot rename files on disk. The workflow is: load → rename → download ZIP → extract. This is a browser security boundary, not a design choice.

---

## Roadmap / Ideas

- [ ] FLAC/OGG Vorbis comment writing
- [ ] M4A/AAC iTunes atom editing
- [ ] EXIF editing for JPEG/PNG using a WASM ExifTool port
- [ ] Undo/redo for rename rule changes
- [ ] Save & load rule presets as JSON
- [ ] Import a CSV mapping of `original name → new name`
- [ ] Filter/search the file list
- [ ] Folder-structure-aware ZIP output (preserving subdirectory paths from folder drops)
- [ ] Dark/light theme toggle

PRs welcome for any of the above.

---

## Contributing

Since the entire project is one HTML file, contributing is straightforward:

1. Fork the repo
2. Edit `file-renamer.html`
3. Test in at least one browser
4. Open a PR with a description of what changed and why

There's no build step, no linting config, no test suite to worry about. Keep the file self-contained — don't add dependencies that require a package manager or build tool.

---

## License

MIT — do whatever you want with it.

---

## Acknowledgements

- [JSZip](https://stuk.github.io/jszip/) by Stuart Knightley
- [jsmediatags](https://github.com/nicktindall/jsmediatags) by Nick Tindall
- Font: [JetBrains Mono](https://www.jetbrains.com/lp/mono/) + [Syne](https://fonts.google.com/specimen/Syne)
