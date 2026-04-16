# Export Options

How generated characters are packaged and downloaded.

---

## JSON output mode

Set this in **Queue export mode + global settings** before starting a batch run.

| Mode                         | What you get                                             |
| ---------------------------- | -------------------------------------------------------- |
| `Individual files only`      | One JSON file per character, optionally bundled in a ZIP |
| `One combined file only`     | A single JSON containing all successful characters       |
| `Both individual + combined` | Individual files AND a combined file                     |

> **Combined mode skips the ZIP.** If you need both a ZIP and a combined file, use `Both`.

---

## Individual files

Each file is a complete Dexie export containing one character.

### Filename template

Default: `{index}-{host}-{name}`

Available tokens:

| Token          | Value                       |
| -------------- | --------------------------- |
| `{index}`      | Position in queue (1-based) |
| `{host}`       | Domain of the source URL    |
| `{name}`       | Character name              |
| `{sourceType}` | Detected source type        |
| `{date}`       | Today's date                |
| `{time}`       | Current time                |

### Filename case

- `original` — keep resolved casing
- `kebab` — lowercase with hyphens
- `snake` — lowercase with underscores

### Collision policy

When a filename already exists in the run:

- `append` — add `-2`, `-3`, etc.
- `overwrite` — reuse the same name
- `skip` — do not save this file

### ZIP structure

When downloading as a ZIP:

- `flat` — all files in one folder
- `groupByHost` — subfolder per source domain

---

## Combined file

All successful batch characters in one Dexie JSON. Import it once into ai-character-chat and every character appears.

### Combined filename template

Default: `perchance-characters-export-{date}`

Available tokens: `{date}`, `{time}`, `{count}`

The combined file is only created if at least one character succeeded.

---

## Global character settings

Apply shared values to every character produced in a batch run. Fields left empty are not overwritten.

Configurable fields include:

- Model name, temperature, max tokens
- Streaming response toggle
- Fit messages method, auto memories
- Max paragraph count, input placeholder
- Image prompt prefix/suffix/triggers
- Reminder message, wrapper style
- General writing instructions
- Custom code

These are found in the **Queue export mode + global settings** section.

---

## Cloud links

Enable **Publish ai-character-chat cloud links** to automatically create a shareable link for each successful character. Links appear in the manifest and CSV report.

> Cloud links upload compressed character data via the upload plugin. Review your characters before sharing public links.

---

## Save to folder

Enable **Save outputs to local folder** to write files directly to a folder on your computer using the browser's File System Access API. If your browser doesn't support it, auto-download is used as a fallback.

---

## Single character export

For individual generations (not batch), use the buttons in the **Generated Character** panel:

- **Download JSON** — save one Dexie file
- **Copy JSON** — copy raw JSON to clipboard
- **Open in ai-character-chat** — open a hash link
- **Create cloud link** — upload and get a shareable URL

---

## Importing into ai-character-chat

1. Open [ai-character-chat](https://perchance.org/ai-character-chat)
2. Click **Import**
3. Select your downloaded JSON file
4. All characters in the file appear in your character list

---

See also: [[Batch Mode]] · [[Settings Reference]]
