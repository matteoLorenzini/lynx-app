# lynx-app

A ResearchSpace platform app, deployed as a docker container volume alongside the platform.

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
  images/                      # app images (server?)
  lib/                          # custom *.jar artefacts (custom services, JDBC drivers, etc.)
```

`plugin.id` must equal the folder/app name (`lynx-app`) once deployed under the platform's `/apps` directory.

## Deployment

On every push to `main` (or from **Run workflow**), [.github/workflows/deploy.yml](.github/workflows/deploy.yml)
runs on a self-hosted Linux runner and syncs this app into the ResearchSpace runtime data
directory. It updates changed files in `plugin.properties`, `assets/`, `config/`, `data/`,
`images/`, and `lib/`. It does not delete files from the runtime. After syncing, it restarts
the `researchspace` Docker Compose service and waits for it to respond.

### Runner configuration

Set `RESEARCHSPACE_COMPOSE_ROOT` in the runner environment to the directory containing the
`researchspace/` checkout. If unset, the workflow uses its configured default, which may be
specific to the runner.

The deployment target is derived from that directory:

```text
/path/to/compose-root/researchspace/runtime-data
```

The runner user must have write access to the target directory. Override the workflow's
default path with `RESEARCHSPACE_COMPOSE_ROOT` rather than editing the workflow for a
runner-specific location.

### Runner requirements 

- `rsync` must be installed on the runner.
- Docker Compose must be available to the runner user. They need permission to
  restart the `researchspace` service.
- The service must respond at `http://localhost:10214/` for the verification step to pass.


