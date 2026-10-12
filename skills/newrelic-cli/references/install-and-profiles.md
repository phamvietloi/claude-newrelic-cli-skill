# Installing the CLI and setting up profiles

Read this when `newrelic` is missing, needs upgrading, or a profile has to be
created without a terminal. Checked against `newrelic` v0.115.0.

## Install sources

Official, all published by New Relic:

| Platform | Command or asset |
|----------|------------------|
| Any | GitHub releases of `newrelic/newrelic-cli`: tar.gz / zip / deb / rpm / `NewRelicCLIInstaller.msi`, with a checksums file |
| Windows | Scoop: `scoop bucket add newrelic-cli https://github.com/newrelic/newrelic-cli.git` then `scoop install newrelic-cli` |
| Windows | The MSI, or the `install.ps1` script from `download.newrelic.com/install/newrelic-cli/scripts/` |
| Windows | `choco install newrelic-cli` — listed by New Relic, but the package has been seen a release behind |
| macOS | `brew install newrelic-cli` |
| Linux / macOS | `curl -Ls https://download.newrelic.com/install/newrelic-cli/scripts/install.sh \| bash` |
| Linux | `sudo snap install newrelic-cli` |
| Container | `newrelic/cli` image |

Not official: a winget package for the CLI exists that is not published by
New Relic. Confirm any install with `newrelic version`.

These commands were taken from New Relic's own README and release assets;
they were not executed while writing this skill.

## What `profile add` does, case by case

Observed by adding a throwaway profile from a shell with no terminal
attached.

| Flags given | Result |
|-------------|--------|
| `--apiKey`, `--region`, `--accountId`, `--licenseKey` | No prompt, exit `0` |
| License key missing, no `-y` | Prints `? License Key:`, then dies with a fatal error (on Windows: `Incorrect function.`), exit `1`. **The profile has already been written**, without a license key |
| License key missing, `-y` | No prompt, exit `0`, profile written without a license key |
| Only `--apiKey`, `-y` | Exit `0` with a warning that the account ID was not set; profile written without an account ID |

Other things to know:

- `add` does not check the key. A wrong key is only discovered on first use
  (`401`).
- Adding a profile whose name already exists overwrites it without asking.
- Adding a profile does not change the default profile when one exists.
- When a value is missing, `add` first tries to work it out through the API.
  With a key the API rejects, each such run took about five minutes before
  it gave up — in CI, pass every value explicitly and set a timeout.
- Re-running `add` on an existing profile with a flag missing shows the
  stored value as the prompt's default, in clear. Don't do that where output
  is logged.
- `profile delete --profile <name>` removes one profile and leaves the
  others untouched.
- Exit `0` from `add` says nothing about completeness — look at
  `profile list` afterwards (an empty account column is easy to miss).

## Running without a profile

`NEW_RELIC_API_KEY`, `NEW_RELIC_ACCOUNT_ID`, `NEW_RELIC_REGION`, and
`NEW_RELIC_LICENSE_KEY` each override the matching profile field for one
process, and the CLI builds its client from them even when the profile named
with `--profile` does not exist.

| Observation | Meaning |
|-------------|---------|
| `could not initialize New Relic client …` | No usable profile and no `NEW_RELIC_API_KEY` |
| `401 … Invalid credentials` | The key in effect is wrong — check the environment before the profile |
| `403 … not authorized for account region` | The region in effect does not match the key's account |
| `unknown region, using default: US` (warning) | `NEW_RELIC_REGION` holds a value the CLI does not know |
| `Access denied.` | The account ID in effect is wrong or not reachable with this key |
| `403 response returned` from `events post` | The license key in effect was rejected |
