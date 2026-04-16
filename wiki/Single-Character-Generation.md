# Single Character Generation

Everything about generating and editing one character at a time.

---

## Basic flow

1. Paste a URL into **Source URL**
2. Add optional instructions
3. Click **Generate character** (or press **Enter** in the URL field)
4. Edit the result in the character editor
5. Download or share

---

## Scratchpad and variables

The **Scratchpad** textarea lets you store reusable notes and variable definitions.

**Variables** work like this:

```
VOICE = calm but stern
SETTING = feudal Japan
```

Then in **Optional extra instructions**, reference them with brackets:

```
Write the character with a [VOICE] voice set in [SETTING].
```

Variables are substituted before the prompt is sent. Non-variable lines in the scratchpad are optionally appended as extra context — enable **Include scratchpad notes in generation** for that.

**Parse VARIABLE = value lines** must be checked for substitution to work.

---

## Remembered inputs

Enable **Remember inputs between visits** to persist most controls to local storage. Values are restored automatically when you reopen the page.

Use **Reset remembered** to clear saved values.

---

## Editing the result

The **Generated Character** panel lets you edit all fields directly:

| Field            | What it is                                       |
| ---------------- | ------------------------------------------------ |
| Name             | Character's display name                         |
| Avatar URL       | Image shown in chat                              |
| Role instruction | Core personality and behavior prompt             |
| Initial messages | Opening lines when a chat starts                 |
| Reminder message | Injected periodically to keep character on track |
| Scene background | Background image URL                             |
| Lore book URLs   | External lore references                         |

Advanced fields (model, temperature, writing instructions, custom code, etc.) are also editable.

The **JSON preview** at the bottom updates live as you edit.

---

## Sharing

| Button                    | What it does                        |
| ------------------------- | ----------------------------------- |
| Download JSON             | Save a Dexie file to import         |
| Copy JSON                 | Copy the raw JSON                   |
| Open in ai-character-chat | Open a hash link in a new tab       |
| Create cloud link         | Upload and generate a shareable URL |
| Copy link                 | Copy the last created cloud link    |

---

## Extraction details

The **Extraction details** panel shows:

- The final URL that was fetched
- Detected source type
- Extracted text content
- Raw AI response

Use this to understand why a result came out weak or incorrect.

---

## Automation

You can pre-fill the page via URL parameters:

```
?url=https://example.com&extraInstructions=Formal+tone&autorun=1
```

`autorun=1` (or `auto=1`) triggers generation immediately on page load.

---

See also: [[Batch Mode]] · [[Export Options]] · [[Supported Sources]]
