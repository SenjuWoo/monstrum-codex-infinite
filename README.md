<p align="center">
  <img src="assets/mark.svg" width="72" height="72" alt="Monstrum Codex Infinite mark">
</p>

<h1 align="center">Monstrum Codex Infinite</h1>

<p align="center"><strong>A local monster-girl encyclopedia that forges as well as it files.</strong></p>

<p align="center">
  Adult fictional bestiary and AI creation studio: succubi, lamia, alraune,<br>
  and every other specimen the archive will hold — browsed, tagged, illustrated, exported.
</p>

<p align="center">
  <a href="https://github.com/ShugokiFable/monstrum-codex-infinite/actions/workflows/ci.yml"><img src="https://github.com/ShugokiFable/monstrum-codex-infinite/actions/workflows/ci.yml/badge.svg" alt="CI"></a>
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-ff4d7a?labelColor=0d0f11" alt="MIT License"></a>
  <a href="DATA-LICENSE.md"><img src="https://img.shields.io/badge/data-CC0%20originals-8f9aa6?labelColor=0d0f11" alt="CC0 original data"></a>
  <img src="https://img.shields.io/badge/app-v1.6.1-8f9aa6?labelColor=0d0f11" alt="v1.6.1">
  <img src="https://img.shields.io/badge/python-3.12-8f9aa6?labelColor=0d0f11" alt="Python 3.12">
</p>

<p align="center">
  <a href="#run">Run</a>
  ·
  <a href="#libraries">Libraries</a>
  ·
  <a href="#official-media-cache">Official cache</a>
  ·
  <a href="#honest-status">Status</a>
  ·
  <a href="CHANGELOG.md">Changelog</a>
</p>

This git tree is **source only**. `userdata/` (SQLite, API keys, cached portraits, generated art, browser profile) is gitignored. Original bundled prose in `data/seed_codex.json` is **CC0 1.0**. The Official Archive is a **source-link index**, not a dump of Monster Girl Encyclopedia page text or image binaries.

## Why it exists

A monster-girl encyclopedia is useless as a folder of unmarked JPEGs. You want dense browsing, source-aware records, anatomy / ecology / culture / abilities / weaknesses / roleplay fields on one card, and an AI studio that can forge a new succubus without pretending she is "official."

Inspired by the *workflow* of Bionus Grabber (dense browse, local cache, fast inspect). Not a fork. No Grabber code.

Adults only. Fictional specimens. No child or age-ambiguous entries.

## What you get

- Three libraries that cannot impersonate each other: **Official Archive**, **AI Forged**, **User Creations**
- Naturalist profile on every entry (diet, cycle, social, disposition, danger response, likes / dislikes, terrain)
- Living habitat scenes (local canvas: embers, snow, fireflies, dust — no GIFs, no network)
- Danger-reactive particles, holographic foil on Rare / Mythic / Legendary, card tilt, hero parallax
- Full-screen "other page" dossier with a living-environment stage
- Fast search, family / habitat / rarity / danger / favorites / recency
- Editor covering anatomy, ecology, culture, abilities, weaknesses, image prompts, provenance, roleplay
- OpenRouter lore forge; OpenAI / Gemini image APIs; ComfyUI; ChatGPT / Gemini subscription handoff
- SillyTavern exports: CCv3 JSON, CCv3 PNG, CHARX, lorebook JSON
- Real-browser Official image + article-text cache (Cloudflare-facing wiki; no HTTP impersonation)
- Windows desktop shell (Edge WebView2) with browser fallback
- 30 unittest methods in `tests/test_smoke.py`

## Libraries

| Library | In this tree | Images |
| --- | --- | --- |
| **Official Archive** | 237 indexed Monster Girl Encyclopedia profiles (`data/official_catalog.json`) | Cached on your machine from the canonical wiki. Not in git. |
| **AI Forged** | 259 bundled original / public-domain archetypes (`data/seed_codex.json`, CC0) plus anything you generate | No wiki mugshot. Generate portraits or attach files. Aello, Ahuizotl Maiden, and the purple-badged Akaname are **AI Forged**, not Official. |
| **User Creations** | Empty until you write / import | Local art or the same generation batch |

The app starts in Official Archive. Combined view is grouped, never interleaved. A generated succubus cannot silently wear an Official badge.

## Run

Windows, first launch:

```text
run_windows.bat
```

Creates `.venv`, installs `requirements.txt`, starts the local server, opens the desktop window. Data goes in `userdata/`.

Browser-only: `run_browser_windows.bat`.

Developer:

```powershell
python -m venv .venv
.venv\Scripts\activate
pip install -r requirements.txt
python -m uvicorn app.main:app --host 127.0.0.1 --port 17373
```

Then open `http://127.0.0.1:17373`.

Linux: `run_linux.sh`.

Double-launch: `desktop.py` reuses the running instance instead of failing to bind the port.

### Upgrade from v1.0 / v1.1

Copy the old `userdata/` folder into the new tree (or extract over a copy). First launch migrates `catalog_kind` / `image_url`, parks bundled seed entries in **AI Forged**, keeps identifiable hand-made records in **User Creations**, and adds missing Official index rows without overwriting edits. Back up `userdata/` first.

## Official media cache

The source wiki puts pages, the MediaWiki API, and image redirects behind Cloudflare (HTTP 403 to ordinary app requests). Caching uses a **real installed Chromium** via Playwright, not header spoofing.

- Settings → Data → **Open Real-Browser Cache**
- or `cache_official_images_windows.bat`

What it does:

1. Prefers Edge, then Chrome, Brave, Opera. Override with `MONSTRUM_BROWSER`.
2. One Playwright-owned persistent context, session in `userdata/official-browser-profile-v2/`.
3. You complete any visible Cloudflare check. Keep that window open while it runs.
4. Indexes **Mugshot Image** and **Profile Image** categories.
5. Per Official entry: mugshot, numbered standalone portraits (`Apsara_0.jpg`, …), English / Japanese entry pages (`Apsara eng1.png`, `Apsara jp1.png`).
6. Exact normalized base-name matching so `Abaddon` cannot steal `Abaddon Folk` media.
7. Stores `extra.official_media` with filename, file-page URL, resolved URL, local path, type, index, language.
8. Reuses healthy local files; `--refresh` redownloads everything.
9. Binaries land in `userdata/media/` — gitignored.

Cards: mugshot → portrait → entry image. Detail hero: portrait → mugshot → entry image. Galleries stay separate (mugshot / portraits / entry scans).

Article prose: `cache_official_text_windows.bat` walks each Official source page through the same browser session and stores up to ~4000 characters in `lore`. Challenge-page leakage is what `tests/test_official_lore.py` is for (needs a filled `userdata/`).

```text
set MONSTRUM_BROWSER=C:\Path\To\browser.exe
cache_official_images_windows.bat
```

Diagnostics: `userdata/official-browser-cache.log`, `userdata/official-image-cache-report.txt`.

Legacy HTTP mode is diagnostics-only:

```text
.venv\Scripts\python.exe -m tools.cache_official_images --transport http
```

## Generate missing AI / User portraits

Settings → Image AI → **Generate Missing Portraits**, or:

```text
generate_missing_ai_images_windows.bat --limit 10
```

```text
generate_missing_ai_images_windows.bat --provider comfyui --limit 0
generate_missing_ai_images_windows.bat --provider openai --limit 10
generate_missing_ai_images_windows.bat --provider gemini --kinds ai,user --limit 25
```

`--limit 0` means all missing. OpenAI and Gemini batches ask for explicit confirmation (billable). Logs: `userdata/ai-image-generation.log`, `userdata/ai-image-generation-report.txt`.

Hosted keys stay on the local backend. ChatGPT Plus / Gemini consumer subscriptions are **not** API credentials — use the manual handoff buttons, then attach the image.

### ComfyUI

1. Start ComfyUI (`http://127.0.0.1:8188`).
2. Dev Mode → Save (API Format).
3. Paste into Settings → ComfyUI.
4. Placeholders: `{{PROMPT}}` `{{NEGATIVE_PROMPT}}` `{{WIDTH}}` `{{HEIGHT}}` `{{SEED}}`

Template: `workflows/comfyui_api_template.json`.

## SillyTavern export

From any entry → **Export**:

- **CCv3 JSON** — Character Card V3
- **CCv3 PNG** — avatar with embedded `chara` / `ccv3`
- **CHARX** — card archive
- **Lorebook JSON** — species-focused World Info

Remote Official preview art is embedded only after it has been cached locally.

## Import

JSON:

```json
[
  {
    "name": "Example",
    "catalog_kind": "user",
    "family": "Fae",
    "summary": "..."
  }
]
```

`catalog_kind` is `official`, `ai`, or `user`. CSV array fields use `|`.

## Local files

```text
userdata/codex.sqlite3   Codex + settings (gitignored)
userdata/media/          Generated, attached, cached portraits (gitignored)
data/seed_codex.json     CC0 original / public-domain archetypes (in git)
data/official_catalog.json   Source-link index only (in git)
```

Do not share `userdata/`.

Portable Windows build (on Windows, PyInstaller does not cross-compile this from Linux):

```text
build_windows_exe.bat
```

Output: `dist\Monstrum Codex Infinite\Monstrum Codex Infinite.exe`. That folder is not in git.

## Project map

```text
app/            FastAPI, DB, providers, official cache, exports
web/            static frontend (cache-bust query v1.6.1)
data/           seed + official index
tests/          unittest smoke + standalone lore check
tools/          image / text cache, AI batch
workflows/      ComfyUI API template
desktop.py      WebView2 shell
docs/           architecture, official-archive, reference audit
CHANGELOG.md    1.6.2 hardening notes and earlier releases
DATA-LICENSE.md CC0 originals + Official index notice
```

## Honest status

Verified in this tree:

- FastAPI / frontend report **v1.6.1** (`app/main.py`, `web/index.html` cache-bust)
- `CHANGELOG.md` also documents **1.6.2** command/path injection hardening; `shell=False` batch spawn and media-path traversal tests are present
- Catalog counts: **237** Official, **≥259** AI (`tests/test_smoke.py`)
- **30** unittest methods; CI runs `python -m unittest discover -s tests -p "test_*.py"` on Python 3.12
- `.gitignore` excludes `userdata/*` (keeps `userdata/.gitkeep`)
- [DATA-LICENSE.md](DATA-LICENSE.md): CC0 for bundled original prose; Official names / setting / artwork remain with their rights holders

Not claimed:

- That this git repository redistributes Monster Girl Encyclopedia image binaries or canonical wiki prose
- That CI solved Cloudflare or downloaded Official media
- That GitHub release `build-v1` (dated Windows zip with bundled caches) matches this tree — it does not; it is an older bundle
- A prebuilt `.exe` inside the git tree
- Butteraugli / LPIPS portrait scoring

Monster Girl Encyclopedia names, setting, profile text, and artwork remain the property of their rights holders. This project is unofficial.

## License

Software: [MIT](LICENSE).

Bundled original data: [CC0 1.0](DATA-LICENSE.md). Folkloric names and broad archetypes may have independent cultural histories and are not claimed as inventions of this project.
