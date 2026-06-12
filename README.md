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


# Important DevOps Commands

## 1. `git clone`

### Purpose

`git clone` downloads a remote GitHub repository to your local machine.

### Example

```bash
git clone git@github.com:masud0932/git-learning-project.git
cd git-learning-project
```

### In DevOps

DevOps engineers clone application, infrastructure, or GitOps repositories before making changes.

---

## 2. `git status`

### Purpose

Shows the current state of your working directory.

### Example

```bash
git status
```

### Example Output

```text
modified: README.md
```

### In DevOps

Before committing pipeline, Terraform, or Kubernetes changes, always check what files were modified.

---

## 3. `git add`

### Purpose

Stages changed files before commit.

### Example

```bash
git add README.md
```

or stage all changes:

```bash
git add .
```

### In DevOps

Used after modifying files such as:

```text
.github/workflows/ci-cd.yml
Dockerfile
deployment.yaml
main.tf
README.md
```

---

## 4. `git commit`

### Purpose

Saves staged changes in local Git history.

### Example

```bash
git commit -m "add git command documentation"
```

### In DevOps

Each commit should clearly describe what changed.

Good examples:

```bash
git commit -m "update github actions pipeline"
git commit -m "fix docker image tag"
git commit -m "update kubernetes deployment replicas"
```

---

## 5. `git push`

### Purpose

Uploads local commits to GitHub.

### Example

```bash
git push origin main
```

### In DevOps

A push often triggers CI/CD pipelines.

```text
git push
   ↓
GitHub Actions starts
   ↓
Build → Test → Docker Build → Deploy
```

---

## 6. `git pull`

### Purpose

Downloads remote changes and merges them into your local branch.

### Example

```bash
git pull origin main
```

### In DevOps

Use this before starting work to make sure your local repository is updated.

### Important

`git pull` is equal to:

```text
git fetch + git merge
```

---

## 7. `git fetch`

### Purpose

Downloads remote changes without modifying your local files.

### Example

```bash
git fetch origin
```

### Check differences after fetch

```bash
git diff main origin/main
```

### In DevOps

Useful when you want to safely check remote updates before merging them.

---

## 8. `git merge`

### Purpose

Merges another branch or remote branch into your current branch.

### Example

```bash
git merge origin/main
```

### In DevOps

Used when you fetched changes first and then want to apply them manually.

---

## 9. `git branch`

### Purpose

Lists local branches.

### Example

```bash
git branch
```

### Output

```text
* main
  feature/update-pipeline
```

### In DevOps

Used to check which branch you are currently working on.

---

## 10. `git checkout -b`

### Purpose

Creates a new branch and switches to it.

### Example

```bash
git checkout -b feature/update-readme
```

Modern alternative:

```bash
git switch -c feature/update-readme
```

### In DevOps

Create a separate branch before changing CI/CD, Docker, Kubernetes, or Terraform files.

---

## 11. `git checkout` / `git switch`

### Purpose

Switches from one branch to another.

### Example

```bash
git checkout main
```

or:

```bash
git switch main
```

### In DevOps

Used when moving between feature branches, hotfix branches, and main branch.

---

## 12. `git log --oneline`

### Purpose

Shows commit history in short format.

### Example

```bash
git log --oneline
```

### Output

```text
a1b2c3d update deployment script
e4f5g6h add dockerfile
i7j8k9l initial commit
```

### In DevOps

Useful for finding which commit introduced a problem.

---

## 13. `git diff`

### Purpose

Shows file differences before committing.

### Example

```bash
git diff
```

### In DevOps

Before committing infrastructure or deployment changes, use `git diff` to verify the exact changes.

Example:

```bash
git diff .github/workflows/ci-cd.yml
```

---

## 14. `git stash`

### Purpose

Temporarily saves uncommitted local changes.

### Example

```bash
git stash
```

### In DevOps

Useful when you have unfinished local work but need to quickly pull remote updates.

### Workflow Example

```bash
git stash
git pull origin main
git stash pop
```

---

## 15. `git stash pop`

### Purpose

Restores previously stashed changes.

### Example

```bash
git stash pop
```

### In DevOps

After pulling the latest changes, you can restore your unfinished work.

---

## 16. `git reset --hard HEAD`

### Purpose

Discards all local uncommitted changes.

### Example

```bash
git reset --hard HEAD
```

### Warning

This permanently removes local changes that are not committed.

### In DevOps

Useful when your local workspace is broken and you want to return to the last committed state.

---

## 17. `git clean -fd`

### Purpose

Removes untracked files and directories.

### Example

```bash
git clean -fd
```

### In DevOps

Useful for cleaning generated files before a fresh build.

### Warning

This deletes untracked files permanently.

---

## 18. `git revert`

### Purpose

Safely undoes a previous commit by creating a new commit.

### Example

```bash
git revert <commit-id>
git push origin main
```

### In DevOps

Very important for rollback.

Example:

```text
Bad deployment commit pushed
        ↓
Application breaks
        ↓
git revert bad-commit-id
        ↓
Push again
        ↓
Pipeline redeploys stable state
```

### Why It Is Safe

It does not delete history. It adds a new commit that reverses the bad change.

---

## 19. `git rebase`

### Purpose

Reapplies your commits on top of another branch.

### Example

```bash
git pull --rebase origin main
```

### In DevOps

Used to keep a clean commit history before merging a feature branch.

### Simple Meaning

```text
Move my local commits on top of latest main branch
```

---

## 20. `git cherry-pick`

### Purpose

Applies a specific commit from another branch.

### Example

```bash
git cherry-pick <commit-id>
```

### In DevOps

Useful when a hotfix from one branch needs to be applied to another branch.

Example:

```text
hotfix branch has important fix
        ↓
Apply same fix to main
        ↓
git cherry-pick <commit-id>
```

---

## 21. `git tag`

### Purpose

Creates a version label for a specific commit.

### Example

```bash
git tag v1.0.0
git push origin v1.0.0
```

### In DevOps

Tags are commonly used for releases.

Example:

```text
v1.0.0
v1.1.0
v2.0.0
```

A CI/CD pipeline can build and deploy based on tags.

---

## 22. `git remote -v`

### Purpose

Shows connected remote repositories.

### Example

```bash
git remote -v
```

### Output

```text
origin git@github.com:masud0932/git-learning-project.git (fetch)
origin git@github.com:masud0932/git-learning-project.git (push)
```

### In DevOps

Used to verify that your local project is connected to the correct GitHub repository.

---

## 23. `git remote add origin`

### Purpose

Connects a local repository to a remote GitHub repository.

### Example

```bash
git remote add origin git@github.com:masud0932/git-learning-project.git
```

### In DevOps

Used when creating a new local project and pushing it to GitHub for the first time.

---

## 24. `git blame`

### Purpose

Shows who last changed each line of a file.

### Example

```bash
git blame README.md
```

### In DevOps

Useful for investigating who changed a pipeline, Terraform file, or Kubernetes manifest.

---

## 25. `git show`

### Purpose

Shows details of a specific commit.

### Example

```bash
git show <commit-id>
```

### In DevOps

Useful during troubleshooting to understand exactly what changed in a commit.

---

# Daily DevOps Git Workflow

This is a common workflow for DevOps engineers:

```bash
git pull origin main
git checkout -b feature/update-ci-pipeline
# edit files
git status
git diff
git add .
git commit -m "update CI/CD pipeline"
git push origin feature/update-ci-pipeline
```

Then open a Pull Request on GitHub.

---

## Example: Updating a GitHub Actions Pipeline

### Step 1: Update local repository

```bash
git pull origin main
```

### Step 2: Create new branch

```bash
git checkout -b feature/update-github-actions
```

### Step 3: Edit workflow file

```text
.github/workflows/ci-cd.yml
```

### Step 4: Check changes

```bash
git status
git diff
```

### Step 5: Commit changes

```bash
git add .github/workflows/ci-cd.yml
git commit -m "update github actions workflow"
```

### Step 6: Push branch

```bash
git push origin feature/update-github-actions
```

### Step 7: Open Pull Request

Open GitHub and create a Pull Request into `main`.

---

## Example: Rollback a Bad Deployment

### Step 1: Check commit history

```bash
git log --oneline
```

### Step 2: Find bad commit

```text
abc1234 bad docker image tag
def5678 working deployment
```

### Step 3: Revert bad commit

```bash
git revert abc1234
```

### Step 4: Push rollback

```bash
git push origin main
```

### Result

The CI/CD pipeline runs again and deploys the corrected state.

---

## Summary Table

| Command | Purpose | DevOps Use Case |
|---|---|---|
| `git clone` | Download repository | Clone app/infrastructure repo |
| `git status` | Check file state | Verify modified files |
| `git add` | Stage files | Prepare changes for commit |
| `git commit` | Save local snapshot | Track pipeline or infra change |
| `git push` | Upload commits | Trigger CI/CD pipeline |
| `git pull` | Fetch and merge | Sync latest remote changes |
| `git fetch` | Download only | Inspect remote changes safely |
| `git merge` | Combine changes | Merge remote branch manually |
| `git branch` | List branches | Check current branch |
| `git checkout -b` | Create branch | Start feature/hotfix work |
| `git log --oneline` | View history | Debug deployment issue |
| `git diff` | Compare changes | Review before commit |
| `git stash` | Save temporary changes | Pull latest without losing work |
| `git stash pop` | Restore stash | Continue unfinished work |
| `git reset --hard` | Discard changes | Recover clean workspace |
| `git clean -fd` | Remove untracked files | Clean build workspace |
| `git revert` | Safe rollback | Undo bad deployment commit |
| `git rebase` | Clean history | Update feature branch cleanly |
| `git cherry-pick` | Apply one commit | Move hotfix between branches |
| `git tag` | Mark release | Create versioned deployment |
| `git remote -v` | Show remote URL | Verify GitHub connection |
| `git blame` | Show line history | Investigate config change |
| `git show` | Show commit details | Review exact commit changes |

---
