# Install Sonos Control with bundled MCP

Users should install from the GitHub repository, sign in to their own Sonos account, and start controlling their own system without editing configuration files or registering a personal marketplace entry.

Bundle the public Sonos HTTP endpoint in `.mcp.json` and reference it from the existing plugin manifest. Add a repository marketplace named `sonos-control` whose local source is `./`, retaining the current plugin layout and existing local installations. Use authentication policy `ON_INSTALL`. Sonos provides OAuth discovery and dynamic client registration; no client secret, API key, shared credentials, or custom server is needed.

Alternatives considered: a setup script would add platform and configuration-management complexity; retaining manual MCP setup would leave the user's main friction unresolved. Native plugin packaging is the smallest supported solution.

The skill must accept the actual tool namespace exposed by Codex, including plugin-prefixed tools, and direct unauthenticated users to the bundled connection. Preserve all playback authorization rules. Document manual MCP setup only as a fallback for clients without plugin support, and explain duplicate installations without automatically deleting existing connections.

Validation: run manifest and skill validators; load and install the marketplace using Codex; inspect the installed MCP dependency and its login requirement; verify the published GitHub install path. Do not authenticate on another user's behalf or change speaker state. Each user needs an eligible Sonos S2 system, their own account, and a client/workspace that allows remote MCP.
