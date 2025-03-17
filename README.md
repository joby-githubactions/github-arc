# GitOps Approach for Managing GitHub Runners Using ArgoCD (ARC)
A GitOps approach uses Git as a source of truth for managing Kubernetes resources and automating the continuous deployment process. In this setup, you would define all your infrastructure as code in Git repositories, and tools like ArgoCD (ARC) would automatically deploy and manage your resources based on the Git state.

In this context, you want to use GitHub Actions Runners on Kubernetes and manage them using ArgoCD. Below is an overview of the GitOps process using ArgoCD (ARC) to deploy and manage GitHub runners.

### Steps

1. **Create the `nextgen-application.yaml` file**:
   Create a new YAML file named `nextgen-application.yaml` with the content you provided:

   ```yaml
   apiVersion: argoproj.io/v1alpha1
   kind: Application
   metadata:
     name: nextgen-application
     namespace: argocd
   spec:
     destination:
       namespace: argocd
       server: https://kubernetes.default.svc
     source:
       path: argocd
       repoURL: https://github.com/joby-githubactions/github-arc.git
       targetRevision: HEAD
       directory:
         recurse: true
     sources: []
     project: default
     syncPolicy:
       automated:
         prune: true
         selfHeal: true
       syncOptions:
         - CreateNamespace=true
   ```

2. **Apply the Application YAML**:
   Run the following `kubectl` command to apply the `nextgen-application.yaml` to your cluster. This will create the application in ArgoCD, and ArgoCD will automatically sync and deploy the resources defined in the repository (`https://github.com/joby-githubactions/github-arc.git`).

   ```bash
   kubectl apply -f nextgen-application.yaml
   ```

3. **Verify in ArgoCD**:
   After applying the `Application` manifest, ArgoCD will begin the process of automatically creating resources. You can check the status of the application in the ArgoCD UI or via the command line:

   ```bash
   argocd app list
   ```

   You should see `nextgen-application` in the list, and ArgoCD will handle syncing and applying the resources.

### Summary

- By applying the `nextgen-application.yaml` file, ArgoCD will automatically pull the repository, process the resources in the `argocd` folder, and sync the configuration.
- The `syncPolicy` ensures that the resources are automatically synced, and any discrepancies will be fixed (self-healing). It also prunes old resources if needed.
- The `CreateNamespace=true` option ensures that the necessary namespaces (if any) are created automatically during the deployment.

This setup should be all you need to get everything up and running.
