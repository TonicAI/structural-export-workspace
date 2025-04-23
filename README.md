# Tonic Structural Workspace Export

A GitHub Action to export the configuration stored in your Tonic Structural workspace.

## Usage

This action allows you to export your Tonic Structural workspace configuration via the Tonic API. The export can be triggered manually or scheduled to run automatically.

### Example Workflow

```yaml
name: Export Tonic Workspace

on:
  workflow_dispatch:
    inputs:
      workspace-id:
        description: 'ID of the Tonic workspace to export'
        required: true
        type: string
  schedule:
    - cron: '0 0 * * 1'  # Every Monday at midnight

jobs:
  export:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      # Create timestamp for files
      - name: Set timestamp
        id: timestamp
        run: echo "timestamp=$(date +'%Y%m%d-%H%M%S')" >> $GITHUB_OUTPUT

      - name: Export Tonic workspace
        id: export
        uses: tonic-ai/structural-export-workspace@v1
        with:
          api-key: ${{ secrets.TONIC_API_KEY }}
          workspace-id: ${{ github.event.inputs.workspace-id || secrets.TONIC_WORKSPACE_ID }}
          output-path: "./exports/workspace-config.json"

      # Create a timestamped copy
      - name: Create timestamped copy
        run: |
          cp "${{ steps.export.outputs.export-path }}" "./exports/workspace-config-${{ steps.timestamp.outputs.timestamp }}.json"

      - name: Commit changes
        run: |
          git config --global user.name 'GitHub Action'
          git config --global user.email 'action@github.com'
          git add ./exports/
          git commit -m "Workspace export $(date +'%Y-%m-%d')" || echo "No changes to commit"
          git push
```

## Inputs

| Input | Description | Required | Default |
|-------|-------------|----------|---------|
| `api-key` | Tonic API key with access to the workspace | Yes | |
| `workspace-id` | ID of the Tonic workspace to export | Yes | |
| `base-url` | Base URL for Tonic API | No | `https://app.tonic.ai` |
| `output-path` | Path where the export file will be saved | No | `workspace-export.json` |

## Outputs

| Output | Description |
|--------|-------------|
| `export-path` | Path to the exported workspace configuration file |

## Security

This action requires a Tonic API key with permissions to export the workspace configuration. Store this key as a secret in your GitHub repository and reference it as shown in the example workflow.

## License

This project is licensed under the terms specified in the [LICENSE](LICENSE) file.