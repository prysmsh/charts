Using the Repository

Once deployed, users can add the repository:

```bash
helm repo add prysm https://prysmsh.github.io/charts
helm repo update
helm search repo prysm
helm install prysm-agent prysm/agent
```
