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
runs on the configured self-hosted Linux runner and syncs this app into the ResearchSpace
runtime data directory.

### Runner path configuration

The workflow expects the ResearchSpace checkout to be available on the runner machine at a
runner-specific location:

```text
<RESEARCHSPACE_CHECKOUT>
```

The workflow derives the deployment target from that checkout:

```text
<RESEARCHSPACE_CHECKOUT>/researchspace/runtime-data
```

Set `compose_root` in [.github/workflows/deploy.yml](.github/workflows/deploy.yml) to the
checkout path configured for the self-hosted runner. Keep this value runner-specific and avoid
documenting personal home directories in project files.

### Runner requirements

- `rsync` is recommended on the runner machine. The workflow falls back to `cp -ru` if `rsync` is unavailable.
- The runner user must have write access to the `researchspace/runtime-data` directory.
- The service must be reachable at `http://localhost:10214/` for the verification step to pass.


