# clojure-lsp

Claude Code plugin that wires up [clojure-lsp](https://clojure-lsp.io/) for
diagnostics, go-to-definition, find-references, and hover on
`.clj` / `.cljs` / `.cljc` / `.edn` / `.bb` files.

The `clojure-lsp` binary is **not** bundled. Install it yourself:

```bash
# Fedora / generic Linux
bash < <(curl -s https://raw.githubusercontent.com/clojure-lsp/clojure-lsp/master/install)

# or via Homebrew
brew install clojure-lsp/brew/clojure-lsp-native
```

Project-root detection is handled by clojure-lsp itself — it walks up from
the edited file looking for `deps.edn`, `project.clj`, `bb.edn`, or `.git`.

## Notes

- `.edn` is mapped to language id `clojure` (rather than a dedicated `edn`)
  because clojure-lsp only registers a single `clojure` language handler;
  using a separate id makes the server ignore the file.
- `startupTimeout` is 5 minutes to cover first-run indexing on large repos.
