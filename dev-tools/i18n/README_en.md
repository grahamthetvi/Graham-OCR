## Contributing translations (translators)

Use the Weblate project—register or sign in with GitHub:

https://hosted.weblate.org/engage/umi-ocr/

You can improve existing languages or add new ones.

## Maintaining `.ts` files (developers)

### Generate or update `.ts` from source

1. Run `lupdate_all.py`
2. Commit changes and wait for Weblate to pick them up

### Build `.qm` from `.ts` and load in the app

1. Run `lrelease_all.py`
2. Under `/release`, find all `.qm` files and copy them to `Umi-OCR/UmiOCR-data/i18n`

## Translating plugin UIs

Plugins (e.g. engines) use a lighter CSV-based workflow:

1. Open the [plugins repo](https://github.com/hiroi-sora/Umi-OCR_plugins) and find a plugin folder.
2. Edit `i18n.csv` (Excel/WPS; if garbled, search for UTF-8 CSV tips).
3. Edit the table in Excel.
4. Save the CSV.
5. Ensure the file is **UTF-8** (e.g. convert in VS Code).
6. Open a PR on the plugins repo.
