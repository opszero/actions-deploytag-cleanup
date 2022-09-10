# actions-deploytag-cleanup


# Prune Environments in Helm Scaffold

```python
#!/usr/bin/env python3

import subprocess

subprocess.getoutput("git remote update")

deployed_branches = subprocess.getoutput("helm list -a -A 2>&1 | grep feature-deploy | awk '{print $2}'").split("\n")
active_branches = subprocess.getoutput("git branch -r | grep feature_deploy | sed -e 's|^  origin/||g' | sed -e 's/[^A-Za-z0-9]/-/g'").split("\n")

APP = "appname"

for branch in deployed_branches:
    if branch not in active_branches:
        subprocess.run(f"KUBECONFIG=./kubeconfig helm delete --wait -n {branch} {APP}", shell=True)
        subprocess.run(f"KUBECONFIG=./kubeconfig kubectl delete namespace -n {branch}", shell=True)
