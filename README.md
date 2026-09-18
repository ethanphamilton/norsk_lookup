# Norwegian Dictionary Lookup

A small Windows utility for reading Norwegian. Select a word in any application, press `Alt+P+N`, and a popup shows the translation next to your cursor.

![Version](https://img.shields.io/badge/version-1.0.0-blue.svg)
![Platform](https://img.shields.io/badge/platform-Windows-lightgrey.svg)
![License](https://img.shields.io/badge/license-MIT-green.svg)

Translations come from [Lexin](https://lexin.oslomet.no/), the Bokmål–English dictionary published by Oslo Metropolitan University. Lookups work in both directions and need an internet connection.

## Install

1. Download `NorwegianDictionary_Setup.exe` from [Releases](https://github.com/ethanphamilton/norsk_lookup/releases).
2. Run the installer. It offers to start the app with Windows.
3. The app runs in the background. There is no window.

## Use

Highlight a word, press `Alt+P+N` — the hotkey stands for "på norsk" — and a popup appears near the cursor. Click anywhere to dismiss it.

```
hund   → hund (noun) → dog
house  → hus (noun) → house
bank   → bank (noun) → bank
          bank (noun) → bench
```

Words with several senses list each one. If nothing is found, the popup says so.

Selection is read through the Windows UI Automation API rather than the clipboard, so your clipboard contents are left alone and the hotkey works in browsers, Office, PDF readers and editors alike. On a multi-monitor setup the popup opens on the screen holding the cursor.

The app checks for a new release once a day. When one exists it shows a notification; clicking it opens the release page in your browser. Nothing is downloaded or installed without you.

## Troubleshooting

**The hotkey does nothing.** Confirm `NorwegianDictionary.exe` is running in Task Manager. If another application has claimed `Alt+P+N`, that application wins and the app cannot see the keypress.

**No popup appears.** The text must be highlighted before you press the hotkey. Single words work more reliably than phrases. Lookups require an internet connection.

**It does not start with Windows.** Enable "Norwegian Dictionary Lookup" under Task Manager → Startup, or reinstall and select the startup option.

## Building from source

Requires Python 3.8+, Windows 10 or 11, and [Inno Setup 6](https://jrsoftware.org/isdl.php) to produce the installer.

```bash
git clone https://github.com/ethanphamilton/norsk_lookup.git
cd norsk_lookup

python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
```

Run from source:

```bash
cd src
python main.py
```

Build:

| Command | Produces |
| --- | --- |
| `build_scripts\build_installer.bat` | `Output\NorwegianDictionary_Setup.exe` |
| `build_scripts\build_debug.bat` | A build with a console window attached |
| `build_scripts\cleanbuild.bat` | Removes build artifacts |

### Releasing

`UpdateChecker.GITHUB_USER` and `GITHUB_REPO` in `src/update_checker.py` are compiled into each build, so an installed copy checks whichever account and repository were current when it was built. If the account is renamed, publish a release before the previous name becomes available to anyone else — GitHub's rename redirect is the only thing keeping existing installs pointed at the right place, and it stops working as soon as someone else registers the old name.

Keep `version.json` in step with `src/version.py`. It is the fallback the updater reads when the GitHub API is unreachable, and a version there without a matching published release will advertise an update that does not exist.

## Source layout

| File | Responsibility |
| --- | --- |
| `src/main.py` | Application entry point; wires the hotkey, capture, lookup and popup together. |
| `src/hotkey_monitor.py` | Polls for the `Alt+P+N` combination via `GetAsyncKeyState`. |
| `src/text_capture.py` | Reads the current selection through UI Automation. |
| `src/lexin_api.py` | Queries the Lexin dictionary and parses the response. |
| `src/popup_ui.py` | Tkinter popup, positioned on the cursor's monitor. |
| `src/update_checker.py` | Daily release check against the GitHub API, with `version.json` as a fallback. |
| `src/version.py` | Version string and application name. |

## Licence

MIT — see [LICENSE](LICENSE).

Dictionary data is provided by [Lexin](https://lexin.oslomet.no/) at Oslo Metropolitan University and remains theirs. Built with Python, PyInstaller and Inno Setup.

Bug reports and pull requests are welcome via [Issues](https://github.com/ethanphamilton/norsk_lookup/issues).
