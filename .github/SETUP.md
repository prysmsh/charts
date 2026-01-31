# GitHub Helm Chart Repository Setup

This guide explains how to set up the Prysm Helm Charts repository on GitHub with automatic publishing to GitHub Pages.

## Prerequisites

- A GitHub repository for this charts directory (e.g., `prysmsh/charts`)
- Write access to the repository

## Setup Steps

### 1. Create GitHub Repository

```bash
# Option A: Create a new repository on GitHub
# - Go to https://github.com/new
# - Repository name: charts
# - Description: Helm charts for Prysm components
# - Public or Private (your choice)
# - Don't initialize with README (we already have one)

# Option B: Use GitHub CLI
gh repo create prysmsh/charts --public --description "Helm charts for Prysm components"
```

### 2. Push Your Charts to GitHub

```bash
cd /home/alessio/code/prysm/prysm/prysm-charts

# Initialize git if not already done
git init
git add .
git commit -m "Initial commit: Prysm Helm charts"

# Add remote and push
git remote add origin https://github.com/prysmsh/charts.git
git branch -M main
git push -u origin main
```

### 3. Enable GitHub Pages

1. Go to your repository on GitHub: `https://github.com/prysmsh/charts`
2. Click **Settings** > **Pages** (in the left sidebar)
3. Under **Build and deployment**:
   - **Source**: Select "GitHub Actions"
   - This allows the workflow to deploy to Pages
4. Save the configuration

### 4. Configure Repository Permissions

The workflow needs specific permissions:

1. Go to **Settings** > **Actions** > **General**
2. Scroll to **Workflow permissions**
3. Select **Read and write permissions**
4. Check **Allow GitHub Actions to create and approve pull requests**
5. Click **Save**

### 5. Trigger the Workflow

```bash
# Make a small change to trigger the workflow
cd charts/agent
# Bump the version in Chart.yaml
# Then commit and push
git add Chart.yaml
git commit -m "Release agent chart v0.1.2"
git push
```

### 6. Verify Deployment

1. Go to **Actions** tab in your repository
2. Watch the "Release Helm Charts" workflow run
3. Once complete, your charts will be available at:
   ```
   https://prysmsh.github.io/charts
   ```

## Using Your Helm Repository

Once published, users can add your repository:

```bash
# Add the repository
helm repo add prysm https://prysmsh.github.io/charts

# Update repositories
helm repo update

# Search for charts
helm search repo prysm

# Install a chart
helm install my-prysm-agent prysm/agent --namespace prysm-system --create-namespace
```

## Repository Structure

After the first successful workflow run, your repository will have:

```
main branch:
├── charts/
│   └── agent/
│       ├── Chart.yaml
│       ├── values.yaml
│       └── templates/
└── .github/
    └── workflows/
        └── release-charts.yml

gh-pages branch (auto-created):
├── index.yaml                    # Helm repository index
└── agent-0.1.1.tgz              # Packaged chart
```

## Releasing New Chart Versions

1. Update `version` in `charts/agent/Chart.yaml`
2. Update `appVersion` if the application version changed
3. Update `CHANGELOG.md` with changes
4. Commit and push to `main` branch
5. The GitHub Action will automatically:
   - Package the chart
   - Create a GitHub Release with the chart tarball
   - Update the `gh-pages` branch with the new chart
   - Update `index.yaml`

## Troubleshooting

### Workflow fails with "403 Forbidden"

- Check that you enabled "Read and write permissions" in workflow settings

### Charts not appearing on GitHub Pages

- Ensure GitHub Pages is enabled and set to "GitHub Actions" source
- Check the Actions tab for any workflow errors
- Verify the repository is public (or you have GitHub Pro/Team for private Pages)

### Chart version already exists

- The workflow skips existing chart versions
- Bump the version in Chart.yaml before pushing

## Manual Publishing (Alternative)

If you prefer manual control, you can disable the GitHub Action and publish manually:

```bash
# Package the chart
helm package charts/agent -d dist/

# Generate/update the index
helm repo index dist/ --url https://prysmsh.github.io/charts

# Commit to gh-pages branch
git checkout gh-pages
cp -r dist/* .
git add index.yaml *.tgz
git commit -m "Release agent chart v0.1.2"
git push origin gh-pages
```

## Chart Repository URL

Your public chart repository URL will be:
```
https://prysmsh.github.io/charts
```

Or for a different organization:
```
https://YOUR-ORG.github.io/charts
```

Update the URLs in README.md and documentation accordingly.
