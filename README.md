# haus-feedback plugin

The Claude Code plugin and Chrome extension for Haus Feedback (https://feedback.haus.se), for Haus colleagues. This repo holds only built output and is published automatically from the private source repo; do not edit it here.

Using it needs a Haus account (`@haus.se`): the service decides who may do what, not this repo.

## Install the plugin

Needs Node 22 or newer on PATH.

```bash
claude plugin marketplace add WeAreHausTech/haus-feedback-plugin
```

```bash
claude plugin install haus-feedback@haus-feedback
```

Restart Claude Code, then ask "sign in to haus-feedback" and run `/haus-feedback:setup` in a customer repo.

## Chrome extension (optional, for "Send to Claude" on a local site)

1. `git clone https://github.com/WeAreHausTech/haus-feedback-plugin ~/Development/haus-feedback-plugin`
2. In Claude Code, ask "install the haus-feedback native host".
3. In Chrome, open `chrome://extensions`, turn on Developer mode, click "Load unpacked" and choose the `extension` folder of the clone.
4. Quit Chrome completely (Cmd-Q) and open it again.

Update later with `git pull` in the clone and `claude plugin marketplace update haus-feedback`, then reload the extension in `chrome://extensions`.
