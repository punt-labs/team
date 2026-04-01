# Punt Labs Team

Shared identity registry for Punt Labs. Every project references this
repo as a git submodule at `.punt-labs/ethos/`, giving all tools —
ethos, biff, vox, beadle — access to the same set of humans and AI
agents.

## The Team

| Handle | Name | Kind | Personality | Role | Inspired By |
|--------|------|------|-------------|------|-------------|
| **jfreeman** | Jim Freeman | human | principal-engineer | CEO | -- |
| **claude** | Claude Agento | agent | friendly-direct | COO / VP Eng | L.L. Zamenhof |
| **bwk** | Brian K | agent | kernighan | Go specialist | Brian Kernighan |
| **mdm** | Doug M | agent | mcilroy | CLI specialist | Doug McIlroy |
| **rmh** | Raymond H | agent | hettinger | Python specialist | Raymond Hettinger |
| **djb** | Dan B | agent | bernstein | Security engineer | Dan Bernstein |
| **adb** | Ada B | agent | lovelace | Infra engineer | Ada Lovelace |
| **adt** | Alan T | agent | turing | PM (grounding) | Alan Turing |
| **ghr** | Grace H | agent | hopper | PM (building blocks) | Grace Hopper |
| **edt** | Edward T | agent | tufte | UX designer | Edward Tufte |
| **ach** | Alex H | agent | hamilton | Finance & ops | Alexander Hamilton |

## Teams

**Engineering** -- ethos, biff, quarry, beadle, vox, lux, punt-kit, z-spec, prfaq, cryptd

```text
jfreeman (CEO)
  └─ claude (COO)
       ├─ bwk (Go specialist)
       ├─ mdm (CLI specialist)
       ├─ rmh (Python specialist)
       ├─ djb (Security engineer)
       └─ adb (Infra engineer)
```

**Website** -- public-website

```text
jfreeman (CEO)
  ├─ claude (COO)
  │    └─ edt (UX designer)
  └─ ghr (PM building blocks)
```

## Directory Structure

| Directory | Contents |
|-----------|----------|
| `identities/` | One YAML per person/agent: handle, kind, email, github, personality, writing style, talents |
| `personalities/` | Markdown files defining behavioral instructions and temperament |
| `writing-styles/` | Markdown files defining communication rules and prose style |
| `talents/` | Markdown files defining domain expertise (engineering, product, security, etc.) |
| `roles/` | YAML files defining responsibilities and permissions per role |
| `teams/` | YAML files defining team membership, collaborations, and repository scope |
| `agents/` | Claude Code agent definitions (`.md` with YAML frontmatter for tools and description) |
| `config.yaml` | Default agent persona for the org |

## Adding as a Submodule

```bash
git submodule add git@github.com:punt-labs/team.git .punt-labs/ethos
```

The path `.punt-labs/ethos` is required. Ethos resolves identities,
personalities, writing styles, talents, roles, and teams from
`.punt-labs/ethos/` relative to the repo root. A different path will
not be found.

## How It's Consumed

When a Claude Code session starts in a project that has this submodule:

1. The ethos `SessionStart` hook resolves the agent's identity from
   `.punt-labs/ethos/identities/`
2. Personality and writing style are injected into the session context
3. Agent definitions from `agents/` are installed into `.claude/agents/`
4. The `SubagentStart` hook injects persona context when subagents
   (bwk, mdm, djb, etc.) are spawned

Other tools read the submodule directly:

- **Biff** reads `teams/` for collaboration graphs and presence
- **Beadle** reads `identities/` for email bindings
- **Vox** reads `identities/` for voice configuration via extensions

## Adding a New Project

Add the repository to the team's `repositories` list in
`teams/engineering.yaml` (or the appropriate team file):

```yaml
repositories:
  - punt-labs/ethos
  - punt-labs/your-new-repo   # add here
```

Then add the submodule in the new project:

```bash
git submodule add git@github.com:punt-labs/team.git .punt-labs/ethos
```

## Submodule Lifecycle

### Initial clone

When cloning a project that already has this submodule:

```bash
git clone --recurse-submodules git@github.com:punt-labs/<project>.git
```

Or if you already cloned without `--recurse-submodules`:

```bash
git submodule init
git submodule update
```

### Checking current state

```bash
# What commit is the submodule on?
git submodule status .punt-labs/ethos

# Is the submodule dirty (local edits)?
git -C .punt-labs/ethos status --short

# Is it behind the remote?
git -C .punt-labs/ethos fetch origin
git -C .punt-labs/ethos log HEAD..origin/main --oneline
```

A leading `-` in `git submodule status` means uninitialized. A `+`
means the checked-out commit differs from what the parent repo expects.
Detached HEAD is normal for submodules — don't try to fix it.

### Pulling latest

To update the submodule to the latest commit on this repo's main:

```bash
git -C .punt-labs/ethos fetch origin
git -C .punt-labs/ethos checkout origin/main
```

This changes the submodule ref. The parent repo now shows a dirty
`.punt-labs/ethos`. Commit the updated ref via PR (branch protection):

```bash
git checkout -b chore/update-team-submodule
git add .punt-labs/ethos
git commit -m "chore: update team submodule"
git push -u origin chore/update-team-submodule
# Create PR, wait for CI, merge
```

### Making changes

If you need to edit identity, role, or team data:

```bash
# Edit files inside the submodule
vim .punt-labs/ethos/identities/bwk.yaml

# Commit and push inside the submodule (pushes to punt-labs/team)
git -C .punt-labs/ethos add identities/bwk.yaml
git -C .punt-labs/ethos commit -m "feat: update bwk identity"
git -C .punt-labs/ethos push origin HEAD:main
```

Then update the submodule ref in the parent repo (same flow as
"Pulling latest" above). Other consuming projects also need their
submodule refs updated to see the change.

### Common pitfalls

- **Detached HEAD is normal.** Submodules check out a specific commit,
  not a branch. Don't run `git checkout main` inside the submodule
  unless you're about to make changes.
- **Push to the team repo, not the parent.** `git -C .punt-labs/ethos
  push` pushes to `punt-labs/team`. `git push` in the parent repo
  pushes the parent — it does not propagate submodule commits.
- **Branch protection requires PRs.** Both `punt-labs/team` (for data
  changes) and the consuming project (for submodule ref updates) have
  branch protection. Direct pushes to main are rejected.
- **Fetch before rebase.** If your submodule commit is rejected on
  push, fetch and rebase: `git -C .punt-labs/ethos fetch origin &&
  git -C .punt-labs/ethos rebase origin/main`.

## Adding a New Team Member

1. Create `identities/<handle>.yaml` with name, handle, kind, and
   attribute references
2. Create `personalities/<name>.md` with behavioral instructions
3. Create `writing-styles/<name>.md` with communication rules
4. Create `agents/<handle>.md` with Claude Code agent definition
5. Create `roles/<role-name>.yaml` if a new role is needed
6. Add the member to `teams/<team>.yaml`
7. Commit, push, and update the submodule in consuming repos

## What NOT to Put Here

**Extensions** (`.ext/` directories) are per-machine configuration and
do not belong in this repo. They live at
`~/.punt-labs/ethos/identities/<handle>.ext/<tool>.yaml` on each
machine. Extensions store tool-specific bindings (API keys, voice
preferences, local paths) that vary between environments.

**Session state** (`sessions/`) is ephemeral and lives at
`~/.punt-labs/ethos/sessions/` on each machine.

## Design

Teams and roles are formally specified in Z notation. The specification
type-checks with fuzz and animates with probcli. See
[ethos/docs/teams.tex](https://github.com/punt-labs/ethos/blob/main/docs/teams.tex)
for the formal model.

Key invariants enforced by ethos:

- Every team member references a valid identity and role
- Every team has at least one member
- Collaborations reference roles filled by team members
- No self-collaboration
- Referential integrity on role deletion
