# Generate Character From URL v6 - User Guide

This guide explains how to use Generate Character From URL v6 from start to finish, including every major feature, option, setting, workflow, and output mode.

[Try it on Perchance](https://perchance.org/generate-character-from-url)

If you want a technical developer reference, use README.md.
This file is focused on practical user usage.

## 1. What v6 Is For

Generate Character From URL v6 lets you:

- Create roleplay-ready character data from URLs
- Import many existing character card formats from supported sources
- Edit and refine character fields before export
- Export ai-character-chat compatible Dexie JSON files
- Run large URL queues in batch mode
- Export batch results as:
- Individual JSON files
- One combined multi-character JSON
- Or both
- Apply global character settings to every queued character

## 2. Before You Start

Recommended:

- Use a modern Chromium browser for best compatibility
- Keep stable internet access (source pages plus model calls)
- Use shorter queue batches first while testing your presets

Nice to know:

- First run may feel slower because runtime dependencies are loaded dynamically
- Large pages and PDFs can take longer to process
- Some websites block scraping or require auth, which can cause extraction failures

## 3. Quick Start

### 3.1 Quick Start: Single Character

1. Open the v6 page.
2. Paste a source URL into Source URL.
3. Optionally add extra instructions.
4. Click Generate character.
5. Wait for progress to complete.
6. Review and edit fields in Generated Character panel.
7. Click Download JSON or Copy JSON.
8. Import into ai-character-chat.

### 3.2 Quick Start: Batch Combined Export

1. Paste multiple URLs in the Batch URL textarea (one per line).
2. In Queue export mode + global settings:

- Set JSON output mode to One combined file only.
- Set Combined filename template if desired.

3. Optionally configure global settings to enforce shared defaults.
4. Click Start queue.
5. When done, download the combined JSON.
6. Import the combined file into ai-character-chat.

## 4. Full Interface Walkthrough

## 4.1 Source Input Panel

### Source URL

- Field: generateCharacterFromUrlInputEl
- What it does: main URL to extract or import.

### Optional extra instructions

- Field: generateCharacterFromUrlExtraInstructionsInputEl
- What it does: modifies AI output style/constraints.
- Example: Make tone more formal and avoid modern slang.

### Generate character button

- Field: generateCharacterFromUrlBtn
- What it does: starts single-run generation.

## 4.2 Scratchpad and Remembered Input

### Scratchpad

- Field: scratchpadTextarea
- Use cases:
- Store reusable notes
- Store variable assignments
- Keep per-project guidance

### Include scratchpad notes in generation

- Field: scratchpadIncludeInGenerationCheckbox
- If enabled:
- Non-variable scratchpad lines are appended to prompt as notes.

### Parse VARIABLE = value lines

- Field: scratchpadParseVariablesCheckbox
- If enabled:
- Lines like VOICE = calm but stern are parsed.
- Tokens like [VOICE] in extra instructions are replaced.

### Remember inputs between visits

- Field: rememberInputsCheckbox
- If enabled:
- Most controls are persisted to local storage.

### Copy/Clear/Reset

- scratchpadCopyButton: copy scratchpad text
- scratchpadClearButton: clear scratchpad field only
- rememberResetButton: clear remembered values

## 4.3 Batch URL Generation Panel

### URL queue input

- Field: batchUrlsTextarea
- Accepts URLs separated by:
- New lines
- Commas
- Semicolons

### Drag and drop zone

- Field: batchDropZone
- Accepts .txt and .csv files with URLs.

### Import URL file button

- Field: batchImportFileButton
- Opens file picker for .txt/.csv URL imports.

### Load failed from last manifest

- Field: batchResumeFailedButton
- Loads failed URLs from previous run into queue.
- Useful for retry sessions.

## 4.4 Batch Tuning Controls

### Batch profile preset

- Field: batchProfilePresetSelect
- Values:
- fast
- quality
- cautious
- custom

Preset behavior:

- fast:
- concurrency = 3
- retry count = 1
- backoff = fixed
- delay = 100 ms
- quality:
- concurrency = 1
- retry count = 3
- backoff = exponential
- delay = 500 ms
- cautious:
- concurrency = 1
- retry count = 5
- backoff = exponential-jitter
- delay = 700 ms
- custom:
- keeps your current values

### Concurrency

- Field: batchConcurrencySelect
- Range: 1 to 3 workers
- Higher concurrency can improve throughput but may increase rate-limit risk.

### Base delay (ms)

- Field: batchDelayMsInput
- Wait between processed URLs.
- Helps reduce server pressure.

### Retry count (0 to 5)

- Field: batchRetryCountInput
- Number of retries after the first attempt fails.

### Backoff strategy

- Field: batchBackoffStrategySelect
- fixed
- exponential
- exponential-jitter

### Max total time/url (ms)

- Field: batchMaxTotalTimeMsInput
- Hard upper limit per URL total processing window.

## 4.5 Naming and Output Structure

### Filename template

- Field: batchFilenameTemplateInput
- Default: {index}-{host}-{name}
- Supported tokens:
- {index}
- {host}
- {name}
- {sourceType}
- {date}
- {time}

### Filename case

- Field: batchFilenameCaseSelect
- original
- kebab
- snake

### Collision policy

- Field: batchCollisionPolicySelect
- append: add -2, -3, etc
- overwrite: keep same base name
- skip: skip on collision

### ZIP structure

- Field: batchZipStructureSelect
- flat
- groupByHost

## 4.6 Source Extraction Profile

### Source preset profile

- Field: batchSourcePresetSelect
- balanced: default behavior
- strict: rejects low-evidence outputs
- aggressive: increases some extraction caps

Strict mode logic:

- Requires character name present
- Requires extracted evidence length >= 180 chars

Aggressive mode logic:

- Doubles generic and fandom extraction caps

### Source-specific timeout settings

- sourceTimeoutGenericInput
- sourceTimeoutFandomInput
- sourceTimeoutCharacterAiInput
- sourceTimeoutPdfInput

### Source-specific extraction caps (characters)

- sourceCapGenericInput
- sourceCapFandomInput
- sourceCapCharacterAiInput
- sourceCapPdfInput

## 4.7 Queue Export Mode + Global Character Settings (v6)

This is one of the most important v6 additions.

### JSON output mode

- Field: batchJsonOutputModeSelect
- Individual files only
- One combined file only
- Both individual + combined

Behavior details:

- combined-only:
- individual JSON files are not written
- ZIP export is skipped
- one multi-character Dexie JSON is created if there are successful results
- both:
- individual JSON files are written
- combined file is also written

### Combined filename template

- Field: batchCombinedFilenameTemplateInput
- Default: perchance-characters-export-{date}
- Supported tokens:
- {date}
- {time}
- {count}

### Global settings applied to every queued character

Fields:

- batchGlobalModelNameInput
- batchGlobalTemperatureInput
- batchGlobalMaxTokensInput
- batchGlobalTextEmbeddingModelNameInput
- batchGlobalStreamingResponseSelect
- batchGlobalFitMessagesMethodInput
- batchGlobalAutoGenerateMemoriesInput
- batchGlobalMaxParagraphCountInput
- batchGlobalMessageInputPlaceholderInput
- batchGlobalImagePromptPrefixInput
- batchGlobalImagePromptSuffixInput
- batchGlobalImagePromptTriggersTextarea
- batchGlobalMetaTitleInput
- batchGlobalMetaDescriptionInput
- batchGlobalMetaImageInput
- batchGlobalReminderMessageTextarea
- batchGlobalMessageWrapperStyleTextarea
- batchGlobalGeneralWritingInstructionsTextarea
- batchGlobalCustomCodeTextarea

Merge logic:

- Only meaningful values are applied.
- Empty values do not force blanking unless explicitly entered as non-empty text.
- Character is re-sanitized after merge.

## 4.8 Validation Controls

Validation section controls:

- validationRequireNameCheckbox
- validationRequireRoleInstructionCheckbox
- validationMinRoleLengthCheckbox
- validationMinRoleLengthInput
- validationMaxJsonSizeCheckbox
- validationMaxJsonSizeInput

What they do:

- Reject weak/invalid outputs before saving.
- Prevent oversized JSON output when size limits matter.

## 4.9 Batch Output Toggles

- batchAutoDownloadCheckbox
- Auto download JSON outputs

- batchDownloadZipCheckbox
- Download ZIP for individual outputs

- batchIncludeManifestCheckbox
- Include manifest JSON inside ZIP

- batchStopOnErrorCheckbox
- Stop entire run on first failed item

- batchResumeFailedOnlyCheckbox
- At run start, load only previous failed URLs

- batchEnableDedupeCheckbox
- Dedupe based on normalized name + roleInstruction hash

- batchPublishAiChatLinksCheckbox
- Create cloud links per success entry (slower)

- batchSaveToFolderCheckbox
- Save outputs to selected local folder if API is available

## 4.10 Batch Action Buttons

- batchStartButton: start queue
- batchPauseResumeButton: pause/resume workers
- batchStopButton: stop requested
- batchSkipCurrentButton: skip current item
- batchRetryCurrentButton: force manual retry for current
- batchImportFileButton: import URL file
- batchResumeFailedButton: load failed URLs from manifest
- batchCopySummaryButton: copy summary text
- batchDownloadCsvButton: download CSV report

Progress and logs:

- batchProgressInput: status text
- batchReportCardInput: end-of-run metrics
- batchLogTextarea: detailed chronological log

## 4.11 Generated Character Editor

Core fields:

- resultNameInput
- resultAvatarUrlInput
- resultRoleInstructionTextarea
- resultInitialMessagesTextarea
- resultReminderTextarea
- resultMessageWrapperStyleTextarea
- resultSceneBackgroundInput
- resultLoreBookUrlsTextarea

Advanced fields:

- resultMaxParagraphCountInput
- resultMessageInputPlaceholderInput
- resultGeneralWritingInstructionsTextarea
- resultCustomCodeTextarea
- resultImagePromptPrefixInput
- resultImagePromptSuffixInput
- resultImagePromptTriggersTextarea
- resultFitMessagesMethodInput
- resultAutoGenerateMemoriesInput
- resultMetaTitleInput
- resultMetaDescriptionInput
- resultMetaImageInput
- resultUserCharacterNameInput
- resultUserCharacterAvatarUrlInput
- resultUserCharacterRoleInstructionTextarea
- resultUserCharacterReminderTextarea
- resultSystemCharacterNameInput
- resultSystemCharacterAvatarUrlInput
- resultShortcutButtonsTextarea

Output actions:

- copyJsonButton
- downloadJsonButton
- openInAiCharacterChatButton
- createAiCharacterChatCloudLinkButton
- copyAiCharacterChatLinkButton
- scrollToSourceButton

Preview:

- characterJsonPreview always reflects current editor values.

## 4.12 Extraction Details Panel

Read-only diagnostics:

- sourceUrlInput
- sourceTypeInput
- originalUrlInput
- extraInstructionsEchoInput
- extractedContentTextarea
- rawAiResponseTextarea

Use this to debug poor outputs or source parsing issues.

## 5. Supported Inputs and Adapters

## 5.1 Direct and adapted URLs

v6 has special handling for:

- aicharactercards.com
- char-archive.evulid.cc
- character-tavern.com
- cards.character-tavern.com
- characterhub.org and chub.ai
- janitorai.com and jannyai.com
- sakura.fm
- character.ai
- fandom wikis

## 5.2 Content type handling

- application/pdf -> PDF.js text extraction
- image/\* -> metadata card import attempt, else image-driven character generation
- html/text -> Readability + site heuristics + metadata extraction

## 6. Export and ai-character-chat Compatibility

v6 exports full Dexie envelopes with all expected tables, including empty rows arrays where needed.

Character rows are normalized with defaults for required ai-character-chat fields.

This is specifically designed to avoid import errors tied to missing table entries.

## 6.1 Export modes recap

- individual:
- one Dexie JSON per success

- combined:
- one Dexie JSON containing all successful characters

- both:
- individual files plus combined file

## 6.2 Combined export notes

- Combined file is created only if at least one success exists.
- Manifest records combined metadata.
- Combined filename uses template tokens and sanitization.

## 7. File Naming Rules

Sanitization rules:

- Illegal filename characters are replaced
- whitespace normalized
- repeated separators collapsed
- length truncated for safety

Case transforms:

- original: keep as resolved
- kebab: lower + hyphen style
- snake: lower + underscore style

## 8. Manifest, CSV, and Summary Artifacts

Manifest includes:

- run metadata
- per-entry status
- timing
- attempts
- error category and message
- output file name
- optional cloud link
- optional combined export metadata

CSV includes key row-level run data for analysis.

Summary text includes:

- success rate
- average duration
- retries used
- top error category
- failed URL list
- published cloud links list

## 9. Practical Usage Recipes

## 9.1 High quality single character

Recommended:

- source preset profile: strict
- detailed extra instructions
- review extraction details
- manually refine roleInstruction and initialMessages

## 9.2 Large queue with stable throughput

Recommended:

- profile preset: quality or cautious
- concurrency: 1 or 2
- delay: 350 to 800 ms
- retries: 2 to 5
- backoff: exponential-jitter

## 9.3 Building one import file for many characters

Recommended:

- JSON output mode: combined
- collision policy: append or overwrite (less relevant in combined mode)
- auto download: on
- validation: all enabled

## 9.4 Shared persona framework across many URLs

Recommended:

- use global settings to enforce common:
- modelName
- maxTokens
- fit method
- writing instructions
- wrapper style

Then queue mixed source URLs and export combined.

## 10. Troubleshooting

## 10.1 Empty or weak result

Try:

- source preset aggressive for sparse pages
- increase source caps for relevant source type
- improve extra instructions specificity
- inspect extractedContentTextarea

## 10.2 Too many failures in batch

Try:

- lower concurrency
- increase delay
- increase retries
- switch backoff to exponential-jitter
- disable strict mode temporarily

## 10.3 Combined file not created

Likely causes:

- JSON output mode is individual only
- no successful queue entries

## 10.4 ZIP not produced in combined-only mode

Expected behavior:

- ZIP export is skipped when no individual files are generated.
- Use both mode if you want combined + ZIP.

## 10.5 Save to folder not working

Likely cause:

- Browser does not support File System Access API or permission denied.

Fallback:

- keep auto download enabled.

## 10.6 ai-character-chat import issue

Check:

- exported file is from v6 run
- file begins with formatName: dexie and contains data.tables plus data.data
- if importing combined file, verify it has at least one characters row

## 11. Tips for Better Character Quality

- Prefer source pages with rich personality/behavior examples.
- Use extra instructions for style and boundaries, not full rewrites.
- Keep roleInstruction focused and specific after generation.
- Use initialMessages for immediate in-character momentum.
- For fandom/wiki sources, verify cleaned sections in extracted content.

## 12. Privacy and Safety Notes

- URLs and content are fetched during generation.
- Character data and remembered settings are stored locally in browser storage when enabled.
- Cloud links upload compressed payloads via upload plugin.
- Review your output before sharing public links.

## 13. Automation via Query Parameters

Supported URL params:

- url
- extraInstructions
- autorun=1 or auto=1

Example behavior:

- Prefills source URL and extra instructions.
- Auto-runs generation when autorun or auto is set to 1.

## 14. What Is Remembered

When Remember inputs is enabled, v6 stores values for:

- source/extra/scratchpad controls
- most batch options
- v6 global settings
- validation toggles and thresholds

Storage keys:

- prefix: generateCharacterFromUrlV6Remembered\_
- last manifest: generateCharacterFromUrlV6LastManifest

## 15. Keyboard and Interaction Notes

- Press Enter in Source URL field to trigger single generation.
- Queue controls are state-aware:
- pause/resume only active during batch
- stop/skip/retry affect current run only

## 16. Best-Practice Checklist

Before running big batches:

- Confirm output mode (individual/combined/both)
- Confirm filename templates
- Confirm validation rules
- Confirm source caps and timeouts
- Decide on strict mode and dedupe
- Optionally enable save-to-folder and test one run first

Before importing into ai-character-chat:

- Open one output and sanity-check name and roleInstruction
- Confirm expected character count in combined file
- Keep manifest and CSV for traceability

---

End of user guide.
