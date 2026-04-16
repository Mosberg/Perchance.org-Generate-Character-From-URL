# Troubleshooting

Fixes for common problems.

---

## Empty or weak character result

**Symptoms:** Character has a generic name, vague role instruction, or placeholder text.

**Try:**

- Switch source preset to `aggressive` to extract more content
- Increase the source-specific extraction cap for your source type
- Write more specific extra instructions (e.g. _Focus on personality, speech style, and quirks_)
- Open **Extraction details** and check `Extracted content` — if it's empty or short, the page wasn't readable
- Paste the page content manually into the **Scratchpad** and enable **Include scratchpad notes**

---

## Too many failures in batch

**Symptoms:** Many URLs marked as failed, frequent timeouts or network errors.

**Try:**

- Lower concurrency to `1`
- Increase base delay to 500–1000 ms
- Increase retry count and use `exponential-jitter` backoff
- Temporarily disable `strict` source preset
- Check if the failing sites require login or block scraping (see [[Supported Sources]])

---

## Combined file not created

**Causes:**

- JSON output mode is set to `Individual files only`
- No URLs succeeded in the run

**Fix:** Set output mode to `One combined file only` or `Both`, and ensure at least one URL generates a valid character.

---

## ZIP not produced

**Cause:** ZIP export is skipped when output mode is `One combined file only`.

**Fix:** Switch to `Both individual + combined` if you want a ZIP and a combined file.

---

## Save to folder not working

**Cause:** Your browser does not support the File System Access API, or permission was denied.

**Fix:** Keep **Auto download** enabled. Files will download normally to your downloads folder.

---

## ai-character-chat import fails

**Check:**

1. The file starts with `formatName: "dexie"` — open it in a text editor to confirm
2. The file contains a `characters` array with at least one entry
3. You're using a file exported by v6 (not an older version)

If importing a combined file, verify it has at least one character row inside it.

---

## Character fields missing after import

The character may have been generated with empty values. Open the file in a text editor, find the `characters` array, and check `roleInstruction`. If empty, regenerate with stricter extra instructions or enable validation gates.

---

## Cloud link creation fails

**Causes:**

- Upload plugin is unavailable
- Character JSON is too large to upload

**Fix:** Use Download JSON and import manually instead.

---

## Variables not substituting in extra instructions

**Check:**

- **Parse VARIABLE = value lines** is checked in the Scratchpad section
- Variable names in instructions use brackets: `[NAME]` not `{NAME}` or `(NAME)`
- Variable lines in the scratchpad use `=` with no extra formatting

---

## Page loads but generation never starts

**Check:**

- You clicked **Generate character** (not just pressed Enter with the button focused)
- A URL is entered in the Source URL field
- The page has fully loaded (first load fetches runtime dependencies which can take a few seconds)

---

## Result looks like the wrong character

**Cause:** The extracted text contained unrelated content (ads, navigation, other characters).

**Fix:**

- Open **Extraction details** → `Extracted content` to see what was sent to the AI
- Add extra instructions to focus the AI: _Only describe the main character of this page_
- For wiki pages, the Fandom adapter usually produces cleaner results automatically

---

## Batch stops immediately with no errors

**Cause:** The URL textarea may be empty or all URLs are duplicates filtered by dedupe.

**Fix:** Check the batch log for the specific reason. Disable **Deduplicate** to rule that out.

---

See also: [[Supported Sources]] · [[Settings Reference]] · [[Batch Mode]]
