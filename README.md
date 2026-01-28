# GitHub Actions — Test 1 Revision Notes (Short + Exam Ready)

> **How to use these notes:**
> 1. Read through each section once — focus on ✅ definitions and ⚠️ traps.
> 2. Attempt the 20 MCQs at the end without peeking.
> 3. Re-read any section where you got questions wrong.

---

## Table of Contents

1. [Workflow & Jobs](#1-workflow--jobs)
   - [Dependent Jobs](#11-dependent-jobs)
   - [Matrix Strategy](#12-matrix-strategy)
   - [Triggers (`on:` keyword)](#13-triggers-on-keyword)
   - [Filtering Event Types](#14-filtering-event-types)
2. [Action Types & Authoring](#2-action-types--authoring)
   - [Docker Container Actions](#21-docker-container-actions)
   - [Composite Actions](#22-composite-actions)
   - [JavaScript Actions](#23-javascript-actions)
   - [Action Metadata (`action.yml`)](#24-action-metadata-actionyml)
   - [Passing Data with `outputs`](#25-passing-data-with-outputs)
3. [Release & Versioning](#3-release--versioning)
   - [Release Strategy](#31-release-strategy)
   - [Version Pinning: SHA vs Tags](#32-version-pinning-sha-vs-tags)
4. [Variables & Environment](#4-variables--environment)
   - [Environment Variable Precedence](#41-environment-variable-precedence)
   - [Filesystem Variables](#42-filesystem-variables)
5. [Secrets & Security](#5-secrets--security)
   - [Using Secrets in Workflows](#51-using-secrets-in-workflows)
   - [Command-Line Secret Risks](#52-command-line-secret-risks)
6. [Runners](#6-runners)
   - [Custom Labels for Self-Hosted Runners](#61-custom-labels-for-self-hosted-runners)
   - [Proxy Configuration](#62-proxy-configuration)
   - [Mixing GitHub-Hosted & Self-Hosted Runners](#63-mixing-github-hosted--self-hosted-runners)
   - [Runner Groups](#64-runner-groups)
7. [Artifacts & Retention](#7-artifacts--retention)
8. [GitHub Marketplace & Publishing](#8-github-marketplace--publishing)
   - [Marketplace Requirements](#81-marketplace-requirements)
   - [Publishing a Release](#82-publishing-a-release)
9. [Scripts in Workflows](#9-scripts-in-workflows)
10. [GitHub Packages](#10-github-packages)
11. [Workflow Badges](#11-workflow-badges)
12. [Scoped Installation Tokens](#12-scoped-installation-tokens)
13. [Quick Self-Test (20 MCQs)](#13-quick-self-test-20-mcqs)

---

## 1. Workflow & Jobs

### 1.1 Dependent Jobs

✅ **Definition:**  
Dependent jobs use `needs:` to define **sequential execution order** — a job starts only after its dependency completes successfully.

🎯 **Why it matters:**  
Exam loves to ask what dependent jobs are **NOT** for.

🧩 **Exam pattern / trick:**

| ✅ Correct Purpose | ❌ NOT the Purpose |
|---|---|
| Define sequential execution order | Enforce conditional logic based on outcomes |
| Ensure Job B waits for Job A | Run jobs concurrently/independently |
| | Control workflow concurrency settings |

🧪 **Example:**
```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Building..."

  deploy:
    needs: build  # ← waits for 'build' to finish
    runs-on: ubuntu-latest
    steps:
      - run: echo "Deploying..."
```

> ⚠️ **Trap:** "Dependent jobs control concurrency" — FALSE. Concurrency is managed at workflow level via `concurrency:` key.

🔗 [Docs: Using jobs in a workflow](https://docs.github.com/en/actions/using-jobs/using-jobs-in-a-workflow)

---

### 1.2 Matrix Strategy

✅ **Definition:**  
Matrix creates multiple job runs by combining values from different axes.

🎯 **Why it matters:**  
Expect questions asking "how many jobs run?" — multiply the values.

🧩 **Formula:**  
`Total jobs = axis1_count × axis2_count × ...`

🧪 **Example:**
```yaml
jobs:
  example:
    strategy:
      matrix:
        animal: [cat, dog, bear]   # 3 values
        color: [black, brown]      # 2 values
    # Total jobs = 3 × 2 = 6
```

> 🧠 **Remember:** Each combination = 1 separate job execution.

🔗 [Docs: Using a matrix for your jobs](https://docs.github.com/en/actions/using-jobs/using-a-matrix-for-your-jobs#using-a-matrix-strategy)

---

### 1.3 Triggers (`on:` keyword)

✅ **Definition:**  
The `on:` keyword specifies events that trigger a workflow (push, pull_request, schedule, etc.).

🎯 **Why it matters:**  
Know the keyword name — it's `on:`, not `trigger:`, `event:`, or `workflow:`.

🧪 **Example:**
```yaml
on:
  push:
    branches: [main]
  pull_request:
  schedule:
    - cron: '0 0 * * *'
```

> ⚠️ **Trap:** `trigger:` and `event:` are NOT valid YAML keywords for this purpose.

🔗 [Docs: Events that trigger workflows](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows)

---

### 1.4 Filtering Event Types

✅ **Definition:**  
Use `types:` under an event to filter which activity types trigger the workflow.

🎯 **Why it matters:**  
Questions may ask how to **exclude** certain activities (e.g., exclude `deleted`).

🧪 **Example — Exclude deletions:**
```yaml
on:
  branch_protection_rule:
    types: [created, edited]  # ← 'deleted' excluded
```

> ⚠️ **Trap:** `notTypes:` does NOT exist. Only include what you want.

🔗 [Docs: Workflow syntax — types](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#onevent_nametypes)

---

## 2. Action Types & Authoring

### 2.1 Docker Container Actions

✅ **Definition:**  
Package an action inside a Docker container with specific OS, dependencies, and tools.

🎯 **Why it matters:**  
Best choice when you need a **specific Linux OS** or **custom tools**.

| ✅ Pros | ❌ Cons |
|---|---|
| Full control over environment | Slower (build + pull container) |
| Consistent across runners | Linux runners only |

🧪 **Example `Dockerfile`:**
```dockerfile
FROM ubuntu:20.04
RUN apt-get update && apt-get install -y custom-tool
COPY entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
```

> 🧠 **Remember:** Docker = isolated, consistent, but slower than JS actions.

🔗 [Docs: About custom actions](https://docs.github.com/en/actions/creating-actions/about-custom-actions)

---

### 2.2 Composite Actions

✅ **Definition:**  
Combine **multiple workflow steps** into a single reusable action.

🎯 **Why it matters:**  
Use for bundling related steps — NOT for custom OS environments.

🧪 **Example `action.yml`:**
```yaml
name: 'Lint and Test'
runs:
  using: 'composite'
  steps:
    - run: npm install
      shell: bash
    - run: npm test
      shell: bash
```

> ⚠️ **Trap:** Composite actions do NOT provide isolated runtime environments like Docker.

🔗 [Docs: Composite actions](https://docs.github.com/en/actions/creating-actions/about-custom-actions#composite-actions)

---

### 2.3 JavaScript Actions

✅ **Definition:**  
Actions written in JavaScript, executed directly on the runner.

🎯 **Why it matters:**  
Fastest action type — no container overhead.

> ⚠️ **Trap:** JS actions are NOT ideal when you need specific OS/custom tools.

---

### 2.4 Action Metadata (`action.yml`)

✅ **Definition:**  
Every action needs `action.yml` (or `action.yaml`) in the **root directory** defining inputs, outputs, and runtime.

🧪 **Minimal example:**
```yaml
name: 'My Action'
description: 'Does something useful'
inputs:
  my-input:
    description: 'An input'
    required: true
runs:
  using: 'node20'
  main: 'index.js'
```

> 🧠 **Remember:** Metadata file MUST be in repo root for Marketplace publishing.

🔗 [Docs: Metadata syntax](https://docs.github.com/en/actions/creating-actions/metadata-syntax-for-github-actions)

---

### 2.5 Passing Data with `outputs`

✅ **Definition:**  
Use `outputs` key in `action.yml` to pass data from one step to subsequent steps.

🎯 **Why it matters:**  
Know the correct key — it's `outputs`, not `environment`, `runs`, or `description`.

🧪 **Example:**
```yaml
outputs:
  result:
    description: 'The result value'
    value: ${{ steps.my-step.outputs.result }}
```

🔗 [Docs: Metadata syntax — outputs](https://docs.github.com/en/actions/creating-actions/metadata-syntax-for-github-actions)

---

## 3. Release & Versioning

### 3.1 Release Strategy

✅ **Definition:**  
A structured approach to versioning: document major updates, critical fixes, and security patches.

🎯 **Why it matters:**  
Exam asks what's essential — structured versioning + documentation.

| ✅ Do | ❌ Don't |
|---|---|
| Define release-management strategy | Make updates without documentation |
| Document major versions & patches | Update periodically without versioning |
| Maintain changelog with versions | Maintain changelog without versioning |

🔗 [Docs: Releasing and maintaining actions](https://docs.github.com/en/actions/creating-actions/releasing-and-maintaining-actions)

---

### 3.2 Version Pinning: SHA vs Tags

✅ **Definition:**  
Commit SHAs are immutable; tags can be deleted or moved.

🎯 **Why it matters:**  
**SHA = most reliable & secure** way to pin action versions.

| Method | Reliability | Security |
|---|---|---|
| Full commit SHA | ✅ Highest | ✅ Immutable |
| Tag (v1.0.0) | ⚠️ Medium | Can be moved/deleted |
| Branch name | ❌ Low | Changes constantly |

🧪 **Example:**
```yaml
- uses: actions/checkout@a1b2c3d4e5f6...  # Full SHA (40 chars)
- uses: actions/checkout@v4              # Tag (less secure)
```

> ⚠️ **Trap:** Use **full SHA**, not abbreviated SHA.

🔗 [Docs: Releasing and maintaining actions](https://docs.github.com/en/actions/creating-actions/releasing-and-maintaining-actions)

---

## 4. Variables & Environment

### 4.1 Environment Variable Precedence

✅ **Definition:**  
Variables can be set at workflow, job, or step level. **Most specific wins** (step > job > workflow).

🧪 **Example:**
```yaml
name: Env Demo
env:
  NAME: 'My Action'          # workflow level

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      JAVA_VERSION: '11'     # job level
    steps:
      - name: Print name
        env:
          BUILD: 'Running Build job'  # step level
        run: echo "Hello $NAME. $BUILD. Java $JAVA_VERSION"
        # Output: Hello My Action. Running Build job. Java 11
```

> 🧠 **Remember:** All three levels are accessible; step-level overrides if names conflict.

🔗 [Docs: Variables — default environment variables](https://docs.github.com/en/actions/learn-github-actions/variables#default-environment-variables)

---

### 4.2 Filesystem Variables

✅ **Definition:**  
Use variables (like `$GITHUB_WORKSPACE`) instead of hardcoded paths.

🎯 **Why it matters:**  
Runners differ — variables adapt dynamically to each environment.

> ⚠️ **Trap:** Hardcoded paths aren't forbidden, but variables are recommended for portability.

🔗 [Docs: Default environment variables](https://docs.github.com/en/actions/learn-github-actions/variables#default-environment-variables)

---

## 5. Secrets & Security

### 5.1 Using Secrets in Workflows

✅ **Definition:**  
Access secrets via `${{ secrets.SECRET_NAME }}` context.

🎯 **Why it matters:**  
Know the exact syntax — especially for environment variables.

🧪 **Correct syntax (as env var):**
```yaml
steps:
  - name: Deploy
    env:
      API_KEY: ${{ secrets.api_key }}  # ✅ Correct
    run: ./deploy.sh
```

> ⚠️ **Trap:** These are WRONG:
> - `${{ api_key }}` — missing `secrets.`
> - `${{ secrets.environment.api_key }}` — wrong path
> - `api_key` (plain text) — not a context reference

🔗 [Docs: Using secrets in a workflow](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions#creating-secrets-for-an-environment)

---

### 5.2 Command-Line Secret Risks

✅ **Definition:**  
Avoid passing secrets via command line — they may appear in logs, process lists, or audit events.

🎯 **Why it matters:**  
Direct exam question: "Why avoid passing secrets via CLI?"

> ⚠️ **Trap:** CLI processes do NOT auto-redact secrets.

🔗 [Docs: Using secrets in a workflow](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions#using-secrets-in-a-workflow)

---

## 6. Runners

### 6.1 Custom Labels for Self-Hosted Runners

✅ **Definition:**  
Custom labels route jobs to specific self-hosted runners based on their capabilities.

🎯 **Why it matters:**  
Primary purpose = **routing jobs**, not security or code readability.

🧪 **Example:**
```yaml
jobs:
  build:
    runs-on: [self-hosted, gpu, linux]  # ← custom labels
```

🔗 [Docs: Using custom labels to route jobs](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/using-self-hosted-runners-in-a-workflow#using-custom-labels-to-route-jobs)

---

### 6.2 Proxy Configuration

✅ **Definition:**  
Set `https_proxy` environment variable for self-hosted runners behind a proxy.

🎯 **Why it matters:**  
Know the exact variable name: `https_proxy`

> ⚠️ **Trap:** `proxy_server`, `network_proxy`, `outbound` are NOT valid.

🔗 [Docs: Using a proxy server with self-hosted runners](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/using-a-proxy-server-with-self-hosted-runners)

---

### 6.3 Mixing GitHub-Hosted & Self-Hosted Runners

✅ **Definition:**  
Use GitHub-hosted for common tasks, self-hosted for **resource-intensive tasks**.

🎯 **Why it matters:**  
Balances cost-effectiveness and performance.

> 🧠 **Remember:** NOT primarily for security, personal projects, or short-lived stateless jobs.

🔗 [Docs: About GitHub-hosted runners](https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners/about-github-hosted-runners)

---

### 6.4 Runner Groups

✅ **Definition:**  
Organize runners into groups to control which repositories/workflows can use them.

🎯 **Why it matters:**  
Use case: **chargeback model** — prevent dev workflows from using production runners.

> ⚠️ **Trap:** Runner **labels** = routing; Runner **groups** = access control.

🔗 [Docs: Managing access to self-hosted runners using groups](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/managing-access-to-self-hosted-runners-using-groups#about-runner-groups)

---

## 7. Artifacts & Retention

✅ **Definition:**  
Customize artifact retention using `retention-days` in `actions/upload-artifact`.

🎯 **Why it matters:**  
Retention CAN be customized per artifact (not just org-level).

🧪 **Example:**
```yaml
- uses: actions/upload-artifact@v4
  with:
    name: my-artifact
    path: dist/
    retention-days: 5  # ← custom retention
```

> 🧠 **Remember:** Retention can be set at repository, organization, and enterprise levels too.

🔗 [Docs: Storing workflow data as artifacts](https://docs.github.com/en/actions/using-workflows/storing-workflow-data-as-artifacts)

---

## 8. GitHub Marketplace & Publishing

### 8.1 Marketplace Requirements

✅ **Definition:**  
To publish an action to Marketplace:

| Requirement | Details |
|---|---|
| Repository | Must be **public** |
| Actions per repo | **One** action per repo |
| Metadata location | `action.yml` in **root** directory |
| Name uniqueness | Must be unique on Marketplace |
| Name restrictions | Cannot match existing category, user, org, or GitHub feature |

> ⚠️ **Trap:** "Metadata can be in subdirectory" — FALSE. Must be root.

🔗 [Docs: Publishing actions in GitHub Marketplace](https://docs.github.com/en/actions/creating-actions/publishing-actions-in-github-marketplace)

---

### 8.2 Publishing a Release

✅ **Definition:**  
When publishing, select a **category** to help users find your action.

🎯 **Why it matters:**  
Category must match existing Marketplace categories.

> 🧠 **Remember:** Two-factor auth IS required for publishing.

🔗 [Docs: Publishing actions in GitHub Marketplace](https://docs.github.com/en/actions/creating-actions/publishing-actions-in-github-marketplace)

---

## 9. Scripts in Workflows

✅ **Definition:**  
To run a script file, manually grant execute permissions on the runner.

🧪 **Example:**
```yaml
steps:
  - run: chmod +x ./scripts/deploy.sh
  - run: ./scripts/deploy.sh
```

> ⚠️ **Trap:** There's NO special YAML keyword to mark scripts executable. No `notTypes:` style flag.

🔗 [Docs: Adding scripts to your workflow](https://docs.github.com/en/actions/learn-github-actions/essential-features-of-github-actions#adding-scripts-to-your-workflow)

---

## 10. GitHub Packages

✅ **Definition:**  
GitHub Packages supports these package managers:

| ✅ Supported | ❌ NOT Supported |
|---|---|
| npm (Node.js) | RPM (Linux) |
| NuGet (.NET) | |
| Maven/Gradle (Java) | |
| RubyGems (Ruby) | |
| Docker | |

> ⚠️ **Trap:** RPM is NOT supported.

🔗 [Docs: Introduction to GitHub Packages](https://docs.github.com/en/packages/learn-github-packages/introduction-to-github-packages#about-github-packages)

---

## 11. Workflow Badges

✅ **Definition:**  
In **private repos**, workflow badges are NOT accessible externally.

🎯 **Why it matters:**  
Prevents unauthorized embedding/linking from external sources.

> 🧠 **Remember:** This is a security feature, not a bug or default setting issue.

---

## 12. Scoped Installation Tokens

✅ **Definition:**  
GitHub creates scoped installation tokens with **read access** for runners to download actions from private/internal repos. Token expires after **1 hour**.

🎯 **Why it matters:**  
Know: auto-generated, read-only, 1-hour expiry.

> ⚠️ **Trap:** NOT personal access tokens. NOT direct repo access. NOT user credential prompts.

🔗 [Docs: Sharing actions and workflows with your organization](https://docs.github.com/en/actions/creating-actions/sharing-actions-and-workflows-with-your-organization)

---

## 13. Quick Self-Test (20 MCQs)

**Instructions:** Answer each question, then check answers at the end.

---

**Q1.** What is the PRIMARY purpose of dependent jobs?  
A) Run concurrently  
B) Define sequential execution order  
C) Control workflow concurrency  
D) Enforce conditional logic  

---

**Q2.** A matrix has `os: [ubuntu, windows]` and `node: [14, 16, 18]`. How many jobs run?  
A) 2  
B) 3  
C) 5  
D) 6  

---

**Q3.** Which keyword triggers a workflow?  
A) `trigger:`  
B) `event:`  
C) `on:`  
D) `workflow:`  

---

**Q4.** How do you exclude `deleted` events from `branch_protection_rule`?  
A) `notTypes: [deleted]`  
B) `types: [created, edited]`  
C) `exclude: [deleted]`  
D) `filter: -deleted`  

---

**Q5.** Best action type for specific Linux OS + custom tools?  
A) JavaScript action  
B) Composite action  
C) Docker container action  
D) Reusable workflow  

---

**Q6.** What does a composite action do?  
A) Creates Docker containers  
B) Combines multiple steps into one action  
C) Runs JavaScript code  
D) Manages secrets  

---

**Q7.** Where must `action.yml` be for Marketplace publishing?  
A) `.github/` folder  
B) `src/` folder  
C) Root directory  
D) Any subdirectory  

---

**Q8.** Which key passes data between steps in action metadata?  
A) `runs`  
B) `environment`  
C) `outputs`  
D) `description`  

---

**Q9.** Most secure way to pin an action version?  
A) Branch name  
B) Tag (v1.0.0)  
C) Abbreviated SHA  
D) Full commit SHA  

---

**Q10.** Correct syntax to use a secret as env var?  
A) `${{ api_key }}`  
B) `${{ secrets.api_key }}`  
C) `${{ secrets.environment.api_key }}`  
D) `api_key`  

---

**Q11.** Why avoid passing secrets via command line?  
A) CLI auto-redacts them  
B) They may be visible to other users/logs  
C) GitHub blocks CLI secrets  
D) It's faster via env vars  

---

**Q12.** Primary purpose of custom labels for self-hosted runners?  
A) Improve code readability  
B) Enhance security  
C) Route jobs to specific runners  
D) Assign descriptive job names  

---

**Q13.** Environment variable for proxy on self-hosted runner?  
A) `proxy_server`  
B) `network_proxy`  
C) `https_proxy`  
D) `outbound`  

---

**Q14.** When to mix GitHub-hosted and self-hosted runners?  
A) For highest security  
B) For resource-intensive tasks  
C) For personal projects  
D) For short-lived stateless jobs  

---

**Q15.** Feature to prevent dev workflows using production runners (chargeback)?  
A) Runner labels  
B) Runner groups  
C) Runner environments  
D) Runner sets  

---

**Q16.** How to customize artifact retention?  
A) Cannot be customized  
B) Only at organization level  
C) Using `retention-days` in upload-artifact  
D) Automatically applied to all repos  

---

**Q17.** Marketplace requirement for action repository?  
A) Private repo allowed  
B) Multiple actions per repo  
C) Public repo, one action, metadata in root  
D) Metadata in subdirectory  

---

**Q18.** How to make a script executable in workflow?  
A) Use `executable: true` in YAML  
B) Run `chmod +x` on the runner  
C) Add script to `.github/scripts/`  
D) Use special YAML keyword  

---

**Q19.** Which is NOT supported by GitHub Packages?  
A) npm  
B) NuGet  
C) Maven  
D) RPM  

---

**Q20.** Token type for runners to access private repo actions?  
A) Personal access token  
B) Scoped installation token (1-hour expiry)  
C) Direct repo access  
D) User credential prompt  

---

### Answer Key

| Q | Answer | Q | Answer |
|---|---|---|---|
| 1 | B | 11 | B |
| 2 | D | 12 | C |
| 3 | C | 13 | C |
| 4 | B | 14 | B |
| 5 | C | 15 | B |
| 6 | B | 16 | C |
| 7 | C | 17 | C |
| 8 | C | 18 | B |
| 9 | D | 19 | D |
| 10 | B | 20 | B |

---

> 🎯 **Final tip:** If you scored <16/20, re-read the sections for questions you missed. Good luck! 🚀
