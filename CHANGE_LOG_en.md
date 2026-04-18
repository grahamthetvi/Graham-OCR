# Branch usage

Click the version links in the list below to open the corresponding backup branch page.

Some backup branches contain large binary libraries and take a long time to download. Download only the branches you need.

**Method 1:** Fork the branch you need into your own account, then clone your fork.

**Method 2:** Clone a specific branch manually:

```
git clone --single-branch --branch [branch-name] https://github.com/hiroi-sora/Umi-OCR.git
```

**Method 3:** Download the ZIP of the specified branch from this repository.

`[branch-name]` can be `main`, `release/2.0.0`, etc.—see the list below.

Branches such as `main` and `dev` may contain unstable work-in-progress features. For research, learning, or downstream development, prefer branches whose names start with `release`.

# Changelog

### v2.1.3 `2024.7.24`

Branch name: `main`

- **Fix:** For the HTTP document recognition API, non-ASCII characters in uploaded filenames are preserved instead of stripped.
- **Improvement:** HTTP document API sample code: the Python example handles non-ASCII filenames better on some Linux systems; the HTML example adds pause/clear task buttons.
- **Improvement:** For the layout parser’s **single-column, single-line** mode, a space is inserted between two adjacent text blocks when they are far apart.

### v2.1.3 beta.1 `2024.7.13`

- **New:** Linux platform support.
- **New:** HTTP document recognition API.
- **Fix:** Abnormal images with zero width or height in documents could hang the task. (#562)

### [v2.1.2](https://github.com/hiroi-sora/Umi-OCR/tree/release/2.1.2) `2024.6`

Branch name: `release/2.1.2`

- **New:** Batch tasks (images, documents) support **pausing**. As long as the app stays open, tasks can resume after standby/sleep. (#469)
- **New:** Batch document tasks can save as **single-layer plain-text PDF**.
- **New:** HTTP OCR API accepts an **ignore region** parameter. (#525)
- **New:** HTTP QR code API accepts **image preprocessing** parameters. (#512)
- **New:** CLI `--screenshot` can take a screen region and capture automatically. (#452)
- **Fix:** Coordinate rotation and scaling when extracting and writing document content.
- **Fix:** When copying across blocks in screenshot preview & history, the last block’s character count was wrong.
- **Improvement:** Performance and responsiveness when dropping many files or submitting large batches on the batch document tab. (#476)
- **UI:** Layout tweaks and visual polish.

### [v2.1.1](https://github.com/hiroi-sora/Umi-OCR/tree/release/2.1.1) `2024.3.29`

Branch name: `release/2.1.1`

- **New:** Batch document recognition supports a page range for ignore regions.
- **New:** CLI `--path` accepts multiple images or folders.
- **New:** CLI and HTTP APIs to recognize/generate QR code images.
- **New:** CLI options `--output`, `--output_append`, `--clip`.
- **New:** Shortcut to clear all recognition history. (#420)
- **New:** Screenshot preview: option and shortcut to remove the image display. (#427)
- **Fix:** Esc canceling screenshot sent the wrong message. (#402)
- **Fix:** In document **full-page forced OCR** mode, better rendering quality for small pages.
- **Fix:** Document recognition failed on Windows 7 and low-end CPUs.
- **Improvement:** Start menu / autostart shortcuts default to the user profile to avoid admin prompts.
- **Improvement:** Saving layered PDF when no new text is written. (#416)
- **Improvement:** Logic for extracting existing text lines when parsing PDFs.
- **Improvement:** Dual-pane panel remembers left/right collapse state.
- **UI:** Clearer prompts, rendering fixes, visual polish.

### [v2.1.0](https://github.com/hiroi-sora/Umi-OCR/tree/release/2.1.0) `2024.2.29`

Branch name: `release/2.1.0`

- **New:** Batch document recognition. Supports `pdf`, `epub`, `mobi`, etc.
- **New:** Option to hide the tray icon. (#338)
- **New:** Shortcut to repeat the last screenshot region. (#357)
- **New:** Quick button to open the image in the default viewer. (#335)
- **Update:** Stronger layout parser.
- **Fix:** Avoid interference from the `QMLSCENE_DEVICE` environment variable. (#270)

### [v2.0.2](https://github.com/hiroi-sora/Umi-OCR/tree/release/2.0.2) `2024.1.15`

Branch name: `release/2.0.2`

- **Update:** Global setting for default on/off state of the image text overlay. (#264)
- **Improvement:** When outputting to separate `txt` files, the specified path is applied. (#269)
- **Improvement:** `Paragraph merge – multi-line – code block` removes extra trailing newlines. (#292)
- **Improvement:** When the renderer is incompatible, reduce the impact of render-layer errors. (#259)
- **Fix:** Check before clearing image cache to avoid empty-image errors. (#279)
- **Fix:** Caret could not move before the first character in the history panel. (#264)
- **Fix:** Startup failed when the system language was not Simplified Chinese. (#274) (#306)
- **Fix:** CSV output encoding compatibility. (#284)
- **Fix:** False positive “paragraph merge parameter does not exist”.
- **Fix:** `DefaultTips` component had no effect.

### [v2.0.1](https://github.com/hiroi-sora/Umi-OCR/tree/release/2.0.1) `2023.12.8`

Branch name: `release/2.0.1`

- **Update:** Redesigned OCR HTTP API: optional parameters, configurable paragraph merge.
- **Update:** CLI adds `-->` and `-->>` to write results to files.
- **Improvement:** Internal image encoding tweaks to reduce loss of English spaces.
- **Improvement:** Some UI strings and layout.
- **Improvement:** CSV defaults to ANSI for Excel compatibility. (#237)
- **Fix:** With “disable visual effects” on, external notification popups could not be closed. (#234)
- **Fix:** When another program invoked Umi-OCR via CLI, stdout was not captured.

### [v2.0.0](https://github.com/hiroi-sora/Umi-OCR/tree/release/2.0.0) `2023.11.19`

Branch name: `release/2.0.0`

- **Improvement:** Plugin UI translation mechanism.
- **Improvement:** Dropdown UI.
- **Fix:** Bug in paragraph merge – natural paragraph mode.

### v2.0.0 dev `2023.11.14`

- **New:** QR code generation.
- **New:** All image preview windows (screenshot, QR page, etc.) can save images to a path.
- **Improvement:** QR library switched to zxingcpp for better performance and features. ([v2 #47](https://github.com/hiroi-sora/Umi-OCR_v2/issues/47)) (Thanks: @Byxs20)
- **Improvement:** More accurate text box positions in screenshot preview.
- **Fix:** Could not copy local images in image preview windows.
- **Fix:** Incorrect vertical distance when tbpu merged natural paragraphs.
- **Fix:** HTTP API CORS. [v2 #52](https://github.com/hiroi-sora/Umi-OCR_v2/issues/52)
- **Fix:** HTTP API base64 size limit. [v2 #49](https://github.com/hiroi-sora/Umi-OCR_v2/issues/49)
- **Fix:** Other minor errors.
- **Translation:** Manual review for `Traditional Chinese` and `English`. (Contributed by: @QZGao)

### v2.0.0 dev `2023.11.5`

- **New:** Remember window position. [v2 #44](https://github.com/hiroi-sora/Umi-OCR_v2/issues/44)
- **New:** Batch image tab: image preview; click an entry to open. [v2 #2](https://github.com/hiroi-sora/Umi-OCR_v2/issues/2)
- **New:** Check read/write access to the config file. [v2 #30](https://github.com/hiroi-sora/Umi-OCR_v2/issues/30)
- **New:** Error dialog: one-click copy and open issues.
- **New:** Global settings: left sidebar table of contents.
- **New:** Multi-language UI for plugins.
- **Improvement:** More accurate text box positions in screenshot preview.
- **Improvement:** Some UI layout tweaks.
- **Fix:** Import error checks for the scan module. [v2 #33](https://github.com/hiroi-sora/Umi-OCR_v2/issues/33)
- **Fix:** Drag-and-drop images on the scan page. [v2 #43](https://github.com/hiroi-sora/Umi-OCR_v2/issues/43)
- **Fix:** When outputting to a separate txt file, strip the original extension from the filename. [v2 #36](https://github.com/hiroi-sora/Umi-OCR_v2/issues/36)
- **Fix:** Minor bugs.

### v2.0.0 dev `2023.10.25`

- **New:** CLI accepts image paths. [v2 #28](https://github.com/hiroi-sora/Umi-OCR_v2/issues/28)
- **New:** HTTP API supports Base64 images. [v2 #28](https://github.com/hiroi-sora/Umi-OCR_v2/issues/28)
- **New:** Ignore regions.
- **New:** QR code page: multiple barcode/QR formats. ([Umi-OCR #95](https://github.com/hiroi-sora/Umi-OCR/issues/95))
- **New:** Alternate launcher `UmiOCR-data/RUN_GUI.bat` when `Umi-OCR.exe` is incompatible. [v2 #21](https://github.com/hiroi-sora/Umi-OCR_v2/issues/21)
- **Improvement:** Image preview: `Tab` toggles text overlay.
- **Improvement:** History panel: copy button on each record. [v2 #32](https://github.com/hiroi-sora/Umi-OCR_v2/issues/32)
- **Improvement:** History panel: pointer can leave the text area while dragging. [v2 #32](https://github.com/hiroi-sora/Umi-OCR_v2/issues/32)
- **Improvement:** Screenshot cache redesigned to avoid memory leaks when destroying Image components.
- **Improvement:** Dynamic tab loading for slightly faster startup.
- **Improvement:** Runtime moved to 64-bit (32-bit support planned to be dropped).
- **Fix:** Boolean config parsing. [v2 #30](https://github.com/hiroi-sora/Umi-OCR_v2/issues/30)
- **Fix:** Dragging non-image files could freeze for seconds.
- **Fix:** PaddleOCR plugin compatibility. ([Umi-OCR #209](https://github.com/hiroi-sora/Umi-OCR/issues/209))

### v2.0.0 dev `2023.10.18`

- **New:** Auto-hide window before screenshot. [v2 #26](https://github.com/hiroi-sora/Umi-OCR_v2/issues/26)
- **New:** Font selection. [v2 #25](https://github.com/hiroi-sora/Umi-OCR_v2/issues/25)
- **New:** Loading animation.
- **New:** Screenshot preview shows OCR text and text selection.
- **New:** Screenshot preview can copy image to clipboard.
- **New:** History panel supports selecting across text boxes. [v2 #18](https://github.com/hiroi-sora/Umi-OCR_v2/issues/18)
- **New:** History panel: delete one or more records. [v2 #10](https://github.com/hiroi-sora/Umi-OCR_v2/issues/10)
- **New:** Esc or right-click cancels screenshot.
- **Improvement:** Plugin directory structure and imports.
- **Fix:** Duplicate files prevented autostart from being added. [v2 #27](https://github.com/hiroi-sora/Umi-OCR_v2/issues/27)

### v2.0.0 dev `2023.10.10`

- **New:** On first launch, pick the best renderer for the system to reduce screenshot flicker. [v2 #7](https://github.com/hiroi-sora/Umi-OCR_v2/issues/7)
- **New:** Initial plugin system for easier engine switching.
- **New:** UI scale (text size).
- **Improvement:** Screenshot tab UI and tab bar shadow. [v2 #8](https://github.com/hiroi-sora/Umi-OCR_v2/issues/8)
- **Improvement:** Double-click notification to open main window. [v2 #10](https://github.com/hiroi-sora/Umi-OCR_v2/issues/10)
- **Improvement:** After screenshot, no success toast if main window is in front. [v2 #10](https://github.com/hiroi-sora/Umi-OCR_v2/issues/10)
- **Improvement:** With visual effects off, external popups skip shadow rendering. [v2 #14](https://github.com/hiroi-sora/Umi-OCR_v2/issues/14)
- **Improvement:** Paddle engine supports Windows 7.

### v2.0.0 dev `2023.9.25`

### v2.0.0 dev `2023.9.8`

- Multiple UI languages (experimental)

### v2.0.0 dev `2023.9.7`

### v2.0.0 dev `2023.8.9`

- Screenshot OCR
- HiDPI and multi-monitor support
- Smarter paragraph merging

### v2.0.0 dev `2023.7.26`

- Batch OCR
- Modern UI
- Custom tab system
- Light/dark themes

---

### [v1.3.7](https://github.com/hiroi-sora/Umi-OCR/tree/release/1.3.7) `2023.10.10`

- Paddle engine compatible with Win7 x64.

### v1.3.6 `2023.9.26`

- **New:** Stronger paragraph merge modes: `single line / multi-line paragraph / multi-line code`. Auto-detects Chinese vs Western paragraphs and applies rules.
- Removed some outdated merge modes.
- **Change:** `Screenshot link` is a separate feature from normal screenshot OCR.

### [v1.3.5](https://github.com/hiroi-sora/Umi-OCR/tree/release/1.3.5) `2023.6.20`

- **New:** After copying OCR text, optionally send key presses to trigger translators and other tools.
- **New:** CLI switch for recognition language.
- **Fix:** False `OCR init timeout: 5s` on low-end machines. [#154](https://github.com/hiroi-sora/Umi-OCR/issues/154), [#156](https://github.com/hiroi-sora/Umi-OCR/issues/156).
- **Change:** Default: release memory once 30s after a task stops.

### [v1.3.4](https://github.com/hiroi-sora/Umi-OCR/tree/release/1.3.4) `2023.4.26`

- **New:** Screenshot preview window.
- **New:** Arrow keys nudge the screenshot frame.
- **Fix:** Rare crash when dragging images [issue #126](https://github.com/hiroi-sora/Umi-OCR/issues/126).
- **Improvement:** Various processing tweaks.

### [v1.3.3](https://github.com/hiroi-sora/Umi-OCR/tree/release/1.3.3) `2023.3.19`

- **New:** CLI mode.
- **New:** Floating notification when OCR completes.
- **New:** Auto engine memory cleanup.
- **Fix:** Various bugs; UI polish.

### [v1.3.2](https://github.com/hiroi-sora/Umi-OCR/tree/release/1.3.2) `2022.12.1`

- **New:** Autostart option: `don’t show main window`.
- **New:** OCR output to a separate txt per image (same basename).
- **New:** Standalone language settings window, reachable from multiple places.
- **New:** Paragraph merge: `merge natural paragraphs – Western mode` adds spaces on English line breaks.
- **New:** Quick OCR option: `auto clear panel`—only this run’s result, hide timestamps.
- **Fix:** Various bugs.

### [v1.3.1](https://github.com/hiroi-sora/Umi-OCR/tree/release/1.3.1) `2022.11.4`

- **Fix:** Hotkey module rewritten with pynput instead of keyboard—fixes intermittent failures and bad recordings.
- **New:** Autostart, desktop shortcut, start menu shortcut.
- **New:** Warning when launching a second instance.
- **New:** Hide window while screenshotting.
- **UI:** Rare settings moved to advanced options.
- **Improvement:** Engine component existence check.
- **Improvement:** `Horizontal – merge multi-line – natural paragraph` supports 0–2 full-width spaces for first-line indent.

### [v1.3.0](https://github.com/hiroi-sora/Umi-OCR/tree/release/1.3.0) `2022.9.29`

- **New:** Region screenshot.
- **New:** System tray icon.
- **New:** Engine process stays resident.
- **New:** Text block post-processing module.
- **New:** Custom main output font.
- **New:** Settings window stay-on-top mode.
- **UI:** Windows-style adaptive controls.
- **Fix:** System language compatibility [issue #16](https://github.com/hiroi-sora/Umi-OCR/issues/16).
- **Fix:** WeChat image paste [issue #22](https://github.com/hiroi-sora/Umi-OCR/issues/22).
- **Update:** PaddleOCR-json to `v1.2.1` with clipboard support; quick OCR uses clipboard without temp files.

### [v1.2.6](https://github.com/hiroi-sora/Umi-OCR/tree/release/1.2.6) `2022.9.1`

- **Update:** PaddleOCR-json to `v1.2.0` for speed and accuracy.
- **UI:** Easier language switching via dropdown.
- **UI:** Drag images from anywhere on the main window / any tab.
- **Fix:** More robust subprocess startup handling.
- **Fix:** Narrow-edge images no longer mis-OCR [issue #7](https://github.com/hiroi-sora/Umi-OCR/issues/7).
- **Improvement:** PP-OCRv3 model tuning—fixes v3 being slower/worse than v2 [issue #4](https://github.com/hiroi-sora/Umi-OCR/issues/4#issuecomment-1141735773).

### v1.2.5 `2022.7.22`

- **New:** Scheduled tasks (e.g. shutdown after OCR).
- **New:** Optional recursive folder import when dropping a folder.
- **UI:** Shortcuts to config files.

### v1.2.4 `2022.6.4`

- **New:** Optional auto-copy text after clipboard image OCR.
- **Extra:** If window is minimized when hotkey triggers clipboard OCR, restore and bring to front.

### v1.2.3 `2022.5.31`

- **New:** Read image from clipboard; global hotkey.

### v1.2.2 `2022.4.30`

- **New:** Optional open output file or folder when task completes.

### v1.2.1 `2022.4.16`

- **Update:** PaddleOCR-json to `v1.1.1`; fixes wrong bounding boxes.

### v1.2.0 `2022.4.8`

- Optional `image links.md` index for nicer browsing.
- Settings panel uses a scrollable layout for more options.
- Config auto-saves after changes.

### v1.1.1 `2022.3.30`

- **Fix:** OCR subprocess not closed when exiting the ignore-region window.

### v1.1.0 `2022.3.30`

- **New:** Ignore-region window shows recognized text blocks as dashed outlines.

### v1.0.0 `2022.3.28`

- Where it all began.
