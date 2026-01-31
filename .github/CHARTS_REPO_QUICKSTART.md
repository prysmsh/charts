# Quick Start: GitHub Helm Chart Repository

## Option 1: Automated Setup (Recommended)

```bash
cd /home/alessio/code/prysm/prysm/prysm-charts
./setup-github-repo.sh
```

Follow the prompts to configure and push to GitHub.

## Option 2: Manual Setup

### Step 1: Create GitHub Repository

Using GitHub CLI:
```bash
gh repo create prysmsh/charts --public --description "Helm charts for Prysm"
```

Or via web: https://github.com/new

### Step 2: Initialize and Push

```bash
cd /home/alessio/code/prysm/prysm/prysm-charts

# Initialize git (if not already done)
git init
git branch -M main

# Add remote
git remote add origin https://github.com/prysmsh/charts.git

# Commit and push
git add .
git commit -m "Initial commit: Prysm Helm charts"
git push -u origin main
```

### Step 3: Enable GitHub Pages

1. Go to: https://github.com/prysmsh/charts/settings/pages
2. Under **Build and deployment**:
   - Source: **GitHub Actions**
3. Save

### Step 4: Configure Workflow Permissions

1. Go to: https://github.com/prysmsh/charts/settings/actions
2. Under **Workflow permissions**:
   - Select: **Read and write permissions**
   - Check: **Allow GitHub Actions to create and approve pull requests**
3. Save

### Step 5: Trigger First Release

```bash
# Make a small change to trigger workflow
cd charts/agent
# Edit Chart.yaml to bump version to 0.1.2

git add Chart.yaml
git commit -m "Release agent v0.1.2"
git push
```

Watch the workflow at: https://github.com/prysmsh/charts/actions

## Using Your Published Charts

Once published (usually within 2-3 minutes):

```bash
# Add your repository
helm repo add prysm https://prysmsh.github.io/charts

# Update
helm repo update

# Install
helm install prysm-agent prysm/agent \
  --namespace prysm-system \
  --create-namespace \
  --set configSecret.data.AGENT_TOKEN="your-token"
```

## Troubleshooting

### Workflow Permission Error

**Error:** "Resource not accessible by integration"

**Fix:**
1. Go to repository Settings > Actions > General
2. Under "Workflow permissions", select "Read and write permissions"
3. Click Save

### GitHub Pages Not Found

**Error:** 404 when accessing `https://prysmsh.github.io/charts`

**Fix:**
1. Ensure Pages is enabled: Settings > Pages
2. Source must be "GitHub Actions"
3. Check Actions tab for any failed workflows
4. Repository must be public (or GitHub Pro/Team for private repos)

### Chart Version Already Exists

**Error:** "Chart version already released"

**Fix:**
- The workflow skips duplicate versions
- Bump the version in `charts/agent/Chart.yaml`
- Commit and push the version change

## Repository URLs

- **GitHub Repo:** `https://github.com/prysmsh/charts`
- **Helm Repository:** `https://prysmsh.github.io/charts`
- **Raw Chart URL:** `https://prysmsh.github.io/charts/agent-{version}.tgz`
- **Index:** `https://prysmsh.github.io/charts/index.yaml`

## Release Process

Every push to `main` that modifies files under `charts/` triggers:

1. **Chart Packaging** - Helm packages the chart
2. **GitHub Release** - Creates a release with the `.tgz` file
3. **Index Update** - Updates `index.yaml` with new chart version
4. **Pages Deploy** - Publishes to GitHub Pages

Charts with unchanged versions are skipped automatically.

## Testing Before Release

Always test locally before pushing:

```bash
# Lint the chart
helm lint charts/agent

# Test rendering
helm template test charts/agent -f charts/agent/examples/values-production.yaml

# Dry run
helm install test charts/agent --dry-run --debug

# Install to test cluster
helm install test charts/agent -n test --create-namespace
```

## Custom Domain (Optional)

To use a custom domain (e.g., `charts.prysm.sh`):

1. Create a `CNAME` file in repository root:
   ```
   charts.prysm.sh
   ```
2. Configure DNS:
   ```
   charts.prysm.sh CNAME prysmsh.github.io
   ```
3. In Pages settings, set custom domain to `charts.prysm.sh`
4. Update all URLs in documentation

## Need Help?

- Full setup guide: [SETUP.md](SETUP.md)
- Chart documentation: [../charts/agent/README.md](../charts/agent/README.md)
- GitHub Actions docs: https://docs.github.com/en/actions
- Helm chart-releaser: https://github.com/helm/chart-releaser-action
