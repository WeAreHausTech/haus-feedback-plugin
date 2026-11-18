# haus-feedback plugin

New here? Follow the getting-started guide (`docs/getting-started.md` in the private source repo WeAreHausTech/haus-feedback, or the README of the public WeAreHausTech/haus-feedback-plugin) for the whole install, from plugin to Chrome extension.

Claude Code plugin for Haus Feedback: an MCP server (`dist/mcp.js`, bundled from `packages/mcp`), a native messaging host (`dist/native-host.js`, bundled from `packages/native-host`) and two skills (`skills/triage`, `skills/setup`).

## Requirements

1. Node 22 or newer on PATH (the MCP server is a Node bundle).
2. The tools use the production API. Set `HAUS_FEEDBACK_API` only to use another one, for local development `http://127.0.0.1:55421/functions/v1/api`.
3. No GitHub access needed: the plugin and the built extension are published to the public repo WeAreHausTech/haus-feedback-plugin. The source repo WeAreHausTech/haus-feedback stays private.

## Install

```bash
claude plugin marketplace add WeAreHausTech/haus-feedback-plugin
claude plugin install haus-feedback@haus-feedback
```

Then run `/haus-feedback:setup` or `/haus-feedback:triage`. Sign in with the `login` tool (admin, @haus.se). The session is stored in `~/.haus-feedback/credentials.json`.

## Chrome extension on this machine

The extension sends local feedback to Claude Code through a native messaging host. It is not in the plugin (the plugin cache holds only `plugin/`), so it is loaded from a clone: the `extension` folder of the public WeAreHausTech/haus-feedback-plugin, or `packages/extension/dist` in the private source repo. The full path from a clean machine is in `docs/setup.md` of the source repo; in short:

1. Clone WeAreHausTech/haus-feedback-plugin (public; developers of haus-feedback itself clone the private WeAreHausTech/haus-feedback).
2. Only when developing haus-feedback itself: in the clone, `pnpm install` and `pnpm dev`, then `pnpm --filter @haus-feedback/extension build:dev` for `packages/extension/dist-dev`, and start Claude Code with `HAUS_FEEDBACK_API=http://127.0.0.1:55421/functions/v1/api`. The extension and the MCP server must use the same API.
3. Otherwise: the `extension` folder of the public clone (the same build as `packages/extension/dist`) and the default API (`HAUS_FEEDBACK_API` unset).
4. Install this plugin, then run `install_native_host`. It writes `~/.haus-feedback/native-host.sh` (a wrapper that runs `dist/native-host.js` with this Node and sets `CLAUDE_BIN` and `HAUS_FEEDBACK_HOME`) and the host manifest `se.haus.feedback.json` into the NativeMessagingHosts folders of Chrome and Chromium (macOS: `~/Library/Application Support/...`, Linux: `~/.config/...`). `HAUS_FEEDBACK_NATIVE_HOSTS_DIRS` (path-delimited list) overrides the folders. Run it again after an nvm switch or a plugin update.
5. Load the unpacked extension (`extension` in the public clone, or `dist-dev` / `dist` in the source clone) in `chrome://extensions` with Developer mode on, then restart Chrome.
6. `map_origin` maps a local address (`localhost`, `*.localhost`, `127.0.0.1`, `[::1]`, `*.test`) to a project and the customer repo checkout, in `~/.haus-feedback/machine.json` (0600). `list_mappings` shows them. Origins in a project may use `*` for the host label and the port (`http://*.localhost:*`, `http://localhost:*`), but machine mappings match the origin exactly: map each address and port you use (for example `http://localhost:3000` and `http://localhost:5173`).
7. On the mapped site, click the toolbar button, sign in with your @haus.se account, file feedback, then use the Queue tab (list button next to Feedback) and "Send to Claude".
8. On the first send, macOS asks whether Chrome may control Terminal; allow it.

## Local development

1. `pnpm build` rewrites `plugin/dist/mcp.js`; commit it.
2. The server talks to production by default. Set `HAUS_FEEDBACK_API=http://127.0.0.1:55421/functions/v1/api` in the environment you start Claude Code from to use the local Supabase stack (the MCP process inherits it).
3. Try a checkout without installing: `claude --plugin-dir ./plugin`.
