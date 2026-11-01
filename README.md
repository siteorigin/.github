# SiteOrigin shared workflows

## Deploy

`.github/workflows/deploy.yml` is the one deploy for SiteOrigin sites. It logs in to the server as `deploy`, checks the server's key against a pinned copy, and runs one script from `/home/deploy/bin`. Those scripts, and `so-deploy` behind them, are in `siteorigin/siteorigin-servers`.

Each site repo has one `.github/workflows/deploy.yml` like this:

```yaml
name: Deploy

on:
  push:
    branches: [master, testing]

jobs:
  deploy:
    uses: siteorigin/.github/.github/workflows/deploy.yml@v1
    with:
      live: deploy-siteorigin-theme
      test: deploy-siteorigin-test-theme
    secrets: inherit
```

`master` or `main` deploys the live site. `testing` or `staging` deploys the test site. A repo that uses other branch names sets `live-branches` or `test-branches`, for example `test-branches: develop`. A repo with no test site leaves out `test`. Either way, `push: branches:` lists every branch that deploys.

### Secrets

Each site repo holds the same secret names. The values never appear in this repo.

| Secret | What it is |
|---|---|
| `DEPLOY_SSH_KEY` | The private `siteorigin-do-deploy` key. |
| `DEPLOY_HOST` | The live site's server address. |
| `DEPLOY_KNOWN_HOSTS` | The live server's public key, as one `known_hosts` line. |
| `TEST_DEPLOY_HOST` | The test site's server address. Only with a test site. |
| `TEST_DEPLOY_KNOWN_HOSTS` | The test server's public key. Only with a test site. |

A missing secret fails the run and names it.

### Versions

Site repos call `@v1`. A change that keeps every caller working moves the `v1` tag. A change that needs callers to change is `v2`, and each repo moves when it is ready.
