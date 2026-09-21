# pesu-dev / actions

Common, reusable GitHub Actions and workflows across [PESU Developer Group (pesu-dev)](https://github.com/pesu-dev) projects.

---

## Directory of Actions

| Action | Path | Type | Description |
|---|---|---|---|
| **Render Deploy** | [`render-deploy/`](./render-deploy/) | Composite Action | Trigger and monitor deployments on Render using the official Render REST API v1. |

---

## Usage

In your project's GitHub Actions workflow, reference actions directly using the repository path and tag:

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v7.0.1

      - name: Deploy to Render
        uses: pesu-dev/actions/render-deploy@v1
        with:
          api-key: ${{ secrets.RENDER_API_KEY }}
          service-id: ${{ vars.RENDER_SERVICE_ID }}
```

---

## Repository Structure

```text
actions/
├── .github/
│   └── workflows/
│       └── ci.yml             # Action and workflow linting (actionlint)
├── render-deploy/             # Render deployment composite action
│   └── README.md              # Action documentation & spec
├── .gitignore
├── LICENSE
└── README.md
```

---

## Versioning & Releases

- Releases follow semantic versioning (`v1.0.0`, `v1.1.0`, etc.).
- Major version tags (e.g., `v1`) are kept updated to point to the latest stable release of that major version, allowing workflows to pin to `@v1` without breaking on backwards-compatible updates.

---

## License

[MIT](LICENSE) © PESU Developer Group
