# Website

Argocd will deploy this website when you add an application to apps/localdev that points to the overlays/localdev folder. Because there is a kustomization.yaml file in the overlays/localdev folder, argocd will apply the configuration using kustomize (kubectl apply -k .)

## demo-website.yaml

Below is an application that instructs argocd to deploy the website in the demo namespace. Save the yaml in a file demo-website.yaml and put it in apps/localdev. After a git commit and git push the website will be deployed by argocd.

Make sure to replace the repoUrl with your repository - replace <YOUR_NAME_HERE>

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: website     # name in argocd
  namespace: argocd
  finalizers:
    - resources-finalizer.argocd.argoproj.io
spec:
  project: default
  source:
    repoURL: 'https://github.com/<YOUR_NAME_HERE>/rancher-desktop-playground'
    path: website/overlays/localdev # path to overlay with kustomization.yaml
    targetRevision: HEAD
  destination:
    server: 'https://kubernetes.default.svc'
    namespace: demo  # namespace to create the website resources in
  syncPolicy:
    automated: 
      prune: true
      selfHeal: true
    syncOptions:
      - CreateNamespace=true
```

## base

| file | description |
|------|-------------|
| deployment.yaml | run nginx image as container and mount configmap with index.html |
| index.html | custom landing page of the website |
| kustomization.yaml | tells kustomize to run deployment.yaml and service.yaml <br/> adds configmap containing index.html |
| service.yaml | service that links to running nginx pod |

## overlays/localdev

| file | description |
|------|-------------|
| ingress.yaml | ingress for localdev so website is available outside of cluster |
| kustomization.yaml | tells kustomize to run the base kustomization and ingress.yaml </br> also specifies the image tag (version) to run |

## Remove

To remove the website you can just remove the demo-website.yaml file from the apps/localdev folder. Argocd will notice the application is removed from git and the *resources-finalizer.argocd.argoproj.io* will remove the resources from your cluster.

During the auto-sync step the resources are removed. An exception to this is when with the removal of the website there are no applications left in the folder - the option [allow-empty](https://argo-cd.readthedocs.io/en/stable/user-guide/auto_sync/#automatic-pruning-with-allow-empty-v18) prevents the removal of all applications. You can force this by running sync with the prune option checked.