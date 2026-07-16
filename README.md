# Creator Studio AI

**Everything a Creator Needs in One Desktop App**

A native Windows desktop application (not a website, not Electron) built with
Python + CustomTkinter, packaged as a standalone `.exe` via PyInstaller.

---

## Current status: All phases implemented

Every sidebar section is fully functional:

| Area | Status |
|---|---|
| Sidebar navigation (all planned sections) | ✅ built |
| Dashboard (live DB stats, disk usage, CPU/RAM, quick actions) | ✅ built |
| Projects (full CRUD, search, status/favorite filters, tags) | ✅ built |
| AI Writer (11 content types, real provider calls, history) | ✅ built |
| Script Writer (structured Hook/Intro/Body/CTA/Outro scripts) | ✅ built |
| Thumbnail Studio (layered canvas editor: text/shapes/images, drag, rotate, shadows, basic background removal, PNG/JPG export) | ✅ built |
| AI Thumbnail Assistant (CTR suggestions via AI provider) | ✅ built |
| Title / Caption / Description / Hashtag Generators | ✅ built |
| Content Ideas (AI-generated + manual backlog with categories) | ✅ built |
| Content Calendar (monthly view, scheduling, event types) | ✅ built |
| Asset Manager (import, tag, search, image thumbnails) | ✅ built |
| Brand Kit (colors, logos, fonts, watermarks, social links, guidelines) | ✅ built |
| Analytics (manual metric logging + hand-drawn trend chart) | ✅ built |
| Productivity (Notes, Checklist, Bookmarks, Clipboard History, Pomodoro Timer, Snippets) | ✅ built |
| Settings (theme, autosave, backups, AI provider config, export) | ✅ built |
| About | ✅ built |
| SQLite database, schema versioning & migrations (v1 -> v2) | ✅ built |
| AI Provider abstraction: OpenRouter, OpenAI, Anthropic, Google Gemini, Ollama, Custom (real HTTP clients, no hardcoded keys) | ✅ built |
| Background services: Autosave, Backup Manager, File Watcher, Cache Manager, Notification Center, Search Indexer, Plugin loader | ✅ built |

Nothing here is a mocked stub — every list, chart, and generated result reads
from and writes to the real SQLite database or, for AI features, a real
HTTP call to whichever provider you configure in Settings.

### Known scope boundaries (by design, not oversight)
- **AI features require your own API key.** Nothing generates until you set
  a provider in Settings -> AI Settings.
- **Thumbnail Studio's "remove background"** is a simple color-key removal
  (good for flat/solid backgrounds), not ML-based segmentation.
- **Analytics** is manual entry, matching the spec's "track manually...
  future-ready for API integrations" requirement; live platform API pulls
  are a natural next step, not part of this build.
- **Plugin system** is a real, working loader (drop a `.py` file with a
  `register(app_window)` function into `/plugins`), but ships with no
  bundled plugins, per the spec's "Future Features" framing.
- **Video editing, speech-to-text, text-to-speech, voice cloning, cloud
  sync, team collaboration, and video analysis** remain explicitly listed
  as "Future Features" in the original spec and are not built here.

---

## Project structure

```
creator_studio_ai/
├── main.py                  # Entry point
├── requirements.txt
├── README.md
├── assets/
│   ├── icons/
│   └── themes/
├── config/
│   ├── app_config.py         # Static constants (name, version, nav items)
│   └── settings_manager.py   # Persisted user settings (config/settings.json)
├── database/
│   └── db_manager.py         # SQLite connection, schema versioning, migrations
├── modules/
│   ├── base_page.py             # Base class every page extends
│   ├── generator_page_base.py    # Shared plumbing for AI-generator pages
│   ├── sidebar.py                 # Sidebar navigation component
│   ├── app_window.py               # Main window + page router + global search
│   └── theme_manager.py            # Dark/Light/System appearance
├── pages/
│   ├── dashboard_page.py
│   ├── projects_page.py
│   ├── ai_writer_page.py
│   ├── script_writer_page.py
│   ├── thumbnail_studio_page.py     # + AI Thumbnail Assistant
│   ├── title_generator_page.py
│   ├── caption_generator_page.py
│   ├── description_generator_page.py
│   ├── hashtag_generator_page.py
│   ├── content_ideas_page.py
│   ├── content_calendar_page.py
│   ├── asset_manager_page.py
│   ├── brand_kit_page.py
│   ├── analytics_page.py
│   ├── productivity_page.py
│   ├── settings_page.py
│   ├── about_page.py
│   └── coming_soon_page.py       # generic fallback (unused now, kept for future sections)
├── services/
│   ├── autosave_service.py
│   ├── backup_service.py
│   ├── file_watcher_service.py
│   ├── notification_center.py
│   ├── cache_manager.py
│   ├── plugin_manager.py
│   ├── search_indexer.py
│   └── ai/
│       └── ai_provider.py     # AI provider abstraction + real HTTP clients
├── utils/
│   ├── paths.py
│   └── logger.py
├── logs/       (created at runtime)
├── cache/      (created at runtime)
├── backups/    (created at runtime)
├── projects/   (created at runtime)
├── templates/  (created at runtime)
└── plugins/    (created at runtime)
```

---

## Running from source

Requires Python 3.12+.

```bash
pip install -r requirements.txt
python main.py
```

On first run, the app creates its own `database/creator_studio.db`,
`config/settings.json`, and log file — no manual setup needed.

---

## AI configuration

No AI provider or API key ships with this app. Go to **Settings → AI
Settings** and enter your own provider, base URL, API key, model, timeout,
and temperature. Until you do, AI-powered features will report:

> "No AI provider configured."

The app is designed to work with **OpenRouter** as well as OpenAI,
Anthropic, Google Gemini, Ollama, or a custom-compatible endpoint. Actual
provider request/response handling for each is implemented in the phase
that ships the first AI-powered feature (AI Writer).

---

## Building the Windows `.exe`

```bash
pip install pyinstaller
pyinstaller --noconfirm --windowed --name "CreatorStudioAI" ^
    --add-data "assets;assets" ^
    main.py
```

The build output will be in `dist/CreatorStudioAI/CreatorStudioAI.exe`.
Because all paths are resolved relative to the executable at runtime
(`utils/paths.py`), the packaged app creates its database, logs, cache, and
backups next to the `.exe` automatically.

---

## What could still be extended

The spec's "Future Features" section (Plugin System UI, AI Image Generation,
Video Editing, Speech-to-Text, Text-to-Speech, Voice Cloning, Cloud Sync,
Team Collaboration, Video Analysis) was intentionally left as architecture
groundwork rather than built out — the plugin *loader* exists and works, but
there's no bundled plugin; the AI provider layer is real but only used for
text generation so far. These are natural next steps whenever you want to
keep going.

---

## Keyboard shortcuts

- `Ctrl+D` — Dashboard
- `Ctrl+,` — Settings
- `Ctrl+Q` — Quit
