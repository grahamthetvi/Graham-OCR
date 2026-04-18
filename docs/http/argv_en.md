## Contents

### Image OCR

1. [Image OCR: query parameters](api_ocr_en.md#/api/ocr/get_options)
2. [Image OCR: Base64 recognition](api_ocr_en.md#/api/ocr)

### Document recognition (PDF)

- [Document workflow](api_doc_en.md#/api/doc)

### QR code

1. [QR: Base64 decode](api_qrcode_en.md#/api/qrcode)
2. [QR: text to image](api_qrcode_en.md#/api/qrcode/text)

### Command line

- [Argv API](#/argv)

<a id="/argv"></a>

---

## Command-line argv API

Forwards CLI arguments across processes; usually called internally. Developers may call it manually.

Because it is sensitive (local files, quit app, etc.), it is only accepted on loopback `127.0.0.1`. LAN/WAN cannot use it.

URL: `/argv`

Example: `http://127.0.0.1:1224/argv`

### Request

Method: `POST`

Body: JSON **array** of CLI argument strings.

- CLI: `Umi-OCR.exe --path "D:/xxx.png"`
- Equivalent body: `["--path", "D:/xxx.png"]`

See [README_CLI_en.md](../README_CLI_en.md) for full CLI rules.

### Example

<details>
<summary>JavaScript example (click to expand)</summary>

```javascript
const url = "http://127.0.0.1:1224/argv";
// Same as: Umi-OCR --screenshot
const data = ["--screenshot"];
fetch(url, {
        method: "POST",
        headers: {"Content-Type": "application/json"},
        body: JSON.stringify(data)
    })
    .then(response => response.text()) // returns a string
    .then(data => {
        console.log("screenshot text:\n", data)
    })
    .catch(error => {
        console.error(error);
    });
```

</details>
