---
name: setup
description: Set up Haus Feedback for a customer site. Use when the user wants to add feedback to a site, create a feedback project, allow a customer to report, or runs /haus-feedback:setup.
---

# Set up Haus Feedback

Use the haus-feedback MCP tools. Admin (a verified @haus.se account) is required. If `whoami` says the account is not an admin, stop and explain that only verified @haus.se accounts can administer; do not try other tools.

The tools use the production API (https://feedback.haus.se) unless Claude Code was started with `HAUS_FEEDBACK_API`, which only developers of haus-feedback itself set (local stack `http://127.0.0.1:55421/functions/v1/api`). If a tool answers that the API was not found, tell the user to unset `HAUS_FEEDBACK_API` and restart Claude Code, unless they are developing haus-feedback. The extension the user loads must use the same API (`dist` for production, `dist-dev` for the local stack), or the project key `map_origin` stores is unknown to it.

1. `install_native_host`. It registers the host that lets the Chrome extension start Claude Code on this machine. Run it first, and again after an nvm switch or a plugin update (the extension then says the native host exited right away). If the result says `claude` was not found, tell the user; the host then falls back to the usual install folders.
2. `login`. It opens the browser; wait until it returns, then check with `whoami`. Skip `login` if `whoami` already shows an admin.
3. `list_projects`. If the site already has a project, use it. To allow another origin on an existing project later, use `add_origin` (and `remove_origin` to drop one); `update_project` with `allowedOrigins` replaces the whole list. Otherwise ask for the project name and the test environment's origin (for example `https://staging.example.com`, no path), then `create_project`.
4. Ask which customer email domain may report (for example `@customer.com`), then `add_reporter` with that pattern. Single addresses work too. Check with `list_reporters`.
5. For a new project, print the script tag exactly as `create_project` returned it, and tell the user to add it to the test environment's pages only, never production. For an existing project, do not rebuild the tag: say it is the one `create_project` returned when the project was made, and that an admin can supply the static host if it is lost. Never invent URLs.
6. `map_origin` for each local address of this repo (for example `http://myshop.localhost:3000`). Ask the user for the dev URL, or read it from the repo's dev config (dev server port, `.env`, `docker-compose`, `package.json` scripts) and confirm it. Pass the project, the origin without a path, and `repoPath` as the absolute path of this repo. Only localhost, `*.localhost`, `127.0.0.1`, `[::1]` and `*.test` are accepted; a warning about a missing `.git` means the path is probably not the repo root. Check with `list_mappings`.
7. Tell the user how to load the extension once. It is not in this repository or in the plugin: it lives in a clone of the public WeAreHausTech/haus-feedback-plugin (no GitHub access needed). Never point at a `packages/extension` folder of the current repo.
   1. If they have no clone yet: `git clone https://github.com/WeAreHausTech/haus-feedback-plugin ~/Development/haus-feedback-plugin`, outside this repo. Update later with `git pull` there.
   2. Load its `extension` folder (production) and leave `HAUS_FEEDBACK_API` unset. Only a developer of haus-feedback with the local stack running (`pnpm dev`, then `pnpm --filter @haus-feedback/extension build:dev`) loads `packages/extension/dist-dev` from the private source repo instead.
   3. `chrome://extensions`, turn on Developer mode, "Load unpacked", choose that folder, then quit Chrome completely (Cmd-Q) and reopen it so it sees the host.
8. Tell the user how to use it: open the mapped local site, click the haus-feedback toolbar button, "Sign in to give feedback" with their @haus.se account, file feedback with the Feedback button, then open the list button next to it, choose the Queue tab, select items and click "Send to Claude". On the first send, macOS asks whether Chrome may control Terminal: allow it.
