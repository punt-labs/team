# Changelog

All notable changes to the punt-labs team registry will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/).

## [Unreleased]

### Fixed

- Action pin comments now state the version actually pinned. The SHA is
  the security control, but the comment is the only part a human reads,
  so a wrong one hides a stale pin from every review — how
  `gh-action-pypi-publish` broke punt-kit's 0.12.0 release. Labels
  resolved against the GitHub API; no SHA changed

### Added

- Scoped inbound MCP tool set on all 37 specialist roles — quarry `find`/`remember`/`show`/`ingest`/`use`/`status`/`list`, biff `plan`/`read_messages`, and ethos `session`, each named under both the released and the `-dev` plugin prefix. ethos `identity` goes to 36 of the 37: `tech-writer` is trimmed of it, being the one role without `Bash` and so the one with no CLI door to identity mutation. `z-specialist` and `b-specialist` also get the z-spec read side. Without this set a dispatched specialist reaches no MCP server at all. Ports ethos #424; the 37 role files are byte-identical to ethos's.
- Boundary, stated as the absence of a name: no biff write, wall, or talk, no beadle mail, no GitHub, no ethos mission, no lux, and no `mcp__<server>__*` wildcard in any specialist role. `ceo.yaml` and `coo.yaml` carry no `tools:` key and are untouched — leadership keeps the outbound set.
- Game designer (gax) and TUI specialist (cht) personas
- Cryptd game personas (archivist, cryptkeeper)
- Tools field to role definitions
- ML engineering specialist (kpz / Andrej K)
- Python specialist (rmh / Raymond H)
- Cryptd to engineering team repositories

## [0.1.0] - 2026-03-25

### Added

- Initial team registry — 10 identities, roles, teams, agent definitions
- README with team roster, structure, and usage
