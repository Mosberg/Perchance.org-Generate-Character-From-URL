# Generate Character From URL v6

A standalone Perchance custom generator that converts URLs, cards, images, PDFs, and web pages into ai-character-chat-compatible Dexie JSON exports.

Version v6 adds:

- Combined batch export (one multi-character Dexie JSON file)
- Global batch character settings (applied to every queued result)
- v6-specific remembered input and manifest storage keys

## Table of Contents

- [1. Scope and Goals](#1-scope-and-goals)
- [2. Runtime Dependencies](#2-runtime-dependencies)
- [3. UI Surface and Features](#3-ui-surface-and-features)
- [4. Core Workflows](#4-core-workflows)
- [5. Source Adapters and Extraction Logic](#5-source-adapters-and-extraction-logic)
- [6. Export Model and Compatibility](#6-export-model-and-compatibility)
- [7. Batch Engine Mechanics](#7-batch-engine-mechanics)
- [8. Settings and Config Reference](#8-settings-and-config-reference)
- [9. Persistence and Local Storage](#9-persistence-and-local-storage)
- [10. Function Reference (Complete)](#10-function-reference-complete)
- [11. Event Listener Map](#11-event-listener-map)
- [12. Error Handling and Recovery](#12-error-handling-and-recovery)
- [13. Query Parameters and Automation](#13-query-parameters-and-automation)
- [14. Extension Notes](#14-extension-notes)

## 1. Scope and Goals

This generator provides:

- Single URL character generation
- Deep source extraction logic with multiple site-specific adapters
- Character editing and preview
- ai-character-chat link generation (hash and cloud)
- Batch queue processing with retries, dedupe, review mode, reporting
- Individual and/or combined Dexie exports that import into ai-character-chat

Design intent:

- Max compatibility with ai-character-chat import path
- Stable behavior across heterogeneous sources
- User-facing controls for quality/speed tradeoffs

## 2. Runtime Dependencies

### Perchance imports

Defined in `generateCharacterFromUrlv6.perchance`:

- `ai-character-chat-dependencies-v1` (Dexie and related dependencies)
- `ai-text-plugin`
- `super-fetch-plugin`
- `upload-plugin`

Also exposes:

- `compressBlobWithGzip(blob)` via `CompressionStream`

### Dynamic imports at runtime

- JSON5: `https://cdn.jsdelivr.net/npm/json5@2.2.2/dist/index.min.mjs`
- Readability:
  - `https://user.uploads.dev/file/93edd249920ca5ac663278139c31868d.js`
  - fallback `https://esm.sh/@mozilla/readability@0.5.0?no-check`
- PDF.js: `https://cdn.jsdelivr.net/npm/pdfjs-dist@4.7.76/build/pdf.min.mjs`
- PDF worker: `https://cdn.jsdelivr.net/npm/pdfjs-dist@4.7.76/build/pdf.worker.min.mjs`
- JSZip: `https://cdn.jsdelivr.net/npm/jszip@3.10.1/+esm`
- ExifReader: `https://cdn.jsdelivr.net/npm/exifreader@4.12.0/+esm`

### Browser APIs used

- `CompressionStream`
- `createImageBitmap`
- `AbortSignal.timeout` (when available)
- `File System Access API` (`showDirectoryPicker`, optional)
- Clipboard APIs
- URL, Blob, File, DOMParser, TextEncoder, WebCrypto

## 3. UI Surface and Features

The page is split into major panels:

- Source input and single generation
- Scratchpad + remembered input
- Batch queue and controls
- Generated character editor
- Extraction details

### 3.1 Single generation controls

- Source URL input
- Optional extra instructions
- Generate button with progress display
- Floating in-page generation notice

### 3.2 Scratchpad and remembered input

- Scratchpad text area
- Include scratchpad notes in generation toggle
- Parse variables toggle
- Copy/clear scratchpad actions
- Remember/reset input behavior

Variable mechanics:

- Variable lines use `NAME = value`
- Tokens in extra instructions use `[NAME]`
- Non-variable lines become optional "Scratchpad notes" appended to prompt

### 3.3 Batch controls

- URL queue textarea
- Drag/drop `.txt` or `.csv` URL import
- Batch profile preset
- Concurrency
- Base delay
- Retry count and backoff strategy
- Max total time per URL
- Filename template and case transform
- Collision policy
- ZIP structure
- Source preset profile
- Review mode
- Source-specific timeout caps and extraction caps
- Validation rules
- Batch action buttons (start/pause/stop/skip/retry/import/load-failed/copy summary/download CSV)

### 3.4 v6 queue export mode and global settings

v6 adds a dedicated group:

- JSON output mode: `individual`, `combined`, `both`
- Combined filename template with `{date}`, `{time}`, `{count}` tokens
- Global character fields applied to all queued characters:
  - modelName
  - temperature
  - maxTokensPerMessage
  - textEmbeddingModelName
  - streamingResponse force mode (`keep`, `true`, `false`)
  - fitMessagesInContextMethod
  - autoGenerateMemories
  - maxParagraphCountPerMessage
  - messageInputPlaceholder
  - imagePromptPrefix
  - imagePromptSuffix
  - imagePromptTriggers
  - metaTitle
  - metaDescription
  - metaImage
  - reminderMessage
  - messageWrapperStyle
  - generalWritingInstructions
  - customCode

### 3.5 Generated character editor

Editable character fields include:

- name, avatar URL
- roleInstruction
- initialMessages (text format parser)
- reminderMessage
- messageWrapperStyle
- scene background URL
- loreBookUrls
- advanced settings (max paragraph count, input placeholder, writing instructions, custom code, image prompt fields, fit method, auto memories, meta fields, user/system character fields, shortcut buttons text)

Actions:

- copy JSON
- download JSON
- open ai-character-chat hash link
- create/copy cloud link
- scroll to extraction details

### 3.6 Extraction details panel

Read-only runtime visibility for:

- final source URL
- detected source type
- original requested URL
- effective extra instructions
- extracted text content
- raw AI response

## 4. Core Workflows

### 4.1 Single URL flow

1. User enters URL + optional instructions.
2. Scratchpad variables and notes are merged into effective instructions.
3. `extractCharacterFromUrl()` routes to import adapters or extraction path.
4. Source content is transformed into AI prompt context.
5. `aiTextPlugin` streams response chunks; UI progress updates continuously.
6. Result is normalized (`sanitizeCharacter`) and rendered in editor.
7. JSON preview updates from editor state using Dexie export builder.

### 4.2 Batch queue flow

1. Parse URLs from textarea and optional failed-only resume.
2. Build config from UI, including v6 global settings and output mode.
3. Initialize worker pool (`concurrency` 1-3).
4. Each item uses retry-aware extraction with timeout and pause/stop checks.
5. Apply global character overrides.
6. Validate output (name, roleInstruction, min length, max bytes).
7. Optional dedupe by hash(name + roleInstruction).
8. Optional review prompt per item.
9. Export individual files and/or prepare combined export.
10. Build manifest + report + CSV; optional ZIP export for individual mode.

## 5. Source Adapters and Extraction Logic

### 5.1 Direct card and conversion adapters

Pre-AI routes that may bypass generic extraction:

- `aicharactercards.com` postId -> direct card download URL
- `char-archive.evulid.cc` hash route -> API image endpoint
- `character-tavern.com/character/...` -> cards URL rewrite
- `cards.character-tavern.com/cdn-cgi/image/...` -> canonical card URL rewrite
- `characterhub.org` rewritten to `chub.ai`
- `chub.ai/characters/...` -> avatar card image endpoint
- `janitorai.com` / `jannyai.com` -> page parse to structured character JSON
- `sakura.fm` -> embedded character payload parsing

### 5.2 External card import parser

`tryImportingExternalCharacterFileFormat(file)` supports:

- Plain JSON card formats
- PNG metadata cards (`chara` or `UserComment`) using ExifReader + JSON5
- Character Book extraction and lore upload to generate `loreBookUrls`
- Avatar extraction fallback to JPEG data URL

### 5.3 Generic extraction path

For non-direct-card pages:

- Download content via `superFetch`
- MIME routing:
  - PDF -> PDF.js text extraction
  - image -> metadata card attempt, otherwise image prompt fallback
  - HTML -> parsing + Readability + site heuristics

### 5.4 Site-specific extraction heuristics

- Character AI chat and profile extraction:
  - greeting, definition, avatar, optional message wrapper style
- Fandom wiki extraction:
  - VisualEditor API pull
  - special handling for Genshin `/Lore`
  - wiki cleanup and section reordering
- Instagram and YouTube fallback snippets
- Metadata extraction: og:image, description, keywords
- Schema.org text capture, image tag heuristics

### 5.5 AI generation prompt mechanics

Prompt enforces structured response template:

- Name
- Visual Description
- Personality Description
- Roleplay Behavior Examples
- Favorite Food (used as stop sequence boundary)

Streaming behavior:

- Progress based on chunk count and adaptive expected length
- Roleplay examples trigger expected-length adjustment

## 6. Export Model and Compatibility

### 6.1 Dexie envelope

All exports use:

- `formatName: "dexie"`
- `formatVersion: 1`
- `databaseName: "chatbot-ui-v1"`
- `databaseVersion: 90`

### 6.2 Table skeleton included

Even for character-only exports, all expected tables are present:

- characters
- threads
- messages
- misc
- summaries
- memories
- lore
- textEmbeddingCache
- textCompressionCache

This avoids runtime import failures where ai-character-chat expects table rows arrays.

### 6.3 Character row defaults

Per row builder logic:

- auto-assigned `id` starting at 1
- generated or preserved `uuid`
- `modelName` default `perchance-ai`
- `temperature` default `1`
- `maxTokensPerMessage` default `400`
- `streamingResponse` default `true`
- empty-safe structures for avatar/scene/userCharacter/systemCharacter
- optional passthrough of `$types` object

### 6.4 v6 combined export

`buildDexieExportFromCharacters(characters)` packs all successful queued characters into one Dexie file.

JSON output mode behavior:

- `individual`: one file per character
- `combined`: one file containing all successful characters
- `both`: both individual files and combined file

Combined filename template tokens:

- `{date}`
- `{time}`
- `{count}`

## 7. Batch Engine Mechanics

### 7.1 Worker and queue model

- Queue entries get deterministic IDs (SHA-256 of URL)
- Workers share a single queue cursor
- Worker count is configurable (1-3)

### 7.2 Retry and timeout model

Per URL attempt loop supports:

- max attempts = `retryCount + 1`
- source-specific timeout per attempt
- max total elapsed time per URL
- backoff strategies:
  - fixed
  - exponential
  - exponential-jitter

### 7.3 Pause/stop/skip/retry controls

- Pause waits at controlled checkpoints (`waitIfPaused`)
- Stop requests cleanly halt after current boundary
- Skip and manual retry flags are honored mid-processing

### 7.4 Validation and quality gates

Optional gates:

- name required
- roleInstruction required
- minimum roleInstruction length
- max serialized JSON byte size

### 7.5 Dedupe and review mode

- Dedupe key: normalized `name + roleInstruction` hash
- Review mode `pauseEach` uses confirmation dialog per generated character

### 7.6 Report and artifacts

Generated artifacts:

- manifest JSON
- CSV report
- summary text
- optional ZIP (individual output mode)

Manifest tracks:

- run metadata
- entry status lifecycle (`running`, `success`, `failed`, `skipped`)
- attempts, durations, errors, source type, file name, optional cloud link
- v6 combined export fields when used

## 8. Settings and Config Reference

### 8.1 Batch profile presets (`batchProfilePresetSelect`)

- `fast`
  - concurrency=3
  - retry=1
  - backoff=fixed
  - delay=100ms
- `quality`
  - concurrency=1
  - retry=3
  - backoff=exponential
  - delay=500ms
- `cautious`
  - concurrency=1
  - retry=5
  - backoff=exponential-jitter
  - delay=700ms
- `custom`
  - leaves current values intact

### 8.2 Source preset profile (`batchSourcePresetSelect`)

- `balanced`: baseline
- `strict`: enables evidence gate (name exists and extracted content length >= 180)
- `aggressive`: doubles generic and fandom extraction caps

### 8.3 Timeout defaults

- generic: 90000 ms
- fandom: 100000 ms
- character.ai: 110000 ms
- pdf: 120000 ms

### 8.4 Extraction cap defaults

- generic: 12000 chars
- fandom: 16000 chars
- character.ai: 10000 chars
- pdf: 10000 chars

### 8.5 Filename template tokens

Individual filename template supports:

- `{index}` (1-based)
- `{host}`
- `{name}`
- `{sourceType}`
- `{date}`
- `{time}`

Post-processing:

- forbidden filesystem chars sanitized
- optional case transform (`original`, `kebab`, `snake`)
- collision policy (`append`, `overwrite`, `skip`)

## 9. Persistence and Local Storage

### 9.1 Remembered inputs

Prefix:

- `generateCharacterFromUrlV6Remembered_`

Behavior:

- all rememberable controls save on input/change when enabled
- checkbox states persisted as booleans
- values restored on load

### 9.2 Manifest cache

- key: `generateCharacterFromUrlV6LastManifest`
- loaded at startup if present

### 9.3 Generation count

- key: `numCharactersGeneratedFromUrl`
- used for first-time helper alert behavior

## 10. Function Reference (Complete)

Functions are grouped by responsibility.

### 10.1 Utility and state helpers

- `delay(ms)`
- `setStatus(message, mode)`
- `saveRememberedInput(inputName, inputValue)`
- `getRememberedInput(inputName)`
- `clearRememberedInputs()`
- `parseScratchpadVariables(text)`
- `getScratchpadNotes(text)`
- `applyScratchpadVariables(text, variables)`
- `getEffectiveExtraInstructions(baseText)`
- `getRememberableElements()`
- `readElementValueForRemembering(element)`
- `applyRememberedValueToElement(element, value)`
- `restoreRememberedInputs()`
- `maybePersistElement(element)`

### 10.2 Asset loading and media transforms

- `blobToDataUrl(blob)`
- `base64DecodeUnicode(str)`
- `ensureJson5()`
- `ensureReadability()`
- `ensurePdfJs()`
- `getPdfText(arrayBuffer)`
- `processAvatarImageUrl(imageUrl, opts)`
- `createFloatingNotice()`

### 10.3 Message and character normalization

- `formatMessagesToText(messages)`
- `parseMessagesFromTextFormat(text)`
- `sanitizeCharacter(character)`
- `buildCharacterFromEditor()`

### 10.4 Dexie export builders

- `buildCharacterRowForDexie(character, index)`
- `buildDexieExportFromCharacters(characters)`
- `buildDexieExport(character)`

### 10.5 Editor rendering and sharing

- `updateAvatarPreview(url)`
- `updateJsonPreview()`
- `renderCharacterResult(result)`
- `copyJson()`
- `downloadJson()`
- `buildSharePayloadFromEditor()`
- `buildAiCharacterChatHashLink()`
- `createAiCharacterChatCloudLink()`

### 10.6 Batch helper primitives

- `appendBatchLog(text)`
- `setBatchReportCard(text)`
- `ensureJsZip()`
- `parseUrlTextToList(text)`
- `detectSourceKindByUrl(url)`
- `normalizeStringForHash(text)`
- `sha256Hex(text)`
- `deterministicIdForUrl(url)`
- `applyCaseTransform(text, mode)`
- `sanitizeFileNameBase(name)`
- `getDateTokens(timestamp)`
- `makeFileNameFromTemplate(template, context, caseMode)`
- `resolveCollisionName(baseName, policy, usedNames)`
- `isRetryableError(error)`
- `categorizeError(error)`
- `getBackoffMs(strategy, attemptNumber, baseDelayMs)`
- `waitIfPaused()`
- `sleepWithPause(ms)`
- `validateCharacterForBatch(character)`
- `applySourcePresetToInputsIfNeeded()`
- `getSourceConfig()`
- `applySourcePostProcessing(result, sourceConfig)`

### 10.7 v6 global settings and combined export helpers

- `getBatchGlobalCharacterSettings()`
- `applyBatchGlobalCharacterSettings(character, settings)`
- `resolveCombinedExportFileName(template, characterCount)`
- `buildBatchConfig(urls)`
- `updateFilenamePreview()`
- `saveBlobToFolderIfEnabled(fileName, blob)`
- `downloadBlobFile(fileName, blob)`
- `createAiCharacterChatCloudLinkForCharacter(character)`
- `manifestToCsv(manifest)`
- `buildSummaryText(manifest, report)`
- `performExtractionWithRetry(item, config, workerId)`
- `buildReportFromManifest(manifest)`
- `processBatchUrls()`

### 10.8 Import and extraction adapters

- `tryImportingExternalCharacterFileFormat(file)`
- `createLoadingModal(initialContent, parentElement)`
- `maybeImportFromKnownDirectCardUrl(url)`
- `extractCharacterFromUrl(originalUrl, extraInstructions, progressCallback)`
- `generateCharacterFromUrl(opts)`

### 10.9 UI flow helpers

- `mergeUrlsIntoTextarea(urls)`
- `handleBatchFileImport(file)`
- `maybeAutoRunFromQueryParams()`

## 11. Event Listener Map

Primary bindings:

- Single generation:
  - generate button click
  - URL Enter key
- Remembered input:
  - every rememberable control on input/change
  - remember toggle change
  - scratchpad copy/clear
  - reset remembered inputs
- Batch actions:
  - start, pause/resume, stop, skip current, retry current
  - import URL file, drag/drop URL files
  - load failed URLs from last manifest
  - copy summary, download CSV
  - filename preview updates on relevant input changes
- Editor actions:
  - all result field edits trigger avatar/JSON preview refresh
  - copy/download JSON
  - open hash link
  - create cloud link
  - copy latest link
- Startup:
  - restore remembered values
  - preload AI plugin and optional JSON5
  - load last manifest from local storage
  - apply query parameter autorun

## 12. Error Handling and Recovery

### 12.1 Retry policy

`isRetryableError` and `categorizeError` classify failures for retry and reporting.

Non-retry examples:

- explicit `retryable=false`
- HTTP 400/401/403/404
- malformed/invalid URL indicators

Retry examples:

- 429
- 5xx
- timeout/network-like errors

### 12.2 Batch resilience

- stop-on-error optional hard stop for failed items
- per-item failure does not stop full run unless configured
- skipped and review-skipped are tracked distinctly

### 12.3 Export fallback

If ZIP generation fails, logic falls back to per-file JSON downloads.

### 12.4 Import resilience

External card parsing has staged fallbacks:

- JSON parse
- metadata parse
- image downscale fallback

## 13. Query Parameters and Automation

Supported URL params:

- `url=<encoded-source-url>`
- `extraInstructions=<encoded-text>`
- `autorun=1` or `auto=1`

Behavior:

- Prefills source and instructions
- Optionally starts generation automatically

## 14. Extension Notes

Recommended extension points:

- Add new source adapters inside `extractCharacterFromUrl()` before generic fallback
- Extend global settings parser in `getBatchGlobalCharacterSettings()`
- Add new export artifacts near end of `processBatchUrls()`
- Expand validation in `validateCharacterForBatch()`

When adding schema-bound fields, update all of:

- `sanitizeCharacter()`
- `buildCharacterFromEditor()`
- `buildCharacterRowForDexie()`
- Any related global batch settings parser/merge logic

---

If you modify this generator, re-test the following minimum matrix:

- single URL generation (generic page)
- direct card import URL
- fandom URL
- character.ai URL
- batch mode with retries
- combined-only export import into ai-character-chat
- both-mode export with ZIP and manifest
