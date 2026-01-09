# Stumble Design

Stumble Design is a local-first desktop app for discovering and saving web design
references into an Obsidian-compatible vault. It blends random discovery with a
minimal library and notes workflow, while keeping untrusted web content in a
sandboxed view.

![Image](https://github.com/user-attachments/assets/38a6ebbf-9475-47b7-a39b-826ac333e6b2)

![Image](https://github.com/user-attachments/assets/38d101a6-c969-484c-bcc9-1a3a573fb6de)

## What you can do
- Discover: Load a random site in a sandboxed WebContentsView.
- Save: Capture URL, title, screenshot, tags, and notes into a vault.
- Library: Browse saved scraps, search by title or URL, and open notes.
- Filters: Choose source pack, language mode, safety blocks, and categories
  (up to 5).
- Inspect: Zoom and full view mode for readable inspection.
- Themes: Switch between UI themes in the header.

## Source packs
- Global sample pack: Small list for quick testing.
- Curated list: Hand-picked design sites.
- Open Web (Tranco): Large domain list with local cache + custom import.

Open Web uses the Tranco top-1M domain list and caches it locally. You can import
a custom list (newline or CSV with domains) to add your own sources.

## Security model (high level)
- Remote web content is treated as hostile.
- Untrusted pages render only in a sandboxed WebContentsView.
- Node integration stays off and webSecurity stays on.
- The renderer is unprivileged; all actions go through a strict IPC allowlist.

See `DOCS/SECURITY.md` for full details.

## Repository layout
- `apps/desktop`: Electron app (main, renderer, preload)
- `packages/core`: Domain logic (vault, indexing, analysis, sources)

## Requirements
- Node.js 20+ (recommended)
- Windows 10/11 for packaging the .exe

## Development
Install dependencies:
```powershell
cd app
npm.cmd install
```

Run the desktop app (renderer + main + Electron):
```powershell
npm.cmd --workspace @stumble/desktop run dev
```

Run tests and typecheck:
```powershell
npm.cmd --workspace @stumble/desktop run test
npm.cmd --workspace @stumble/desktop run typecheck
```

## Packaging (Windows)
Build a distributable exe:
```powershell
cd app/apps/desktop
npm.cmd run dist:win
```

Outputs land in `app/apps/desktop/release`:
- `Stumble Setup <version>.exe` (installer)
- `Stumble <version>.exe` (portable)

## Vault format
Scraps are stored as Markdown files with YAML frontmatter and assets on disk.
The vault is the source of truth; the index is derived.

See `DOCS/VAULT_SCHEMA.md` for the full schema and examples.

## Key files
- UI shell: `app/apps/desktop/src/renderer/App.tsx`
- UI styles: `app/apps/desktop/src/renderer/styles.css`
- Renderer webPreferences: `app/apps/desktop/src/main/security.ts`
- Untrusted view prefs: `app/apps/desktop/src/main/untrustedPreferences.ts`
- Untrusted view wiring: `app/apps/desktop/src/main/untrustedView.ts`
- Renderer loader: `app/apps/desktop/src/main/rendererLoader.ts`
- Stumble IPC: `app/apps/desktop/src/main/stumbleIpc.ts`
- Vault IPC: `app/apps/desktop/src/main/vaultIpc.ts`
- Open Web cache: `app/apps/desktop/src/main/openWebSources.ts`

## Notes and constraints
- Core domain must stay independent of Electron and UI code.
- Untrusted web content must remain sandboxed; no `<webview>`, no Node in renderers.
- Open Web safety filters are keyword-based and not perfect; keep them enabled
  for general browsing.
