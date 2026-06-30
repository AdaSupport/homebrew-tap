# homebrew-tap

Homebrew tap for **adacli** — the agent-native CLI for managing and improving Ada AI Agents.

## Install

```bash
brew install adasupport/tap/adacli
```

A standalone binary — no Node required. Supported: macOS (Apple Silicon + Intel) and Linux (x64 + arm64). Upgrade later with `brew upgrade adacli`.

> **macOS:** the binary is ad-hoc signed (not yet notarized). Installed via brew it runs with no Gatekeeper prompt. If you download a release tarball manually and macOS blocks it, clear the quarantine flag once:
> `xattr -d com.apple.quarantine "$(which adacli)"`

## What's here

- `Formula/adacli.rb` — regenerated automatically by the adacli release pipeline on each release.
- Per-arch binary tarballs are attached to this repo's [Releases](../../releases) (tag `adacli-v<version>`); the formula points at them and verifies their SHA-256.
