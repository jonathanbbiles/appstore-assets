# appstore-assets

Public App Store screenshot sets for Jonathan Biles' apps, served over HTTPS so that
**App Store Connect can be fed from a URL** — no local hard-drive dependency, no manual
drag-and-drop into the ASC media dropzone.

```
render (headless Playwright)
   └─> scripts/publish-screenshots.sh <app> <dir>    (in App Builder Template)
         └─> this repo  ->  https://jonathanbbiles.github.io/appstore-assets/<app>/...
               └─> Codemagic `asc-metadata` workflow curls them  ->  fastlane deliver  ->  ASC
```

## Layout

```
<app>/manifest.json          machine-readable index: url + pixel size + bytes + md5 per file
<app>/iphone69/NN-name.png   1290x2796   iPhone 6.9"/6.7"   (always required)
<app>/ipad13/NN-name.png     2048x2732   iPad 13"/12.9"     (required if the app is Universal)
<app>/iphone65/NN-name.png   1242x2688 or 1284x2778         (only if still targeted)
```

- **The `NN-` prefix is the App Store display order.** `fastlane deliver` sorts alphabetically.
- **Pixel sizes are exact.** ASC rejects off-spec files at upload time; the publish script
  refuses to publish them in the first place.
- **A publish is a full replace** of that app's slot directories, so a screenshot for a
  feature you removed can't survive as a stale leftover (that's a Guideline 2.3.3 reject).

## Why these are public

They are App Store screenshots — they are about to be on a public product page anyway.
Nothing secret lives here: no keys, no tokens, no build config. Being public is the whole
point, because it is what lets CI `curl` them without handling a credential.

## Custom domain (optional)

To serve these from `assets.jonathanscribbles.com` instead of `github.io`:

1. Cloudflare DNS for `jonathanscribbles.com` → add `CNAME assets → jonathanbbiles.github.io`
   (set to **DNS only**, not proxied — GitHub issues the TLS cert).
2. Add a `CNAME` file to this repo containing `assets.jonathanscribbles.com`.
3. Update `ASSETS_BASE_URL` in `scripts/publish-screenshots.sh` and re-publish so the
   manifests carry the new URLs.

The `github.io` URL keeps working either way; the custom domain is branding only.
