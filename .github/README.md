# Setting up the prysmsh/charts Repository

## Files to add to the prysmsh/charts repository

### 1. Create `.github/workflows/release-charts.yml`

Copy the content from `release-charts.yml` in this directory to `.github/workflows/release-charts.yml` in the charts repository.

### 2. Enable GitHub Pages

1. Go to https://github.com/prysmsh/charts/settings/pages
2. Under "Build and deployment":
   - Source: **GitHub Actions**
3. Save changes

### 3. Repository Structure

The charts repository should have this structure:

```
prysmsh/charts/
├── .github/
│   └── workflows/
│       └── release-charts.yml
├── charts/
│   └── agent/
│       ├── Chart.yaml
│       ├── values.yaml
│       ├── templates/
│       └── README.md
└── README.md
```

### 4. Set up Custom Domain (Optional)

If you want to use `https://charts.prysm.sh` instead of `https://prysmsh.github.io/charts`:

1. Go to https://github.com/prysmsh/charts/settings/pages
2. Add custom domain: `charts.prysm.sh`
3. Create a CNAME record in your DNS:
   ```
   charts.prysm.sh  CNAME  prysmsh.github.io
   ```

### 5. Trigger the Workflow

After setting everything up:

```bash
cd /path/to/charts
git add .github/workflows/release-charts.yml
git commit -m "Add Helm chart release workflow"
git push origin main
```

The workflow will:
- Package all charts in the `charts/` directory
- Create GitHub releases with the packaged charts
- Generate `index.yaml`
- Deploy to GitHub Pages

### 6. Using the Repository

Once deployed, users can add the repository:

```bash
# With default GitHub Pages URL
helm repo add prysm https://prysmsh.github.io/charts

# Or with custom domain (after DNS setup)
helm repo add prysm https://charts.prysm.sh

helm repo update
helm search repo prysm
helm install prysm-agent prysm/agent
```

## Troubleshooting

### Workflow fails with permissions error

Make sure the repository has the correct permissions:
1. Go to Settings → Actions → General
2. Under "Workflow permissions", select "Read and write permissions"
3. Save

### Charts not appearing

1. Check the Actions tab for workflow runs
2. Verify the `charts/` directory structure
3. Ensure Chart.yaml has proper version numbers
4. Check that GitHub Pages is enabled with "GitHub Actions" as source
