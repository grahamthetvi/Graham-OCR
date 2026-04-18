# Translation workflow (full)



## Setup

This document describes the full Qt translation workflow for Umi-OCR.

Fork or clone the repo. Prefer **only** `main`—some branches contain large binaries.

```
 git clone --branch main --single-branch https://github.com/hiroi-sora/Umi-OCR_v2.git
```

**Windows** is assumed for tool paths below.

Run commands from `dev-tools/i18n` unless noted.

Example: **Win+R** → `cmd` →

```
cd /d D:\Projects\Umi-OCR_v2\dev-tools\i18n
```

This guide uses `en_US` as the target locale—replace with your locale id everywhere:

| ID      | Language   |
| ------- | ---------- |
| `zh_CN` | Simplified Chinese |
| `zh_TW` | Traditional Chinese |
| `en_US` | English   |
| `es_ES` | Español   |
| `fr_FR` | Français  |
| `de_DE` | Deutsch   |
| `ja_JP` | Japanese  |
| `ko_KR` | Korean    |
| `ru_RU` | Russian   |
| `pt_BR` | Portuguese |
| `it_IT` | Italian   |

Locales not listed but close to one above reuse the nearest file (e.g. `zh_HK`, `en_GB`, `en_CA`, `es_MX`, `fr_CA`, `de_AT`, `de_CH`, `pt_PT`).

To add a completely new locale, extend the mapping in `UmiOCR-data\py_src\utils\i18n_configs.py` or ask the maintainers.

## Overview

Three steps (example: English `en_US`):

1. Extract strings from source → `en_US.ts`.
2. Translate `en_US.ts`.
3. Compile `en_US.ts` → `en_US.qm` and load it in Umi-OCR.

## 1. Extract strings

### Option A: one `.ts`

Use Qt `lupdate.exe`:

```cmd
lupdate.exe "../../UmiOCR-data/qt_res/qml" -recursive -ts "en_US.ts"
```

If `lupdate.exe` is not found, prefix with `./`:

```cmd
./lupdate.exe "../../UmiOCR-data/qt_res/qml" -recursive -ts "en_US.ts"
```

Success looks like:

```cmd
Scanning directory '../../UmiOCR-data/qt_res/qml'...
Updating 'en_US.ts'...
    Found 307 source text(s) (140 new and 167 already existing)
    Kept 42 obsolete entries
    Same-text heuristic provided 21 translation(s)
```

Re-running `lupdate.exe` after edits is safe—existing work is preserved (still keep backups).

### Option B: all languages

Run `lupdate_all.py` in this directory.

## 2. Translate

Open `linguist.exe`:

```cmd
linguist.exe "en_US.ts"
```

Or drag `en_US.ts` onto `linguist.exe`.

First open: pick **Source language** Chinese (Simplified) and your target language; **Country** can be **Any Country**.

Linguist basics:

1. **Context**: files and counts, e.g. `BatchOCR 18/19`.
2. **Strings**: `?`/`!` unfinished, `✓` done.
3. **Sources and Forms**: code locations.
4. Center: **Source text** vs translation.

Read [Translation notes](Translation_notes_en.md) first.

## 3. Compile

**In Linguist:** **File → Release** → `en_US.qm` next to the `.ts`.

**CLI:**

```cmd
lrelease.exe "en_US.ts"
```

## Install `.qm`

Copy `en_US.qm` to:

```
Umi-OCR_v2/UmiOCR-data/i18n
```

Run Umi-OCR, switch language in settings, verify text and layout; adjust font scale per [Translation notes](Translation_notes_en.md).

## Submitting

Commit updated `.ts` files here and `.qm` files under `Umi-OCR_v2/UmiOCR-data/i18n`. No unrelated files.

Open a PR.
