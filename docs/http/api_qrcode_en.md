## Contents

### Image OCR

1. [Image OCR: query parameters](api_ocr_en.md#/api/ocr/get_options)
2. [Image OCR: Base64 recognition](api_ocr_en.md#/api/ocr)

### Document recognition (PDF)

- [Document workflow](api_doc_en.md#/api/doc)

### QR code

1. [QR: Base64 decode](#/api/qrcode)
2. [QR: text to image](#/api/qrcode/text)

### Command line

- [Argv API](argv_en.md#/argv)

---

<a id="/api/qrcode"></a>

## 1. QR / barcode: Base64 decode

URL: `/api/qrcode`

Example: `http://127.0.0.1:1224/api/qrcode`

### 1.1. Request

Method: `POST`

Body: JSON:

- **base64** (required): image as Base64 **without** `data:image/...;base64,` prefix.
- **options** (optional):
    - **preprocessing.median_filter_size**: median filter kernel; odd 1–9; default: no filter.
    - **preprocessing.sharpness_factor**: sharpness; 0.1–10.0; default: unchanged.
    - **preprocessing.contrast_factor**: contrast; 0.1–10.0; `>1` increases, `<1` decreases, `1` unchanged; default: unchanged.
    - **preprocessing.grayscale**: convert to grayscale when `true`; default `false`.
    - **preprocessing.threshold**: binarization threshold 0–255 (integer); only when `preprocessing.grayscale` is `true`; default `false`.


Example:

```json
{
    "base64": "iVBORw0KGgoAAAAN……",
    "options": {
        "preprocessing.sharpness_factor": 1.0,
        "preprocessing.contrast_factor": 1.0,
        "preprocessing.grayscale": false,
        "preprocessing.threshold": false,
    }
}
```

### 1.2. Response

JSON—similar to OCR responses.

| Field     | Type   | Description |
| --------- | ------ | ----------- |
| code      | int    | `100` success, `101` no codes found, else error |
| data      | list   | See below |
| time      | double | Seconds |
| timestamp | double | Start time (seconds) |

#### `data` shape

No codes (`code==101`) or failure:

- `data` is a string—error message, e.g. `{"code": 204, "data": "【Error】zxingcpp 二维码解析失败。\n[Error] zxingcpp read_bar……"}`

Success (`code==100`):

- `data` is a list—one entry per detected symbol (image may contain many):

| Field       | Type   | Description |
| ----------- | ------ | ----------- |
| text        | string | Decoded payload |
| format      | string | Symbology, e.g. `"QRCode"`—see list below |
| box         | list   | Corners clockwise: TL, TR, BR, BL |
| orientation | int    | Orientation; `0` = upright |
| score       | int    | Compatibility with OCR format; always `1` |

<a id="qrcode_format"></a>

##### `format` values

`"Aztec","Codabar","Code128","Code39","Code93","DataBar","DataBarExpanded","DataMatrix","EAN13","EAN8","ITF","LinearCodes","MatrixCodes","MaxiCode","MicroQRCode","PDF417","QRCode","UPCA","UPCE"`

Example:

```json
{
    "code": 100,
    "data": [ {
        "orientation": 0,
        "box": [[4,4],[25,4],[25,25],[4,25]],
        "score": 1,
        "format": "QRCode",
        "text": "abc"
    } ],
    "time": 0,
    "timestamp": 1711521012.625574
}
```

### 1.3. Examples

<details>
<summary>JavaScript (click to expand)</summary>

```javascript
const url = "http://127.0.0.1:1224/api/qrcode";
const base64 = "/9j/4AAQSkZJRgABAQAAAQABAAD/2wBDAAgGBgcGBQgHBwcJCQgKDBQNDAsLDBkSEw8UHRofHh0aHBwgJC4nICIsIxwcKDcpLDAxNDQ0Hyc5PTgyPC4zNDL/wAALCAAdAB0BAREA/8QAHwAAAQUBAQEBAQEAAAAAAAAAAAECAwQFBgcICQoL/8QAtRAAAgEDAwIEAwUFBAQAAAF9AQIDAAQRBRIhMUEGE1FhByJxFDKBkaEII0KxwRVS0fAkM2JyggkKFhcYGRolJicoKSo0NTY3ODk6Q0RFRkdISUpTVFVWV1hZWmNkZWZnaGlqc3R1dnd4eXqDhIWGh4iJipKTlJWWl5iZmqKjpKWmp6ipqrKztLW2t7i5usLDxMXGx8jJytLT1NXW19jZ2uHi4+Tl5ufo6erx8vP09fb3+Pn6/9oACAEBAAA/APU/GfjM+EjAzW9o0DW8txNPdXEkSxKkkMYAEcUjMS069hjBrn3+K0yi3B0/RozO52y3OtG3gaPy7WRWV5IVJO27DFSoIEbYycCrF18Sb2z1a20u70rTbO8uLiKzigutRl3NcNDBIyAxW7rhTcIu4sAcE8Cu00LU/wC2/D2mat5Pk/brSK58rdu2b0Dbc4GcZxnAri/iSdPGs6AuqySW+nzpcW11dg27xwIzQspkimikDIZUiG/5QhC5PzCuPI1qz8ISalajUtNu1czLGsxnt7tHhhhiijNmkSF22W8aFeFWZ2RjIjeVXvrq0t/EWmaTpq3d9rTXFpCqpa2iRW92sCJOUP2WZYjEsNszrG7Bd/GNhr2zQtP/ALI8PaZpuMfY7SK3x5nmY2IF+9tXd067Vz6DpXH/ABK1LVrN7SLTIr6622k159isYYnknkjuLVUI8yGXGzzWfhc5UHPFeeSyav4dtI9R8O+Ho5dYS4WNrSK1EV2sb29ncFJY7aOPzIkkYhjhSGaME7WdHy72y8NWthbfDxrrfDDdpdXH2eVvtIu/IcStcOUaCGFMqGKNKUELZDEsU+g/DUcMXhXSI7cRrAllCsYjIKhQgxgh3BGP9t/95upk1PQtH1vyv7W0qxv/ACc+X9rt0l2ZxnG4HGcDp6Co7Xw1oNiipaaJptuiPvVYbVEAbcjZGB13RxnPqin+EYksdC0fTIo4rDSrG0jjlM6JBbpGFkKlC4AHDFSVz1wcdKuQQQ2tvFb28UcMESBI441CqigYAAHAAHGK/9k="
const data = { "base64": base64 };

fetch(url, {
        method: "POST",
        headers: {"Content-Type": "application/json"},
        body: JSON.stringify(data)
    })
    .then(response => response.json())
    .then(data => {
        if(data.code === 100) {
            console.log("QRCode count:", data.data.length);
            for (let d of data.data) {
                console.log("    text: ", d.text);
                console.log("    format: ", d.format);
                console.log("    orientation: ", d.orientation);
                console.log("    ====");
            }
        }
        else {
            console.log("Error! Code", data.code, " Msg: ", data.data);
        }
    })
    .catch(error => { console.error(error); });
```

</details>

<a id="/api/qrcode/text"></a>

---

## 2. QR: text to image

Send text; receive a generated QR/barcode image as Base64.

URL: `/api/qrcode` (same path; body differs)

Example: `http://127.0.0.1:1224/api/qrcode`

### 2.1. Request

Method: `POST`

Body: JSON:

- **text** (required): payload to encode.
- **options** (optional):
    - **format**: symbology—see [list above](#qrcode_format). Default `"QRCode"`.
    - **w**: width (px); `0` = auto minimum.
    - **h**: height (px); `0` = auto minimum.
    - **quiet_zone**: margin width; `-1` = automatic.
    - **ec_level**: error correction; default `-1`. Values: `-1` auto, `1` ~7%, `0` ~15%, `3` ~25%, `2` ~30%. Applies to `Aztec`, `PDF417`, `QRCode`.

Example:

```json
{
    "text": "要写入二维码的文本",
    "options": {
        "format": "QRCode",
        "w": 0,
        "h": 0,
        "quiet_zone": -1,
        "ec_level": -1,
    }
}
```


### 2.2. Response

JSON:

| Field | Type   | Description |
| ----- | ------ | ----------- |
| code  | int    | `100` success |
| data  | string | Base64 JPEG on success; error text otherwise |

### 2.3. Examples

<details>
<summary>JavaScript (click to expand)</summary>

```javascript
const url = "http://127.0.0.1:1224/api/qrcode";
const data = {
    "text": "test abc 123 !!!",
    // "options": {
    //     "format": "QRCode",
    //     "w": 0,
    //     "h": 0,
    //     "quiet_zone": -1,
    //     "ec_level": -1,
    // }
};
fetch(url, {
        method: "POST",
        headers: {"Content-Type": "application/json"},
        body: JSON.stringify(data)
    })
    .then(response => response.json())
    .then(data => {
        if(data.code === 100) {
            console.log("Image base64: \n", data.data);
        }
        else {
            console.log("Error! Code", data.code, " Msg: ", data.data);
        }
    })
    .catch(error => { console.error(error); });
```

</details>
