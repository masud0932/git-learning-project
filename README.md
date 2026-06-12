# Important DevOps Git Concepts

This document explains the most important Git concepts for DevOps engineers with simple examples. These concepts are useful for CI/CD, GitOps, infrastructure repositories, and team collaboration.

---

## 1. Branching

### Purpose

Branching allows developers and DevOps engineers to work on new features, fixes, or infrastructure changes without directly changing the main production branch.

### Example

```bash
git checkout -b feature/update-pipeline
```

or modern syntax:

```bash
git switch -c feature/update-pipeline
```

This creates a new branch for pipeline changes.

### DevOps Use Case

A DevOps engineer can update a GitHub Actions workflow, Terraform file, or Kubernetes manifest in a separate branch before merging it into `main`.

```text
main branch
   |
   |--- feature/update-pipeline
```

After testing and review, the branch can be merged into `main`.

---

## 2. Pull Requests

### Purpose

A Pull Request, also called PR, is used to review and discuss changes before merging them into the main branch.

### Example Workflow

```bash
git checkout -b feature/add-docker-build
git add .
git commit -m "add docker build step"
git push origin feature/add-docker-build
```

Then open a Pull Request on GitHub.

### DevOps Use Case

Before changing CI/CD pipelines, Kubernetes manifests, or Terraform code, teams usually create a PR so other team members can review the changes.

### Benefits

- Prevents direct changes to production branch
- Enables code review
- Triggers automated tests
- Improves collaboration

---

## 3. Merge Conflicts

### Purpose

A merge conflict happens when Git cannot automatically combine changes from two branches because the same lines were modified differently.

### Example

Local file:

```text
Docker image: app:v1
```

Remote file:

```text
Docker image: app:v2
```

When pulling or merging, Git may show:

```text
<<<<<<< HEAD
Docker image: app:v1
=======
Docker image: app:v2
>>>>>>> origin/main
```

### How to Fix

Edit the file manually:

```text
Docker image: app:v2
```

Then run:

```bash
git add .
git commit -m "resolve merge conflict"
```

### DevOps Use Case

Merge conflicts often happen in Kubernetes YAML files, Terraform files, GitHub Actions workflows, and Docker Compose files.

---

## 4. GitOps

### Purpose

GitOps means using Git as the single source of truth for infrastructure and application deployment.

In GitOps, infrastructure and deployment configuration are stored in Git. Tools like ArgoCD or Flux continuously sync the desired state from Git to Kubernetes.

### Example

A Kubernetes deployment file is stored in Git:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: java-app
spec:
  replicas: 2
```

When this file changes in Git, ArgoCD can automatically apply the change to the Kubernetes cluster.

### DevOps Use Case

Instead of manually running:

```bash
kubectl apply -f deployment.yaml
```

the DevOps workflow becomes:

```bash
git add deployment.yaml
git commit -m "update application replicas"
git push
```

Then ArgoCD syncs the change to the cluster.

### Benefits

- Version-controlled infrastructure
- Easy rollback
- Better audit history
- Automated deployment
- Consistent environments

---

## 5. CI/CD Trigger

### Purpose

Git events can automatically trigger CI/CD pipelines.

Common GitHub Actions triggers:

```yaml
on:
  push:
    branches:
      - main

  pull_request:
    branches:
      - main

  workflow_dispatch:
```

### Meaning

| Trigger | Purpose |
|---|---|
| `push` | Runs pipeline when code is pushed |
| `pull_request` | Runs checks before merging |
| `workflow_dispatch` | Allows manual pipeline execution |

### DevOps Use Case

When a DevOps engineer pushes a change to the GitHub Actions workflow, the pipeline can automatically:

1. Build the application
2. Run tests
3. Build Docker image
4. Push image to DockerHub
5. Deploy to AWS EC2 or Kubernetes

---

## 6. Version Control

### Purpose

Version control tracks every change made to the project over time.

### Example

```bash
git log --oneline
```

Output:

```text
a1b2c3d update docker deployment
e4f5g6h add github actions pipeline
i7j8k9l initial commit
```

### DevOps Use Case

Version control helps DevOps engineers understand who changed the pipeline, when infrastructure was modified, which commit caused a deployment issue, and which version was deployed to production.

### Useful Commands

```bash
git log --oneline
git show <commit-id>
git diff
git blame filename
```

---

## 7. Rollback

### Purpose

Rollback means returning the application or infrastructure to a previous stable version.

### Safe Rollback with `git revert`

```bash
git revert <commit-id>
git push
```

This creates a new commit that undoes the bad commit.

### DevOps Use Case

If a bad pipeline or deployment change breaks production, a DevOps engineer can revert the commit and trigger the pipeline again.

### Example

```bash
git log --oneline
```

Output:

```text
abc1234 bad deployment change
def5678 stable deployment
```

Rollback:

```bash
git revert abc1234
git push origin main
```

### Why `git revert` is Safer

`git revert` does not delete commit history. It keeps the history clean and safe for team collaboration.

---

## Summary Table

| Concept | Purpose | DevOps Example |
|---|---|---|
| Branching | Isolate work | Create branch for pipeline updates |
| Pull Requests | Review changes | Review Terraform or Kubernetes changes |
| Merge Conflicts | Resolve overlapping changes | Fix conflicts in YAML files |
| GitOps | Manage infrastructure from Git | ArgoCD syncs Kubernetes manifests |
| CI/CD Trigger | Start automation from Git events | Push triggers build and deploy |
| Version Control | Track project history | Find who changed pipeline config |
| Rollback | Return to stable state | Revert bad deployment commit |

---

## Recommended DevOps Git Workflow

```bash
git pull origin main
git checkout -b feature/update-ci-pipeline
git add .
git commit -m "update CI/CD pipeline"
git push origin feature/update-ci-pipeline
```

Then:

1. Open a Pull Request
2. Wait for CI checks
3. Review changes
4. Merge into `main`
5. Pipeline deploys automatically

---

## Final Note

For DevOps engineers, Git is not only used for source code. It is also used for CI/CD pipelines, Docker files, Kubernetes manifests, Terraform infrastructure, Helm charts, and GitOps deployment workflows.
