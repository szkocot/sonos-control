# Sonos Control

Control your Sonos system from Codex using natural language. Discover rooms, see what is playing, choose music, adjust volume, and manage speaker groups through an existing Sonos MCP connection.

This is a community-maintained Codex plugin. It contains a skill and tool reference; it does not include an MCP server and is not an official Sonos plugin.

## What you can do

- Discover households, rooms, speakers, and groups.
- Check playback, volume, and mute state.
- Play, pause, resume, and skip tracks.
- Play tracks, albums, artists, service playlists, Sonos favorites, Sonos playlists, and radio.
- Adjust room or group volume and mute settings.
- Group or ungroup rooms and move audio between speakers.
- Use line-in on supported hardware and Night Sound / Speech Enhancement on compatible home-theater players.

Availability depends on the connected MCP server, your speakers, and your music services. The included tool snapshot does not expose arbitrary URI/URL playback or TV-input switching.

## Requirements

- Codex with skills support; plugin installation also requires the `codex plugin` commands.
- A Sonos system and an account authorized to control it.
- Access to the Sonos MCP endpoint at `https://mcp.ws.sonos.com/mcp`.
- Git to clone this repository.

## Connect Sonos

If Sonos tools are already available in Codex, reuse that connection. Otherwise, add the server:

```sh
codex mcp add sonos --url https://mcp.ws.sonos.com/mcp
codex mcp login sonos
```

Complete the authentication flow when prompted. The [TOML example](config/sonos-existing-server.toml) is an alternative to `codex mcp add`; use one configuration method and avoid duplicate entries. Keep authentication credentials outside this repository.

The plugin intentionally omits `.mcp.json` because it uses an existing connection exposing `mcp__sonos__*` tools.

## Install

Clone the repository:

```sh
mkdir -p ~/plugins
git clone https://github.com/szkocot/sonos-control.git ~/plugins/sonos-control
```

### As a standalone skill

This is the simplest way to use the instructions without registering a plugin marketplace:

```sh
mkdir -p ~/.agents/skills
cp -R ~/plugins/sonos-control/skills/sonos-control ~/.agents/skills/
```

If that destination already exists, update its contents instead of nesting another `sonos-control` directory inside it. Start a new Codex task after installation.

### As a Codex plugin

Register the cloned directory in your personal plugin marketplace using Codex's plugin-creator skill. You can ask Codex:

> Register `~/plugins/sonos-control` as an existing plugin in my personal marketplace, preserving the plugin files and other marketplace entries, then install it.

Once the entry exists in `~/.agents/plugins/marketplace.json`, install it using the marketplace's name:

```sh
codex plugin add sonos-control@personal
```

Replace `personal` if your marketplace uses another name. The default personal marketplace is discovered automatically. Start a new Codex task to load the plugin. Choose either the standalone skill or plugin installation to avoid duplicate skill registrations.

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

Pull the latest source:

```sh
git -C ~/plugins/sonos-control pull --ff-only
```

For a standalone installation, replace the installed skill's files with the updated contents of `skills/sonos-control/`. For a plugin installation, ask Codex's plugin-creator skill to refresh the local plugin cache and reinstall it from the existing marketplace. Start a new task after either update.

## Troubleshooting

| Problem | What to check |
|---|---|
| Sonos tools are missing | Configure the MCP connection, authenticate, and start a new task. Installing this plugin alone does not connect your account. |
| Authentication has expired | Run `codex mcp login sonos` and reconnect. |
| Plugin installation cannot find the plugin | Confirm that your personal marketplace contains an entry pointing to the clone and that you used its actual marketplace name. |
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
| [`config/sonos-existing-server.toml`](config/sonos-existing-server.toml) | Optional connection configuration example |
| [`TESTING.md`](TESTING.md) | Validation commands and manual scenarios |

## Development and testing

This repository has no runtime code, dependency installation, or build step. See [TESTING.md](TESTING.md) for structural validation and read-only checks. Live playback and grouping tests should be run only when deliberately requested by the person controlling the speakers.

When contributing, keep tool names and parameters aligned with the connected server, preserve authorization rules, and describe the validation performed. The tool snapshot is a reference, not a guarantee that every account exposes every tool.
