# lynx-app

A ResearchSpace platform app (extension), deployed as a docker container volume alongside the platform.

## Structure

```
lynx-app/
  plugin.properties        # mandatory app descriptor (plugin.id, provider, version, dependencies)
  config/
    page-layout/            # *.hbs page-layout templates (compiled & cached, restart required)
    repositories/            # repository config .ttl files (e.g. default.ttl)
    services/                 # Ephedra service config .ttl files
  data/
    templates/                # *.html templates, hot-reloaded (no restart needed)
  assets/                      # static assets, hot-reloaded (no restart needed)
  images/                      # app images
  lib/                          # custom *.jar artefacts (custom services, JDBC drivers, etc.)
```

`plugin.id` must equal the folder/app name (`lynx-app`) once deployed under the platform's `/apps` directory.

## Deployment

On every push to `main` (or from **Run workflow**), [.github/workflows/deploy.yml](.github/workflows/deploy.yml)
runs on the configured self-hosted Linux runner, syncs this repo to the local app folder with `rsync`, and
restarts the ResearchSpace container so the app is reloaded.

### Required repository secrets

| Secret | Description |
| --- | --- |
| `DEPLOY_APP_PATH` | Absolute Linux path to this app's local folder (e.g. `/opt/researchspace/apps/lynx-app`) |
| `DEPLOY_CONTAINER_NAME` | Name of the local ResearchSpace Docker container to restart after sync |

### Linux self-hosted runner requirements

- `rsync`, `curl`, and `docker` must be installed on the runner machine.
- The runner service user must have write access to `DEPLOY_APP_PATH`.
- The runner service user must be allowed to run `docker restart` for `DEPLOY_CONTAINER_NAME`.

### Quick test: small push to `main`

Use this once your runner and secrets are configured to trigger deployment with a minimal change.

```bash
git checkout main
git pull --ff-only
printf "\nTest deploy: %s\n" "$(date -u +'%Y-%m-%d %H:%M:%S UTC')" >> README.md
git add README.md
git commit -m "chore: trigger deploy workflow test"
git push origin main
```

Then open **Actions** in GitHub and check the latest **Deploy ResearchSpace** run.

If you do not want to commit to `main` directly, use **Run workflow** manually from the Actions page.

Note: config file changes (repositories, services, page-layout) require the platform restart performed by the
workflow. Changes under `data/templates` and `assets` are picked up immediately without a restart.
