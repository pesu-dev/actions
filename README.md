# pesu-dev / actions

Common, reusable GitHub Actions workflows across [PESU Developer Group (pesu-dev)](https://github.com/pesu-dev) projects.

---

## Design Philosophy: Reusable Workflows Preferred

In `pesu-dev/actions`, **Reusable Workflows (`workflow_call`) are preferred over Composite Actions**:

1. **Individual Step Visibility in GitHub UI**:
   Composite actions collapse all execution into a single log step. Reusable workflows display each phase (payload validation, triggering, polling, summary generation) as its own separate step in the Actions web interface, making pipeline progress and failures instantly identifiable.
2. **Native Run Summaries**:
   Enables writing directly to `$GITHUB_STEP_SUMMARY` to display rich status cards and dashboard links on the workflow run overview.
3. **Runner Isolation & Job Capabilities**:
   Reusable workflows run in isolated environments with explicit `permissions`, `secrets`, and future support for GitHub Environment protection rules (manual approvals).

---

## Directory of Workflows

| Workflow | Path | Description |
|---|---|---|
| **Deploy to Render** | [`.github/workflows/deploy_render.yml`](.github/workflows/deploy_render.yml) | Trigger and monitor service deployments on Render via the official Render REST API v1. |

---

## `deploy_render.yml`

Deploys a service to Render and tracks deployment progress until it is live.

### Usage

```yaml
jobs:
  deploy:
    name: Deploy to Render
    uses: pesu-dev/actions/.github/workflows/deploy_render.yml@v1
    with:
      service_id: ${{ vars.RENDER_SERVICE_ID }}
      commit_sha: ${{ github.sha }}
      wait_for_completion: true
    secrets:
      render_api_key: ${{ secrets.RENDER_API_KEY }}
```

### Inputs

| Input | Type | Required | Default | Description |
|---|---|---|---|---|
| `service_id` | string | **Yes** | — | Target Render Service ID (`srv-...`). |
| `commit_sha` | string | No | `""` | Specific Git commit SHA to deploy (defaults to latest on connected branch). |
| `image_url` | string | No | `""` | Container image URL for image-backed Render services. |
| `clear_cache` | boolean | No | `false` | Whether to clear build cache before building. |
| `wait_for_completion` | boolean | No | `true` | Whether to poll until deploy reaches `live` or fails. |
| `timeout_seconds` | number | No | `900` | Maximum seconds to poll before timing out (15 mins). |
| `poll_interval_seconds` | number | No | `10` | Seconds between status polling requests. |

### Secrets

| Secret | Required | Description |
|---|---|---|
| `render_api_key` | **Yes** | Render Public REST API Bearer token. |

### Outputs

| Output | Description |
|---|---|
| `deploy_id` | The created Render deploy ID (`dep-...`). |
| `deploy_status` | Final deployment status (`live`, `build_failed`, `timed_out`, etc.). |
| `deploy_url` | Render dashboard URL for this specific deploy. |

---

## Repository Structure

```text
actions/
├── .github/
│   └── workflows/
│       ├── ci.yml             # Action & workflow linting via actionlint
│       └── deploy_render.yml  # Reusable workflow: Deploy to Render
├── .gitignore
├── LICENSE
└── README.md
```

---

## Future Plans

- **GitHub Environments Support**: Introduce an optional `environment` input (`staging`, `prod`) to bind deployments to GitHub Environment protection rules, reviewers, and environment-scoped secrets.
- **PR Source Checker**: Standardized check ensuring PRs originate from forks and not from a fork's `main` branch.
- **GHCR Image Build & Push**: Reusable Docker build and push workflow with GitHub Actions caching.
- **GHCR Retention Cleanup**: Reusable workflow to prune stale commit-sha tags in GitHub Container Registry.

---

## Versioning & Releases

- Releases follow semantic versioning (`v1.0.0`, `v1.1.0`, etc.).
- Major version tags (e.g., `v1`) are kept updated to point to the latest stable release of that major version, allowing workflows to pin to `@v1` without breaking on backwards-compatible updates.

---

## License

[MIT](LICENSE) © PESU Developer Group
