# oneshot-wme-web-patch

A patch for the **OneShot: World Machine Edition** web port.
No game data is included. You apply it to an unmodified copy of the web port with `patch.py`.

## What it changes

| Change | Details |
| --- | --- |
| `OneshotWeb.dll` | A small IL patch in `Game1.Draw`. The .NET loader expects file names like `<name>.<hash prefix>.dll`, so the file is renamed from `OneshotWeb.rpf3oktgfj.dll` to `OneshotWeb.d9c8449e38.dll`. The name and SRI hash (`sha256-…`) in `_framework/dotnet.js` are updated to match. |
| Unused files removed | 78 files the game never loads (about 3 MB): old and test images, unused sounds, and the root `manifest.txt`. They are also removed from `gamedata/manifest.txt`. |

`index.html` and `index.js` only load files from their own folder, so the patched game runs fully offline.

## Usage

Requirements: Python 3.8+ (standard library only).

```sh
python patch.py <web-port-folder> --check   # verify only, changes nothing
python patch.py <web-port-folder>           # a dist/ subfolder is found automatically

python -m http.server 8000 -d <folder-with-index.html>   # then open http://localhost:8000/
```

- **Checks:**
  - The patch targets one specific version of the web port.
  - Every input file is verified by SHA-256 before anything is written. If the version differs or a file was modified, the patch stops with an error and changes nothing.
  - The results are verified too. Running the patch twice is harmless.
- **Line endings:** files that were checked out with CRLF line endings (git `core.autocrlf`), such as `dotnet.js`, are converted back to LF before patching.

## License

The scripts and the patch description are released under the MIT License (see `LICENSE`). OneShot and its assets belong to Future Cat and Degica. None of them are included here.
