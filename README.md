# chock-cursor-plugins

Chock policies packaged as installable plugins for **Cursor**. Guard policies ship a real
`beforeShellExecution` hook, so a matched destructive command is **denied in the editor
before it runs** — witnessed blocking on a real Cursor install (2026-08-24), with benign
commands in the same session still allowed.

**This repository is generated.** Every file is compiled from policy sources in
[chock-catalog](https://github.com/open-coder-ai/chock-catalog) by
[chock](https://github.com/open-coder-ai/chock). Pull requests here are closed with a
pointer to the catalog — review belongs where the source is.

## Install

Cursor reads this repository as a plugin marketplace via `.cursor-plugin/marketplace.json`:

```
# Team marketplace: Dashboard -> Plugins -> Add Marketplace -> Import from Repo
#   https://github.com/open-coder-ai/chock-cursor-plugins
# Local install of one plugin (developer flow):
#   copy cursor/<plugin-id>/ to ~/.cursor/plugins/local/<plugin-id>/ and reload Cursor
```

## What a plugin actually does — read this before installing

- **Guard policies** ship the hook, a guard script and a stdlib-only adapter, and are
  **session-enforced**: the hook returns Cursor's `permission: "deny"` response and the
  command is refused. This needs `python3` and a usable `bash` on PATH; without them
  Cursor allows the command silently — the hook **fails OPEN**, and every guard's
  description says so verbatim. On Windows, disable the `python3` Microsoft Store alias
  or install Python.
- **Advisory policies** are a skill the client reads; nothing stops a violation.

See **[PLUGINS.md](PLUGINS.md)** for every policy, its version and its posture — generated
from the packages themselves, so it cannot drift from what is published.

**A plugin is not the same as adopting Chock.** A plugin governs one person's session in
one editor. Repo-wide enforcement — git hooks and a CI gate a session cannot skip — comes
from installing Chock in the repository:

```bash
pip install chock
chock init && chock sync --ci
```

## Layout

```
cursor/<policy-id>/                Cursor plugin packages (.cursor-plugin/plugin.json;
                                   hooks/hooks.json where the policy has a guard)
.cursor-plugin/marketplace.json    the index Cursor reads
```

## Trust

- **Generated only:** CI regenerates from the pinned catalog and fails on any difference.
- **Byte-identical guards:** guard scripts and the hook adapter are verbatim copies of
  their framework sources — a plugin cannot quietly behave differently from a repo install.
- **Best-effort, not a boundary:** guards are pattern-based filters. See
  [SECURITY.md](https://github.com/open-coder-ai/chock/blob/main/SECURITY.md).

## License

Apache-2.0, same as the framework and the catalog.
