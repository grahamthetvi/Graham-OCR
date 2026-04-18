# Umi-OCR Docker deployment

Before deployment, verify that the host CPU supports the AVX instruction set:

```sh
lscpu | grep avx
```

If you see output like:

```
Flags:          ... avx ... avx2 ...
```

you can continue.

**If there is no output, this CPU does not support AVX. Umi-OCR for Linux cannot be used on it for now.**

## 1. Download the Dockerfile

```sh
wget https://raw.githubusercontent.com/hiroi-sora/Umi-OCR_runtime_linux/main/Dockerfile
```

## 2. Build the image

```sh
docker build -t umi-ocr-paddle .
```

Notes:

- Image name is set to `umi-ocr-paddle`. (The Dockerfile downloads the PaddleOCR-json engine by default.)

> The build downloads the Umi-OCR release from GitHub. If you hit network issues, you may need a proxy:
>
> ```sh
> docker build -t umi-ocr-paddle . \
>     --build-arg HTTP_PROXY=http://X.X.X.X:7897 \
>     --build-arg HTTPS_PROXY=http://X.X.X.X:7897
> ```

## 3. Run the container

### Headless mode

For servers without a display, systems without X11, or when you only need the HTTP API.

```sh
docker run -d --name umi-ocr \
    -e HEADLESS=true \
    -p 1224:1224 \
    umi-ocr-paddle
```

Notes:

- Container name `umi-ocr` (you can pick another).
- `-e HEADLESS=true` enables headless mode.
- `-p xxxx:1224` maps host port `xxxx` to container port `1224`.
- Image: `umi-ocr-paddle`.
- [HTTP API manual](https://github.com/hiroi-sora/Umi-OCR/blob/main/docs/http/README_en.md)

### GUI mode

For Xorg desktops with a display: screenshot, file import, etc.

Allow X11 access from the host:

```sh
xhost +
```

```sh
docker run -d --name umi-ocr \
    -v /tmp/.X11-unix:/tmp/.X11-unix -e DISPLAY=$DISPLAY \
    -v /home:/home \
    -p 1224:1224 \
    umi-ocr-paddle
```

Notes:

- Mount `/tmp/.X11-unix` and pass `$DISPLAY` into the container.
- Mount the host `home` directory at the **same path** inside the container so drag-and-drop import works.
  - Mount more paths if needed; host and container paths must match, e.g. `-v /aa/bb/cc:/aa/bb/cc`.
- After startup, wait a few seconds; the Umi-OCR window should appear on the host.

## 4. GUI mode controls

Closing the Umi-OCR window with × hides the UI, but Umi keeps running in the background and still serves the HTTP API.

**Show the window again:**

```sh
docker exec umi-ocr /bin/sh -c "/app/umi-ocr.sh --show"
```

**Screenshot OCR:**

```sh
docker exec umi-ocr /bin/sh -c "/app/umi-ocr.sh --screenshot"
```

See the [CLI manual](https://github.com/hiroi-sora/Umi-OCR/blob/main/docs/README_CLI_en.md). For file paths (e.g. `--path`), ensure every parent directory was mounted with `-v` when you ran `docker run`.

In Docker GUI mode most features work like a normal desktop app:

- Use host memory for screenshots and pasting images.
- Access host disks via drag-and-drop (only paths that were mounted into the container).

Some features are limited:

- Cannot create desktop or Start Menu shortcuts.
- No system tray icon.

If the container logs show errors like the following, they can be ignored:

```
ERROR: No native SystemTrayIcon implementation available.
Qt Labs Platform requires Qt Widgets on this setup.
Add 'QT += widgets' to .pro and create QApplication in main().


ERROR: No native Menu implementation available.
Qt Labs Platform requires Qt Widgets on this setup.
Add 'QT += widgets' to .pro and create QApplication in main().
```
