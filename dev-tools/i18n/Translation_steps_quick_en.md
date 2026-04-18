# Translation workflow (quick)

A short path for non-developers who want to translate Umi-OCR.

## Using Git

Fork or clone this repo. Prefer **only** the `main` branch—some branches ship large binaries and take a long time to download.

```
 git clone --branch main --single-branch https://github.com/hiroi-sora/Umi-OCR_v2.git
```

## Not using Git

1. Fork the project.
2. Download ZIP: project home → green **Code** → **Download ZIP**.
3. Translate locally.

## Translating

Several `.ts` files live in this directory—each maps to a language. They may already contain partial work (MT or other contributors).

Pick the file for your language and open it in Qt Linguist (see below). Improve or fill in translations.

If your language file is missing, see [Translation steps (full)](Translation_steps_full_en.md) to generate it.

| File       | Language   |
| ---------- | ---------- |
| `zh_CN.ts` | Simplified Chinese |
| `zh_TW.ts` | Traditional Chinese |
| `en_US.ts` | English   |
| `es_ES.ts` | Español   |
| `fr_FR.ts` | Français  |
| `de_DE.ts` | Deutsch   |
| `ja_JP.ts` | Japanese  |
| `ko_KR.ts` | Korean    |
| `ru_RU.ts` | Russian   |
| `pt_BR.ts` | Portuguese |
| `it_IT.ts` | Italian   |

Drag the file onto `linguist.exe` in this folder to open it.

Linguist basics:

1. **Context** (left): source files and counts, e.g. `BatchOCR 18/19` means 18 of 19 strings done in `BatchOCR`.
2. **Strings** (top middle): each string; `?`/`!` = unfinished, `✓` = done—mark each finished string as `✓`.
3. **Sources and Forms** (right): where the string appears in code—use comments for context.
4. Center: **Source text** vs **Translation**; translator comments are optional.

Before you start, read [Translation notes](Translation_notes_en.md).

When done, in Linguist use **File → Release** to build `en_US.qm` (for `en_US.ts`) next to the `.ts`.

Copy the `.qm` to:

```
Umi-OCR_v2/UmiOCR-data/i18n
```

Launch Umi-OCR and switch language in global settings. Check wording and sizing; adjust font scale per [Translation notes](Translation_notes_en.md).

## Submitting

### Using Git

Commit the updated `.ts` here and the `.qm` under `Umi-OCR_v2/UmiOCR-data/i18n`. Do not add unrelated files.

Open a PR.

### Not using Git

1. Open your translated `xxx.ts` in a text editor, select all, copy.
2. On GitHub, open the same `xxx.ts` in **your** fork.
3. Click the pencil (**Edit**), replace contents, **Commit changes**.
4. Open a PR toward the main repo.

Thank you to all contributors!
