# app-of-apps
# ArgoCD Setup Guide

This guide provides instructions for installing the ArgoCD CLI, setting up ArgoCD on your Kubernetes cluster, and creating an application using ArgoCD.

## Install ArgoCD CLI with PowerShell

To install the ArgoCD CLI on Windows, run the following PowerShell commands:

```powershell
$version = (Invoke-RestMethod https://api.github.com/repos/argoproj/argo-cd/releases/latest).tag_name

$url = "https://github.com/argoproj/argo-cd/releases/download/" + $version + "/argocd-windows-amd64.exe"
$output = "argocd.exe"
Invoke-WebRequest -Uri $url -OutFile $output

Environment::SetEnvironmentVariable("Path", "$env:Path;C:\Path\To\ArgoCD-CLI", "User")
```

More info here: https://argo-cd.readthedocs.io/en/stable/cli_installation/

## ArgoCD Setup

Create namespace where ArgoCD will be installed:
```
kubectl create namespace argocd
```

Install ArgoCD:
```
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

Open another terminal and port-forward to have access to the API server:
```
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

Retrieve the initial password for the admin user:
```
argocd admin initial-password -n argocd
```

Copy the password, and now login:
```
argocd login localhost:8080
```

That will ask first if you want to proceed insecurely, press y.
Then it will ask for the user, it's admin
And then, the previously retrieved password.

Now that we're logged in, we can create our app.

## ArgoCD Application

First, we need to add our repository.
```
argocd repo add https://github.com/southworks/tech-titans --username <User_github> --password <PAT_github>
```

Create the app:
```
argocd app create app --repo https://github.com/southworks/tech-titans --path pocs/azure-isolated-subscription/argocd --dest-server https://kubernetes.default.svc --dest-namespace argocd
```

