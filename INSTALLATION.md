# Installation

Two layers:

- **Host-wide setup** — done once per machine. Configures Claude Code itself
  so it knows about clojure-lsp as a tool. Nothing here touches any project.
- **Per-project setup** — done once per Clojure repo you want intelligence
  on. The plugin will technically work without any per-project steps, but a
  few small touches (gitignore, optional `.lsp/config.edn`) keep things
  tidy and let you verify it's actually wired up.

---

## Host-wide setup (once per machine)

### 1. Install the `clojure-lsp` binary

The plugin **does not** ship the language server. Install it on PATH.

#### macOS

```bash
brew install clojure-lsp/brew/clojure-lsp-native
```

#### Linux

```bash
# Official installer — drops the native binary into /usr/local/bin (needs sudo)
curl -s https://raw.githubusercontent.com/clojure-lsp/clojure-lsp/master/install | sudo bash

# Or, install into ~/.local/bin without sudo
curl -s https://raw.githubusercontent.com/clojure-lsp/clojure-lsp/master/install | bash -s -- --dir "$HOME/.local/bin"
```

#### Verify (both platforms)

```bash
clojure-lsp --version
# expected: "clojure-lsp <date>" and "clj-kondo <version>"
```

If you get `command not found`, your install directory isn't on `PATH` —
add it to your shell rc and reopen the shell.

### 2. Register this repository as a Claude Code marketplace

User scope so every project on this machine picks it up.

```bash
# Recommended — pull straight from GitHub, no local clone required
claude plugin marketplace add Kynde/clojure-lsp-plugin
```

If you cloned the repo locally and want to point Claude at your clone (so
local edits show up immediately — useful if you're hacking on the plugin):

```bash
claude plugin marketplace add /path/to/your/clone/clojure-lsp-plugin
```

Verify:

```bash
claude plugin marketplace list
# expected: clojure-lsp-marketplace — Source: GitHub (Kynde/clojure-lsp-plugin)
#       or: clojure-lsp-marketplace — Source: Directory (/path/to/your/clone)
```

### 3. Install the plugin

```bash
claude plugin install clojure-lsp@clojure-lsp-marketplace
```

Verify:

```bash
claude plugin list
# expected: clojure-lsp@clojure-lsp-marketplace — Status: enabled

claude plugin details clojure-lsp@clojure-lsp-marketplace
# expected: LSP servers (1)  clojure-lsp
```

After this, **every** Claude Code session on this machine has clojure-lsp
available. Nothing else is needed at the host level.

---

## Per-project setup (once per Clojure repo)

Plugin is enabled at user scope, so no per-project install command is
needed. These steps are about making clojure-lsp actually useful inside a
given repo.

### 1. Confirm project root markers exist

clojure-lsp walks up from the file being edited and starts indexing at the
nearest of: `deps.edn`, `project.clj`, `bb.edn`. If none is present
clojure-lsp falls back to the git root, which is usually too broad and
gives slow startup + noisy results.

For `leanheat-app`: each Clojure subdir should already have its own
`deps.edn` or `project.clj`. If not, that's the first thing to fix — and
it's a fix you'd want regardless of this plugin.

### 2. Gitignore clojure-lsp's per-project cache

clojure-lsp creates `.lsp/.cache/` and `.clj-kondo/.cache/` inside each
project root the first time it indexes. Add to the project's `.gitignore`
(once):

```gitignore
.lsp/.cache/
.clj-kondo/.cache/
```

`.lsp/config.edn` and `.clj-kondo/config.edn` themselves are committed.

### 3. (Optional) Per-project `.lsp/config.edn`

Most projects need nothing here — defaults are sensible. Common reasons to
add one:

- Non-standard source paths (e.g. `src/main/clojure` instead of `src`):
  ```edn
  {:source-paths #{"src/main/clojure" "src/test/clojure"}}
  ```
- Silencing a noisy lint inside the project:
  ```edn
  {:linters {:unused-public-var {:level :off}}}
  ```

Commit this file. It applies to everyone who opens the project, not just
Claude.

### 4. Verify in this specific project

Open Claude Code in the project (or a Clojure subdir of it):

```bash
cd /path/to/your-clojure-project
claude
```

In the session:

```
/plugin            # check the Errors tab is empty for clojure-lsp
```

Then ask Claude to do something that requires LSP, e.g.:

> Find every caller of `myapp.foo/bar` in this codebase.

First-run indexing on a large repo can take a couple of minutes (the
plugin sets `startupTimeout` to 5 minutes). After that, diagnostics and
navigation are instant.

---

## Updating the plugin

If you registered the GitHub source, pull the latest version with:

```bash
claude plugin marketplace update clojure-lsp-marketplace
claude plugin update clojure-lsp@clojure-lsp-marketplace
```

If you registered a local clone, edits in your working copy are picked up
by the same two commands — or `/reload-plugins` inside a running session.

## Uninstall (host-wide)

```bash
claude plugin uninstall clojure-lsp@clojure-lsp-marketplace
claude plugin marketplace remove clojure-lsp-marketplace
```

Per-project `.lsp/` and `.clj-kondo/` directories are not removed by this
— delete them manually if you want a clean slate.
