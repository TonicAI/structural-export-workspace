# Export Workspace Configuration

This action exports the configuration stored in your Tonic Structural workspace.

## Inputs

- `api-key` (required): Structural API key with access to the workspace
- `workspace-id` (required): ID of the Structural workspace to export
- `base-url` (optional): Base URL for Structural API, defaults to 'https://app.tonic.ai'
- `output-path` (optional): Path where the export file will be saved, defaults to 'workspace-export.json'

## Outputs

- `export-path`: Path to the exported workspace configuration file

## Example Usage

### Basic Usage

```yaml
steps:
  - name: Export Structural workspace
    id: export
    uses: TonicAI/structural-export-workspace@v1
    with:
      api-key: ${{ secrets.STRUCTURAL_API_KEY }}
      workspace-id: ${{ vars.STRUCTURAL_WORKSPACE_ID }}
```

### Complete Workflow Example

```yaml
name: Export Structural Workspace

on:
  workflow_dispatch:
    inputs:
      workspace-id:
        description: 'ID of the Structural workspace to export'
        required: true
        type: string

jobs:
  export:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set timestamp
        id: timestamp
        run: echo "timestamp=$(date +'%Y%m%d-%H%M%S')" >> $GITHUB_OUTPUT

      - name: Export Structural workspace
        id: export
        uses: TonicAI/structural-export-workspace@v1
        with:
          api-key: ${{ secrets.STRUCTURAL_API_KEY }}
          workspace-id: ${{ github.event.inputs.workspace-id }}
          output-path: "./exports/workspace-config.json"

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

## Security

This action requires a Structural API key with permissions to export the workspace configuration. Store this key as a secret in your GitHub repository and reference it as shown in the example workflow.