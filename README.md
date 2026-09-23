<p align="center">
  <img src="https://raw.githubusercontent.com/open-coder-ai/chock/main/docs/assets/logo.svg" alt="chock logo" width="110">
</p>

<h1 align="center">chock-cursor-plugins</h1>

<p align="center"><strong>Chock policies as Cursor plugins — guards ship a real <code>beforeShellExecution</code> deny hook, gates judge the write at <code>preToolUse</code>.</strong></p>

<p align="center">

[![Generated-only](https://github.com/open-coder-ai/chock-cursor-plugins/actions/workflows/generated-only.yml/badge.svg)](https://github.com/open-coder-ai/chock-cursor-plugins/actions/workflows/generated-only.yml)
[![License: Apache 2.0](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](LICENSE)
[![Contribute upstream](https://img.shields.io/badge/contribute-chock--catalog-8957e5)](https://github.com/open-coder-ai/chock-catalog)

</p>

<p align="center">
  <img src="https://raw.githubusercontent.com/open-coder-ai/chock/main/docs/assets/demo.gif" alt="Chock's demo: an agent runs a destructive command and a guard plugin denies it before it executes" width="760">
</p>

An agent running in your editor can already touch your shell, your git history, and your CI
config. You want it to move fast without being the reason a stray `terraform destroy`
actually happens. Telling it to be careful in a prompt is not a guarantee; a plugin that can
refuse the command is closer to one — a matched destructive command is denied in the editor
before it runs, witnessed on a real Cursor install (2026-08-24), with benign commands in the
same session still allowed.

## Install

Cursor reads this repository as a plugin marketplace via `.cursor-plugin/marketplace.json`:

```
# Team marketplace: Dashboard -> Plugins -> Add Marketplace -> Import from Repo
#   https://github.com/open-coder-ai/chock-cursor-plugins
# Local install of one plugin (developer flow):
#   copy cursor/<plugin-id>/ to ~/.cursor/plugins/local/<plugin-id>/ and reload Cursor
```

## What you get

Two kinds of package enforce here, at different events.

**Guard policies** ship a `beforeShellExecution` hook, a guard script and a stdlib-only
adapter. The hook returns Cursor's `permission: "deny"` response and the command is refused.
This needs `python3` and a usable `bash` on PATH — without them the hook fails **open**, and
every guard's description says so verbatim.

**Gate policies** judge what a turn writes, not what it runs. They ship the policy's gate and
its runner instead of a shell guard, wired at `preToolUse` — judging the file a write would
create, before it lands — and again at `stop`, re-reading what the turn actually left on disk.
They need `python3`; without it a fail-open client allows silently, but a gate that cannot
reach a decision refuses rather than allowing something it never judged. Cursor does not hold
the turn's end, so a refusal at `stop` is handed back to the agent as a follow-up message once.

Advisory policies are a skill the client reads; nothing stops a violation. See **[PLUGINS.md](PLUGINS.md)** for the full list:
each policy, its version, and whether it enforces or advises in this client.

## Generated from chock-catalog

Every file here is compiled from policy sources in
[chock-catalog](https://github.com/open-coder-ai/chock-catalog) by
[chock](https://github.com/open-coder-ai/chock). Pull requests against this repository are
closed automatically — open them against the catalog instead.

- **Generated only:** CI regenerates from the pinned catalog and fails on any difference.
- **Byte-identical guards:** each guard script is a verbatim copy of its policy's source in the
  catalog, and the hook adapter a verbatim copy of its framework source.
- **Best-effort, not a boundary:** guards are pattern-based filters; see
  [SECURITY.md](https://github.com/open-coder-ai/chock/blob/main/SECURITY.md).
- **Tested upstream, and gated:** every policy ships an eval suite
  (`base/<policy>/evals/suite.yaml`) in the catalog, and the publish workflow runs
  `chock check` and `chock check --only evals` before packaging anything — a policy whose
  evals fail cannot reach this repository. The tests live in the catalog because the policy
  source does; this repository is compiled output.
- This README is hand-written, as are `SECURITY.md` and the workflows under `.github/`, so they
  sit outside the generated-only guarantee.

### Verify it yourself

Nothing above asks for trust that cannot be checked. This rebuilds the published tree from
source and compares it with what is committed here:

```bash
git clone https://github.com/open-coder-ai/chock-cursor-plugins dist
git clone https://github.com/open-coder-ai/chock-catalog catalog
git clone --branch "$(tr -d '[:space:]' < catalog/.framework-ref)" \
  https://github.com/open-coder-ai/chock framework
pip install ./framework
chock plugin build --repo catalog --policies-dir base --format cursor --out-dir dist
chock marketplace build --dist dist --tree cursor
git -C dist diff --exit-code && git -C dist status --porcelain
```

Silence from both `git` commands means this repository is byte-identical to a fresh build
from the catalog. The framework ref comes from the catalog's own `.framework-ref`, which is
what the publish and Generated-only workflows read, so this recipe cannot drift from the
release a tree was actually built with.
`chock-market.lock` records a sha256 per published plugin directory, so one package can be
checked without rebuilding the rest.

**If you are listing these plugins in a marketplace,** pin both a tag and the full commit
SHA. The tag names the release; the SHA is what holds the reviewed bytes still.

## Contributing

| You want to | Go to |
| :--- | :--- |
| Fix or add a policy | [chock-catalog](https://github.com/open-coder-ai/chock-catalog/blob/main/CONTRIBUTING.md) — it reaches every client from there, including this one |
| Report that a guard did or did not block on your Cursor version | an issue on [chock](https://github.com/open-coder-ai/chock/issues/new/choose), which records the witnessed-blocking claims these packages carry; "it fails open where you say it fails closed" is the most useful result you can send |
| Report a bug in how packages are generated | [chock](https://github.com/open-coder-ai/chock/issues/new/choose), where the emitter lives |
| Fix this README | here — it is hand-written, not generated |

## Part of open-coder-ai

| | |
|---|---|
| [agentseam](https://github.com/open-coder-ai/agentseam) | the primitives — one handler API and a verified capability matrix across 16 agents |
| [chock](https://github.com/open-coder-ai/chock) | the compiler — one policy into git hooks, CI gates and native pre-tool hooks |
| [chock-catalog](https://github.com/open-coder-ai/chock-catalog) | the policies — 42, each labelled enforced or advisory, with replayed evals |
| [context-report](https://github.com/open-coder-ai/context-report) | the evidence — a signed report of whether an agent artifact actually works |
| [chock-threat-intel](https://github.com/open-coder-ai/chock-threat-intel) | the threat ledger the catalog's policies answer to |
| chock-{claude,cursor,copilot,codex}-plugins | the catalog, packaged for each agent's plugin format (generated) |
| chock-quickstart · chock-example | template repos: what `chock init` leaves behind, and a full adoption |

## License

Apache-2.0, same as the framework and the catalog.
