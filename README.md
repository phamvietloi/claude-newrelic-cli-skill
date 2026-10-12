# claude-newrelic-cli-skill

A [Claude Code](https://claude.com/claude-code) skill for operating New Relic
from the terminal with the official
[`newrelic` CLI](https://github.com/newrelic/newrelic-cli).

| Skill | Covers |
|-------|--------|
| [`newrelic-cli`](skills/newrelic-cli/SKILL.md) | NRQL queries, NerdGraph (GraphQL), entity search and tags, APM applications, deployment markers and change tracking, custom events, workloads, synthetics, profiles, install, diagnostics |

The skill gives an agent the command grammar, the rules for choosing the
profile, account, and region a command runs against, the output shapes it
will have to parse, shell-quoting rules for NRQL and GraphQL (bash,
PowerShell 7, Windows PowerShell 5.1), a table of common mistakes, and a
safety section that separates read-only commands from writes that need
explicit approval. It also says what the CLI cannot do — there are no
`alerts` commands and no MCP server in the binary — and what to use instead.

It is for developers and operators who let a coding agent run `newrelic` for
them and want it to check where it is pointed before it writes: a deployment
marker, a tag change, or a custom event lands in a real account and cannot be
taken back. The skill is plain Markdown and does not install or run anything
itself; the CLI must already be available wherever the agent works.

## Install

Pick one method. Using both loads the skill twice.

### As a Claude Code plugin

Inside Claude Code:

```
/plugin marketplace add phamvietloi/claude-newrelic-cli-skill
/plugin install newrelic-cli-skill@claude-newrelic-cli-skill
```

The skill then appears as `newrelic-cli-skill:newrelic-cli`. The same from a
shell: `claude plugin marketplace add phamvietloi/claude-newrelic-cli-skill`,
then `claude plugin install newrelic-cli-skill@claude-newrelic-cli-skill`.

### From a clone

Link the skill folder into your personal skills directory, so a `git pull`
updates it in place.

Windows (directory junction, no admin rights needed), from the repo root:

```powershell
$repo = (Get-Location).Path
New-Item -ItemType Junction -Path "$env:USERPROFILE\.claude\skills\newrelic-cli" -Target "$repo\skills\newrelic-cli"
```

macOS / Linux:

```bash
ln -s "$PWD/skills/newrelic-cli" ~/.claude/skills/newrelic-cli
```

Start a new Claude Code session; the skill appears in the skill list.

## Limitations

- Verified against one CLI version only (table below). Command trees, flags,
  and output shapes were taken from that version's help text and from
  read-only runs; the CLI changes between releases, so check
  `newrelic help <command>` when something disagrees.
- Write commands were exercised once, on throwaway objects in a single
  account: custom events, workloads, entity tags, one NerdGraph mutation,
  the three deployment-marker commands, profile creation, `diagnose
  validate`, and the JUnit dry run. `synthetics run`, `install`,
  `diagnose run`, and the `apiAccess`, `usermanagement`,
  `accessmanagement`, `fleetcontrol`, `nerdstorage`, and `edge` writes were
  not run; what the skill says about those comes from their help text.
- The skill has not been benchmarked yet: the evals below exist but no
  with-skill / without-skill comparison has been run.

### Version the skill was verified against

| Component | Version |
|-----------|---------|
| New Relic CLI | 0.115.0 |

## Evals

`skills/newrelic-cli/evals/evals.json` holds three test prompts in the
skill-creator schema. Each asks for exact commands and an explanation as a
written answer, so they can be run without executing writes and without data
in the account. Results go to `skills/newrelic-cli-workspace/` (gitignored).

## Contributing

See [CLAUDE.md](CLAUDE.md) for the safety rules and layout. In short: verify
every claim against the CLI's help or a read-only run, never write to a real
account to test something, and keep account IDs, keys, GUIDs, and names out
of the repo.

## License and disclaimer

[MIT](LICENSE).

This is an independent project. It is not affiliated with or endorsed by New
Relic, Inc.; New Relic is a trademark of its owner.