[中文](README_CLI.md)

- Command-line manual
- [HTTP API manual](http/README_en.md)

# Command-line manual

### Basics

The CLI entry point is the main program `Umi-OCR.exe`. If you use the alternate launcher (e.g. `UmiOCR-data/RUN_GUI.bat`), the CLI may not work.

![Umi-OCR global settings - service](https://tupian.li/images/2023/10/25/653907e9bac06.png)

As shown above, HTTP service must be enabled for the CLI (on by default). **Local only** is enough for the host.

> Umi-OCR uses the HTTP API for cross-process communication: your CLI arguments are sent to the background worker. Traffic stays on the local loopback and does not leave the machine (not routed through the physical NIC).

**Help:** `umi-ocr --help`

### Window control

**Show main window:** `umi-ocr --show`

**Hide main window:** `umi-ocr --hide`

**Quit application:** `umi-ocr --quit`

### OCR commands

**Mouse screenshot:** `umi-ocr --screenshot`

<details>
<summary><b>Region screenshot</b> (no mouse selection)</summary>
</br>
Capture a specific monitor or rectangle automatically.

**Region screenshot:**

```bash
umi-ocr --screenshot screen=0 rect=x,y,w,h
```

Parameters:

- `screen`: Monitor index (0-based) when multiple monitors are present. Default `0`.
- `rect`: Region `x,y,width,height`. Default: full screen.

Notes:

- Do not prefix these with `--`.
- At least one of the two must be set to use region mode. With neither, normal mouse screenshot runs.

Example 1: Full screen on the first monitor

```bash
umi-ocr --screenshot screen=0
```

Example 2: On the second monitor, rectangle from (50,100) with size 300×200

```bash
umi-ocr --screenshot screen=1 rect=50,100,300,200
```

Example 3: With [HotkeysCMD](https://github.com/hiroi-sora/HotkeysCMD), bind a **hotkey** to region capture.

Add this line to HotkeysCMD’s config so **F10** runs region screenshot:

```bash
F10 umi-ocr --screenshot screen=0 rect=50,100,300,200
```

See the [HotkeysCMD](https://github.com/hiroi-sora/HotkeysCMD) docs for more hotkey definitions.

</details></br>

**Paste image:** `umi-ocr --clipboard`

**Paths:** `umi-ocr --path "D:/xxx.png"`

- You can pass a folder path; all images under it (including subfolders) are processed and results combined.
- You can pass multiple paths. Wrap each path in double quotes `""` and separate with spaces.

**Multiple paths example:** `umi-ocr --path "D:/img1.png" "D:/img2.png" "D:/image/test"`

Tips:

- Batch images can take a long time; do not start another command until the previous one finishes.
- For screenshot, paste, and path commands, OCR options (language, copy to clipboard, show main window) follow the **Screenshot OCR** tab. To avoid popping the main window from CLI, turn that off on the **Screenshot OCR** tab.

### QR code commands

**Decode QR/barcode:** `umi-ocr --qrcode_read "D:/xxx.png"`

- Same as OCR: multiple images and folders are supported.

**Encode QR code:** `umi-ocr --qrcode_create "text" "D:/out.jpeg"`

- Default size is the minimum that fits the content. You can append numbers for width and height:

Same width and height 128 px: `umi-ocr --qrcode_create "text" "D:/out.jpeg" 128`

Width 128, height 256: `umi-ocr --qrcode_create "text" "D:/out.jpeg" 128 256`

### Abbreviations

- Commands can be shortened to a unique prefix, e.g. `--screenshot` → `--sc`, `--clipboard` → `--clipbo`. Try what works on your system.
- On many systems you can invoke with a lowercase basename without `.exe`, e.g. `umi-ocr --sc` is equivalent to `Umi-OCR.exe --sc`.

---

### CLI output

- **Copy to clipboard** ` --clip`
- **Write file (overwrite)** ` --output "file.txt"`
- **Write file (append)** ` --output_append "file.txt"`

Arrow aliases:

- `"-->"` is the same as `--output`
- `"-->>"` is the same as `--output_append`

Examples:

```bash
umi-ocr --screenshot --clip
umi-ocr --screenshot --output test.txt
umi-ocr --screenshot "-->" test.txt
```

> Due to runtime limits, Umi-OCR cannot always use stdout redirection; `>` and `|` may not work.  
> If another program invokes the CLI and gets no stdout, use [HTTP argv forwarding](http/argv_en.md#/argv) instead.

---

## Advanced commands

(For experienced developers only.)

Advanced commands can call arbitrary functions on any tab (module), but you need to read the source to know which function and parameters to use.

### Page commands

> “Page templates” are like favorites—you open a new page from a template.  
> “Open pages” can be closed.

List open pages and all templates (returns `[index]`):

```
umi-ocr --all_pages
```

New tab: `[index]` is the template index

```
umi-ocr --add_page [index]
```

Close a tab: `[index]` is an existing page index

```
umi-ocr --del_page [index]
```

### Module commands

> Each tab usually has a py module and a qml module; there may also be standalone modules.  
> Each module exposes callable functions.

Module name `[name]` can be abbreviated: e.g. `ScreenshotOCR_1` → `ScreenshotOCR`.  
The numeric suffix may differ each run—use the short form to ignore it.

List py and qml modules (returns `[name]`):

```
umi-ocr --all_modules
```

### Function commands

List callable functions on a py module (`[name]` = module name):

```
umi-ocr --call_py [name]
```

List callable functions on a qml module:

```
umi-ocr --call_qml [name]
```

Call a py function:

- `[name]` module, `[function]` name, `[..paras]` optional arguments.
- Parameters are strings parsed into `int`, `float`, `list`, or `dict` when possible.

```
umi-ocr --call_py [name] --func [function] [..paras]
```

Call a qml function:

```
umi-ocr --call_qml [name] --func [function] [..paras]
```

Example: call the path-scan function on the QR tab’s qml module with a list of paths:

```
umi-ocr --call_qml QRCode --func scanPaths '[\"D:/Pictures/Screenshots/test/qr/1111.png\",\"D:/Pictures/Screenshots/test/qr/2222.png\"]'
```

### Synchronous calls

> The CLI parser runs on a worker thread. For thread safety, calls are marshaled to the main thread by default—so they are asynchronous and return values are not available.  
> Pass `--thread` to run synchronously on the calling thread (unsafe; may crash or misbehave).

```
umi-ocr --call_qml [name] --func [function] --thread [..paras]
```

---

### Advanced example

Goal: add PDFs to the app and produce searchable layered PDFs.

##### 1. (Optional) Open the **Batch documents** tab if it is not open:

- 1.1 List templates:

```bash
umi-ocr --all_pages
```

- 1.2 If `BatchDOC` has `template_index` `3`:

```bash
umi-ocr --add_page 3
```

- 1.3 Check that `BatchDOC` exists:

```bash
umi-ocr --all_modules
```

If you see `BatchDOC_1` under Qml modules, you are good.

##### 2. Pass document paths into the app:

Example files:

```bash
C:\Users\My\Desktop\111.epub
C:\Users\My\Desktop\222.pdf
```

Use (backslashes → forward slashes in paths):

```bash
umi-ocr --call_qml BatchDOC --func addDocs '[ \"C:/Users/My/Desktop/111.epub\", \"C:/Users/My/Desktop/222.pdf\"]'
```

Path string rules for `addDocs`:

- In PowerShell: outer **single** quotes, and a **space** before each `\"`. Pattern: `'[■\"path_1\",■\"path_2\"]'` (replace `■` with a space). Single path: `'[■\"path\"]'`.
- In Terminal: outer **double** quotes: `"[\"path_1\",\"path_2\"]"`.

This follows Windows shell quoting rules, not Umi-specific design.

##### 3. Start the job:

```bash
umi-ocr --call_qml BatchDOC --func docStart
```

You cannot change the output format from the CLI yet (default: layered searchable PDF). Add other formats in the UI first.
