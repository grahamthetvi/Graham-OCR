# Translation notes

Translators should follow these rules.

## Newlines `\n`

Source strings may use `\n` for line breaks. In Qt Linguist they appear as real line breaks.

When translating, use real line breaks—do **not** type the two characters `\` and `n`.

Source:

```
Line one\nLine two
```

Wrong:

```
Line one\nLine two
```

Correct:

```
Line one
Line two
```

## Placeholders `%1`

Strings may contain `%number` placeholders. Keep every placeholder in the translation.

Source:

```
Port %1 is in use,\nswitching to port %2.
```

Correct:

```
The original port number %1 is occupied.
Switching to the new port number %2.
```

## Markdown `#`

Some sources (e.g. `PagesManager`) contain long Markdown strings.

Preserve the original structure—keep characters such as `#` and the full-width space sequence `　  ` (one full-width space + two half-width spaces) used to insert a blank line in the QML Markdown parser—copy that sequence as-is.

Source:

```
# Screenshot OCR

　  

Screenshot, quick text. Paste images too.
```

Translation:

```
# Screenshot OCR

　  

Screenshot, quick text. Paste images too.
```

## Font scale

Many UI sizes are tied to line height (e.g. button width = 5× line height). Coefficients assume Chinese metrics.

Some languages need more horizontal space than Chinese (e.g. `截屏` vs `Screenshot`). For English UI, slightly **reduce** font size while keeping line height—adjust the per-language scale.

In `Size_`, the translatable string `1.0` maps to:

```
property string languageScale: qsTr("1.0")
```

Change it (e.g. to `0.92`) to scale fonts for that translation.

For testing: Global settings → Advanced → bottom → `developer tools` → `languageScale (textScale)` for live preview, then copy the value into the translation file.

## Plain text / machine translation

If Python is installed, drag a `.ts` onto `convert_ts_txt.py` to produce `en_US.txt` (one source line per row).

Or from a shell:

```
convert_ts_txt.py en_US.ts
```

Output: `en_US.ts` → `en_US.txt`

(At this step newlines are represented as `\n`; converting back to `.ts` restores them.)

You can then process `en_US.txt` automatically, e.g.:

```
You are helping translate app UI strings.
Each input line is one source string. Translate to English.
Preserve special formatting: newlines, placeholders starting with %, Markdown, etc.

[paste text here]
```

Overwrite `en_US.txt` with the result (back up first). Use a line-numbered editor to verify line alignment.

Then drag `en_US.txt` onto `convert_txt_ts.py` to generate `en_US.txt.ts` (back up `.ts` first).

Open `en_US.txt.ts` in Linguist, review, compile to `en_US.txt.qm`, rename to `en_US.qm`, and test in Umi-OCR.
