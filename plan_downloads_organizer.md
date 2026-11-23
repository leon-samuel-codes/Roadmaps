# Plan — Downloads Folder Organizer

**Script:** `organize_downloads.py`
**Concepts:** dicts, loops, string methods, `os` basics, error handling.
**New:** `shutil.move()`, `os.makedirs()`, `pathlib` as an optional cleaner alternative.
**Safety rule:** never run a live pass against a real Downloads folder during testing — dry-run first, every time.

## Definition of done

Pointed at a folder, it correctly sorts files into category subfolders, handles
unknown extensions gracefully, and prints a summary. Dry-run mode exists and is
the way this is tested before ever running it for real.

## Phases

### Phase 1 — Define the mapping
- `CATEGORIES` dict mapping file extensions to category folder names:
  ```python
  CATEGORIES = {
      ".pdf": "Documents", ".docx": "Documents", ".txt": "Documents",
      ".jpg": "Images", ".png": "Images",
      ".mp4": "Videos", ".mov": "Videos",
      ".mp3": "Audio",
      ".zip": "Archives",
  }
  ```
- Fallback constant: `FALLBACK_CATEGORY = "Other"`.
- Same dict-lookup pattern as the FRIDAY `APPS` dict.

### Phase 2 — Scan the target folder
- Hardcode a default Downloads path first (`os.path.expanduser("~/Downloads")`),
  make it configurable later via `--folder`.
- `os.listdir(folder)` for every entry; filter out subdirectories with
  `os.path.isfile(...)` so only files are processed.

### Phase 3 — Determine each file's category
- Extension via `os.path.splitext(filename)[1].lower()`.
- Look it up in `CATEGORIES`; missing key → `"Other"` (never crash, never skip silently).

### Phase 4 — Create destination folders
- `os.makedirs(destination_path, exist_ok=True)` before moving anything —
  `exist_ok=True` prevents an error if the folder already exists.
- In dry-run mode, do **not** create folders (preview only).

### Phase 5 — Move the files
- `shutil.move(source_path, destination_path)` per file.
- Collision handling: if a same-named file exists at the destination, rename to
  `file (1).ext` (increment until free) rather than overwriting. Track how many
  renames happened.
- Wrap the move in `try/except OSError` and report failures without aborting the run.

### Phase 6 — Print a summary
- Track counts per category (`collections.Counter` or a plain dict).
- Output shaped like:
  ```
  Moved 12 files:
    Documents: 5
    Images: 4
    Videos: 2
    Other: 1
  ```
- Also report collisions renamed and hidden files skipped.

### Phase 7 — Dry-run mode *(most important safety feature)*
- argparse flags: `--dry-run` and `--real`.
- **Dry-run is the default** — moving files requires the explicit `--real` flag.
- In dry-run, print `[dry-run] would move: x -&gt; Category/` instead of calling `shutil.move()`.
- Print a `Mode: DRY RUN / LIVE` banner and the target folder up front.

### Phase 8 — Polish
- Check the target folder exists (`os.path.isdir`) before scanning; print a clear error otherwise.
- Guard `os.listdir` with `try/except OSError`.
- Ignore hidden/system files (names starting with `.`) so `.DS_Store` etc. are never moved.

## Verify

1. Create a scratch folder with mixed files (known extensions, an unknown
   extension, a dotfile, a subfolder, and a name-collision case).
2. Run `python organize_downloads.py --dry-run --folder &lt;scratch&gt;` — confirm the
   planned moves, rename note, and hidden-skip count; confirm **nothing moved**.
3. Run the same folder with `--real` — confirm files land in category subfolders,
   collisions got ` (1)` names, and the summary matches the dry-run preview.
4. Run against a nonexistent folder — confirm the clear error message.