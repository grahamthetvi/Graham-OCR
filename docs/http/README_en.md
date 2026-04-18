[中文](README.md)

- [Command-line manual](../README_CLI_en.md)
- HTTP API manual

# HTTP API manual

(This document applies to the latest Umi-OCR. For older releases, see the docs on the matching [GitHub backup branch](https://github.com/hiroi-sora/Umi-OCR/branches).)

#### Basics

![Umi-OCR global settings - service](https://tupian.li/images/2023/10/25/653907e9bac06.png)

As shown above, HTTP service must be enabled (default on). To allow access from the LAN, set the host to **any available address**.

Enable **Advanced** on the global settings page to see these options.

##### Notes

1. If clients are still connected when you exit Umi-OCR, shutdown may be incomplete (UI thread ends but the networking thread may remain). Wait for clients to disconnect or end the process from Task Manager.
2. The backend has limited concurrency—avoid heavy parallel load.
3. Under long, heavy, continuous use you may occasionally see HTTP errors like `Error: connect ECONNREFUSED`. Retry the request. If the worker is still alive, this is usually transient.

---

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

- [Argv API](argv_en.md#/argv)
