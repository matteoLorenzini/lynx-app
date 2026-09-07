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
runs on the configured self-hosted Linux runner, syncs this repo into the ResearchSpace container, and
restarts the container so the app is reloaded.

### Required repository secrets

| Secret | Description |
| --- | --- |
| `DEPLOY_CONTAINER_NAME` | Name of the local ResearchSpace Docker container to restart after sync |
| `DEPLOY_CONTAINER_APP_PATH` | Absolute app path inside the container (e.g. `/apps/lynx-app`) |

### Optional fallback secret

| Secret | Description |
| --- | --- |
| `DEPLOY_APP_PATH` | Absolute Linux host path to this app's local folder when the container uses a bind mount |

The workflow prefers direct container sync when `DEPLOY_CONTAINER_APP_PATH` is set. Use `DEPLOY_APP_PATH` only if you explicitly want to sync through a bind-mounted host folder.


