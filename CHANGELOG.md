# Changelog

## v1.1.0 — 2026-02-25

Upstream improvements from production use across AIC Director and Greenmark cockpits.

### Takeoff
- Reuse `gitStatus` from session context instead of re-fetching (faster boot)
- ASCII art header for visual identity
- Handle corrupted bookmark files gracefully ("starting fresh" instead of crashing)

### Land
- Three invocation modes: interactive (`/land`), scripted (`/land <debrief>`), silent (`/land clean`)
- ASCII art header for visual identity
- Max 2 tool calls for faster exit

### New Skills
- `/cockpit-repair` — validate state.json, bookmarks, and skills; offer fixes for corruption

### State
- Added `template` field to `cockpit` section in state.json
- Bumped version to 1.1.0

## v1.0.0 — 2026-02-25

Initial release.

### Skills
- `/takeoff` — Boot sequence with bookmark resume and drift detection
- `/land` — Park sequence with outcome capture and bookmark write
- `/cockpit-status` — Instrument panel for active work, blockers, ages

### State Management
- `state.json` with watermarks, counters, and extensible `custom` key
- Bookmark schema v1.1 written to `~/.claude/bookmarks/`

### Documentation
- README with quick start, architecture, design principles
- Customization guide with examples (meeting-driven, email-driven, code-driven cockpits)
- CLAUDE.md template with placeholder sections
