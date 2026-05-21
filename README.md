# clojure-lsp-plugin

A [Claude Code](https://claude.com/claude-code) marketplace that ships a
single plugin (`clojure-lsp`) wiring up
[clojure-lsp](https://clojure-lsp.io/) so Claude gets real-time code
intelligence (diagnostics, go-to-definition, find-references, hover) on
Clojure projects.

## Quick install

```bash
# 1. Make sure the clojure-lsp binary is on PATH
brew install clojure-lsp/brew/clojure-lsp-native     # macOS
# or: bash < <(curl -s https://raw.githubusercontent.com/clojure-lsp/clojure-lsp/master/install)

# 2. Add this marketplace and install the plugin
claude plugin marketplace add Kynde/clojure-lsp-plugin
claude plugin install clojure-lsp@clojure-lsp-marketplace
```

See **[INSTALLATION.md](./INSTALLATION.md)** for the full walk-through
(host-wide vs. per-project setup, verification, updating, uninstall).

## Layout

```
clojure-lsp-plugin/
├── .claude-plugin/
│   └── marketplace.json          # marketplace manifest (lists the plugin)
├── plugins/
│   └── clojure-lsp/
│       ├── .claude-plugin/
│       │   └── plugin.json       # plugin manifest
│       ├── .lsp.json             # LSP server config
│       └── README.md
├── INSTALLATION.md
└── README.md
```

## License

MIT — see `plugins/clojure-lsp/.claude-plugin/plugin.json`.
