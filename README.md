# helm-actions
GitHub Action for Helm Tasks

Package a Helm chart, push it to an OCI registry (e.g. GHCR), and optionally update a `HELMCHART-VERSION` file back to the repository.

This action uses a prebuilt **toolset** image (contains `helm`, `yq`, `git`, etc.) and a lightweight action image that bundles the `entrypoint.sh` logic.

---

## Features

- Update `values.yaml` and `Chart.yaml` (`image.tag`, `version`, `appVersion`)
- `helm lint`, `helm package`
- Push packaged chart to OCI registry (GHCR) with `helm push`
- Optionally commit `HELMCHART-VERSION` back to repo
- Outputs: `chart_version`, `image_tag`, `package_file`

---

## Inputs

| Input | Required | Default | Description |
|---|---:|---|---|
| `chart_dir` | false | `charts` | Path to Helm chart folder |
| `oci_registry` | true | — | OCI registry host (eg. `ghcr.io`) |
| `oci_repository` | true | — | OCI repository (eg. `my-org/my-helm-charts`) |
| `version_prefix` | false | `0.1.0` | Base version prefix |
| `push_chart` | false | `true` | Push packaged chart to OCI |
| `update_repo` | false | `true` | Commit HELMCHART-VERSION back to repo |
| `target_branch` | false | (current) | Branch to push HELMCHART-VERSION |
| `package_output_dir` | false | `./` | Directory where packaged chart is created |
| `auth_token` | true | - | Token to login to registry and push to repo |

---

## Outputs

| Output | Description |
|---|---|
| `chart_version` | Final chart version string |
| `image_tag` | Generated image tag (runid-shortsha) |
| `package_file` | Packaged chart filename |

---

## Example usage (MarketPlace)

```
name: Publish Helm Chart
on:
  push:
    branches: [ main ]

permissions:
  contents: write
  packages: write

jobs:
  publish-helm-chart:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4

      - name: Publish Helm Chart
        uses: kulbhushanmayer/helm-actions@v1.0
        with:
          chart_dir: "charts"
          oci_registry: "ghcr.io"
          oci_repository: "my-org/my-helm-charts"
          version_prefix: "0.1.0"
          push_chart: "true"
          update_repo: "true"
          target_branch: "main"
          auth_token: ${{ secrets.GHCR_TOKEN }}   
```
