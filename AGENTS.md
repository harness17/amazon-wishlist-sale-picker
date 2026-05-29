# amazon-wishlist-sale-picker

Amazon.co.jp wishlist sale filter extension. Manifest V3 / plain JavaScript.

## Rules

- Keep `extension/shared/detect.js` as the single source of truth for sale detection.
- Do not commit private wishlist URLs, cookies, purchase history, account details, or raw personal data.
- Do not broaden `permissions`, `host_permissions`, or `content_scripts.matches` without explicit user confirmation.
- Keep Chrome and Firefox releases separate through `manifests/chrome.json` and `manifests/firefox.json`.

## Verify

```powershell
node .\verify-detect.mjs
```

When changing release packaging:

```powershell
.\scripts\package-release.ps1 -Target all
```

## Release

- Chrome package: update `manifests/chrome.json`, then run `.\scripts\package-release.ps1 -Target chrome`.
- Firefox package: update `manifests/firefox.json`, then run `.\scripts\package-release.ps1 -Target firefox`.
- Generated packages are written under `dist/` and are intentionally ignored by Git.
