# Supported Sources

Sites and file types with special handling, plus what happens with everything else.

---

## Directly supported sites

These sites have custom logic that bypasses or supplements the generic extraction path.

| Site                        | What happens                                                    |
| --------------------------- | --------------------------------------------------------------- |
| aicharactercards.com        | Post ID is resolved to a direct card download                   |
| char-archive.evulid.cc      | Hash route is rewritten to the API image endpoint               |
| character-tavern.com        | URL is rewritten to the cards CDN endpoint                      |
| characterhub.org            | Rewritten to chub.ai                                            |
| chub.ai                     | Character avatar card is fetched and parsed                     |
| janitorai.com / jannyai.com | Page is parsed to extract structured character JSON             |
| sakura.fm                   | Embedded character payload is parsed from the page              |
| character.ai                | Greeting, definition, and avatar are extracted from the profile |
| Fandom wikis                | VisualEditor API is used for clean structured text              |

---

## File and content types

### Character card files (PNG / JPEG)

Images with embedded character metadata (common in the card-sharing community) are detected and parsed automatically. Metadata is read from EXIF `UserComment` or the `chara` field.

Character books embedded in cards are extracted and uploaded as lore book URLs.

### PDFs

Text is extracted page by page using PDF.js. Useful for character sheets, game manuals, or any PDF source.

### Plain HTML pages

For pages without special handling:

1. The page is fetched via the super-fetch proxy
2. Mozilla Readability strips navigation, ads, and boilerplate
3. Site-specific heuristics extract additional structured data
4. Metadata (og:image, description, schema.org) is captured
5. Extracted text is sent to the AI model

### Images (non-card)

If no metadata card is found, the image URL is used as context for character generation (best effort).

---

## Sites that may fail

Some sites actively block scraping or require authentication:

- Pages behind login walls
- Sites with aggressive bot detection (Cloudflare, Akamai, etc.)
- Pages that require JavaScript to render content

**What to try:** Copy the relevant text manually, paste it into the scratchpad, enable **Include scratchpad notes in generation**, and leave the URL blank or use a generic URL.

---

## Instagram and YouTube

Only snippet/metadata extraction is possible — full content is not accessible. Results will be limited. Use extra instructions to supplement with manually copied text.

---

## Source-specific timeouts and caps

You can tune how long to wait and how much text to extract per source type in the **Source extraction profile** section:

| Setting              | Default      |
| -------------------- | ------------ |
| Generic timeout      | 90 000 ms    |
| Fandom timeout       | 100 000 ms   |
| Character.AI timeout | 110 000 ms   |
| PDF timeout          | 120 000 ms   |
| Generic cap          | 12 000 chars |
| Fandom cap           | 16 000 chars |
| Character.AI cap     | 10 000 chars |
| PDF cap              | 10 000 chars |

The `aggressive` source preset doubles the generic and fandom caps automatically.

---

See also: [[Single Character Generation]] · [[Batch Mode]] · [[Troubleshooting]]
