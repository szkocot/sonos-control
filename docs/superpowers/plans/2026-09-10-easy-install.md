# Easy Sonos Installation Plan

**Goal:** Install the plugin and its Sonos connection from GitHub without manual configuration.

**Architecture:** Keep the root plugin; add a repository marketplace pointing to `./` and a bundled HTTP MCP definition. Let Codex and Sonos handle per-user OAuth.

**Tech stack:** JSON manifests, Markdown skill/docs, Codex CLI.

- [x] Add `.agents/plugins/marketplace.json` with name `sonos-control`, source `./`, installation `AVAILABLE`, authentication `ON_INSTALL`, and category `Productivity`.
- [x] Add `.mcp.json` with `mcpServers.sonos.type` = `http` and URL `https://mcp.ws.sonos.com/mcp`; reference it in the manifest and release as version `0.2.0`.
- [x] Update the skill to resolve available Sonos tools by function name across namespaces and provide connection guidance without inventing tool names.
- [x] Replace README installation steps with `codex plugin marketplace add szkocot/sonos-control` and `codex plugin add sonos-control@sonos-control`, followed by sign-in and a new task. Keep manual setup as a separate fallback.
- [x] Run plugin/skill validation and exercise marketplace loading and installation with the real CLI; verify OAuth requirements without controlling speakers.
- [x] Review the diff, commit, push, and verify the published installation path.
