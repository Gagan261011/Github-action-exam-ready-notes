# 📘 GitHub Actions – Test 1 Concept Revision (Simple Notes)

> **How to use this file:**
> 1. Read each concept's 📌 definition first
> 2. Check ⚠️ traps — these are exam favorites!
> 3. Use 🧠 Exam Tips for last-minute revision

---

## 📑 Table of Contents

1. [Dependent Jobs (`needs`)](#1--dependent-jobs-needs)
2. [Matrix Strategy (Job Count)](#2--matrix-strategy-job-count)
3. [Workflow Triggers (`on` keyword)](#3--workflow-triggers-on-keyword)
4. [Event Type Filtering (`types`)](#4--event-type-filtering-types)
5. [Docker Container Actions](#5--docker-container-actions)
6. [Composite Actions](#6--composite-actions)
7. [JavaScript Actions](#7--javascript-actions)
8. [Action Metadata File (`action.yml`)](#8--action-metadata-file-actionyml)
9. [Passing Data with `outputs`](#9--passing-data-with-outputs)
10. [Making Scripts Executable](#10--making-scripts-executable)
11. [Environment Variable Precedence](#11--environment-variable-precedence)
12. [Variables vs Hardcoded Paths](#12--variables-vs-hardcoded-paths)
13. [Secrets as Environment Variables](#13--secrets-as-environment-variables)
14. [Why NOT Pass Secrets via CLI](#14--why-not-pass-secrets-via-cli)
15. [Release Strategy for Actions](#15--release-strategy-for-actions)
16. [Commit SHA Versioning](#16--commit-sha-versioning)
17. [GitHub Packages (Supported Managers)](#17--github-packages-supported-managers)
18. [PowerShell Command Count](#18--powershell-command-count)
19. [Marketplace Publishing Requirements](#19--marketplace-publishing-requirements)
20. [Marketplace Category Requirement](#20--marketplace-category-requirement)
21. [Scoped Installation Token](#21--scoped-installation-token)
22. [Custom Labels for Self-Hosted Runners](#22--custom-labels-for-self-hosted-runners)
23. [Proxy for Self-Hosted Runners](#23--proxy-for-self-hosted-runners)
24. [Mixing GitHub-Hosted & Self-Hosted Runners](#24--mixing-github-hosted--self-hosted-runners)
25. [Runner Groups](#25--runner-groups)
26. [Artifact Retention](#26--artifact-retention)
27. [Workflow Templates](#27--workflow-templates)
28. [Workflow Badges in Private Repos](#28--workflow-badges-in-private-repos)

---

## 1. 🧩 Dependent Jobs (`needs`)

📌 **What is it?**  
→ A way to make Job B wait for Job A to finish first. Uses `needs:` keyword.

🎯 **Why do we use it?**  
→ To run jobs in a **specific order** (not at the same time)

🛠 **Example**
```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Building..."

  deploy:
    needs: build   # 👈 waits for build to finish
    runs-on: ubuntu-latest
    steps:
      - run: echo "Deploying..."
```

⚠️ **Common Mistake / Confusion**
- ❌ "Dependent jobs run concurrently" — NO! They run **sequentially**
- ❌ "Dependent jobs control concurrency settings" — NO! That's `concurrency:` key
- ❌ "Dependent jobs enforce conditional logic" — NO! That's `if:` conditions

🧠 **Exam Tip:** Primary purpose = **define sequential execution order**

🔗 [Official Docs](https://docs.github.com/en/actions/using-jobs/using-jobs-in-a-workflow)

---

## 2. 🧩 Matrix Strategy (Job Count)

📌 **What is it?**  
→ Creates multiple jobs by combining different values (like testing on multiple OS + Node versions)

🎯 **Why do we use it?**  
→ Test your code on many combinations without writing separate jobs

🛠 **Example**
```yaml
jobs:
  test:
    strategy:
      matrix:
        animal: [cat, dog, bear]   # 3 values
        color: [black, brown]      # 2 values
    # Total = 3 × 2 = 6 jobs!
```

⚠️ **Common Mistake / Confusion**
- Don't add values — **multiply them!**
- `[a, b, c]` × `[x, y]` = 3 × 2 = **6 jobs**

🧠 **Exam Tip:** Total jobs = axis1 × axis2 × axis3...

🔗 [Official Docs](https://docs.github.com/en/actions/using-jobs/using-a-matrix-for-your-jobs#using-a-matrix-strategy)

---

## 3. 🧩 Workflow Triggers (`on` keyword)

📌 **What is it?**  
→ The `on:` keyword tells GitHub **when** to run your workflow

🎯 **Why do we use it?**  
→ Without it, workflow won't know when to start!

🛠 **Example**
```yaml
on:
  push:
    branches: [main]
  pull_request:
  schedule:
    - cron: '0 0 * * *'   # daily at midnight
```

⚠️ **Common Mistake / Confusion**
- ❌ `trigger:` — does NOT exist
- ❌ `event:` — does NOT exist  
- ❌ `workflow:` — used for name, NOT triggers
- ✅ `on:` — correct keyword!

🧠 **Exam Tip:** Only `on:` is valid for triggers

🔗 [Official Docs](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows)

---

## 4. 🧩 Event Type Filtering (`types`)

📌 **What is it?**  
→ Filter WHICH activity types trigger your workflow (created, edited, deleted, etc.)

🎯 **Why do we use it?**  
→ Run workflow only for specific activities, not all

🛠 **Example**
```yaml
on:
  branch_protection_rule:
    types: [created, edited]   # 👈 excludes 'deleted'
```

⚠️ **Common Mistake / Confusion**
- ❌ `notTypes: [deleted]` — does NOT exist!
- ❌ `exclude: [deleted]` — does NOT exist!
- ✅ Only include what you want in `types:`

🧠 **Exam Tip:** To exclude something, just don't include it in `types:`

🔗 [Official Docs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#onevent_nametypes)

---

## 5. 🧩 Docker Container Actions

📌 **What is it?**  
→ An action that runs inside a Docker container with specific OS, tools, dependencies

🎯 **Why do we use it?**  
→ When you need **specific Linux OS** or **custom tools** that aren't on GitHub runners

🛠 **Example**
```dockerfile
FROM ubuntu:20.04
RUN apt-get update && apt-get install -y my-tool
COPY entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
```

⚠️ **Common Mistake / Confusion**
- Docker actions are **slower** than JavaScript actions (need to build/pull image)
- Docker actions only work on **Linux runners**
- "Actions cannot use custom OS" — FALSE! Docker can!

🧠 **Exam Tip:** Need specific Linux + custom tools? → Docker container action

🔗 [Official Docs](https://docs.github.com/en/actions/creating-actions/about-custom-actions)

---

## 6. 🧩 Composite Actions

📌 **What is it?**  
→ Bundle **multiple steps** into one reusable action

🎯 **Why do we use it?**  
→ Reuse a group of steps across many workflows (like "lint + test + build")

🛠 **Example**
```yaml
name: 'Setup and Test'
runs:
  using: 'composite'
  steps:
    - run: npm install
      shell: bash
    - run: npm test
      shell: bash
```

⚠️ **Common Mistake / Confusion**
- ❌ Composite actions do NOT create isolated Docker environments
- ❌ Composite actions are NOT for custom OS — use Docker for that
- ✅ Composite = combining steps, NOT isolation

🧠 **Exam Tip:** Composite = bundle steps. Docker = custom environment.

🔗 [Official Docs](https://docs.github.com/en/actions/creating-actions/about-custom-actions#composite-actions)

---

## 7. 🧩 JavaScript Actions

📌 **What is it?**  
→ Actions written in JavaScript, run directly on the runner

🎯 **Why do we use it?**  
→ **Fastest** action type — no container overhead

🛠 **Example**
```yaml
# action.yml
runs:
  using: 'node20'
  main: 'index.js'
```

⚠️ **Common Mistake / Confusion**
- JS actions are NOT good for specific OS/custom tools
- They're fast but less isolated than Docker

🧠 **Exam Tip:** JS = fast. Docker = isolated environment.

---

## 8. 🧩 Action Metadata File (`action.yml`)

📌 **What is it?**  
→ Required file that describes your action (inputs, outputs, how to run)

🎯 **Why do we use it?**  
→ GitHub needs this to understand and run your action

🛠 **Example**
```yaml
name: 'My Action'
description: 'Does something cool'
inputs:
  name:
    description: 'Your name'
    required: true
runs:
  using: 'node20'
  main: 'index.js'
```

⚠️ **Common Mistake / Confusion**
- Must be named `action.yml` or `action.yaml`
- Must be in **root directory** for Marketplace publishing
- ❌ Cannot be in subdirectory for Marketplace

🧠 **Exam Tip:** Marketplace = `action.yml` must be in repo ROOT

🔗 [Official Docs](https://docs.github.com/en/actions/creating-actions/metadata-syntax-for-github-actions)

---

## 9. 🧩 Passing Data with `outputs`

📌 **What is it?**  
→ The `outputs` key in `action.yml` lets you pass data to other steps

🎯 **Why do we use it?**  
→ Share results between steps/jobs

🛠 **Example**
```yaml
outputs:
  result:
    description: 'The calculation result'
    value: ${{ steps.calc.outputs.result }}
```

⚠️ **Common Mistake / Confusion**
- ❌ `environment` — NOT for passing data
- ❌ `runs` — defines how action runs, NOT outputs
- ❌ `description` — describes action, NOT passes data
- ✅ `outputs` — correct!

🧠 **Exam Tip:** Pass data between steps → use `outputs`

🔗 [Official Docs](https://docs.github.com/en/actions/creating-actions/metadata-syntax-for-github-actions)

---

## 10. 🧩 Making Scripts Executable

📌 **What is it?**  
→ Use `chmod +x` to give execute permission to script files

🎯 **Why do we use it?**  
→ Scripts need execute permission to run on Linux/macOS runners

🛠 **Example**
```yaml
steps:
  - run: chmod +x ./scripts/deploy.sh
  - run: ./scripts/deploy.sh
```

⚠️ **Common Mistake / Confusion**
- ❌ No special YAML keyword makes scripts executable
- ❌ `executable: true` — does NOT exist
- ❌ Embedding script in YAML with flag — NOT a thing
- ✅ Manually run `chmod +x` on the runner

🧠 **Exam Tip:** Make executable = run `chmod +x` manually

🔗 [Official Docs](https://docs.github.com/en/actions/learn-github-actions/essential-features-of-github-actions#adding-scripts-to-your-workflow)

---

## 11. 🧩 Environment Variable Precedence

📌 **What is it?**  
→ Variables can be set at 3 levels: workflow, job, step. **Step wins** if same name.

🎯 **Why do we use it?**  
→ Override variables at more specific levels

🛠 **Example**
```yaml
env:
  NAME: 'Workflow Level'      # 👈 workflow

jobs:
  build:
    env:
      VERSION: '11'           # 👈 job level
    steps:
      - name: Print
        env:
          MSG: 'Step Level'   # 👈 step level
        run: echo "$NAME $VERSION $MSG"
        # Output: Workflow Level 11 Step Level
```

⚠️ **Common Mistake / Confusion**
- All 3 levels are accessible in a step
- If same name: step > job > workflow (most specific wins)

🧠 **Exam Tip:** Step-level overrides job-level overrides workflow-level

🔗 [Official Docs](https://docs.github.com/en/actions/learn-github-actions/variables#default-environment-variables)

---

## 12. 🧩 Variables vs Hardcoded Paths

📌 **What is it?**  
→ Use `$GITHUB_WORKSPACE` instead of `/home/runner/work/...`

🎯 **Why do we use it?**  
→ Different runners have different paths. Variables adapt automatically.

🛠 **Example**
```yaml
# ✅ Good - uses variable
- run: cd $GITHUB_WORKSPACE && ./build.sh

# ❌ Bad - hardcoded path
- run: cd /home/runner/work/myrepo && ./build.sh
```

⚠️ **Common Mistake / Confusion**
- Hardcoded paths are NOT forbidden, just not recommended
- Variables make workflows portable across runners

🧠 **Exam Tip:** Variables = dynamic, adapts to runner environment

🔗 [Official Docs](https://docs.github.com/en/actions/learn-github-actions/variables#default-environment-variables)

---

## 13. 🧩 Secrets as Environment Variables

📌 **What is it?**  
→ Access secrets using `${{ secrets.SECRET_NAME }}` syntax

🎯 **Why do we use it?**  
→ Safely use passwords, API keys, tokens in workflows

🛠 **Example**
```yaml
steps:
  - name: Deploy
    env:
      API_KEY: ${{ secrets.api_key }}   # ✅ Correct!
    run: ./deploy.sh
```

⚠️ **Common Mistake / Confusion**
- ❌ `${{ api_key }}` — missing `secrets.`
- ❌ `${{ secrets.environment.api_key }}` — wrong path
- ❌ `api_key` (plain text) — not a context reference
- ✅ `${{ secrets.api_key }}` — correct!

🧠 **Exam Tip:** Always use `secrets.` prefix: `${{ secrets.NAME }}`

🔗 [Official Docs](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions#creating-secrets-for-an-environment)

---

## 14. 🧩 Why NOT Pass Secrets via CLI

📌 **What is it?**  
→ Don't put secrets in command line arguments!

🎯 **Why do we use it?**  
→ CLI commands can appear in logs, process lists, audit events

🛠 **Example**
```yaml
# ❌ BAD - secret visible in command
- run: ./deploy.sh --password=${{ secrets.PASS }}

# ✅ GOOD - secret as environment variable
- env:
    PASS: ${{ secrets.PASS }}
  run: ./deploy.sh  # script reads $PASS internally
```

⚠️ **Common Mistake / Confusion**
- ❌ "CLI processes auto-redact secrets" — FALSE!
- ❌ "Passing via CLI is recommended" — FALSE!
- Secrets in CLI = visible to other users, captured in logs

🧠 **Exam Tip:** Never pass secrets via command line — use env vars

🔗 [Official Docs](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions#using-secrets-in-a-workflow)

---

## 15. 🧩 Release Strategy for Actions

📌 **What is it?**  
→ A plan for versioning and documenting your action updates

🎯 **Why do we use it?**  
→ Users need to know what changed, what's new, what's fixed

🛠 **Example**
```
v1.0.0 - Initial release
v1.1.0 - Added new feature X
v1.1.1 - Security patch for Y
v2.0.0 - Breaking changes (major update)
```

⚠️ **Common Mistake / Confusion**
- ❌ "Update without documentation" — BAD practice
- ❌ "Update without versioning" — BAD practice
- ❌ "Changelog without versions" — Still confusing!
- ✅ Versioning + Changelog + Documentation = Good!

🧠 **Exam Tip:** Good release = versioning + changelog + docs

🔗 [Official Docs](https://docs.github.com/en/actions/creating-actions/releasing-and-maintaining-actions)

---

## 16. 🧩 Commit SHA Versioning

📌 **What is it?**  
→ Pin action to a specific commit using its SHA hash (40 characters)

🎯 **Why do we use it?**  
→ Tags can be deleted/moved. SHA is **permanent and immutable**.

🛠 **Example**
```yaml
# ✅ Most secure - full SHA
- uses: actions/checkout@a1b2c3d4e5f6g7h8i9j0...

# ⚠️ Less secure - tag can be moved
- uses: actions/checkout@v4

# ❌ Least secure - branch changes constantly
- uses: actions/checkout@main
```

⚠️ **Common Mistake / Confusion**
- ❌ Abbreviated SHA — NOT recommended (use full 40-char SHA)
- ❌ Branch names — change constantly
- Tags are useful but CAN be deleted or moved

🧠 **Exam Tip:** Most secure = **Full commit SHA** (not abbreviated!)

🔗 [Official Docs](https://docs.github.com/en/actions/creating-actions/releasing-and-maintaining-actions)

---

## 17. 🧩 GitHub Packages (Supported Managers)

📌 **What is it?**  
→ GitHub's package hosting service

🎯 **Why do we use it?**  
→ Host your packages alongside your code

🛠 **Supported Package Managers**
| ✅ Supported | ❌ NOT Supported |
|-------------|-----------------|
| npm (Node.js) | RPM (Linux) |
| NuGet (.NET) | |
| Maven (Java) | |
| Gradle (Java) | |
| RubyGems (Ruby) | |
| Docker | |

⚠️ **Common Mistake / Confusion**
- ❌ RPM is NOT supported (Linux package manager)

🧠 **Exam Tip:** Remember: npm, NuGet, Maven, Gradle, RubyGems, Docker ✅ | RPM ❌

🔗 [Official Docs](https://docs.github.com/en/packages/learn-github-packages/introduction-to-github-packages#about-github-packages)

---

## 18. 🧩 PowerShell Command Count

📌 **What is it?**  
→ Count individual commands in a PowerShell step, not just steps

🎯 **Why do we use it?**  
→ Exam asks "how many commands execute?"

🛠 **Example**
```yaml
steps:
  - name: Install PSScriptAnalyzer
    shell: pwsh
    run: |
      Set-PSRepository PSGallery -InstallationPolicy Trusted  # Command 1
      Install-Module PSScriptAnalyzer -ErrorAction Stop       # Command 2
  
  - name: Get rules
    shell: pwsh
    run: Get-ScriptAnalyzerRule   # Command 3

# Total = 3 PowerShell commands
```

⚠️ **Common Mistake / Confusion**
- Don't count steps — count individual commands!
- 2 steps can have 3+ commands total

🧠 **Exam Tip:** Count each command line separately

🔗 [Official Docs](https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners/about-github-hosted-runners)

---

## 19. 🧩 Marketplace Publishing Requirements

📌 **What is it?**  
→ Rules to publish your action to GitHub Marketplace

🎯 **Why do we use it?**  
→ Share your action with the world!

🛠 **Requirements Checklist**
| Requirement | Details |
|-------------|---------|
| Repository | Must be **PUBLIC** |
| Actions per repo | Only **ONE** action |
| Metadata file | `action.yml` in **ROOT** directory |
| Name | Must be **UNIQUE** on Marketplace |
| Name restrictions | Can't match category, user, org, or GitHub feature |

⚠️ **Common Mistake / Confusion**
- ❌ "Private repo allowed" — NO! Must be public
- ❌ "Multiple actions per repo" — NO! Only one
- ❌ "Metadata in subdirectory" — NO! Must be root
- ❌ "Name can match category" — NO! Must be unique

🧠 **Exam Tip:** Public repo + 1 action + action.yml in root + unique name

🔗 [Official Docs](https://docs.github.com/en/actions/creating-actions/publishing-actions-in-github-marketplace)

---

## 20. 🧩 Marketplace Category Requirement

📌 **What is it?**  
→ When publishing, you must select a category for your action

🎯 **Why do we use it?**  
→ Helps users find your action

🛠 **Example Categories**
- Continuous Integration
- Deployment
- Code Quality
- Security
- Utilities

⚠️ **Common Mistake / Confusion**
- Category is selected during publishing process
- Action name must NOT match existing category name
- Two-factor auth IS required to publish

🧠 **Exam Tip:** 2FA required + select category when publishing

🔗 [Official Docs](https://docs.github.com/en/actions/creating-actions/publishing-actions-in-github-marketplace)

---

## 21. 🧩 Scoped Installation Token

📌 **What is it?**  
→ GitHub auto-creates a temporary token for runners to access private/internal repos

🎯 **Why do we use it?**  
→ Securely download actions from private repos without manual auth

🛠 **How it works**
```
1. Workflow starts
2. GitHub creates scoped token (read access)
3. Token expires after 1 hour
4. Runner uses token to download actions
```

⚠️ **Common Mistake / Confusion**
- ❌ NOT a personal access token (PAT)
- ❌ NOT direct repository access
- ❌ NOT user credential prompt each time
- ✅ Auto-generated, read-only, 1-hour expiry

🧠 **Exam Tip:** Scoped installation token = auto, read-only, expires in 1 hour

🔗 [Official Docs](https://docs.github.com/en/actions/creating-actions/sharing-actions-and-workflows-with-your-organization)

---

## 22. 🧩 Custom Labels for Self-Hosted Runners

📌 **What is it?**  
→ Tags you add to runners to identify their capabilities (GPU, ARM, etc.)

🎯 **Why do we use it?**  
→ **Route jobs** to specific runners based on labels

🛠 **Example**
```yaml
jobs:
  ml-training:
    runs-on: [self-hosted, gpu, linux]  # 👈 custom labels
    steps:
      - run: python train.py
```

⚠️ **Common Mistake / Confusion**
- ❌ NOT for code readability
- ❌ NOT for security
- ❌ NOT for naming jobs
- ✅ Primary purpose = **route jobs to specific runners**

🧠 **Exam Tip:** Custom labels = routing jobs, not security or names

🔗 [Official Docs](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/using-self-hosted-runners-in-a-workflow#using-custom-labels-to-route-jobs)

---

## 23. 🧩 Proxy for Self-Hosted Runners

📌 **What is it?**  
→ Configure `https_proxy` environment variable for runners behind a firewall

🎯 **Why do we use it?**  
→ Let runner communicate with GitHub through corporate proxy

🛠 **Example**
```bash
# Set on the runner machine
export https_proxy=http://proxy.company.com:8080
```

⚠️ **Common Mistake / Confusion**
- ❌ `proxy_server` — NOT valid
- ❌ `network_proxy` — NOT valid
- ❌ `outbound` — NOT valid
- ✅ `https_proxy` — correct!

🧠 **Exam Tip:** Proxy variable = `https_proxy` (lowercase!)

🔗 [Official Docs](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/using-a-proxy-server-with-self-hosted-runners)

---

## 24. 🧩 Mixing GitHub-Hosted & Self-Hosted Runners

📌 **What is it?**  
→ Use both runner types in same workflow

🎯 **Why do we use it?**  
→ GitHub-hosted for simple tasks, self-hosted for **resource-intensive** tasks

🛠 **Example**
```yaml
jobs:
  lint:
    runs-on: ubuntu-latest          # GitHub-hosted (simple task)
  
  ml-train:
    runs-on: [self-hosted, gpu]     # Self-hosted (heavy task)
```

⚠️ **Common Mistake / Confusion**
- ❌ NOT primarily for "highest security"
- ❌ NOT for "personal projects with minimal dependencies"
- ❌ NOT for "short-lived stateless jobs"
- ✅ Best for: resource-intensive tasks

🧠 **Exam Tip:** Mix runners = balance cost + performance for heavy tasks

🔗 [Official Docs](https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners/about-github-hosted-runners)

---

## 25. 🧩 Runner Groups

📌 **What is it?**  
→ Organize runners into groups to control **who can use them**

🎯 **Why do we use it?**  
→ Chargeback model — prevent dev team using prod team's runners

🛠 **Example Use Case**
```
Runner Group: "Production-Team"
  - Runner: prod-runner-1
  - Runner: prod-runner-2
  - Access: Only Production repositories

Result: Dev workflows cannot use prod runners
```

⚠️ **Common Mistake / Confusion**
- **Labels** = routing (which runner)
- **Groups** = access control (who can use)
- ❌ `runner sets` — does NOT exist in GitHub Actions

🧠 **Exam Tip:** Labels = routing | Groups = access control

🔗 [Official Docs](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/managing-access-to-self-hosted-runners-using-groups#about-runner-groups)

---

## 26. 🧩 Artifact Retention

📌 **What is it?**  
→ How long GitHub keeps your uploaded artifacts (build files, logs, etc.)

🎯 **Why do we use it?**  
→ Control storage costs, keep important artifacts longer

🛠 **Example**
```yaml
- uses: actions/upload-artifact@v4
  with:
    name: build-output
    path: dist/
    retention-days: 5   # 👈 custom retention!
```

⚠️ **Common Mistake / Confusion**
- ❌ "Cannot customize retention" — FALSE! You can!
- ❌ "Only org-level config" — FALSE! Per-artifact works too
- ✅ Can be set at artifact, repo, org, enterprise levels

🧠 **Exam Tip:** Use `retention-days` in upload-artifact action

🔗 [Official Docs](https://docs.github.com/en/actions/using-workflows/storing-workflow-data-as-artifacts)

---

## 27. 🧩 Workflow Templates

📌 **What is it?**  
→ Reusable workflow files your org can share across repos

🎯 **Why do we use it?**  
→ Ensure automation is **reused and maintained** across enterprise

🛠 **How it works**
```
1. Create workflow in .github repo of your org
2. Put it in workflow-templates/ folder
3. Org members can use it when creating new workflows
```

⚠️ **Common Mistake / Confusion**
- ❌ Naming conventions — don't ensure reuse
- ❌ GitHub wiki — documentation, not reuse
- ❌ Contribution guidelines — collaboration rules, not reuse
- ✅ Workflow templates — correct!

🧠 **Exam Tip:** Enterprise reuse = workflow templates

🔗 [Official Docs](https://docs.github.com/en/actions/using-workflows/creating-starter-workflows-for-your-organization)

---

## 28. 🧩 Workflow Badges in Private Repos

📌 **What is it?**  
→ Status badges (passing/failing) for workflows

🎯 **Why do we use it?**  
→ Show build status on README

🛠 **Why Not Accessible Externally?**
```
Private repo badge → NOT accessible to public
Why? → Prevents unauthorized embedding/linking
This is a SECURITY FEATURE
```

⚠️ **Common Mistake / Confusion**
- ❌ "Badge visibility disabled by default" — NOT the reason
- ❌ "Only visible to collaborators" — NOT the full answer
- ✅ Prevent external embedding from unauthorized sources

🧠 **Exam Tip:** Private repo badges blocked = security feature

---

## 📝 Quick Reference Table

| Concept | Key Point |
|---------|-----------|
| `needs:` | Sequential job order |
| Matrix | Multiply axes for job count |
| `on:` | Only valid trigger keyword |
| `types:` | Filter events (no `notTypes`) |
| Docker action | Custom OS + tools (slower) |
| Composite action | Bundle steps (no isolation) |
| `outputs` | Pass data between steps |
| `chmod +x` | Make script executable |
| Env precedence | Step > Job > Workflow |
| Secrets syntax | `${{ secrets.NAME }}` |
| CLI secrets | Never! Use env vars |
| SHA versioning | Most secure (full 40-char) |
| Custom labels | Route jobs to runners |
| `https_proxy` | Proxy for self-hosted |
| Runner groups | Access control (chargeback) |
| `retention-days` | Artifact retention |
| Workflow templates | Enterprise reuse |
| Private badges | Security (no external access) |
| Marketplace | Public + 1 action + root action.yml |
| GitHub Packages | npm, NuGet, Maven, Gradle, RubyGems, Docker (NO RPM) |

---

> 🎯 **Final Tip:** Focus on ⚠️ traps — exams love testing what something is **NOT** for!

Good luck! 🚀
