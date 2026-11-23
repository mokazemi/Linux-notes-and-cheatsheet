# OpenCode setup

<!-- TODO: Rough notes, clean up later. -->

## Passwords / env vars in opencode config

Secrets (API keys, passwords, ...) should be set as env vars in the opencode background service config, not written directly into `opencode.json`:

```
opencode service set env <VAR_NAME> <value>
```

- Check them: `opencode service get env` (or `opencode service get env <VAR_NAME>` for one).
- Remove one: `opencode service unset env <VAR_NAME>`.
- **Remember `NO_PROXY` too**, so requests to localhost/the background service don't get sent through the proxy:

```
opencode service set env NO_PROXY "localhost,127.0.0.1,::1"
```

## Custom run script (make sure env vars are set)

The real binary is `/usr/bin/opencode`. Since `~/.local/bin` comes first in `PATH`, I wrote a wrapper at `~/.local/bin/opencode` so the proxy env vars are always set, no matter which shell launches it:

```
❯ which opencode
~/.local/bin/opencode

❯ cat ~/.local/bin/opencode
#!/usr/bin/env bash
export HTTP_PROXY="http://127.0.0.1:2080"
export HTTPS_PROXY="http://127.0.0.1:2080"
export NO_PROXY="localhost,127.0.0.1,::1"
exec /usr/bin/opencode "$@"
```

Background service commands, just in case: `opencode service status` / `start` / `restart` / `stop`.
