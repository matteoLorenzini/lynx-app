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

On every push to `main`, [.github/workflows/deploy.yml](.github/workflows/deploy.yml) syncs this repo to the
production host's app volume via `rsync` over SSH and restarts the ResearchSpace container so the app is reloaded.

### Required repository secrets

| Secret | Description |
| --- | --- |
| `DEPLOY_SSH_HOST` | Hostname/IP of the production server |
| `DEPLOY_SSH_PORT` | SSH port (e.g. `22`) |
| `DEPLOY_SSH_USER` | SSH user with write access to the app path |
| `DEPLOY_SSH_KEY` | Private key for the above user (add the matching public key to the server's `authorized_keys`) |
| `DEPLOY_APP_PATH` | Absolute path to this app's folder on the server (e.g. `/apps/lynx-app`), mounted as a docker volume into the platform container |
| `DEPLOY_CONTAINER_NAME` | Name of the running ResearchSpace docker container to restart after sync |

Note: config file changes (repositories, services, page-layout) require the platform restart performed by the
workflow. Changes under `data/templates` and `assets` are picked up immediately without a restart.
