# Rarely needed command groups

Read this when a task involves AI Monitoring telemetry, NerdStorage, user or
access management, API keys, Fleet Control, trace observers, host
installation, or the Terraform helpers. Taken from the help text of
`newrelic` v0.115.0; none of the write commands here were executed while
writing this skill, so check `newrelic help <group> <verb>` before use.

R = read-only · W = changes New Relic data · L = local only · H = changes
this host or local files.

| Command | | What it does |
|---------|-|--------------|
| `aimonitoring application search` / `aimonitoring events` | R | LLM telemetry reported by APM agents |
| `nerdstorage document\|collection get` | R | Read NerdStorage |
| `nerdstorage document write\|delete`, `collection delete` | W | Change NerdStorage |
| `usermanagement …`, `accessmanagement …`, `apiAccess …`, `fleetcontrol …` | R/W | Users, groups, grants, API keys, fleets — see the notes below |
| `install` | H | Installs agents/integrations on this host |
| `utils terraform dashboard`, `utils semver check` | L | Generate dashboard HCL from exported JSON; check a version against a constraint |
| `migrate nrqldroprules tf-*` | H | Terraform migration utilities — read their help before use |
| `edge trace-observer list` | R | Trace observers of an account |
| `edge trace-observer create\|delete` | W | Change trace observers |
| `agent config obfuscate --value <v> --key <k>` | L | Obfuscate a value for an agent config file |
| `agent config migrateV3toV4` | H | Rewrite an integration config file in the v4 format |
| `agent version <name>` | R | Latest released version of an agent |

Details worth knowing before touching these:

- `aimonitoring events` builds an NRQL query for you: `--type` (summary,
  message, embedding, feedback, tool, agent, vectorsearch,
  vectorsearchresult), `--select`, `--where`, `--since`, `--limit`.
- `usermanagement users|groups|auth-domains get` and
  `accessmanagement grants|roles|permissions get` are reads, but they return
  names and email addresses. Everything else in those groups (`create`,
  `update`, `delete`, `members add|remove`, `grants create|revoke`) changes
  who can access the account.
- `apiAccess` has `apiAccessGetKey`, `apiAccessCreateKeys`,
  `apiAccessUpdateKeys`, and `apiAccessDeleteKeys`. All of them handle key
  material; leave them to the user unless explicitly asked, and never print
  the result.
- `fleetcontrol`: `fleet get|search`, `fleet members list`,
  `entities get-managed|get-unassigned`, `configuration get`, and
  `configuration versions list` read; `create`, `update`, `delete`,
  `deploy`, `members add|remove`, and `versions add|delete` write.
- `nerdstorage` needs `--packageId` and a `--scope` of `ACCOUNT`, `ENTITY`,
  or `USER` (default `USER`).
- `install` takes `--recipe <name>` and `-y`; it changes the machine it runs
  on, not just the account.