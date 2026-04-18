## Contents

### Image OCR

1. [Image OCR: query parameters](api_ocr_en.md#/api/ocr/get_options)
2. [Image OCR: Base64 recognition](api_ocr_en.md#/api/ocr)

### Document recognition (PDF)

- [Document workflow](#/api/doc)

### QR code

1. [QR: Base64 decode](api_qrcode_en.md#/api/qrcode)
2. [QR: text to image](api_qrcode_en.md#/api/qrcode/text)

### Command line

- [Argv API](argv_en.md#/argv)

---

> [!TIP]
> Document recognition requires Umi-OCR [v2.1.3 (beta)](https://github.com/hiroi-sora/Umi-OCR/releases) or newer.

<a id="/api/doc"></a>

---

## Document workflow

Typical flow:

0. Before coding, confirm parameters—see [query](#/api/doc/get_options).
1. Upload the file and get a task ID—see [upload](#/api/doc/upload).
2. Poll by ID until OCR finishes—see [result](#/api/doc/result).
3. Request the output file (e.g. layered PDF) and get a download URL—see [download](#/api/doc/download).
4. Download the file—see [download by URL](#/api/doc/download/id).
5. Clear the task—see [clear](#/api/doc/clear).

Sample code:

Python — [api_doc_demo.py](api_doc_demo.py)  
Web — [api_doc_demo.html](api_doc_demo.html)

<a id="/api/doc/get_options"></a>

---

## 0. Document upload: query parameters

> Depending on the OCR engine, **document upload** may accept different parameters.  
> Use **get options** to read definitions, defaults, and allowed values.  
> Call it manually or build UI from the response.


URL: `/api/doc/get_options`

Example: `http://127.0.0.1:1224/api/doc/get_options`

### 0.1. Request

Method: `GET`

### 0.2. Response

JSON describing upload parameters.

PaddleOCR example:

<details>
<summary>Expand</summary>

```json
{
    "ocr.language": {
        "title": "语言/模型库",
        "optionsList": [
            ["models/config_chinese.txt","简体中文"],
            ["models/config_en.txt","English"],
            ["models/config_chinese_cht(v2).txt","繁體中文"],
            ["models/config_japan.txt","日本語"],
            ["models/config_korean.txt","한국어"],
            ["models/config_cyrillic.txt","Русский"]
        ],
        "type": "enum",
        "default": "models/config_chinese.txt"
    },
    "ocr.cls": {
        "title": "纠正文本方向",
        "default": false,
        "toolTip": "启用方向分类，识别倾斜或倒置的文本。可能降低识别速度。",
        "type": "boolean"
    },
    "ocr.limit_side_len": {
        "title": "限制图像边长",
        "optionsList": [
            [960,"960 （默认）"],
            [2880,"2880"],
            [4320,"4320"],
            [999999,"无限制"]
        ],
        "toolTip": "将边长大于该值的图片进行压缩，可以提高识别速度。可能降低识别精度。",
        "type": "enum",
        "default": 960
    },
    "tbpu.parser": {
        "title": "排版解析方案",
        "toolTip": "按什么方式，解析和排序图片中的文字块",
        "default": "multi_para",
        "optionsList": [
            ["multi_para","多栏-按自然段换行"],
            ["multi_line","多栏-总是换行"],
            ["multi_none","多栏-无换行"],
            ["single_para","单栏-按自然段换行"],
            ["single_line","单栏-总是换行"],
            ["single_none","单栏-无换行"],
            ["single_code","单栏-保留缩进"],
            ["none","不做处理"]
        ],
        "type": "enum"
    },
    "tbpu.ignoreArea": {
        "title": "忽略区域",
        "toolTip": "数组，每一项为[[左上角x,y],[右下角x,y]]。",
        "default": [],
        "type": "var"
    },
    "tbpu.ignoreRangeStart": {
        "title": "忽略区域起始",
        "toolTip": "忽略区域生效的页数范围起始。从1开始。",
        "default": 1,
        "type": "number",
        "isInt": true
    },
    "tbpu.ignoreRangeEnd": {
        "title": "忽略区域结束",
        "toolTip": "忽略区域生效的页数范围结束。可以用负数表示倒数第X页。",
        "default": -1,
        "type": "number",
        "isInt": true
    },
    "pageRangeStart": {
        "title": "OCR页数起始",
        "toolTip": "OCR的页数范围起始。从1开始。",
        "default": 1,
        "type": "number",
        "isInt": true
    },
    "pageRangeEnd": {
        "title": "OCR页数结束",
        "toolTip": "OCR的页数范围结束。可以用负数表示倒数第X页。",
        "default": -1,
        "type": "number",
        "isInt": true
    },
    "pageList": {
        "title": "OCR页数列表",
        "toolTip": "数组，可指定单个或多个页数。例：[1,2,5]表示对第1、2、5页进行OCR。如果与页数范围同时填写，则 pageList 优先。",
        "default": [],
        "type": "var"
    },
    "password": {
        "title": "密码",
        "toolTip": "如果文档已加密，则填写文档密码。",
        "default": "",
        "type": "text"
    },
    "doc.extractionMode": {
        "title": "内容提取模式",
        "toolTip": "若一页文档既存在图片又存在文本，如何进行处理。",
        "default": "mixed",
        "optionsList": [
            ["mixed","混合OCR/原文本"],
            ["fullPage","整页强制OCR"],
            ["imageOnly","仅OCR图片"],
            ["textOnly","仅拷贝原有文本"]
        ],
        "type": "enum"
    }
}
```

This example has 12 root parameters.

</details></br>

Field meanings match [image OCR query](api_ocr_en.md#/api/ocr/get_options).

Document-only fields (summary):

- **tbpu.ignoreRangeStart** / **tbpu.ignoreRangeEnd**: page range where ignore regions apply (1-based; negative end = last N pages).
- **pageRangeStart** / **pageRangeEnd**: OCR page range (same indexing rules).
- **pageList**: explicit page numbers; if set with a range, **pageList wins**.
- **password**: PDF password if encrypted.
- **doc.extractionMode**: how to mix OCR and embedded text—`mixed`, `fullPage`, `imageOnly`, `textOnly`.

See also: [Image OCR parameter notes](api_ocr_en.md#/api/ocr/options_info).

Example combined `options`:

<details>
<summary>Expand</summary>

```json
{
    "ocr.language": "models/config_chinese.txt",
    "ocr.cls": true,
    "ocr.limit_side_len": 4320,
    "tbpu.parser": "multi_none",
    "pageRangeStart": 1,
    "pageRangeEnd": 10,
    "doc.extractionMode": "fullPage",
}
```

</details></br>

### 0.3. Query examples

<details>
<summary>JavaScript (click to expand)</summary>

```javascript
const url = "http://127.0.0.1:1224/api/doc/get_options";
fetch(url, {
        method: "GET",
        headers: { "Content-Type": "application/json" },
    })
    .then(response => response.json())
    .then(data => { console.log(data); })
    .catch(error => { console.error(error); });
```

</details>

<details>
<summary>Python (click to expand)</summary>

```python
import json, requests

response = requests.get("http://127.0.0.1:1224/api/doc/get_options")
res_dict = json.loads(response.text)
print(json.dumps(res_dict, indent=4, ensure_ascii=False))
```

</details></br>

Manual check:

- Ensure Umi-OCR is running.
- Open http://127.0.0.1:1224/api/doc/get_options in a browser.
- Copy the JSON into a formatter for readability.

<a id="/api/doc/upload"></a>

---

## 1. Document upload

Upload a file to start OCR; returns a task ID.

URL: `/api/doc/upload`

Example: `http://127.0.0.1:1224/api/doc/upload`

### 1.1. Request

Method: `POST`

Body: `multipart/form-data`:

- **file** (required): the document.
- **json** (optional): JSON string of options—see query API.



<details>
<summary>JavaScript (click to expand)</summary>

```JavaScript
    const fileInput = document.getElementById('file_path').files[0];
    const missionOptions = {
        "doc.extractionMode": "mixed",
    };

    const formData = new FormData();
    formData.append('file', fileInput);
    formData.append('json', JSON.stringify(missionOptions));

    let response = await fetch("http://127.0.0.1:1224/api/doc/upload", {
        method: 'POST',
        body: formData
    });
```

</details>

### 1.2. Response

JSON:

- **code** (int): `100` = upload OK; otherwise failed.
- **data** (string): task ID on success; error message on failure.

<a id="/api/doc/result"></a>

---

## 2. Task status

Poll by task ID for progress and optional text.

URL: `/api/doc/result`

Example: `http://127.0.0.1:1224/api/doc/result`

### 2.1. Request

Method: `POST`

Body: JSON:

- **id** (required): task ID from upload.
- **is_data** (bool, optional):
  - `true`: include recognition payload.
  - `false` (default): status only.
- **is_unread** (bool, optional):
  - `true` (default): only unread result chunks.
  - `false`: full history.
- **format** (string, optional):
  - `"dict"` (default): structured lines/blocks.
  - `"text"`: plain text.

### 2.2. Response

JSON:

- **code** (int): `100` = query OK; otherwise failed.
- **data** (string): error text, recognition payload (if `is_data`), or `[]` when empty.

When `code==100`:

- **processed_count** (int): pages done.
- **pages_count** (int): total pages.
- **is_done** (bool): `true` when finished (`state` may be `success` or `failure`).
- **state** (string): `waiting` | `running` | `success` | `failure`.
- **message** (string): present when `state=="failure"`—reason (partial results may still exist in `data`).

<a id="/api/doc/download"></a>

---

## 3. Build download URL

Call only after success: `is_done==true && state=="success"`.

Pass task ID and desired output types; receive a download URL.

URL: `/api/doc/download`

Example: `http://127.0.0.1:1224/api/doc/download`

### 3.1. Request

Method: `POST`

Body: JSON:

- **id** (required): task ID.
- **file_types** (array of strings): one value → single file URL; multiple → one ZIP.
    - `"pdfLayered"` (default): searchable layered PDF.
    - `"pdfOneLayer"`: single-layer text PDF.
    - `"txt"`: txt with page metadata.
    - `"txtPlain"`: text only.
    - `"jsonl"`: one JSON object per line (similar to `result` with `format="dict"`).
    - `"csv"`: table, one row per page.
- **ingore_blank** (bool): skip blank pages in exports.
  - `true` (default): empty pages omitted in txt/csv, etc.
  - `false`: keep rows for blank pages as empty strings.

### 3.2. Response

JSON:

- **code** (int): `100` if the export was built.
- **data** (string): download URL or error.
- **name** (string): filename (when successful).

<a id="/api/doc/download/id"></a>

---

## 4. Downloading

Use GET on the URL from step 3, or open it in a browser.

The opaque id in the URL is **not** the task id—you cannot guess download URLs from the task id alone.

<a id="/api/doc/clear"></a>

---

## 5. Clear task

Append the task id to clear it.

URL: `/api/doc/clear/<id>`

Example: `http://127.0.0.1:1224/api/doc/clear/cbe2f874-84a9-48b4-a6c0-9157245f7bae`

### 5.1. Request

Method: `GET`

### 5.2. Response

JSON:

- **code** (int): `100` if cleared; otherwise failed or missing task.
- **data** (string): reason.

### 5.3. Notes

Clearing **stops** a running job if needed and **deletes** temp files for that task.

After clear, you cannot poll status or download again.

Call clear after you finish to free resources. If you skip clearing, tasks auto-expire after **24 hours**:

- Running jobs: max 24 hours wall time.
- Finished jobs: kept up to 24 hours from completion.

If Umi-OCR crashes, leftover tasks are cleared on the next start.
