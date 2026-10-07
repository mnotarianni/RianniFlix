# Rianni Flix — Jellyfin theme

Custom branding for the Rianni Flix Jellyfin server, built on the
[riannitech.com](https://riannitech.com) design tokens: `#0a0a0f` background,
amber → pink gradient (`#f59e0b` → `#ec4899`), Space Grotesk headings, Inter body.

Targets **Jellyfin 12.x** (MUI `--jf-palette-*` variables, modern app bar). Covers the
header, nav, home sections, posters, buttons, inputs, login page, item details, player OSD,
skip-intro button, Up Next card, dialogs, menus, toasts, and settings.

## Install

Dashboard → Branding → **Custom CSS**, paste this one line, Save, then hard-refresh
(Ctrl+Shift+R). No server restart. Close and reopen the Samsung TV app.

```css
@import url('https://cdn.jsdelivr.net/gh/mnotarianni/RianniFlix@main/rianniflix-theme.css');
```

Custom CSS applies to Jellyfin in a browser and the Tizen/webOS TV apps.
Native apps (Swiftfin, Infuse, Android TV) draw their own UI.

## Repo layout

```
rianniflix-theme.css     the theme (all sections numbered + commented)
assets/
  logo.svg               header logo, light text          (278×64)
  logo-dark.svg          header logo, dark text (light backgrounds)
  wordmark.svg           text-only wordmark, light text
  wordmark-dark.svg      text-only wordmark, dark text
  icon.svg               square "rf" tile (512×512, scalable)
  icon-padded.svg        tile on dark rounded square (app-icon style)
  logo.png               logo @4x raster for places that can't take SVG
  icon-512.png / icon-192.png / apple-touch-icon.png / favicon-64.png / favicon-32.png
  app-icon-1024.png      padded icon, for app stores / Homepage tiles
  splash.png             1920×1080 splash with logo + wordmark
  background.png         1920×1080 branded background, no text (login page)
```

All SVG text is converted to paths, so the logos render identically without the fonts installed.

## Editing

- Colors, radii, fonts, and asset URLs are all variables in **section 1** of the CSS.
  Change them there; the rest of the file references the variables.
- `--rf-logo-url: none;` falls back to a gradient text wordmark (see the comment in section 4).
- jsDelivr caches `@main` for up to 12 h. After a push, purge to see it immediately:
  `https://purge.jsdelivr.net/gh/mnotarianni/RianniFlix@main/rianniflix-theme.css`
  (same pattern for files under `assets/`).
- For a stable release, tag it (`v1.0`) and change `@main` to `@v1.0` in the import.
  Tags are cached permanently and never need purging.

## Splash screen

Jellyfin's splash is toggled in Dashboard → Branding. To use `assets/splash.png` as the
custom splash image, upload it with the API (admin API key from Dashboard → API Keys):

```bash
curl -X POST "http://192.168.40.4:8096/Branding/Splash" \
  -H "Authorization: MediaBrowser Token=YOUR_API_KEY" \
  -H "Content-Type: image/png" \
  --data-binary @assets/splash.png
```

To remove it later: `curl -X DELETE ".../Branding/Splash" -H "Authorization: MediaBrowser Token=..."`.

## Other places to use the assets

- Login disclaimer (Dashboard → Branding): e.g. `Request shows at requests.rianniflix.com`
- Homepage dashboard: use `assets/icon.svg` as the Jellyfin tile icon
- Seerr: logo upload in Settings → General (use `logo.svg` or `logo.png`)
- Browser bookmark / PWA: `icon-192.png`, `apple-touch-icon.png`

## Known limits

- The admin **Dashboard** (`/dashboard`) does not load Custom CSS in Jellyfin 12.x by design,
  so it stays on the stock dashboard theme.
- Native apps (Swiftfin, Infuse, Android TV) draw their own UI; only the web client and the
  Tizen/webOS TV apps use Custom CSS.
- The theme never sets a `<body>` background and keeps `html.transparentDocument` transparent:
  TV apps and Jellyfin Media Player render video behind the page. Don't add one.
- Jellyfin waits for `transitionend` / `animationend` to hide the skip button, the Up Next card,
  dialogs and old backdrops. Don't add transitions inside those elements or blanket
  `animation: none` rules; see the comments in sections 13–16.

## Performance notes

- On the TV layout (`.layout-tv`) the theme disables `backdrop-filter` and the looping
  skip-button/Up Next animations, since TV browsers handle them poorly.
- `prefers-reduced-motion` disables all animation.
