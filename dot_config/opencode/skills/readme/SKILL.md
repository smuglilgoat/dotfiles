---
name: readme
description: Write or rewrite a repository's README.md — the front page developers scan before cloning. Use when asked to write, generate, rewrite or review a README, a project front page or a repo landing page. Covers section order (logo, one-phrase description, badges, intro, install/run, features, screenshots, doc links, contributing, build, credits, license), the developer voice, and the fact checks that keep badges, commands and screenshots real.
---

# README

A developer scans a README for about 30 seconds to decide three things: what
this is, whether it solves their problem, and how to run it. Write for that
scan. The README is the repo's front door, not its manual.

## Voice

Concise and developer-oriented. Every sentence carries a fact; cut the rest.

- Present tense, active voice. The reader is "you".
- No marketing adjectives ("powerful", "blazing-fast", "robust") unless a
  number in the same sentence backs them.
- Emoji in headings only if the project's other docs already use them.
- Language: the project's existing docs language, default English.
- Final pass is the declauding skill, embedded mode. If unavailable, strip at
  least: "seamless", "effortless", "dive in", "delve", "unleash",
  "supercharge", "look no further", "it's not just X, it's Y", "whether
  you're a beginner or", forced triads, and em-dash drama.

## Before writing

Read the repo first. Only claim what exists there:

- Manifest (`package.json`, `Cargo.toml`, `pyproject.toml`…) — real name,
  version, license, package registry.
- CI config, `Makefile`/`justfile`/scripts — commands that actually run.
- `CONTRIBUTING.md`, `LICENSE`, `docs/`, wiki links — the links to use.
- An existing README — keep what is true, drop what is not.

Missing logo or screenshot: leave a visible TODO and ask. Never invent one.
No CI means no CI badge — a badge that lies costs more trust than no badge.

## Structure

Fixed order. Drop a section only when it has nothing real to say.

| # | Section | Rules |
|---|---------|-------|
| 1 | Title + logo | `# Name`, logo centered above. No logo: title alone. |
| 2 | One-phrase description | One line: what it does, for whom. A definition, not a tagline. Example: `Terminal file manager with Vim keybindings, in one binary.` |
| 3 | Badges | 3-6, live, each linking to what it reports: CI, coverage, package version, license. shields.io. |
| 4 | Introduction | 2-4 sentences: what, why it exists, who uses it. Link out for depth instead of expanding here. |
| 5 | Install / run | Prerequisites first, then copy-paste blocks, one per method, package manager first. Build-from-source: one line pointing at the Build section. |
| 6 | Features | One phrase per feature: what it does. No adjectives, no periods. |
| 7 | Screenshots | Only if there is a UI. Alt text mandatory. CLI tool: a GIF of one real session beats still frames. |
| 8 | Documentation | Link functional/usage docs and technical/internals docs as separate entries, when they exist. |
| 9 | Contributing | 2-3 lines, link `CONTRIBUTING.md`. |
| 10 | Build | Clone, install deps, build, test — exact commands, tested. |
| 11 | Credits | Inspirations, the libraries that carry the project, contributors. |
| 12 | License | Name + link to the `LICENSE` file. One line. |

Optional sections, only when they earn their place, inserted after Features:

- **Table of contents** — only past ~150 lines.
- **FAQ** — only questions actually asked (issues, discussions).
- **Security** — if it touches secrets, auth or the network: how to report a vuln.
- **Requirements** — only versions that surprise (Node >= 20, Linux only).

## Never

- Inline the license text, the changelog or the API reference: link them.
- Badges, screenshots or commands that do not exist or have not run.
- Roadmap essays and architecture deep-dives: link, don't inline.
- Wall-of-text paragraphs. Break with a code block, a table, or a cut.

## Before done

1. The 30-second test: one scroll answers what / install / run. If not, cut,
   don't add.
2. Every badge and link resolves.
3. Every command was run or copied verbatim from CI or the Makefile.
4. One `#` for the title, `##` for every section. Markdown lints clean.
5. Declauding pass done.
