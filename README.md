# ikenga-registry

Static JSON registry of published [`@ikenga/pkg-*`](https://www.npmjs.com/org/ikenga) packages,
served via GitHub Pages and consumed by the Ikenga shell and `ikenga` CLI for pkg
discovery, install, and update.

**Endpoint:** https://royalti-io.github.io/ikenga-registry/index.json

## Layout

```
index.json              — root catalog: pkg names + latest version + detail pointer
index.json.minisig      — minisign signature over index.json
pkgs/<short-name>.json  — per-pkg detail: every published version, manifest, tarball URL, integrity
schema/                 — JSON Schemas (mirrors of @ikenga/contract Zod types)
REGISTRY_PUBLIC_KEY.txt — minisign public key for verifying signatures
REGISTRY_PUBLIC_KEY.md  — human-readable verify instructions
```

## Updating

This repo is **not edited by hand**. The release workflow in
[`Royalti-io/ikenga-pkgs`](https://github.com/Royalti-io/ikenga-pkgs)
runs `scripts/update-registry-index.mjs` after every successful Changesets
publish; that script writes/updates `index.json` and `pkgs/<name>.json`,
re-signs `index.json` with minisign, and pushes the changes.

The only manual edits accepted here are:

- README/governance updates
- Schema bumps (after the matching `@ikenga/contract` release)
- Rotating the signing key (requires shell-side public-key update)

## Signature

`index.json.minisig` is a [minisign](https://jedisct1.github.io/minisign/) signature
over `index.json`. The shell embeds the public key and refuses to trust an unsigned
or mismatched index.

Verify locally:

```bash
minisign -Vm index.json -P RWRTqugAYXnZRgZPMyuqRNB3G41wg+AhSU2yT8nmDNNQlWQPeCfRXAvI
```

The current public key also lives at [`REGISTRY_PUBLIC_KEY.txt`](REGISTRY_PUBLIC_KEY.txt).

## Schema

The shape of `index.json` and `pkgs/<name>.json` is defined by Zod schemas
in [`@ikenga/contract`](https://github.com/Royalti-io/ikenga-contract) (`./registry`
export). JSON Schemas are mirrored under [`schema/`](schema/) for non-TypeScript
consumers and editor autocomplete.

## License

[Apache-2.0](LICENSE).
