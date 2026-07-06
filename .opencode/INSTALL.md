# Installing Vibe Contributer for OpenCode

Add Vibe Contributer to the `plugin` array in your global or project `opencode.json`:

```json
{
  "plugin": ["vibe-contributer@git+https://github.com/lord-Rheagar/vibe-contributer.git"]
}
```

Restart OpenCode after changing the config. The OpenCode plugin registers the skills directory directly; no manual skill copy is required.

## Local Development

From this checkout, point OpenCode at the package path:

```json
{
  "plugin": ["/path/to/vibe-contributer"]
}
```

Restart OpenCode after changing the package source.
