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
runs on the configured self-hosted Linux runner and syncs this repo into the local ResearchSpace
runtime-data app directory.

### Runner path configuration

The workflow currently expects this ResearchSpace checkout on the runner machine:

```text
/home/mlorenzini/researchspace-docker-desktop-main
```

It syncs this repo into:

```text
/home/mlorenzini/researchspace-docker-desktop-main/researchspace/runtime-data/apps/lynx-app
```

If your Ubuntu machine uses a different path, update the `compose_root` value in [.github/workflows/deploy.yml](.github/workflows/deploy.yml).

### Runner requirements

- `rsync` is recommended on the runner machine. The workflow falls back to `cp -ru` if `rsync` is unavailable.
- The runner user must have write access to the runtime-data app directory.
- The service must be reachable at `http://localhost:10214/` for the verification step to pass.


