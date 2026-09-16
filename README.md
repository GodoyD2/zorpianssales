# Zorpians sales bot scheduler

This repository does one thing: every few minutes it calls the Zorpians site,
which reads new secondary sales from Robinhood Chain and posts them in Discord.

The site holds the logic, the state and every credential. Nothing here reads a
wallet, a chain or a channel. It is a clock.

## How it runs

`.github/workflows/sales.yml` runs on a schedule and can also be started by
hand from the Actions tab. Each run makes one authenticated request. The route
keeps its own cursor, so a late run reads a wider window and a missed run is
caught up by the next one. Sales are never lost by a run that did not happen.

## The one secret

`SALES_SECRET`, under Settings, Secrets and variables, Actions. It must match
the value the site has. It is sent in a header, never in the url, so it stays
out of logs. Secrets are not shared with workflows from forked pull requests.

## Changing the site address

`SALES_URL` sits at the top of the workflow. It is not a secret.

## If a run fails

- **401**: the secret here and the one on the site do not match.
- **409**: another run was already going. Not a failure.
- **5xx**: the site answered with a problem; the next run tries again.

A scheduled workflow is paused after 60 days without activity in the
repository. Pressing Run workflow, or any push, brings it back.
