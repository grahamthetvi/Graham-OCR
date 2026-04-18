## Contents

### Image OCR

1. [Image OCR: query parameters](#/api/ocr/get_options)
2. [Image OCR: Base64 recognition](#/api/ocr)

### Document recognition (PDF)

- [Document workflow](api_doc_en.md#/api/doc)

### QR code

1. [QR: Base64 decode](api_qrcode_en.md#/api/qrcode)
2. [QR: text to image](api_qrcode_en.md#/api/qrcode/text)

### Command line

- [Argv API](argv_en.md#/argv)

<a id="/api/ocr/get_options"></a>

---

## 1. Image OCR: query parameters

> Depending on the OCR engine plugin, the image API may accept different parameters.  
> The **get options** endpoint returns definitions, defaults, and allowed values.  
> Call it manually or use the response to build UI automatically.


URL: `/api/ocr/get_options`

Example: `http://127.0.0.1:1224/api/ocr/get_options`

(Default port `1224`; change in Umi-OCR global settings.)

### 1.1. Request

Method: `GET`

### 1.2. Response

JSON object describing image OCR parameters.

PaddleOCR plugin example:

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
    "data.format": {
        "title": "数据返回格式",
        "toolTip": "返回值字典中，[\"data\"] 按什么格式表示OCR结果数据",
        "default": "dict",
        "optionsList": [
            ["dict","含有位置等信息的原始字典"],
            ["text","纯文本"]
        ],
        "type": "enum"
    }
}
```

The example has five root keys—five parameters.

</details></br>

Each parameter may include:

- `title`: display name.
- `toolTip`: description.
- `default`: default value.
- `type`: value type:
  - `enum`: must be one of `optionsList[i][0]`.
  - `boolean`: `true` / `false`.
  - `text`: string.
  - `number`: number; if `isInt==true`, must be integer.
  - `var`: special type—see `toolTip`.

<a id="/api/ocr/options_info"></a>

#### Example parameter notes

> [!TIP]
> Parameter names, shapes, and ranges may differ by plugin. Always confirm with `/api/ocr/get_options`.

- **ocr.language**: OCR language / model.
- **ocr.cls**: `true` enables orientation correction for rotated images.
- **ocr.limit_side_len**: Longest side is resized down before OCR (faster; may hurt accuracy on huge images—increase if needed).
- **data.format**: How `["data"]` is shaped in the response:
    - `dict`: boxes, scores, positions, etc.
    - `text`: plain concatenated text.
- **tbpu.parser**: Layout mode:
    - `multi_para`: multi-column, paragraph breaks
    - `multi_line`: multi-column, line breaks
    - `multi_none`: multi-column, no extra breaks
    - `single_para`: single column, paragraph breaks
    - `single_line`: single column, line breaks
    - `single_none`: single column, no extra breaks
    - `single_code`: single column, preserve indentation (code screenshots)
    - `none`: no layout processing
- **tbpu.ignoreArea**: Rectangles whose **interior** text blocks are skipped.
    - Outer: list `[]`, each item one rectangle.
    - Inner: `[[x1,y1],[x2,y2]]` — top-left and bottom-right.
    - Example with three regions:

        ```javascript
        [
            [[0,0],[100,50]],
            [[0,60],[200,120]],
            [[400,0],[500,30]]
        ]
        ```

    - Whole **text blocks** must lie inside a region to be ignored (not single glyphs). In the screenshot, the yellow box is an ignore region—only `key_mouse` is dropped; `pubsub_connector.py` and `pubsub_service.py` remain.

<p align="center"><img src="https://tupian.li/images/2024/05/30/66587bf03ae15.png" alt="Ignore region example" style="width: 80%;"></p>

Example `options` built from the sample response:

<details>
<summary>Expand</summary>

```json
{
    "ocr.language": "models/config_chinese.txt",
    "ocr.cls": true,
    "ocr.limit_side_len": 4320,
    "tbpu.parser": "multi_none",
    "data.format": "text"
}
```

</details></br>

### 1.3. Query examples

<details>
<summary>JavaScript (click to expand)</summary>

```javascript
const url = "http://127.0.0.1:1224/api/ocr/get_options";
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

response = requests.get("http://127.0.0.1:1224/api/ocr/get_options")
res_dict = json.loads(response.text)
print(json.dumps(res_dict, indent=4, ensure_ascii=False))
```

</details>

<a id="/api/ocr"></a>

---

## 2. Image OCR: Base64 API

Send a Base64-encoded image; receive OCR results.

URL: `/api/ocr`

Example: `http://127.0.0.1:1224/api/ocr`

### 2.1. Request

Method: `POST`

Body: JSON object:

- **base64** (required): image bytes as Base64 **without** a `data:image/png;base64,` prefix.
- **options** (optional): parameter object—see [query API](#/api/ocr/get_options).



<details>
<summary>Example `options` (click to expand)</summary>

```json
{
    "base64": "iVBORw0KGgoAAAAN……",
    "options": {
        "ocr.language": "models/config_chinese.txt",
        "ocr.cls": true,
        "ocr.limit_side_len": 4320,
        "tbpu.parser": "multi_none",
        "data.format": "text"
    }
}
```

</details>

### 2.2. Response

JSON fields:

| Field     | Type        | Description |
| --------- | ----------- | ----------- |
| code      | int         | `100` success, `101` no text, other values = failure |
| data      | list/string | See below |
| time      | double      | Seconds spent in OCR |
| timestamp | double      | Start time (seconds) |

#### `data` shape

No text (`code==101`) or failure (`code` not `100` or `101`):

- `data` is a string—the error message, e.g. `{"code": 902, "data": "向识别器进程传入指令失败，疑似子进程已崩溃"}`

Success (`code==100`) with `data.format` = `dict` (default):

- `data` is a list of objects:

| Field | Type   | Description |
| ----- | ------ | ----------- |
| text  | string | Text |
| score | double | Confidence 0–1 |
| box   | list   | Corners clockwise: top-left, top-right, bottom-right, bottom-left |
| end   | string | Line ending from layout (may be empty, space, or newline) |

Example:

```json

{
    "code": 100,
    "data": [
        {
            "text": "第一行的文本，",
            "score": 0.99800001,
            "box": [[x1,y1], [x2,y2], [x3,y3], [x4,y4]],
            "end": "",
        },
        {
            "text": "第二行的文本",
            "score": 0.97513333,
            "box": [[x1,y1], [x2,y2], [x3,y3], [x4,y4]],
            "end": "\n",
        },
    ]
}
```

Success with `data.format` = `text`:

- `data` is one string—concatenated OCR text, e.g.:

```json
"data": "第一行的文本，第二行的文本\n"
```

### 2.3. Examples

<details>
<summary>JavaScript (click to expand)</summary>

```javascript
const url = 'http://127.0.0.1:1224/api/ocr';
const data = {
    base64: "iVBORw0KGgoAAAANSUhEUgAAAC4AAAAXCAIAAAD7ruoFAAAACXBIWXMAABnWAAAZ1gEY0crtAAAAEXRFWHRTb2Z0d2FyZQBTbmlwYXN0ZV0Xzt0AAAHjSURBVEiJ7ZYrcsMwEEBXnR7FLuj0BPIJHJOi0DAZ2qSsMCxEgjYrDQqJdALrBJ2ASndRgeNI8ledutOCLrLl1e7T/mRkjIG/IXe/DWBldRTNEoQSpgNURe5puiiaJehrMuJSXSTgbaby0A1WzLrCCQCmyn0FwoN0V06QONWAt1nUxfnjHYA8p65GjhDKxcjedVH6JOejBPwYh21eE0Wzfe0tqIsEkGXcVcpoMH4CRZ+P0lsQp/pWJ4ripf1XFDFe8GHSHlYcSo9Es31t60RdFlN1RUmrma5oTzTVB8ZUaeeYEC9GmL6kNkDw9BANAQYo3xTNdqUkvHq+rYhDKW0Bj3RSEIpmyWyBaZaMTCrCK+tJ5Jsa07fs3E7esE66HzralRLgJKp0/BD6fJRSxvmDsb6joqkcFXGqMVVFFEHDL2gTxwCAaTabnkFUWhDCHTd9iYrGcAL1ZnqIp5Vpiqh7bCfua7FA4qN0INMcN1+cgCzj+UFxtbmvwdZvGIrI41JiqhZBWhhF8WxorkYPpQwJiWYJeA3rXE4hzcwJ+B96F9zCFHC0FcVegghvFul7oeEE8PvHeJqC0w0AUbbFIT8JnEwGbPKcS2OxU3HMTqD0r4wgEIuiKJ7i4MS16+og8/+bPZRPLa+6Ld2DSzcAAAAASUVORK5CYII=",
    // optional options
    "options": {
        "data.format": "text",
    }
};

fetch(url, {
        method: "POST", body: JSON.stringify(data),
        headers: {"Content-Type": "application/json"},
    })
    .then(response => response.json())
    .then(data => {
        console.log(data);
    })
    .catch(error => {
        console.error(error);
    });
```

</details>

<details>
<summary>Python (click to expand)</summary>

```python
import requests
import json

url = "http://127.0.0.1:1224/api/ocr"
data = {
    "base64": "iVBORw0KGgoAAAANSUhEUgAAAC4AAAAXCAIAAAD7ruoFAAAACXBIWXMAABnWAAAZ1gEY0crtAAAAEXRFWHRTb2Z0d2FyZQBTbmlwYXN0ZV0Xzt0AAAHjSURBVEiJ7ZYrcsMwEEBXnR7FLuj0BPIJHJOi0DAZ2qSsMCxEgjYrDQqJdALrBJ2ASndRgeNI8ledutOCLrLl1e7T/mRkjIG/IXe/DWBldRTNEoQSpgNURe5puiiaJehrMuJSXSTgbaby0A1WzLrCCQCmyn0FwoN0V06QONWAt1nUxfnjHYA8p65GjhDKxcjedVH6JOejBPwYh21eE0Wzfe0tqIsEkGXcVcpoMH4CRZ+P0lsQp/pWJ4ripf1XFDFe8GHSHlYcSo9Es31t60RdFlN1RUmrma5oTzTVB8ZUaeeYEC9GmL6kNkDw9BANAQYo3xTNdqUkvHq+rYhDKW0Bj3RSEIpmyWyBaZaMTCrCK+tJ5Jsa07fs3E7esE66HzralRLgJKp0/BD6fJRSxvmDsb6joqkcFXGqMVVFFEHDL2gTxwCAaTabnkFUWhDCHTd9iYrGcAL1ZnqIp5Vpiqh7bCfua7FA4qN0INMcN1+cgCzj+UFxtbmvwdZvGIrI41JiqhZBWhhF8WxorkYPpQwJiWYJeA3rXE4hzcwJ+B96F9zCFHC0FcVegghvFul7oeEE8PvHeJqC0w0AUbbFIT8JnEwGbPKcS2OxU3HMTqD0r4wgEIuiKJ7i4MS16+og8/+bPZRPLa+6Ld2DSzcAAAAASUVORK5CYII=",
    # optional options
    "options": {
        "data.format": "text",
    }
}
headers = {"Content-Type": "application/json"}
data_str = json.dumps(data)
response = requests.post(url, data=data_str, headers=headers)
response.raise_for_status()
res_dict = json.loads(response.text)
print(res_dict)
```

</details>

