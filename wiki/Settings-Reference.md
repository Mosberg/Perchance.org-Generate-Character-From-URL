# Settings Reference

All controls, grouped by section.

---

## Source input

| Control                     | Description                      |
| --------------------------- | -------------------------------- |
| Source URL                  | URL to generate a character from |
| Optional extra instructions | Modifies AI style or constraints |

---

## Scratchpad

| Control                        | Description                                          |
| ------------------------------ | ---------------------------------------------------- |
| Scratchpad textarea            | Reusable notes and variable definitions              |
| Include scratchpad notes       | Appends non-variable lines to the AI prompt          |
| Parse VARIABLE = value lines   | Enables `[TOKEN]` substitution in extra instructions |
| Remember inputs between visits | Persists most controls to local storage              |

---

## Batch tuning

| Control                     | Description                                    |
| --------------------------- | ---------------------------------------------- |
| Batch profile preset        | `fast` / `quality` / `cautious` / `custom`     |
| Concurrency                 | Parallel workers: 1–3                          |
| Base delay (ms)             | Wait between URLs                              |
| Retry count                 | Extra attempts per URL (0–5)                   |
| Backoff strategy            | `fixed` / `exponential` / `exponential-jitter` |
| Max total time per URL (ms) | Hard cap per URL including all retries         |

---

## Naming and output structure

| Control           | Description                        |
| ----------------- | ---------------------------------- |
| Filename template | Template for individual file names |
| Filename case     | `original` / `kebab` / `snake`     |
| Collision policy  | `append` / `overwrite` / `skip`    |
| ZIP structure     | `flat` / `groupByHost`             |

---

## Source extraction profile

| Control                                       | Description                          |
| --------------------------------------------- | ------------------------------------ |
| Source preset profile                         | `balanced` / `strict` / `aggressive` |
| Generic / Fandom / Character.AI / PDF timeout | Per-source timeout in ms             |
| Generic / Fandom / Character.AI / PDF cap     | Max extracted characters per source  |

---

## Queue export mode + global settings

| Control                             | Description                                                                       |
| ----------------------------------- | --------------------------------------------------------------------------------- |
| JSON output mode                    | `Individual files only` / `One combined file only` / `Both individual + combined` |
| Combined filename template          | Template for the combined file; supports `{date}`, `{time}`, `{count}`            |
| Global model name                   | Forced model for all queued characters                                            |
| Global temperature                  | Forced temperature (e.g. `1`)                                                     |
| Global max tokens                   | Forced max tokens per message                                                     |
| Global streaming response           | Force streaming on or off                                                         |
| Global fit messages method          | How the context window is managed                                                 |
| Global auto memories                | Auto memory generation setting                                                    |
| Global max paragraph count          | Max paragraphs per AI response                                                    |
| Global message input placeholder    | Placeholder text in the chat input                                                |
| Global image prompt prefix / suffix | Prepend/append to image generation prompts                                        |
| Global image prompt triggers        | Comma-separated trigger words                                                     |
| Global reminder message             | Injected context reminder                                                         |
| Global message wrapper style        | CSS or style override for messages                                                |
| Global writing instructions         | General style/tone instructions                                                   |
| Global custom code                  | Injected JavaScript                                                               |

---

## Validation controls

| Control                         | Description                                |
| ------------------------------- | ------------------------------------------ |
| Require character name          | Reject characters with no name             |
| Require role instruction        | Reject characters with no role instruction |
| Minimum role instruction length | Reject below N characters                  |
| Maximum JSON size               | Reject files above N bytes                 |

---

## Batch output toggles

| Control                         | Description                                        |
| ------------------------------- | -------------------------------------------------- |
| Auto download outputs           | Download each file as it completes                 |
| Download ZIP                    | Bundle individual files in a ZIP                   |
| Include manifest in ZIP         | Add the manifest JSON to the ZIP                   |
| Stop on first error             | Abort the run on any failure                       |
| Load failed URLs only on resume | Populate queue from failed entries                 |
| Deduplicate results             | Skip duplicate name + role instruction pairs       |
| Publish cloud links             | Create a shareable link for each character         |
| Save to local folder            | Use File System Access API to write files directly |

---

## Timeout and cap defaults

| Source       | Timeout    | Cap          |
| ------------ | ---------- | ------------ |
| Generic      | 90 000 ms  | 12 000 chars |
| Fandom       | 100 000 ms | 16 000 chars |
| Character.AI | 110 000 ms | 10 000 chars |
| PDF          | 120 000 ms | 10 000 chars |

---

See also: [[Batch Mode]] · [[Export Options]] · [[Troubleshooting]]
