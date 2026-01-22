# Claude Code Notes

## Spec File Synchronization

When updating the OAF specification, **both files must be updated**:

1. `docs/OPEN_AGENT_FORMAT.md` - The canonical markdown source
2. `docs/spec.html` - The HTML rendering for the website

Changes to one must be reflected in the other. They are not auto-generated from each other.

## Version Updates

When changing the spec version, update it in all locations:

- `docs/OPEN_AGENT_FORMAT.md` - Version field at top
- `docs/spec.html` - Title, header, and footer
- `docs/index.html` - Header and footer version references
