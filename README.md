# talk.oidfed.com

Public talks by Justin Dah-Kenangnon. Each talk is a self-contained Marp deck
served at a stable path, deployed via Cloudflare Pages.

| Path | Talk | Date |
|---|---|---|
| [`/openid-working-group-call-2026-05-21/`](./openid-working-group-call-2026-05-21/) | @oidfed implementation overview & feedback | 2026-05-21 |

## Layout

```
.
├── CNAME · _headers · _redirects · wrangler.toml   # Cloudflare Pages config
├── index.html                                       # landing redirect
├── LICENSE                                          # MIT
└── <slug>/                                          # one directory per talk
    ├── deck.md                                      # Marp source
    ├── index.html                                   # built deck (committed)
    ├── themes/oidfed.css                            # custom Marp theme
    ├── assets/                                      # images referenced by the deck
    └── package.json                                 # marp-cli build scripts
```

## Build a deck

```bash
cd <slug>/
pnpm install            # or npm install
pnpm build              # writes index.html
pnpm watch              # rebuild on save
```

Marp is the only build tool. The deck is one Markdown file; the theme is one
CSS file. The committed `index.html` is what Cloudflare Pages serves.

## Deploy

Cloudflare Pages serves the repo root verbatim — no build step in CI. The
landing page (`/`) redirects via `_redirects` to the current default talk.

```bash
wrangler pages deploy . --project-name=talk-oidfed-com --branch=main
```

## Add a new talk

1. Copy the existing talk directory to `<YYYY-MM-DD-slug>/`.
2. Edit `deck.md`. Run `pnpm build`.
3. Update the table above and the default target in `_redirects`.
4. Push.

## License

MIT — see [`LICENSE`](./LICENSE).
