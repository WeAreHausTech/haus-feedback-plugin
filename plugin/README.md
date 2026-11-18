# haus-feedback plugin

Claude Code plugin for Haus Feedback (https://feedback.haus.se): read, fix and answer feedback from customer test environments, set up projects and reporters, and send local feedback from Chrome to Claude Code.

## Install

The official install for every Haus colleague is the public repo [WeAreHausTech/haus-feedback-plugin](https://github.com/WeAreHausTech/haus-feedback-plugin). It holds only the built plugin and extension, so you need no GitHub account or seat. Access to feedback is decided by your `@haus.se` sign-in, not by GitHub.

Needs Node 22 or newer on PATH.

```bash
claude plugin marketplace add WeAreHausTech/haus-feedback-plugin
```

```bash
claude plugin install haus-feedback@haus-feedback
```

Restart Claude Code, ask "sign in to haus-feedback" (the `login` tool, `@haus.se` for admin), then run `/haus-feedback:setup` in a customer repo or `/haus-feedback:triage` to work through open feedback. The session is stored in `~/.haus-feedback/credentials.json`.

Already added the old private marketplace (`WeAreHausTech/haus-feedback`)? Remove it first with `claude plugin marketplace remove haus-feedback`, then run the two commands above.

## Update

```bash
claude plugin marketplace update haus-feedback
```

Restart Claude Code, then ask Claude to "install the haus-feedback native host" again if you use the Chrome extension.

## Chrome extension (for "Send to Claude" on a local site)

The extension is not inside the plugin, so it is loaded from a clone of the same public repo.

1. `git clone https://github.com/WeAreHausTech/haus-feedback-plugin ~/Development/haus-feedback-plugin`
2. In Claude Code, run `install_native_host` (ask "install the haus-feedback native host"). It writes `~/.haus-feedback/native-host.sh` and the host manifest `se.haus.feedback.json` into the Chrome and Chromium `NativeMessagingHosts` folders. Run it again after an nvm switch or a plugin update.
3. In Chrome, open `chrome://extensions`, turn on Developer mode, click "Load unpacked" and choose the `extension` folder of the clone.
4. Quit Chrome completely (Cmd-Q) and open it again.
5. `map_origin` (or `/haus-feedback:setup`) maps each local address you open, for example `http://localhost:3000`, to the project and the customer repo. Mappings match host and port exactly; `list_mappings` shows them.
6. On the mapped site, click the toolbar button, sign in with your `@haus.se` account, file feedback, then use the Queue tab and "Send to Claude". On the first send, allow Chrome to control Terminal when macOS asks.

Update the extension with `git pull` in the clone, then reload it in `chrome://extensions`.

## When it does not work

1. The tools say the API was not found: `HAUS_FEEDBACK_API` is set in your shell. Unset it and restart Claude Code.
2. The extension says the native host was not found or exited: run `install_native_host` again, then quit and reopen Chrome.
3. "Send to Claude" says the origin is not mapped: map that exact address with `map_origin`.

## Developing haus-feedback itself

This folder is built from the private source repo WeAreHausTech/haus-feedback (`dist/mcp.js` from `packages/mcp`, `dist/native-host.js` from `packages/native-host`) and published to the public repo by its `Publish plugin` workflow on every merge to `main`. Developers need a seat on the source repo; see `docs/setup.md` there.

1. `pnpm build` rewrites `plugin/dist/`; commit it.
2. Set `HAUS_FEEDBACK_API=http://127.0.0.1:55421/functions/v1/api` in the environment you start Claude Code from to use the local Supabase stack, and load `packages/extension/dist-dev` (`pnpm --filter @haus-feedback/extension build:dev`). The extension and the MCP server must use the same API.
3. Try a checkout without installing: `claude --plugin-dir ./plugin`.
