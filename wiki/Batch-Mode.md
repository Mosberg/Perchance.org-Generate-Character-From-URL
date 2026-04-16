# Batch Mode

Process a list of URLs automatically, with retries, validation, and flexible export options.

---

## Getting started

1. Paste URLs into the **Batch URL** textarea — one per line, or separated by commas/semicolons
2. Optionally drag-and-drop a `.txt` or `.csv` file onto the drop zone
3. Configure settings (see below)
4. Click **Start queue**

---

## Profile presets

Pick a preset to fill in sensible defaults:

| Preset     | Concurrency | Retries | Backoff            | Delay  |
| ---------- | ----------- | ------- | ------------------ | ------ |
| `fast`     | 3           | 1       | fixed              | 100 ms |
| `quality`  | 1           | 3       | exponential        | 500 ms |
| `cautious` | 1           | 5       | exponential-jitter | 700 ms |
| `custom`   | —           | —       | —                  | —      |

`custom` leaves whatever you've set manually untouched.

---

## Key settings

### Concurrency

How many URLs are processed in parallel (1–3). Higher concurrency is faster but increases the chance of rate limiting.

### Base delay

Milliseconds to wait between URLs. Raise this if sources start blocking requests.

### Retry count

How many times to retry a failed URL after the first attempt (0–5).

### Backoff strategy

How wait time grows between retries:

- `fixed` — same delay every time
- `exponential` — doubles each retry
- `exponential-jitter` — exponential with randomness to spread load

### Max total time per URL

Hard limit in milliseconds for a single URL including all retries. Prevents one stuck URL from blocking the queue.

---

## Source preset profile

| Profile      | Behavior                                                            |
| ------------ | ------------------------------------------------------------------- |
| `balanced`   | Default extraction settings                                         |
| `strict`     | Rejects results with no name or fewer than 180 extracted characters |
| `aggressive` | Doubles generic and fandom extraction caps                          |

---

## Validation gates

Optional checks that reject weak outputs before saving:

- Require character name
- Require role instruction
- Minimum role instruction length
- Maximum JSON file size

Enable these under **Validation controls**.

---

## Queue controls during a run

| Button         | What it does                                 |
| -------------- | -------------------------------------------- |
| Pause / Resume | Halt at the next checkpoint and continue     |
| Stop           | Cleanly end the run after the current URL    |
| Skip current   | Move on from the current URL without waiting |
| Retry current  | Force an immediate retry of the current URL  |

---

## Resuming failed URLs

Click **Load failed from last manifest** to populate the textarea with only the URLs that failed in the previous run — useful for targeted retry sessions.

---

## Dedupe

Enable **Deduplicate results** to skip characters whose name + role instruction matches one already saved in the current run.

---

## Review mode

Enable **Review mode (pause each)** to confirm or skip each generated character before it is exported. Useful for quality control on small batches.

---

## Logs and report

- **Batch log** — chronological log of all events
- **Report card** — summary metrics after the run (success rate, duration, retries, top errors)
- **Copy summary** — copy a text summary to clipboard
- **Download CSV** — download a per-URL report spreadsheet

---

## Stop on error

Enable **Stop on first error** to abort the entire run when any URL fails after all retries. Off by default.

---

See also: [[Export Options]] · [[Settings Reference]] · [[Troubleshooting]]
