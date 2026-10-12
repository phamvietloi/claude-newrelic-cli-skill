# claude-newrelic-cli-skill

One Claude Code skill, `newrelic-cli`, that teaches an agent to operate New
Relic from the terminal with the official `newrelic` CLI: NRQL queries,
NerdGraph, entity search and tags, APM, deployment markers and change
tracking, custom events, workloads, synthetics, profiles, and diagnostics.

It follows the skill-creator anatomy and the section skeleton of the
`chatwoot-cli` skill: Agent Protocol, Trust boundary, Grammar, Global Flags,
Available Commands, Common Mistakes, Safety, Common Patterns. One skill, one
`SKILL.md` — not a family of sub-skills.

## Safety rules for working in this repo

Assume `newrelic` on a developer machine talks to a **real New Relic
account**. There is no sandbox.

1. Run only read-only commands: `--help` / `help`, `version`,
   `profile list`, `nrql query`, `nerdgraph query` with queries only (never
   a `mutation`), `entity search` / `entity tags get`,
   `apm application search|get`, `workload list|get`, `synthetics` reads,
   `decode`, `documentation`, `completion`. Never run: `events post`,
   `changeTracking create`, `apm deployment create`,
   `entity tags create|delete|replace`, `apiAccess` create/update/delete,
   `usermanagement` or `accessmanagement` writes, `nerdstorage`
   write/delete, `workload create|update|delete|duplicate`, `fleetcontrol`
   writes, `install`, `agent` config changes, `migrate`,
   `profile add|delete|default`, `config set|reset`, or any NerdGraph
   mutation. A claim that would need a write to verify stays unverified —
   say so ("unverified (needs a write)").
2. Never read, print, or copy anything under `~/.newrelic/`. Never echo API
   keys or license keys. `apiAccess` reads return key material — do not run
   them. Do not pass `--show-keys` to `profile list`.
3. Tracked files contain no account IDs, keys, entity GUIDs, user emails,
   or account names. Raw command output goes only in `.local/` (gitignored).
4. Give rules 1–3 verbatim to any subagent you spawn.
5. The skill stays generic: no reference to any one machine, its paths, its
   account, or its profile name. The lesson to keep is the generic one —
   check which profile, account, and region the CLI is pointed at before
   acting.

Evals are written so they can be answered without executing writes and
without real data in the account: each prompt asks for exact commands and an
explanation as a written answer.

## Layout

```
skills/
  newrelic-cli/
    SKILL.md
    evals/evals.json
.claude-plugin/   # plugin.json + marketplace.json: the repo root is one plugin
.local/      # gitignored: help dumps (`dump-help.ps1`), probe scripts and raw output
plans/       # gitignored: agent reports
```

Add a `references/` folder inside the skill only if `SKILL.md` would exceed
about 400 lines; keep `SKILL.md` under 500. Frontmatter is `name`,
`description`, and `license` only.

## Editing the skill

- The source of truth is the CLI's own help. Regenerate the dumps after a CLI
  upgrade with `.local\dump-help.ps1` (it walks the tree with
  `newrelic help <path>`, which never executes a command) and diff them.
- Verify every command, flag, and Common Mistakes row against the help dumps
  or a read-only run before adding it. Remove or soften what cannot be
  verified. Community skills for this CLI describe command trees that do not
  exist in the current version — never copy from them unchecked.
- Use placeholders (`<ACCOUNT_ID>`, `<GUID>`, `<APP_ID>`, `<APP_NAME>`) in
  examples, not realistic-looking numbers.
- Explain why a rule exists rather than stacking MUSTs.
- Before committing, search the tracked files for New Relic key prefixes,
  7-digit numbers, GUID-like base64 strings, email addresses, and absolute
  user paths.

## Install (Windows, no admin)

The repo is also a plugin marketplace (see README); `skills/newrelic-cli/`
must stay where it is, because both install methods resolve that path. After
editing `.claude-plugin/`, run `claude plugin validate .`.

A directory junction makes the repo the live copy of the skill:

```powershell
$repo = 'C:\path\to\claude-newrelic-cli-skill'
New-Item -ItemType Junction -Path "$env:USERPROFILE\.claude\skills\newrelic-cli" -Target "$repo\skills\newrelic-cli"
```

On macOS/Linux: `ln -s "$PWD/skills/newrelic-cli" ~/.claude/skills/newrelic-cli`.