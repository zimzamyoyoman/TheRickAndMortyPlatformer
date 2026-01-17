# Technical Write-up: Hosting a Unity WebGL Build on GitHub Pages (Build-Only Repo)

## Summary
This repository is a **build-only** Unity WebGL project hosted on **GitHub Pages**. The Unity source project is not included; the repo contains the exported WebGL artifacts (`index.html`, `Build/`, `TemplateData/`) and serves them as a static site.

This write-up explains:
1) What files Unity WebGL exports and what they do  
2) How GitHub Pages hosts the build  
3) Common pitfalls and how to debug them  
4) Practical performance considerations for WebGL hosting

---

## 1) What Unity WebGL exports (and what each part does)
A typical Unity WebGL export contains three key parts:

- **`index.html`**  
  The entry point loaded by the browser. It boots the Unity loader and references build artifacts.

- **`Build/`**  
  The compiled build output produced by Unity (e.g., WebAssembly and data files) plus the Unity loader JavaScript. File names vary by Unity version/build settings.

- **`TemplateData/`**  
  Supporting assets for the WebGL template (CSS, icons, UI assets, etc.).

Because these are static files, the project can be hosted on any static host (GitHub Pages, Netlify, S3 static hosting, etc.).

---

## 2) How GitHub Pages hosts the build
GitHub Pages serves a branch/folder as a static website. In this repo, the WebGL build output sits at the repository root:
- `index.html`
- `Build/`
- `TemplateData/`

### Recommended Pages settings
In the GitHub repository:
- Settings → Pages
- **Source:** Deploy from a branch
- **Branch:** `master` (or `main`), **Folder:** `/ (root)`

Once enabled, GitHub Pages serves the site at:
`https://<username>.github.io/<repo>/`

Example for this project:
`https://zimzamyoyoman.github.io/TheRickAndMortyPlatformer/`

---

## 3) Common deployment pitfalls (and fixes)

### A) Blank page / infinite loading
Typical causes:
- `index.html` points to missing files in `Build/`
- wrong relative paths after moving build folders
- browser blocking assets due to caching mismatches

Fix checklist:
- confirm `index.html`, `Build/`, and `TemplateData/` are in the same structure Unity exported
- open DevTools → Console/Network and look for 404s (missing files)

### B) 404s due to incorrect base path
When hosting under a repo path (`/<repo>/`), builds must use **relative paths** correctly. If the template expects a different base URL, assets may fail to load.

Fix:
- keep Unity’s exported folder structure intact
- avoid renaming or nesting the `Build/` folder unless you also update references in `index.html`

### C) Compression / MIME-type issues (advanced)
Some Unity WebGL exports use compressed assets. If the server doesn’t deliver correct headers, the loader may fail.

Mitigation:
- prefer build settings compatible with static hosting
- if you see loader errors related to decompression, rebuild (if source exists) with compatible compression settings for GitHub Pages

(For this repo, since source is unavailable, the practical approach is to keep the currently working build as-is.)

---

## 4) Performance considerations for Unity WebGL on static hosting
Even on static hosting, you can meaningfully improve perceived performance:

- **Keep build size low:** Large textures/audio inflate `Build/` payloads.
- **Cache stability:** Avoid unnecessary file name churn so browsers can cache large artifacts.
- **Measure load time:** Use DevTools → Network to identify the largest artifacts and bottlenecks.

If rebuilding from source, typical levers include:
- texture compression / resolution limits
- audio compression settings
- stripping unused assets
- appropriate WebGL compression options compatible with static hosting

---

## 5) “Build-only” limitation (and how to present it professionally)
This repo intentionally functions as:
- a playable demo (GitHub Pages)
- a reference for how Unity WebGL builds are hosted as static sites

Because the Unity source project is not included, this is **not a code sample** repository. For recruiting, the value is in:
- demonstrating a shipped artifact (live demo)
- explaining the deployment structure clearly (this write-up)
- showing technical communication skills

---

## Appendix: Quick checklist for future WebGL hosting
- Export WebGL build from Unity
- Ensure `index.html`, `Build/`, `TemplateData/` stay together
- Enable GitHub Pages from repo root
- Verify in browser; debug 404s via DevTools Network tab
