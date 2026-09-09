# Sonos Control

Control your Sonos system from Codex using natural language. Discover rooms, see what is playing, choose music, adjust volume, and manage speaker groups through the bundled Sonos MCP connection.

This is a community-maintained Codex plugin. It bundles a skill, tool reference, and connection to the Sonos-hosted MCP server; it does not run a local server and is not an official Sonos plugin.

## What you can do

- Discover households, rooms, speakers, and groups.
- Check playback, volume, and mute state.
- Play, pause, resume, and skip tracks.
- Play tracks, albums, artists, service playlists, Sonos favorites, Sonos playlists, and radio.
- Adjust room or group volume and mute settings.
- Group or ungroup rooms and move audio between speakers.
- Use line-in on supported hardware and Night Sound / Speech Enhancement on compatible home-theater players.

Availability depends on the connected MCP server, your speakers, and your music services. The included tool snapshot does not expose arbitrary URI/URL playback or TV-input switching.

## Quick start

You need a Sonos **S2** system registered to your own Sonos account, and Codex with plugin support. Sonos must be reachable through [the Sonos web player](https://play.sonos.com/). No API key, developer account, client secret, or local server is needed. Sonos handles registration and sign-in through OAuth. See [Sonos's setup requirements](https://tech-blog.sonos.com/posts/sonos-27mcp/).

### Install in Codex

Run these commands in your terminal:

```sh
codex plugin marketplace add szkocot/sonos-control
codex plugin add sonos-control@sonos-control
codex mcp login sonos
```

The first two commands install the skill **and the Sonos MCP connection**. The last command opens Sonos sign-in in your browser; sign in with the account that owns your system and approve access. If Codex already prompted you to connect Sonos during installation, you can skip the login command.

Start a **new Codex task**, then ask:

> Show my Sonos rooms and what is playing. Do not change anything.

That's it. You do not need to clone this repository, copy skill files, edit TOML, or add the MCP server separately. Every user connects their own account; credentials are managed by Codex and Sonos and are never bundled with the plugin.

In the Codex plugin browser (`/plugins` in the CLI), the plugin appears under the **Sonos Control** marketplace after adding the repository. If your Codex app offers a connection prompt, use it to sign in. Workplace policies must allow third-party plugins and remote MCP connections.

### Already using an older installation?

If you installed version 0.1.0 through a personal marketplace or copied the standalone skill, you can install the GitHub version with the commands above. Once the new version works, disable or remove the older skill/plugin registration to avoid duplicate instructions. An existing working `sonos` MCP connection does not need to be added again. If your client displays multiple Sonos connections, keep one active connection for the account you intend to control.

Do not delete a working connection or credentials before verifying the replacement.

### Other MCP clients or standalone skills

The Sonos server also works with compatible MCP clients without this Codex plugin. Add a **remote HTTP MCP server** with this URL and complete the client's OAuth sign-in:

```text
https://mcp.ws.sonos.com/mcp
```

The URL is sufficient for clients that support OAuth discovery and dynamic client registration. This configures Sonos's tools; it does not install this plugin's Codex skill.

For Codex users choosing a standalone skill instead of the plugin, configure the connection manually:

```sh
codex mcp add sonos --url https://mcp.ws.sonos.com/mcp
codex mcp login sonos
```

The [TOML example](config/sonos-existing-server.toml) is an alternative to the `mcp add` command. **Plugin users should skip this manual setup.**

## Try it

Use your actual Sonos room names in place of these examples:

> Use sonos-control. Show my Sonos rooms and their status. Do not change anything.

> What is playing in the Living Room?

> Set the Living Room volume to 15.

> Show my Sonos favorites.

> Play my selected favorite in the Living Room.

> Add the Bedroom to the Living Room group. It can replace the music currently playing in the Bedroom.

## How control works

The skill discovers current player and group IDs before using them and refreshes discovery after grouping changes. Playback controls apply to an entire group, while individual speaker volume and mute controls can target one room.

Explicit requests authorize ordinary changes without repeated confirmation. If a change would affect additional rooms, replace a different active session, or implicitly unmute a group beyond what you requested, the skill asks before proceeding. Changing group volume can unmute a fully muted group; changing one player's volume preserves that player's mute state.

Tool results are checked before reporting success. Ambiguous mutation timeouts trigger a state check instead of a blind retry.

## Updates

Refresh the GitHub marketplace and reinstall the latest plugin:

```sh
codex plugin marketplace upgrade sonos-control
codex plugin add sonos-control@sonos-control
```

Start a new task after updating. Sign in again only if Codex requests it.

## Troubleshooting

| Problem | What to check |
|---|---|
| Sonos tools are missing | Confirm the plugin is installed and enabled, connect Sonos, and start a new task. The plugin supplies the server configuration; you still need to sign in. |
| Authentication has expired | Run `codex mcp login sonos` and reconnect. |
| Plugin installation cannot find the plugin | Run `codex plugin marketplace add szkocot/sonos-control`, then install `sonos-control@sonos-control`. |
| `codex plugin` is not recognized | Update Codex to a version with plugin support, or use the manual MCP connection above. |
| Login reports that `sonos` is unknown | Check `codex mcp list` for the bundled server name and use it with `codex mcp login`. Confirm the plugin is enabled before adding any manual connection. |
| Sign-in succeeds but no system appears | Use the Sonos account that owns the S2 system and confirm it works in the Sonos web player. |
| A room cannot be found | Ask Codex to discover the system again and use the current room name. |
| An action affects more rooms than expected | Check group membership; playback controls act on the whole group. |
| A source or feature is unavailable | Check hardware capabilities and the current server's tool declarations. |
| An update is not visible | Refresh the installed copy or plugin cache, then start a new task. |

## Repository contents

| Path | Purpose |
|---|---|
| [`.codex-plugin/plugin.json`](.codex-plugin/plugin.json) | Plugin metadata and example prompts |
| [`skills/sonos-control/SKILL.md`](skills/sonos-control/SKILL.md) | Sonos control instructions and authorization rules |
| [`skills/sonos-control/references/tool-schemas.md`](skills/sonos-control/references/tool-schemas.md) | Snapshot of tool declarations from the development environment |
| [`.mcp.json`](.mcp.json) | Bundled Sonos HTTP MCP connection |
| [`.agents/plugins/marketplace.json`](.agents/plugins/marketplace.json) | GitHub-installable marketplace |
| [`config/sonos-existing-server.toml`](config/sonos-existing-server.toml) | Manual connection fallback for standalone skills |
| [`TESTING.md`](TESTING.md) | Validation commands and manual scenarios |

## Development and testing

This repository has no runtime code, dependency installation, or build step. See [TESTING.md](TESTING.md) for structural validation and read-only checks. Live playback and grouping tests should be run only when deliberately requested by the person controlling the speakers.

When contributing, keep tool names and parameters aligned with the connected server, preserve authorization rules, and describe the validation performed. The tool snapshot is a reference, not a guarantee that every account exposes every tool.
