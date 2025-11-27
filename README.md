# Postman to PV Auto Client GitOps

Automated API client generation from Postman collections using GitHub Actions.

## Setup

### 1. Repository Secrets

Add these secrets to your repository (Settings → Secrets and variables → Actions):

- `POSTMAN_API_KEY` - Your Postman API key
- `POSTMAN_COLLECTION_UID` - Your Postman collection UID

### 2. Repository Structure

Your repository should be a Dart/Flutter project with:
- `pubspec.yaml` at the root
- Standard Dart project structure

## Usage

### Manual Trigger (GitHub UI)

1. Go to Actions tab
2. Select "Generate API Client" workflow
3. Click "Run workflow"
4. Optionally specify target branch (default: main)

### API Trigger

Trigger the workflow via GitHub API:

```bash
curl -X POST \
  -H "Accept: application/vnd.github+json" \
  -H "Authorization: Bearer YOUR_GITHUB_TOKEN" \
  -H "X-GitHub-Api-Version: 2022-11-28" \
  https://api.github.com/repos/OWNER/REPO/actions/workflows/generate-client.yml/dispatches \
  -d '{"ref":"main","inputs":{"target_branch":"main"}}'
```

**Requirements:**
- GitHub Personal Access Token with `repo` scope
- Replace `OWNER/REPO` with your repository
- Replace `YOUR_GITHUB_TOKEN` with your token

**Example with PowerShell:**
```powershell
$headers = @{
    "Accept" = "application/vnd.github+json"
    "Authorization" = "Bearer YOUR_GITHUB_TOKEN"
    "X-GitHub-Api-Version" = "2022-11-28"
}

$body = @{
    ref = "main"
    inputs = @{
        target_branch = "main"
    }
} | ConvertTo-Json

Invoke-RestMethod -Uri "https://api.github.com/repos/OWNER/REPO/actions/workflows/generate-client.yml/dispatches" -Method Post -Headers $headers -Body $body
```

**Webhook/Integration:**
You can trigger this from:
- CI/CD pipelines
- Postman webhooks (when collection changes)
- Scheduled tasks
- Custom automation scripts

### What It Does

1. Checks out your repository
2. Sets up Dart SDK
3. Clones and runs pv-auto-client tool
4. Generates API client from Postman collection
5. Commits changes if any
6. Creates a new branch with format: `generated-YYYY-MM-DD`
7. Pushes branch and creates a Pull Request

### Branch Naming

- Format: `generated-YYYY-MM-DD`
- If branch exists, appends counter: `generated-YYYY-MM-DD-1`, `generated-YYYY-MM-DD-2`, etc.

## Workflow Outputs

- Generated code in `lib/generated/`
- Swagger spec in `specs/api_spec.json`
- Updated `build.yaml` configuration
- Pull request for review

## Requirements

- Dart/Flutter project with `pubspec.yaml`
- Postman API key with collection access
- GitHub Actions enabled
