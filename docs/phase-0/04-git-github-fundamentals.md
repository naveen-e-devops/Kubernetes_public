# Phase 0 — Topic 4: Git and GitHub Fundamentals

**Level:** Beginner · **Format:** Hands-on · **Assignment:** 0.4

## Learning objectives

By the end of this topic, you should be able to:
- Explain Git vs. GitHub.
- Initialize a repository and understand the working directory, staging area, commits, and remote.
- Stage, commit, inspect history, push, and pull changes.
- Create branches and merge them.
- Resolve a basic merge conflict.
- Use `.gitignore` appropriately.
- Explain how Git supports GitOps.

## 1. Git vs. GitHub

**Git** is a distributed version control system that records changes to files over time. It runs locally and supports commits, branches, merges, and history.

**GitHub** is a hosting and collaboration platform for Git repositories. It adds remote hosting, pull requests, reviews, and automation. GitLab and Bitbucket provide similar hosting capabilities.

## 2. The Git workflow

```text
Working directory
      |
      | git add
      v
Staging area
      |
      | git commit
      v
Local repository
      |
      | git push
      v
Remote repository
```

Useful commands:

| Command | Purpose |
|---|---|
| `git status` | Inspect current changes |
| `git diff` | Show unstaged changes |
| `git diff --staged` | Show staged changes |
| `git add <file>` | Stage changes |
| `git commit -m "message"` | Create a local commit |
| `git log --oneline` | Show commit history |
| `git show` | Inspect a commit |
| `git branch` | List branches |
| `git switch -c <branch>` | Create and switch to a branch |
| `git switch main` | Switch to main |
| `git merge <branch>` | Merge a branch into the current branch |
| `git pull` | Fetch and integrate remote changes |
| `git push` | Send commits to a remote |

## 3. Configure Git

Check installation:

```bash
git --version
```

Configure the author details used in commits:

```bash
git config --global user.name "Your Name"
git config --global user.email "your-email@example.com"
git config --global --list
```

Use an email associated with GitHub or a GitHub-provided noreply email if desired.

## 4. Create a local lab repository

```bash
mkdir -p ~/kubernetes-learning/git-lab
cd ~/kubernetes-learning/git-lab
git init
git branch -M main
```

Create a README:

```bash
cat > README.md <<'EOF'
# Kubernetes Learning Lab

This repository is for practicing Git fundamentals.

## Topics
- Linux fundamentals
- Networking fundamentals
- YAML and JSON
- Git and GitHub
EOF
```

Stage and commit:

```bash
git status
git add README.md
git status
git commit -m "Add initial learning lab README"
git log --oneline
```

## 5. Branches and merges

Create a feature branch:

```bash
git switch -c feature/add-topics
```

Add content and commit it:

```bash
cat >> README.md <<'EOF'

## Git practice
- Created a local repository
- Practiced branches and commits
EOF

git add README.md
git commit -m "Add Git practice topics"
git log --oneline --all --graph
```

Switch to main and merge:

```bash
git switch main
git merge feature/add-topics
cat README.md
```

A branch isolates development work. Merging brings its changes into the current branch.

## 6. Merge conflict exercise

A conflict happens when Git cannot automatically combine overlapping changes.

1. On `main`, change the README title to `# Kubernetes Learning Repository` and commit it.
2. Create `feature/change-title` from main and change the title to `# Kubernetes DevOps Lab`; commit it.
3. Return to main, change the same title to `# Kubernetes Platform Engineering`, and commit it.
4. Run `git merge feature/change-title`. Git should report a conflict.

Conflict markers look like:

```text
<<<<<<< HEAD
# Kubernetes Platform Engineering
=======
# Kubernetes DevOps Lab
>>>>>>> feature/change-title
```

Edit the file to keep the intended final content and remove all conflict markers. Then:

```bash
git add README.md
git commit -m "Resolve README title conflict"
git status
git log --oneline --graph --all
```

In real projects, agree on the intended content with the relevant contributor before resolving conflicting changes.

## 7. Push to GitHub

Create a new **empty** GitHub repository named `kubernetes-git-lab` (do not initialize it with a README). Then connect it:

```bash
git remote add origin https://github.com/YOUR_USERNAME/kubernetes-git-lab.git
git remote -v
git push -u origin main
```

Replace `YOUR_USERNAME` with your GitHub username. Authenticate with a supported method such as GitHub CLI, Git Credential Manager, or SSH. GitHub does not accept your regular account password for Git-over-HTTPS authentication.

## 8. Use .gitignore

`.gitignore` specifies patterns for untracked files Git should ignore.

```bash
cat > .gitignore <<'EOF'
*.log
.env
__pycache__/
*.pyc
.terraform/
*.tfstate
*.tfstate.*
EOF

git add .gitignore
git commit -m "Add Git ignore rules"
git push
```

Important:
- `.gitignore` does not remove files already tracked by Git.
- Never commit passwords, tokens, access keys, or other secrets. If a secret is committed, treat it as exposed and rotate it.
- In Terraform projects, generally commit `.terraform.lock.hcl`; don't ignore it by default.

## 9. Git and GitOps

A typical GitOps workflow looks like:

```text
Developer changes YAML / Terraform
             |
             v
       Git commit / branch
             |
             v
        Pull request
             |
             v
      CI validation and review
             |
             v
     Argo CD reconciles Git state
             |
             v
        Kubernetes cluster
```

Git records the desired configuration and its history. Argo CD can compare that configuration with the live cluster and reconcile differences according to its settings. This will be implemented in a later phase.

## Assignment 0.4 checklist

- [ ] Install Git and configure your name and email.
- [ ] Create a local repository and make an initial commit.
- [ ] Use `git status`, `git diff`, `git log`, and `git show`.
- [ ] Create a feature branch and commit a change.
- [ ] Merge a feature branch into main.
- [ ] Reproduce and resolve a merge conflict.
- [ ] Create a GitHub repository and push the local main branch.
- [ ] Create a `.gitignore` file and explain what it does.
- [ ] Explain working directory vs. staging area vs. commit vs. remote.
- [ ] Explain how Git supports GitOps.

## Knowledge check

1. Which command saves staged changes as a local commit?
2. What is the purpose of a feature branch?
3. What does `git push` do?
4. What does `.gitignore` do, and what does it not do?
5. What causes a merge conflict?

**Completion gate:** Complete the checklist and be able to explain your workflow. Then continue to [Topic 5 — DNS, HTTP and HTTPS](05-dns-http-https.md).
