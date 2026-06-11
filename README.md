# OpenPencil

[![CI](https://github.com/open-pencil/open-pencil/actions/workflows/ci.yml/badge.svg)](https://github.com/open-pencil/open-pencil/actions/workflows/ci.yml)
[![Release](https://img.shields.io/github/v/release/open-pencil/open-pencil)](https://github.com/open-pencil/open-pencil/releases/latest)
[![npm](https://img.shields.io/npm/v/%40open-pencil%2Fcli?label=%40open-pencil%2Fcli)](https://www.npmjs.com/package/@open-pencil/cli)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

Open-source design editor built on one premise: **a design is a node tree, not a picture.** It opens Figma's `.fig` files natively, exposes every operation to scripts and agents, and turns design changes into diffs you can review. Local-first — a ~7 MB desktop app or a browser tab; no account, no server.

> **Status:** Active development. Not ready for production use.

**[Try it online →](https://app.openpencil.dev/demo)** · [Download](https://github.com/open-pencil/open-pencil/releases/latest) · [Documentation](https://openpencil.dev) · [Roadmap](https://openpencil.dev/development/roadmap)

![OpenPencil](packages/docs/public/screenshot.png)

## Install

| | |
|---|---|
| **Web** | [app.openpencil.dev](https://app.openpencil.dev) — nothing to install |
| **Desktop** | `brew install open-pencil/tap/open-pencil` or [download](https://github.com/open-pencil/open-pencil/releases/latest) for macOS, Windows, Linux |
| **CLI** | `npm install -g @open-pencil/cli` |

## Why

Design tools treat the canvas as the product and the file as an implementation detail — a proprietary binary only the vendor's software can fully read, behind APIs that fight automation. Figma's MCP server is read-only. [figma-use](https://github.com/dannote/figma-use) added full read/write automation via CDP — then [Figma 126 killed CDP](https://forum.figma.com/report-a-problem-6/remote-debugging-port-not-working-in-figma-desktop-126-1-2-50858). Your workflows break when they decide to ship a point release.

OpenPencil inverts that. The document is a node tree you can open, query, transform, lint, and diff; the editor is one client of it. A designer moves things on the canvas; a script renames every color token; an AI agent builds a screen — all through the same small set of operations, and each change is data you can review like a code change. The editor, the engine, the file codec, the CLI, the MCP server, and the Vue SDK are all MIT; your files never leave your machine.

## The editor

- **Opens `.fig` and `.pen` files** — reads and writes Figma's native format; copy & paste nodes between Figma and OpenPencil. [Compatibility matrix →](https://openpencil.dev/development/roadmap#figma-compatibility-matrix)
- **AI in the editor** — press <kbd>⌘</kbd><kbd>J</kbd>, describe what you want; 100+ tools create and modify real nodes in your file. Bring your own API key (Anthropic, OpenAI, Google AI, OpenRouter, and compatible endpoints), or run Claude Code, Codex, or Gemini CLI directly in the chat panel
- **Components, variants, variables** — reusable components, variant sets, color/number/string/boolean variables with modes
- **Auto layout & CSS Grid** — flex and grid via Yoga, with gap, padding, alignment, track sizing
- **Real-time collaboration** — share a link, edit together: cursors, presence, follow mode. P2P over WebRTC; no server, no account
- **Local-first** — ~7 MB Tauri app for macOS, Windows, Linux, or a browser PWA. Works offline

## The toolkit

The same document operations, headless. Inspect, query, lint, analyze, and export `.fig`/`.pen` files from the terminal — or drive the running editor over RPC:

```sh
openpencil tree design.fig                                # node tree
openpencil query design.fig "//TEXT[contains(@name,'Button')]"   # XPath
openpencil lint design.fig --preset strict                # 18 rules: naming, layout, contrast
openpencil analyze colors design.fig                      # the real palette, by usage
openpencil export design.fig -f jsx --style tailwind      # design → Tailwind JSX
openpencil eval design.fig -c "figma.currentPage.selection.forEach(n => n.opacity = 0.5)" -w
```

```
#1d1b20  ██████████████████████████████ 17155×
#49454f  ██████████████████████████████ 9814×
#ffffff  ██████████████████████████████ 8620×
#6750a4  ██████████████████████████████ 3967×
```

`eval` is the full Figma Plugin API against your file. Every command supports `--json`. When the desktop app is running, omit the file argument and the CLI operates on the live canvas — useful for CI and agents.

[CLI documentation →](https://openpencil.dev/programmable/cli/)

## For AI agents

- **MCP server** — `npm install -g @open-pencil/mcp`, then connect Claude Code, Cursor, Windsurf, or any MCP client. 100+ tools over stdio or HTTP. [Setup →](https://openpencil.dev/programmable/mcp-server)
- **Agent skill** — `npx skills add open-pencil/skills@open-pencil` teaches coding agents the full inspect → edit → render → compare loop. [Skills repo →](https://github.com/open-pencil/skills)
- **llms.txt** — the docs site publishes [llms.txt](https://openpencil.dev/llms.txt), [llms-full.txt](https://openpencil.dev/llms-full.txt), and per-page Markdown

## For developers

- **Vue SDK** — headless components and composables for embedding OpenPencil or building custom editors on the same core. [SDK docs →](https://openpencil.dev/programmable/sdk/)
- **Packages** — [`@open-pencil/core`](https://www.npmjs.com/package/@open-pencil/core) (engine), [`@open-pencil/cli`](https://www.npmjs.com/package/@open-pencil/cli), [`@open-pencil/mcp`](https://www.npmjs.com/package/@open-pencil/mcp), [`@open-pencil/vue`](https://www.npmjs.com/package/@open-pencil/vue) — all from this repository

## Documentation

| | |
|---|---|
| [User guide](https://openpencil.dev/user-guide/) | The editor: canvas, shapes, text, components, export |
| [Programmable](https://openpencil.dev/programmable/) | CLI, MCP, SDK, AI chat, JSX renderer |
| [Reference](https://openpencil.dev/reference/) | CLI commands, node types, scene graph, file format |
| [Roadmap](https://openpencil.dev/development/roadmap) | Direction + the Figma compatibility matrix |

## Contributing

```sh
bun install
bun run dev        # Dev server at localhost:1420
bun run tauri dev  # Desktop app (requires Rust)
```

Quality gates: `bun run check` (lint + typecheck), `bun run test` (E2E visual regression), `bun run test:unit`, `bun run format`. See [CONTRIBUTING.md](CONTRIBUTING.md) for the workflow.

```
packages/
  core/           @open-pencil/core — scene graph, renderer, layout, file formats, tools
  vue/            @open-pencil/vue — headless Vue SDK
  cli/            @open-pencil/cli — headless CLI
  mcp/            @open-pencil/mcp — MCP server (stdio + HTTP)
  docs/           Documentation site (openpencil.dev)
src/              Vue app (components, composables, stores)
desktop/          Tauri v2 (Rust + config)
tests/            E2E + unit
```

Built on Skia (CanvasKit WASM), Yoga ([fork with CSS Grid](https://github.com/open-pencil/yoga/tree/grid)), Vue 3, Tauri v2, Kiwi + Zstd for the file format, Trystero (WebRTC) + Yjs for collaboration.

## Part of a larger stack

OpenPencil is the design layer of an open stack built so that software written by AI (and by people) can be checked, not just generated — every change carrying enough structure to verify. The argument and the map: [Building Blocks for the Future Web](https://github.com/elixir-vibe/building-blocks).

## Naming

There is another open-source project with the same name — [OpenPencil by ZSeven-W](https://github.com/ZSeven-W/openpencil), focused on AI-native design-to-code workflows. This project focuses on Figma-compatible visual design with real-time collaboration.

## Acknowledgments

Thanks to [@sld0Ant](https://github.com/sld0Ant) (Anton Soldatov) for creating and maintaining the [documentation site](https://openpencil.dev).

## License

MIT
