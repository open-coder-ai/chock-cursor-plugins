<p align="center">
  <img src="https://raw.githubusercontent.com/open-coder-ai/chock/main/docs/assets/logo.svg" alt="chock logo" width="110">
</p>

<h1 align="center">chock-cursor-plugins</h1>

<p align="center"><strong>Blocks your Cursor agent's wrong moves before they land.</strong></p>
<p align="center">Chock policies as Cursor plugins — guards ship a real <code>beforeShellExecution</code> deny hook, gates judge the write at <code>preToolUse</code>.</p>

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

## Two ways to get these plugins

**This marketplace: no chock on the machine.** Each policy is its own plugin, republished with
every chock release. You need Python 3.11+ and git, and nothing else. Cursor reads this
repository as a plugin marketplace via `.cursor-plugin/marketplace.json`:

```
# Team marketplace: Dashboard -> Plugins -> Add Marketplace -> Import from Repo
#   https://github.com/open-coder-ai/chock-cursor-plugins
# Local install of one plugin (developer flow):
#   copy cursor/<plugin-id>/ to ~/.cursor/plugins/local/<plugin-id>/ and reload Cursor
```

**The builder: one plugin, needs chock.** Pick the policies you want at
**<https://chock.sh/coding/builder/>**; it gives you one command that builds a single
`chock-guardrails` plugin on your machine from
[chock-catalog](https://github.com/open-coder-ai/chock-catalog). That command needs
`pip install chock`:

```bash
pip install chock
chock install --selection '<code>' --client cursor --apply
```

`<code>` is the selection code the builder shows for your choice. Cursor has no command to run
for a local plugin, so the output tells you to run **Developer: Reload Window**; then check the
plugin under **Customize**.

**Use one route for a given policy, never both.** Every copy of a plugin runs its own hook, so
a policy installed from both places runs twice. To switch a policy from one route to the other,
remove it from the first route before installing it from the second:

1. **Team marketplace import:** in Cursor, open **Dashboard -> Plugins & MCPs**, find this
   repository under **Team Marketplaces** and remove it (an admin action on Enterprise). Then
   open **Customize** and remove each plugin you installed from it; a plugin set to
   **Required** cannot be uninstalled until it is changed in the marketplace's settings. See
   [Cursor's plugin docs](https://cursor.com/docs/plugins).
2. **Local copy:** delete each `~/.cursor/plugins/local/<id>/` folder you copied from
   `cursor/<id>/`, then run **Developer: Reload Window** (or restart Cursor).

### Builder links, one policy at a time

Each link opens the builder with that one policy chosen for Cursor. The selections pin
`catalog.ref` to chock-catalog commit `e885f918a335e20dfddb4fad0331d4d5b4c5eb43`, so a link builds the
policy version at that commit, which can be newer than the one listed in [PLUGINS.md](PLUGINS.md).
"Published here as" is what [PLUGINS.md](PLUGINS.md) says for this client.

| Policy | Published here as | Builder |
| :--- | :--- | :--- |
| `agent-discipline` | advisory | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6ImFnZW50LWRpc2NpcGxpbmUiLCJ2ZXJzaW9uIjoiMC4wLjMiLCJzaGEyNTYiOiIwMzhiMTlkNzI2ZWZjMTI1ZWVhZjEzMjFhYzRmZWI3MGM3ZmQxYTIwN2RhOGU4OTUzYmVkNjVkNGFjZGQ4NDcyIn1dfQ) |
| `block-curl-pipe-sh` | enforces | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6ImJsb2NrLWN1cmwtcGlwZS1zaCIsInZlcnNpb24iOiIwLjEuNCIsInNoYTI1NiI6IjM0Yjk2MWM0ODI1ODhkMGI1MWM4ZGM3NjU1ZTkxNWVhOThjNWM0NzAyN2RjYTExNGY4YTdkMTI3ZjM3NjYyZjIifV19) |
| `block-destructive-commands` | enforces | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6ImJsb2NrLWRlc3RydWN0aXZlLWNvbW1hbmRzIiwidmVyc2lvbiI6IjAuMy4wIiwic2hhMjU2IjoiNTQxYjRkMzI3Nzg4NDI0OGQzMjE2MDFkMDRjZTAxODI1MzdjMzBlMjU5NmRjNzgwYzAwMWFlNTMyYjFiOWQ1MCJ9XX0) |
| `block-invisible-unicode` | enforces | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6ImJsb2NrLWludmlzaWJsZS11bmljb2RlIiwidmVyc2lvbiI6IjAuMS4xIiwic2hhMjU2IjoiYjEwODQzMTU3MDZkYzI2ZTMwOTdkZDFjMWE4OTNjNzgwNDE1ZjExNjhhYjVhOTk1YzE5YTg5NWY5MzY4OGM1YSJ9XX0) |
| `block-no-verify` | enforces | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6ImJsb2NrLW5vLXZlcmlmeSIsInZlcnNpb24iOiIwLjUuMCIsInNoYTI1NiI6ImE0MDZkNTBlNGNmMzBkNTY0MzA4NDA4MGFkNDI1OWQzNDJmM2VkZDA3MDM2ODg4YzQ3YTgyMTRmNjYwOTk0Y2MifV19) |
| `block-test-skips` | enforces | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6ImJsb2NrLXRlc3Qtc2tpcHMiLCJ2ZXJzaW9uIjoiMC4xLjAiLCJzaGEyNTYiOiI4NTM1YzUyZWQwNTViMDg0YjNkODcwYjMzOTQ0ODc0MWUxODM0ZjZmZDkxYWIyZDMyZWFkMWQyNjNmNDc1NTc0In1dfQ) |
| `block-unapproved-egress` | enforces | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6ImJsb2NrLXVuYXBwcm92ZWQtZWdyZXNzIiwidmVyc2lvbiI6IjAuMi40Iiwic2hhMjU2IjoiZTQ4NmZlMzdiMTIxYzIwNTQxZjcxY2VmNmNiODU2MDVmZTBhMDZlZWViNmU1NjM4M2M5OGZmYjAwYzAwN2UxMCJ9XX0) |
| `block-unguarded-agent-spawn` | enforces | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6ImJsb2NrLXVuZ3VhcmRlZC1hZ2VudC1zcGF3biIsInZlcnNpb24iOiIwLjIuMCIsInNoYTI1NiI6ImU0NWMyMTU1MWYxZjFhMjQyMjc1NjgzYWZlOWFlMDA0NjI3N2Q4ZDU3ZjUzNzM0NGFjNzEzZmZiZmJhNDYxMzcifV19) |
| `block-wildcard-agent-permissions` | enforces | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6ImJsb2NrLXdpbGRjYXJkLWFnZW50LXBlcm1pc3Npb25zIiwidmVyc2lvbiI6IjAuMC4xMCIsInNoYTI1NiI6IjZiMzE5ZGY2OGYwM2M5MzYyNmM2OTVkMjk4NzBjMTc5NDU2ZWZiZGY0ZWZiMzMwMTJiZTlmZDBlZDhhMzhlZWQifV19) |
| `chock-mise` | advisory | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6ImNob2NrLW1pc2UiLCJ2ZXJzaW9uIjoiMC4wLjMiLCJzaGEyNTYiOiIxYjA3MmJkNTE3NGFjOGE3NWJkNDQ0ZWQxMDBkNDFjZGI3NmNjMzlmMWQ2NmM4NjZhN2MyZDlhNzNlZWNmNTFmIn1dfQ) |
| `code-safety` | advisory | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6ImNvZGUtc2FmZXR5IiwidmVyc2lvbiI6IjAuMC40Iiwic2hhMjU2IjoiNTEwNjc5NTEyMzVhOWFkZDMwZjJhZmEyMDc2MmM1M2ZkMzkyYmEyZDU3Mzg0NTk3MGMyOWJkMzAyOTUxZWFlMiJ9XX0) |
| `context-hygiene` | advisory | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6ImNvbnRleHQtaHlnaWVuZSIsInZlcnNpb24iOiIwLjAuMiIsInNoYTI1NiI6IjQ4OTJkMjE0YzYyODk3YmIyODNlZjUyZmY2ZWNjODA1NmQ2NzIxMzYzMGY2MTdjMjEyMDZkZjVkNWI1ZTdkNzQifV19) |
| `firecrawl-fallback-only` | advisory | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6ImZpcmVjcmF3bC1mYWxsYmFjay1vbmx5IiwidmVyc2lvbiI6IjAuMS4xIiwic2hhMjU2IjoiYzFmYjNmNGNkMjRkZmIwNzBiYzFkY2ZhMGQ1N2Q3ZTkwMTc0ODE5MDhiYjU0ODUwYzU2ZDU2ZGZjYzA3OTA2NCJ9XX0) |
| `git-safety` | advisory | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6ImdpdC1zYWZldHkiLCJ2ZXJzaW9uIjoiMC4wLjQiLCJzaGEyNTYiOiI3ODRjM2ExODhjNWM0MmE4ODYwNzAzMDE5OWRlNGI1YWNhOWYzYzJmNDYyNzk4ZWI5NjY5MWM3MTMwN2NhODU4In1dfQ) |
| `guard-memory-writes` | enforces | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6Imd1YXJkLW1lbW9yeS13cml0ZXMiLCJ2ZXJzaW9uIjoiMC4wLjkiLCJzaGEyNTYiOiJlODhmZDQ2ZWJiYjdhNWE2NmY3N2U2NGI3YWU2NWU0MDk0NWE0ZDJhNTdkYzI1ZDUzYzMxZjczZTU3ODBjZDdiIn1dfQ) |
| `injection-defense` | advisory | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6ImluamVjdGlvbi1kZWZlbnNlIiwidmVyc2lvbiI6IjAuMC40Iiwic2hhMjU2IjoiNjQzN2NlMmEzNmE3Y2I0YTYwZmI3OGMzOTc4YTMzNjJhYTM1Y2M5Y2RmYjkzZjg2N2U1ODU4NTI3ZTc2MDljMCJ9XX0) |
| `java-security` | enforces | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6ImphdmEtc2VjdXJpdHkiLCJ2ZXJzaW9uIjoiMC43LjAiLCJzaGEyNTYiOiIyYjFhODU4ODE5NDZlMWJiNjUxNTcxODMyYzRiZWQ0N2YzZjIyZTYyNTU3NTllZDIxZDNhMTcwZGVjOTcwMDU4In1dfQ) |
| `limit-diff-size` | advisory | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6ImxpbWl0LWRpZmYtc2l6ZSIsInZlcnNpb24iOiIwLjAuMyIsInNoYTI1NiI6ImE3ZTVhZGQ2NmE1NzA2YTA4ODk4NDc2Njk1ZmUyYzMwYzA1ZTA2ZTRiZjgwYzBiNGM2NzIyY2QyODllNThhZjQifV19) |
| `memory-discipline` | advisory | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6Im1lbW9yeS1kaXNjaXBsaW5lIiwidmVyc2lvbiI6IjAuMC41Iiwic2hhMjU2IjoiMGY3YTY2N2EwN2U4ZjViOWM5NmVhZDZmOTZkZjdhMmU5ZTk4OWMzYzBlYzc3NmFiZjgzYTA3ZmRkYzY5NTVmOCJ9XX0) |
| `no-a11y-regression` | enforces | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6Im5vLWExMXktcmVncmVzc2lvbiIsInZlcnNpb24iOiIxLjEuMiIsInNoYTI1NiI6IjIxYzQ3Y2ZhZWE4ZDlmN2Q2MWU5ZjRkOWQ5YjEyN2I3MzcxNTM1NjEzYTU4OGUxYzVlNzQ0MzYwMTNkMGIxZGIifV19) |
| `pin-github-actions` | enforces | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6InBpbi1naXRodWItYWN0aW9ucyIsInZlcnNpb24iOiIwLjAuNyIsInNoYTI1NiI6IjE2OGQ2ZTk0NjMyNDkzMDlhYmQ0MDIyYWRjMTc2YjhkOTdhYjFmZGIxNmY0Y2E1OTY3ZjVjNGNhODU4NTY5ZWMifV19) |
| `protect-agent-config` | enforces | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6InByb3RlY3QtYWdlbnQtY29uZmlnIiwidmVyc2lvbiI6IjAuNy4xIiwic2hhMjU2IjoiMTk2NDQ1NzUzNjVmMjU1YTYxMjM0NTQyYzY3NmIzYmRmMDI4YmZkMjBhN2MyMDU3M2FjMjNkMzBiNTkwODdhMSJ9XX0) |
| `protect-ci-workflows` | enforces | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6InByb3RlY3QtY2ktd29ya2Zsb3dzIiwidmVyc2lvbiI6IjAuMi42Iiwic2hhMjU2IjoiMGU1MGNlOTc4ZmJhNzk5ZjNkZTZlYmE1MjRmZDIwYjFjZTg2YWRkOWM5YzFmNGJkNjE5Y2M5NGZkOGIzZjAzNyJ9XX0) |
| `protect-commit-privacy` | enforces | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6InByb3RlY3QtY29tbWl0LXByaXZhY3kiLCJ2ZXJzaW9uIjoiMC4yLjUiLCJzaGEyNTYiOiIzMzE0Mjk1YmFlMWE0Yjc1ZDUwM2Q5NzdjODQ0OWU4OThhODE1OTFjNWNhMWU5ODk2OGM5Zjc4OTkwMmZjNzAxIn1dfQ) |
| `protect-main-branch` | advisory | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6InByb3RlY3QtbWFpbi1icmFuY2giLCJ2ZXJzaW9uIjoiMC4wLjMiLCJzaGEyNTYiOiJlYmExNjRkMDljMjJiNDZmNWMyZDI2NTY2MDdkNzM1OTZkM2IwY2U0Yzc3ODE4NzM3ZWZjNTI3MmQ3ODcxOWVlIn1dfQ) |
| `protect-test-integrity` | enforces | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6InByb3RlY3QtdGVzdC1pbnRlZ3JpdHkiLCJ2ZXJzaW9uIjoiMC4wLjMiLCJzaGEyNTYiOiJlNTM2MGQ2ZTkzMjA1NGNiNmUzN2U3N2VkYTNiZTZlODFlZDkwMDBmZjU0MWIwYmRkM2YyZWU5MjU1ZDJlOTJmIn1dfQ) |
| `rtk-dangerous-actions-blocker` | enforces | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6InJ0ay1kYW5nZXJvdXMtYWN0aW9ucy1ibG9ja2VyIiwidmVyc2lvbiI6IjAuMy4wIiwic2hhMjU2IjoiMTQ5MDk3OTY2ZDJjOGJkYjJlMTU3YTQ0YjNmODdiYTk0ZWI5NWI0YWM3ZjcyNDI0MGU1MjUyZGQ1NmY0OGViMSJ9XX0) |
| `scan-secrets` | enforces | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6InNjYW4tc2VjcmV0cyIsInZlcnNpb24iOiIwLjEuMiIsInNoYTI1NiI6IjgwYzI3YjZjZTJmMTQ1NzFiMWMyYTFmYTIzZTdmNzFlMzA1Y2EzZTIxZjc2MGIzNTU3NTA5ZGZkNDMwNTYwZDAifV19) |
| `token-efficiency` | advisory | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6InRva2VuLWVmZmljaWVuY3kiLCJ2ZXJzaW9uIjoiMC4xLjEiLCJzaGEyNTYiOiIxOTAwM2ZhZmFhMmE0NDgzNmRhNTQ4NmY5ZjUyOGE2N2E3NWM0NGIyYjQ5MjJlMjY0NDUyZTU5MjliNWExYzc2In1dfQ) |
| `verify-dependency-exists` | enforces | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6InZlcmlmeS1kZXBlbmRlbmN5LWV4aXN0cyIsInZlcnNpb24iOiIwLjIuMCIsInNoYTI1NiI6ImE5ODYzZmY0OGUyNDlmMDdhNjBhNTZmMmI0Y2ViYWNmMjRiN2M1NDExNzNjOTA5YmU4NGY2ZjY5OTE3ODg5NTUifV19) |
| `verify-mcp-allowlist` | enforces | [Open in the builder](https://chock.sh/coding/builder/#s=eyJzY2hlbWEiOjIsImNsaWVudCI6ImN1cnNvciIsImJ1bmRsZSI6eyJ2ZXJzaW9uIjoiMS4wLjAifSwiY2F0YWxvZyI6eyJzb3VyY2UiOiJodHRwczovL2dpdGh1Yi5jb20vb3Blbi1jb2Rlci1haS9jaG9jay1jYXRhbG9nIiwicmVmIjoiZTg4NWY5MThhMzM1ZTIwZGZkZGI0ZmFkMDMzMWQ0ZDViNGM1ZWI0MyJ9LCJwb2xpY2llcyI6W3siZnJvbSI6ImNhdGFsb2ciLCJpZCI6InZlcmlmeS1tY3AtYWxsb3dsaXN0IiwidmVyc2lvbiI6IjAuNS4zIiwic2hhMjU2IjoiMDQyMTY4ODlhNjAxMmIyZTU4MjJkYWI4MWRmNTg2MzZhMDdiNDg3YzIwZDFmZmI4MDIzYjcwZTE4YjRjZTFhNCJ9XX0) |

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
