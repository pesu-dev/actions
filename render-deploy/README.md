# Deploy to Render (Render REST API)

Composite GitHub Action to trigger and monitor deployments on Render using the official Render REST API v1 (`https://api.render.com/v1`).

> **Status**: Specification / Research phase. Implementation code has not been written yet.

---

## Planned Capabilities

1. **Direct Render REST API v1 Integration**:
   - Triggers deployment via `POST /services/{serviceId}/deploys`.
   - Authenticates using standard Render API Bearer token (`Authorization: Bearer <RENDER_API_KEY>`).
2. **Selective Deploy Targets**:
   - Support deploying a specific commit SHA (`commitId`), e.g., the commit that passed CI checks.
   - Support deploying custom image URLs (`imageUrl`) for image-backed Render services.
   - Option to clear build cache (`clearCache: "clear" | "do_not_clear"`).
3. **Synchronous Polling & Failure Detection (Optional / Configurable)**:
   - Polls `GET /services/{serviceId}/deploys/{deployId}` until a terminal state is reached.
   - Success state: `live`.
   - Failure states: `build_failed`, `update_failed`, `pre_deploy_failed`, `canceled`, `deactivated`.
   - Fails the GitHub Actions step if the Render deployment fails or times out.
4. **Outputs**:
   - `deploy-id`: Render deployment identifier.
   - `deploy-status`: Final deployment status (`live`, `failed`, etc.).
   - `deploy-url`: Link to the deployment in Render dashboard.

---

## Planned Inputs

| Input | Required | Default | Description |
|---|---|---|---|
| `api-key` | **Yes** | — | Render Public API key (passed from repository secret). |
| `service-id` | **Yes** | — | Target Render Service ID (`srv-...`). |
| `commit-sha` | No | `${{ github.sha }}` | Specific Git commit SHA to deploy. |
| `image-url` | No | `""` | Image reference for image-backed services. |
| `clear-cache` | No | `false` | Whether to clear build cache before building. |
| `wait-for-completion` | No | `true` | Whether to poll until deploy reaches `live` or fails. |
| `timeout-minutes` | No | `15` | Maximum time to poll before timing out. |
| `poll-interval-seconds` | No | `10` | Interval between polling status requests. |

---

## Planned Outputs

| Output | Description |
|---|---|
| `deploy-id` | The ID of the created deploy on Render (`dep-...`). |
| `deploy-status` | Final deployment status (`live`, `build_failed`, etc.). |

---

## Planned Usage Example

```yaml
steps:
  - name: Deploy to Render
    uses: pesu-dev/actions/render-deploy@v1
    with:
      api-key: ${{ secrets.RENDER_API_KEY }}
      service-id: ${{ vars.RENDER_SERVICE_ID_STAGING }}
      commit-sha: ${{ github.sha }}
      wait-for-completion: true
```
