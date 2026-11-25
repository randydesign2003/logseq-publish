<h1 align="center">Logseq Publish - Static Graph Publisher for Windows 10/11</h1>

<p align="center"><i>Logseq Publish turns selected Logseq pages and graph content into a browsable static website for sharing outside the app. This page documents requirements, configuration paths, CLI flags, shortcuts, and fixes for administrators and power users running it on Windows.</i></p>

<p align="center"><img src="https://img.shields.io/badge/Status-Stable-166534?style=flat&labelColor=24292e" alt="Status Stable">&nbsp;&nbsp;<img src="https://img.shields.io/badge/Windows-10%20%7C%2011-1f6feb?style=flat&logo=windows&logoColor=white&labelColor=24292e" alt="Windows 10 | 11">&nbsp;&nbsp;<img src="https://img.shields.io/badge/Type-Reference-8957e5?style=flat&labelColor=24292e" alt="Type Reference">&nbsp;&nbsp;<img src="https://img.shields.io/badge/License-MIT-2b8a3e?style=flat&labelColor=24292e" alt="License MIT">&nbsp;&nbsp;<img src="https://img.shields.io/badge/Version-v1.0-06b6d4?style=flat&labelColor=24292e" alt="Version v1.0"></p>

<p align="center"><img src="./assets/cover.png" alt="Logseq Publish on Windows, reference page banner" width="100%"></p>

<p align="center">
  <a href="https://randydesign2003.github.io/logseq-publish/">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="./assets/Click-dark.svg">
      <img src="./assets/Click-light.svg" alt="Open reference page" width="300">
    </picture>
  </a>
</p>

<p align="center"><a href="#overview">Overview</a> &nbsp;|&nbsp; <a href="#requirements">Requirements</a> &nbsp;|&nbsp; <a href="#key-features">Key Features</a> &nbsp;|&nbsp; <a href="#configuration">Configuration</a> &nbsp;|&nbsp; <a href="#export-mode-comparison">Export Mode Comparison</a> &nbsp;|&nbsp; <a href="#keyboard-shortcuts">Keyboard Shortcuts</a> &nbsp;|&nbsp; <a href="#command-line-options">Command-Line Options</a> &nbsp;|&nbsp; <a href="#troubleshooting">Troubleshooting</a> &nbsp;|&nbsp; <a href="#faq">FAQ</a> &nbsp;|&nbsp; <a href="#notes">Notes</a></p>

## Overview

Logseq Publish reads a local Logseq graph, resolves block references, and emits a static HTML site that can be hosted on any web server or static host. It runs natively on Windows 10/11 and ships with both a tray GUI and a helper CLI.

### What It Does

The tool filters a graph down to published pages, rewrites internal `[[links]]` and `((block refs))` into relative URLs, renders markdown and queries, and writes the output to a target folder. Embedded assets and file references are copied alongside the generated HTML.

### Who It Is For

Teams and individuals who keep a second brain in Logseq and want to expose a subset of it publicly without giving readers the desktop app. Admins use it to schedule nightly exports of a documentation vault to a static host.

### What Ships in the Build

The installer places a tray application, a `logseq-publish.exe` helper CLI, default config templates, and a set of resync shells under `%PROGRAMFILES%\Logseq Publish`. No runtime install is required beyond the bundled WebView2 loader.

## Requirements

| Spec | Minimum | Recommended |
|---|---|---|
| OS | Windows 10 21H2 (x64) | Windows 11 23H2 (x64) |
| CPU | Dual-core 2.0 GHz | Quad-core 2.5 GHz or better |
| RAM | 4 GB | 8 GB |
| Display | 1280x720, 100% scaling | 1920x1080, 125% scaling |
| Storage | 250 MB free for app + output cache | 2 GB free SSD |
| Logseq version | Logseq 0.9.x graph format | Logseq 0.10.x graph format |

## Key Features

* **Selective publishing** , marks only pages tagged `#public` or listed in the publish whitelist for export.
* **Static HTML output** , generates a self-contained site with relative links and an index page.
* **Block reference resolution** , rewrites `((block-uuid))` refs into stable anchors in the output.
* **Asset copying** , mirrors referenced images and attachments into the output folder tree.
* **Incremental rebuild** , hashes graph files and only regenerates pages whose source changed.
* **Tray resync** , watches the graph for edits and queues a rebuild when files are saved.
* **Static host presets** , emits ready-to-deploy folders for GitHub Pages, Netlify, and generic SFTP.
* **CLI automation** , exposes a headless export mode for scheduled tasks and CI runners.

## Configuration

| Path | Purpose |
|---|---|
| `%APPDATA%\Logseq Publish\config.json` | Primary user configuration, publish whitelist, and host presets |
| `%APPDATA%\Logseq Publish\shortcuts.json` | User overrides for tray and app keybinds |
| `%LOCALAPPDATA%\Logseq Publish\cache\` | Render cache, hashed page artifacts, and incremental rebuild state |
| `%LOCALAPPDATA%\Logseq Publish\logs\publish.log` | Rolling runtime log for GUI and CLI sessions |
| `%PROGRAMDATA%\Logseq Publish\defaults.json` | Machine-wide defaults applied before per-user config |
| `%APPDATA%\Logseq Publish\hosts\` | Per-host deploy profiles (GitHub Pages, Netlify, SFTP) |

| Setting | Default | Notes |
|---|---|---|
| `publish.whitelistTag` | `public` | Tag name that marks pages for inclusion in the export |
| `publish.outputDir` | `%USERPROFILE%\LogseqPublish\site` | Target folder for generated HTML and assets |
| `sync.watchGraph` | `true` | Enables tray graph watcher and rebuild queue |
| `sync.debounceMs` | `1500` | Delay before triggering rebuild after file save |
| `updates.channel` | `stable` | Switch between `stable` and `beta` update feeds |
| `logging.level` | `info` | One of `error`, `warn`, `info`, `debug`, `trace` |
| `telemetry.enabled` | `false` | Opt-in anonymous usage counters, off by default |

## Export Mode Comparison

| Mode | Output Size (1k pages) | Rebuild Time | Incremental | When to Pick |
|---|---|---|---|---|
| Full static HTML | ~85 MB | 3 to 5 min | No | First-time publish or major restructure |
| Incremental HTML | ~85 MB total | 8 to 20 s | Yes | Watch-mode publishing during edits |
| Single-page bundle | ~2.4 MB | 10 to 15 s | No | Handing a reading copy to a single recipient |
| JSON-only export | ~6 MB | 5 to 8 s | Yes | Feeding a custom front end or search index |

## Keyboard Shortcuts

| Action | Shortcut |
|---|---|
| Open main Logseq Publish window | `Ctrl+Alt+L` |
| Trigger immediate rebuild | `Ctrl+R` |
| Toggle watch mode | `Ctrl+W` |
| Open output folder in Explorer | `Ctrl+O` |
| Open log file | `Ctrl+Shift+L` |
| Copy public site URL to clipboard | `Ctrl+Shift+U` |
| Show tray menu | `Win+B`, then arrow keys |
| Force stop running export | `Esc` while progress bar is focused |
| Open settings | `Ctrl+,` |
| Quit tray app | `Ctrl+Q` |

## Command-Line Options

| Flag | Argument | Purpose |
|---|---|---|
| `--graph` | path | Absolute path to the Logseq graph directory |
| `--out` | path | Override the configured output directory |
| `--tag` | string | Publish only pages carrying the given tag |
| `--incremental` | none | Rebuild only pages whose source hashes changed |
| `--clean` | none | Wipe the output directory before writing |
| `--host` | name | Apply a saved host profile by name |
| `--log-level` | `error\|warn\|info\|debug` | Set verbosity for the current run |
| `--dry-run` | none | Resolve the plan and report without writing files |

```bash
logseq-publish.exe --graph "D:\Vaults\public-graph" --out "D:\sites\brain" --tag public --incremental --log-level info
```

| Exit Code | Meaning |
|---|---|
| `0` | Export completed, all pages written |
| `1` | Configuration error, invalid graph path or tag |
| `2` | Partial export, some pages failed to render |
| `3` | Output directory is locked or unwritable |
| `4` | Host profile requested but not found |

## Troubleshooting

### Export finishes but pages are missing

Pages not tagged with the whitelist tag are skipped silently, which looks like data loss.

1. Confirm the page has the exact `publish.whitelistTag` value (case-sensitive by default).
2. Run `logseq-publish.exe --dry-run` and inspect the resolved page count.
3. Check `%LOCALAPPDATA%\Logseq Publish\logs\publish.log` for skipped-page lines.
4. Verify no `#private` tag is present, which overrides the whitelist.

### Block references render as plain text

Out-of-date cache prevents the renderer from rewriting refs to anchors.

1. Close the tray app so the cache is not locked.
2. Delete the contents of `%LOCALAPPDATA%\Logseq Publish\cache\`.
3. Re-run a full export with `--clean`.
4. Open the output and confirm `#block-` anchors now resolve.

### Rebuild loops every few seconds

Watch mode fires repeatedly when a sync client touches files in the graph folder.

1. Raise `sync.debounceMs` to `3000` or higher in `config.json`.
2. Exclude the graph folder from OneDrive or Dropbox sync.
3. Restart the tray app and confirm the log shows debounced events.

### Output folder locked by antivirus

Real-time scanners hold handles on freshly written HTML, causing exit code 3.

1. Add `%USERPROFILE%\LogseqPublish\site` to your AV exclusion list.
2. Retry the export with `--incremental` disabled to slow write bursts.
3. If the lock persists, redirect `--out` to a non-scanned drive.

## FAQ

### Can I publish only a subset of pages without tagging every one?

Yes. Use `publish.whitelistTag` for tag-driven selection, or supply a `publish.include.txt` file in `%APPDATA%\Logseq Publish\` listing page names. Pages on the include list are published even without the tag.

### Does the generated site work offline on a local file share?

Yes, with a caveat. The output uses relative URLs and no CDN assets, so opening `index.html` over `file://` works for browsing, but in-browser search requires serving from HTTP. Point IIS or `python -m http.server` at the output folder for full functionality.

### Can the CLI run under Task Scheduler without an interactive session?

Yes. Register the task with "Run whether user is logged on or not", set the working directory to the app folder, and pass `--graph` and `--out` as absolute paths. Ensure the account has read access to the graph and write access to the output folder.

## Notes

Logseq Publish is an independent tool that publishes content from graphs created by Logseq, a project of its respective maintainers. Logseq is a trademark of its owner. This documentation reflects the current Windows build and may lag individual releases.