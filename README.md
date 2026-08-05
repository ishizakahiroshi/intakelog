# intakelog

> 🚧 **Status: early development.** The npm name is reserved; the CLI is not implemented yet.

Record what you pulled onto your dev machine — an append-only **intake ledger** across npm, scoop,
winget, pip, cargo, `git clone` and `curl | sh`.

## Why

Lockfiles and SBOMs answer *"what is installed right now"*. When a supply-chain incident breaks,
the questions you actually need answered are different:

- **When** did this land on my machine?
- **Where** did it come from — the registry, a GitHub tarball, or a shell one-liner?
- **Why** did I install it, and can I remove it?
- What about everything **outside npm** — the tools from scoop/winget, the repos I cloned, the
  binaries I curl-piped into a shell?

None of that is in a lockfile. During the August 2026 keyv/cacheable compromise, answering
"what did I install after the attack started?" meant guessing from `node_modules` timestamps.
intakelog exists so that question has a factual answer instead of a guess.

## What it does

1. **Records intake events.** One append-only row per acquisition: timestamp, name, version,
   source URL, integrity hash, project, reason, and who did it (a human or which agent).
2. **Backfills what it can.** A snapshot pass diffs the current state of each package manager
   against the ledger, so things installed by hand still get picked up.
3. **Reports.** Generates a single self-contained HTML file summarising your posture, calling
   [osv-scanner](https://github.com/google/osv-scanner) for the vulnerability data.

## What it deliberately does not do

- **It is not a vulnerability scanner.** Matching against advisory databases is delegated to
  osv-scanner. A false negative in a security tool is worse than no tool at all, because it
  manufactures false confidence — so that responsibility stays upstream.
- **It never modifies your dependencies.** No auto-remove, no auto-upgrade, no lockfile rewrites.
- **It has no server.** Output is a local file. Nothing is hosted, nothing phones home.
- **It has zero runtime dependencies.** A tool that watches the supply chain should not drag in
  a supply chain of its own.

## Design principles

- **Never discard a target silently.** Anything that could not be scanned is reported with a count.
  Silently dropping unscannable input turns "not checked" into "looks clean", which is the exact
  failure mode this tool is meant to prevent.
- **Append-only.** Rows are never edited or reordered. Corrections are new rows.
- **Never invent a timestamp.** If the acquisition time cannot be determined, the field stays empty.
  A guessed value recorded as fact is worse than a gap.
- **Local-first.** Vulnerability matching runs against an offline database by default, so your
  dependency graph is not sent anywhere.

## Install

Not published yet. Once released:

```sh
npx intakelog            # try it once
npm i -g intakelog       # regular use
```

Requires [osv-scanner](https://github.com/google/osv-scanner) on `PATH` for the reporting step.

## License

MIT
