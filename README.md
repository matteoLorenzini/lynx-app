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
runs on the configured self-hosted Windows runner, syncs this repo to the local app volume with `robocopy`, and
restarts the ResearchSpace container so the app is reloaded.

### Required repository secrets

| Secret | Description |
| --- | --- |
| `DEPLOY_APP_PATH` | Absolute Windows path to this app's local folder (e.g. `C:\apps\lynx-app`) |
| `DEPLOY_CONTAINER_NAME` | Name of the local ResearchSpace Docker container to restart after sync |

Note: config file changes (repositories, services, page-layout) require the platform restart performed by the
workflow. Changes under `data/templates` and `assets` are picked up immediately without a restart.
