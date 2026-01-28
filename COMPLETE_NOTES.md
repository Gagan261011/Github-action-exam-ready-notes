# 📘 GitHub Actions – Test 1 Complete Concept Notes (Beginner Friendly)

> **How to use these notes:**
> 1. Read 📌 definitions first — understand the "what"
> 2. Check 🛠 examples — see the "how"
> 3. Memorize ⚠️ traps and 🧠 tips — these appear in exams!

---

## 📑 Table of Contents

### Jobs & Execution
1. [Dependent Jobs & `needs`](#1--dependent-jobs--needs)
2. [Sequential vs Parallel Jobs](#2--sequential-vs-parallel-jobs)
3. [Workflow Concurrency vs Job Dependency](#3--workflow-concurrency-vs-job-dependency)

### Action Types
4. [Docker Container Action Purpose](#4--docker-container-action-purpose)
5. [JS vs Docker vs Composite Actions](#5--js-vs-docker-vs-composite-actions)
6. [Composite Actions Purpose](#6--composite-actions-purpose)
7. [Combining Multiple Steps](#7--combining-multiple-steps)

### Scripts
8. [Making Scripts Executable](#8--making-scripts-executable-chmod-x)
9. [Where Scripts Should Be Stored](#9--where-scripts-should-be-stored)

### Environment Variables
10. [Environment Variables in Workflows](#10--environment-variables-in-workflows)
11. [Env Precedence (Workflow → Job → Step)](#11--env-precedence-workflow--job--step)
12. [Accessing Env Vars in Run Commands](#12--accessing-env-vars-in-run-commands)
13. [Why Use Variables for Filesystem Paths](#13--why-use-variables-for-filesystem-paths)
14. [Variables vs Hardcoded Paths](#14--variables-vs-hardcoded-paths)

### Release & Versioning
15. [Release Strategy for Actions](#15--release-strategy-for-actions)
16. [Versioning + Changelog Importance](#16--versioning--changelog-importance)
17. [Major Version Updates](#17--major-version-updates)
18. [Security Patches](#18--security-patches)
19. [Commit SHA Versioning](#19--commit-sha-versioning)
20. [Why Tags Are Risky](#20--why-tags-are-risky)

### GitHub Packages
21. [GitHub Packages Overview](#21--github-packages-overview)
22. [Supported Package Managers](#22--supported-package-managers)

### Commands & Steps
23. [Counting Commands in Steps](#23--counting-commands-in-steps)

### Marketplace Publishing
24. [Marketplace Requirement: Public Repo](#24--marketplace-requirement-public-repo)
25. [Metadata File Location](#25--metadata-file-location-actionyml)
26. [Unique Naming Rules](#26--unique-naming-rules-in-marketplace)
27. [Marketplace Category Selection](#27--marketplace-category-selection)

### Tokens & Authentication
28. [Scoped Installation Tokens](#28--scoped-installation-tokens)
29. [Token Expiry (1 Hour)](#29--token-expiry-1-hour)

### Event Filtering
30. [Event Filtering Using `types`](#30--event-filtering-using-types)
31. [`on.branch_protection_rule.types`](#31--onbranch_protection_ruletypes)

### Action Metadata
32. [Action Metadata File Keys](#32--action-metadata-file-keys)
33. [`outputs` in Custom Actions](#33--outputs-in-custom-actions)
34. [Passing Data Between Steps](#34--passing-data-between-steps)
35. [Required Files for Custom Action](#35--required-files-for-custom-action)

### Secrets
36. [Using Secrets as Environment Variables](#36--using-secrets-as-environment-variables)
37. [Correct Syntax: `${{ secrets.NAME }}`](#37--correct-syntax--secretsname-)
38. [Why NOT Pass Secrets via CLI](#38--why-not-pass-secrets-via-cli)
39. [Command Line Exposure Risk](#39--command-line-exposure-risk)

### Runners
40. [Custom Labels for Self-Hosted Runners](#40--custom-labels-for-self-hosted-runners)
41. [Routing Jobs Using Labels](#41--routing-jobs-using-labels)
42. [Proxy Config for Runners](#42--proxy-config-for-runners-https_proxy)
43. [GitHub-Hosted vs Self-Hosted Runners](#43--github-hosted-vs-self-hosted-runners)
44. [When to Mix Both Runners](#44--when-to-mix-both-runners)
45. [Runner Groups (Enterprise)](#45--runner-groups-enterprise-cost-control)

### Artifacts
46. [Artifact Retention](#46--artifact-retention)
47. [`actions/upload-artifact` Usage](#47--actionsupload-artifact-usage)
48. [Custom Retention Days](#48--custom-retention-days)

### Triggers
49. [YAML `on` Keyword](#49--yaml-on-keyword)
50. [Workflow Triggers](#50--workflow-triggers-push-issues-discussion)
51. [Scheduled Workflow Minimum Time](#51--scheduled-workflow-minimum-time-5-min)

### Matrix Strategy
52. [Matrix Strategy](#52--matrix-strategy)
53. [Job Count = Combinations Logic](#53--job-count--combinations-logic)

### Enterprise Features
54. [Workflow Templates](#54--workflow-templates)
55. [Reusing Automation in Org](#55--reusing-automation-in-org)

### Workflow Status
56. [Workflow Badges (Private Repo)](#56--workflow-badges-private-repo-limitation)
57. [Where to View PR Workflow Status](#57--where-to-view-pr-workflow-status)
58. [Filtering Failed Runs](#58--filtering-failed-runs-failure)
59. [Approval Timeout (30 Days)](#59--approval-timeout-30-days--fail)

### Docker Specifics
60. [Docker Action Inputs Using `args`](#60--docker-action-inputs-using-args)

---

## 1. 🧩 Dependent Jobs & `needs`

📌 **What is it?**  
→ A way to make one job wait for another job to finish first.  
→ Uses the `needs:` keyword.

🎯 **Why do we use it?**  
→ To run jobs in a specific ORDER (not all at once)  
→ Example: Deploy only AFTER build succeeds

🛠 **Example**
```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - run: echo "Building app..."

  test:
    needs: build              # 👈 waits for build
    runs-on: ubuntu-latest
    steps:
      - run: echo "Testing app..."

  deploy:
    needs: [build, test]      # 👈 waits for BOTH
    runs-on: ubuntu-latest
    steps:
      - run: echo "Deploying app..."
```

⚠️ **Common Trap**
- ❌ "Dependent jobs run at the same time" — WRONG!
- ❌ "Dependent jobs control concurrency" — WRONG! (that's `concurrency:` key)
- ✅ They define **sequential execution order**

🧠 **Exam Tip:** Primary purpose = define sequential order

🔗 [Official Docs](https://docs.github.com/en/actions/using-jobs/using-jobs-in-a-workflow)

---

## 2. 🧩 Sequential vs Parallel Jobs

📌 **What is it?**  
→ **Parallel**: Jobs run at the same time (default behavior)  
→ **Sequential**: Jobs run one after another (use `needs:`)

🎯 **Why do we use it?**  
→ Parallel = faster  
→ Sequential = when order matters

🛠 **Example**
```yaml
jobs:
  # These run IN PARALLEL (no needs)
  job-a:
    runs-on: ubuntu-latest
    steps:
      - run: echo "A"
  
  job-b:
    runs-on: ubuntu-latest
    steps:
      - run: echo "B"

  # This runs AFTER both (sequential)
  job-c:
    needs: [job-a, job-b]
    runs-on: ubuntu-latest
    steps:
      - run: echo "C"
```

⚠️ **Common Trap**
- Without `needs:`, jobs run in PARALLEL by default
- With `needs:`, jobs become SEQUENTIAL

🧠 **Exam Tip:** No `needs:` = parallel | Has `needs:` = sequential

---

## 3. 🧩 Workflow Concurrency vs Job Dependency

📌 **What is it?**  
→ **Concurrency**: Controls how many workflow RUNS happen at once  
→ **Job Dependency**: Controls order of jobs WITHIN a run

🎯 **Why do we use it?**  
→ Concurrency: Prevent multiple deploys at same time  
→ Dependency: Ensure build before deploy

🛠 **Example**
```yaml
# CONCURRENCY - workflow level
concurrency:
  group: production
  cancel-in-progress: true    # cancels old runs

jobs:
  # DEPENDENCY - job level
  deploy:
    needs: build              # waits for build job
```

⚠️ **Common Trap**
- ❌ `needs:` does NOT control concurrency
- ✅ `concurrency:` controls how many workflows run together
- ✅ `needs:` controls job order within ONE workflow

🧠 **Exam Tip:** `concurrency:` = workflow runs | `needs:` = job order

---

## 4. 🧩 Docker Container Action Purpose

📌 **What is it?**  
→ An action that runs inside a Docker container  
→ You define the OS, tools, dependencies inside the container

🎯 **Why do we use it?**  
→ When you need a **specific Linux version**  
→ When you need **custom tools** not on GitHub runners

🛠 **Example Dockerfile**
```dockerfile
FROM ubuntu:22.04
RUN apt-get update && apt-get install -y python3 curl
COPY entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
```

⚠️ **Common Trap**
- Docker actions are **SLOWER** (need to build/pull image)
- Docker actions only work on **Linux** runners
- ❌ "Actions cannot use custom OS" — FALSE! Docker can!

🧠 **Exam Tip:** Need specific Linux + custom tools? → Docker action

🔗 [Official Docs](https://docs.github.com/en/actions/creating-actions/about-custom-actions)

---

## 5. 🧩 JS vs Docker vs Composite Actions

📌 **What is it?**  
→ Three types of custom actions you can create

| Type | Speed | Isolation | Use Case |
|------|-------|-----------|----------|
| **JavaScript** | ⚡ Fastest | ❌ None | Quick tasks, API calls |
| **Docker** | 🐢 Slowest | ✅ Full | Custom OS, tools |
| **Composite** | ⚡ Fast | ❌ None | Bundle multiple steps |

🎯 **Why do we use it?**  
→ Choose based on your needs

🛠 **Example action.yml for each**
```yaml
# JavaScript Action
runs:
  using: 'node20'
  main: 'index.js'

# Docker Action
runs:
  using: 'docker'
  image: 'Dockerfile'

# Composite Action
runs:
  using: 'composite'
  steps:
    - run: npm install
      shell: bash
```

⚠️ **Common Trap**
- ❌ Composite does NOT provide isolation like Docker
- ❌ JS is NOT good for specific OS requirements
- ✅ Docker = isolated environment

🧠 **Exam Tip:** JS = fast | Docker = isolated | Composite = bundle steps

🔗 [Official Docs](https://docs.github.com/en/actions/creating-actions/about-custom-actions)

---

## 6. 🧩 Composite Actions Purpose

📌 **What is it?**  
→ Bundle multiple steps into ONE reusable action

🎯 **Why do we use it?**  
→ Avoid repeating same steps in multiple workflows  
→ Example: "Setup + Install + Test" becomes one action

🛠 **Example**
```yaml
# action.yml
name: 'Setup and Test'
description: 'Sets up Node and runs tests'
runs:
  using: 'composite'
  steps:
    - uses: actions/setup-node@v4
      with:
        node-version: '20'
    - run: npm ci
      shell: bash
    - run: npm test
      shell: bash
```

⚠️ **Common Trap**
- Composite actions do NOT create isolated environments
- They just bundle steps — they run on the SAME runner
- ❌ NOT for custom OS — use Docker for that

🧠 **Exam Tip:** Composite = combining steps, NOT isolation

🔗 [Official Docs](https://docs.github.com/en/actions/creating-actions/about-custom-actions#composite-actions)

---

## 7. 🧩 Combining Multiple Steps

📌 **What is it?**  
→ Using composite actions to group related steps together

🎯 **Why do we use it?**  
→ Keep workflows clean and DRY (Don't Repeat Yourself)

🛠 **Example — Before vs After**
```yaml
# ❌ BEFORE: Repeating in every workflow
steps:
  - run: npm ci
  - run: npm run lint
  - run: npm test

# ✅ AFTER: One composite action
steps:
  - uses: my-org/setup-and-test@v1
```

⚠️ **Common Trap**
- Each step in composite needs `shell:` specified
- Composite can include other actions with `uses:`

🧠 **Exam Tip:** Composite = streamlined workflow execution

---

## 8. 🧩 Making Scripts Executable (`chmod +x`)

📌 **What is it?**  
→ Give a script file permission to run  
→ Use command: `chmod +x script.sh`

🎯 **Why do we use it?**  
→ Linux/macOS require execute permission to run scripts

🛠 **Example**
```yaml
steps:
  - name: Checkout code
    uses: actions/checkout@v4
  
  - name: Make script executable
    run: chmod +x ./scripts/deploy.sh
  
  - name: Run script
    run: ./scripts/deploy.sh
```

⚠️ **Common Trap**
- ❌ No YAML keyword makes scripts executable
- ❌ `executable: true` — does NOT exist
- ❌ Embedding script with special flag — NOT a thing
- ✅ Must manually run `chmod +x`

🧠 **Exam Tip:** Make executable = `chmod +x` on the runner

🔗 [Official Docs](https://docs.github.com/en/actions/learn-github-actions/essential-features-of-github-actions#adding-scripts-to-your-workflow)

---

## 9. 🧩 Where Scripts Should Be Stored

📌 **What is it?**  
→ Scripts are stored in your repository (not in YAML)

🎯 **Why do we use it?**  
→ Keep workflows clean  
→ Scripts can be versioned with code

🛠 **Example Structure**
```
my-repo/
├── .github/
│   └── workflows/
│       └── ci.yml
├── scripts/
│   ├── deploy.sh
│   └── test.sh
└── src/
    └── app.js
```

```yaml
# In workflow
steps:
  - uses: actions/checkout@v4
  - run: chmod +x ./scripts/deploy.sh
  - run: ./scripts/deploy.sh
```

⚠️ **Common Trap**
- ❌ Scripts are NOT embedded in YAML
- ✅ Scripts are stored in repo and called from YAML

🧠 **Exam Tip:** Scripts in repo → call from workflow

---

## 10. 🧩 Environment Variables in Workflows

📌 **What is it?**  
→ Key-value pairs available during workflow execution  
→ Set using `env:` keyword

🎯 **Why do we use it?**  
→ Pass configuration to scripts  
→ Avoid hardcoding values

🛠 **Example**
```yaml
env:
  APP_NAME: my-app       # Available everywhere

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      NODE_ENV: production   # Available in this job
    steps:
      - name: Print vars
        env:
          DEBUG: true        # Available in this step
        run: |
          echo "App: $APP_NAME"
          echo "Env: $NODE_ENV"
          echo "Debug: $DEBUG"
```

⚠️ **Common Trap**
- Use `$VAR_NAME` in bash (Linux/macOS)
- Use `$env:VAR_NAME` in PowerShell (Windows)

🧠 **Exam Tip:** `env:` sets environment variables at any level

🔗 [Official Docs](https://docs.github.com/en/actions/learn-github-actions/variables#default-environment-variables)

---

## 11. 🧩 Env Precedence (Workflow → Job → Step)

📌 **What is it?**  
→ If same variable name exists at multiple levels, **most specific wins**  
→ Order: Step > Job > Workflow

🎯 **Why do we use it?**  
→ Override variables at more specific levels

🛠 **Example**
```yaml
env:
  NAME: "Workflow"          # Level 1

jobs:
  build:
    runs-on: ubuntu-latest
    env:
      NAME: "Job"           # Level 2 (overrides workflow)
    steps:
      - name: Step 1
        env:
          NAME: "Step"      # Level 3 (overrides job)
        run: echo "$NAME"   # Output: "Step"
      
      - name: Step 2
        run: echo "$NAME"   # Output: "Job" (no step override)
```

⚠️ **Common Trap**
- All levels are accessible in a step
- Same name? Most specific level wins

🧠 **Exam Tip:** Step > Job > Workflow (specific beats general)

---

## 12. 🧩 Accessing Env Vars in Run Commands

📌 **What is it?**  
→ Use `$VAR_NAME` (bash) or `$env:VAR_NAME` (PowerShell)

🎯 **Why do we use it?**  
→ Use variables in your commands

🛠 **Example**
```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    env:
      VERSION: "1.0.0"
    steps:
      # Bash (Linux/macOS)
      - run: echo "Version is $VERSION"
      
      # PowerShell (Windows)
      - run: echo "Version is $env:VERSION"
        shell: pwsh

      # Expression syntax (works everywhere)
      - run: echo "Version is ${{ env.VERSION }}"
```

⚠️ **Common Trap**
- `$VAR` works in bash
- `$env:VAR` works in PowerShell
- `${{ env.VAR }}` works everywhere

🧠 **Exam Tip:** Know the difference: `$VAR` vs `$env:VAR` vs `${{ env.VAR }}`

---

## 13. 🧩 Why Use Variables for Filesystem Paths

📌 **What is it?**  
→ Use `$GITHUB_WORKSPACE` instead of hardcoded paths like `/home/runner/work/...`

🎯 **Why do we use it?**  
→ Different runners have different paths  
→ Variables adapt automatically

🛠 **Example**
```yaml
steps:
  # ✅ GOOD - uses variable
  - run: cd $GITHUB_WORKSPACE && ls -la
  
  # ❌ BAD - hardcoded path
  - run: cd /home/runner/work/my-repo/my-repo && ls -la
```

**Common Variables:**
| Variable | What it is |
|----------|------------|
| `$GITHUB_WORKSPACE` | Repo checkout directory |
| `$GITHUB_ACTION_PATH` | Current action's path |
| `$RUNNER_TEMP` | Temp directory |

⚠️ **Common Trap**
- Hardcoded paths are NOT forbidden
- But they're NOT recommended (not portable)

🧠 **Exam Tip:** Variables = dynamic, adapts to environment

🔗 [Official Docs](https://docs.github.com/en/actions/learn-github-actions/variables#default-environment-variables)

---

## 14. 🧩 Variables vs Hardcoded Paths

📌 **What is it?**  
→ Variables adapt to different environments  
→ Hardcoded paths only work on specific systems

🎯 **Why do we use it?**  
→ Make workflows portable across runners

🛠 **Comparison**
```yaml
# ✅ Portable (works everywhere)
- run: cat $GITHUB_WORKSPACE/config.json

# ❌ Not portable (only works on specific runner)
- run: cat /home/runner/work/my-repo/my-repo/config.json
```

⚠️ **Common Trap**
- GitHub does NOT forbid hardcoded paths
- But variables are RECOMMENDED for flexibility

🧠 **Exam Tip:** Why variables? → Adapt to different runner environments

---

## 15. 🧩 Release Strategy for Actions

📌 **What is it?**  
→ A plan for how you version and release your action updates

🎯 **Why do we use it?**  
→ Users need to know:
  - What version they're using
  - What changed between versions
  - If there are security fixes

🛠 **Example Release Strategy**
```
v1.0.0 - Initial release
v1.1.0 - Added feature X
v1.1.1 - Bug fix for Y
v1.2.0 - Added feature Z
v2.0.0 - Breaking changes (major)
```

⚠️ **Common Trap**
- ❌ "Release without documentation" — BAD!
- ❌ "Release without versioning" — BAD!
- ✅ Always: Version + Changelog + Docs

🧠 **Exam Tip:** Good strategy = versioning + changelog + documentation

🔗 [Official Docs](https://docs.github.com/en/actions/creating-actions/releasing-and-maintaining-actions)

---

## 16. 🧩 Versioning + Changelog Importance

📌 **What is it?**  
→ **Versioning**: Numbers like v1.0.0, v2.1.3  
→ **Changelog**: List of what changed in each version

🎯 **Why do we use it?**  
→ Users know if they should update  
→ Users understand what broke/fixed

🛠 **Example Changelog**
```markdown
# Changelog

## v2.0.0 (2024-01-15)
### ⚠️ Breaking Changes
- Changed input `file` to `files` (now accepts array)

### ✨ New Features
- Added support for multiple files

## v1.1.0 (2024-01-10)
### ✨ New Features
- Added `timeout` input

## v1.0.0 (2024-01-01)
- Initial release
```

⚠️ **Common Trap**
- Changelog WITHOUT versions = still confusing
- Must have BOTH versioning AND changelog

🧠 **Exam Tip:** Versioning alone isn't enough — need changelog too

---

## 17. 🧩 Major Version Updates

📌 **What is it?**  
→ Changes that BREAK existing usage (v1 → v2)  
→ Called "breaking changes"

🎯 **Why do we use it?**  
→ Signal to users: "You may need to update your workflow"

🛠 **Example**
```yaml
# v1.x - old way
- uses: my-action@v1
  with:
    file: single-file.txt

# v2.x - new way (breaking change!)
- uses: my-action@v2
  with:
    files: ['file1.txt', 'file2.txt']  # changed from 'file' to 'files'
```

⚠️ **Common Trap**
- Major version = breaking changes
- Users on `@v1` won't auto-update to `@v2`

🧠 **Exam Tip:** Major update = breaking changes = user action needed

---

## 18. 🧩 Security Patches

📌 **What is it?**  
→ Fixes for security vulnerabilities  
→ Usually released as patch versions (v1.0.0 → v1.0.1)

🎯 **Why do we use it?**  
→ Keep users safe  
→ Should be applied quickly

🛠 **Example**
```
v1.2.3 - Security patch: Fixed credential exposure in logs
v1.2.4 - Security patch: Updated vulnerable dependency
```

⚠️ **Common Trap**
- Security patches should be documented
- Users need to know WHY they should update

🧠 **Exam Tip:** Security patches = document + notify users

---

## 19. 🧩 Commit SHA Versioning

📌 **What is it?**  
→ Pin action to a specific commit using its SHA hash (40 characters)

🎯 **Why do we use it?**  
→ **Most secure** way to pin versions  
→ SHA cannot be changed or deleted

🛠 **Example**
```yaml
# ✅ MOST SECURE - Full SHA (40 chars)
- uses: actions/checkout@8ade135a41bc03ea155e62e844d188df1ea18608

# ⚠️ MEDIUM - Tag (can be moved)
- uses: actions/checkout@v4

# ❌ LEAST SECURE - Branch (changes constantly)
- uses: actions/checkout@main
```

⚠️ **Common Trap**
- ❌ Abbreviated SHA — NOT recommended
- ✅ Full 40-character SHA — recommended

🧠 **Exam Tip:** Most secure = **full commit SHA** (not abbreviated!)

🔗 [Official Docs](https://docs.github.com/en/actions/creating-actions/releasing-and-maintaining-actions)

---

## 20. 🧩 Why Tags Are Risky

📌 **What is it?**  
→ Tags CAN be deleted or moved to different commits

🎯 **Why do we use it?**  
→ Understanding why SHA is more secure

🛠 **Example Risk**
```
Day 1: v1.0.0 tag points to commit ABC123
Day 2: Maintainer deletes v1.0.0 tag
Day 3: Maintainer creates v1.0.0 tag pointing to commit XYZ789

Your workflow now runs DIFFERENT code!
```

⚠️ **Common Trap**
- Tags are USEFUL and COMMON
- But they're NOT immutable like SHA

🧠 **Exam Tip:** Tags can be deleted/moved | SHA is permanent

---

## 21. 🧩 GitHub Packages Overview

📌 **What is it?**  
→ GitHub's package hosting service  
→ Host packages alongside your code

🎯 **Why do we use it?**  
→ One place for code AND packages  
→ Integrated with GitHub permissions

🛠 **Example Usage**
```yaml
# Publish npm package to GitHub Packages
- name: Publish
  run: npm publish
  env:
    NODE_AUTH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

⚠️ **Common Trap**
- GitHub Packages ≠ npm registry (different URL)
- Uses `npm.pkg.github.com` not `registry.npmjs.org`

🧠 **Exam Tip:** GitHub Packages = host packages with your code

🔗 [Official Docs](https://docs.github.com/en/packages/learn-github-packages/introduction-to-github-packages)

---

## 22. 🧩 Supported Package Managers

📌 **What is it?**  
→ List of package managers GitHub Packages supports

| ✅ Supported | ❌ NOT Supported |
|-------------|-----------------|
| npm (Node.js) | RPM (Linux) |
| NuGet (.NET) | apt (Debian) |
| Maven (Java) | yum (RedHat) |
| Gradle (Java) | |
| RubyGems (Ruby) | |
| Docker | |

🎯 **Why do we use it?**  
→ Know what you can/cannot host

⚠️ **Common Trap**
- ❌ **RPM is NOT supported** (exam favorite!)
- ✅ npm, NuGet, Maven, Gradle, RubyGems, Docker

🧠 **Exam Tip:** Remember: RPM = ❌ NOT supported

🔗 [Official Docs](https://docs.github.com/en/packages/learn-github-packages/introduction-to-github-packages#about-github-packages)

---

## 23. 🧩 Counting Commands in Steps

📌 **What is it?**  
→ Count INDIVIDUAL commands, not just steps

🎯 **Why do we use it?**  
→ Exam asks "how many commands execute?"

🛠 **Example**
```yaml
steps:
  - name: Step 1 (has 2 commands)
    shell: pwsh
    run: |
      Set-PSRepository PSGallery -InstallationPolicy Trusted   # Command 1
      Install-Module PSScriptAnalyzer -ErrorAction Stop        # Command 2
  
  - name: Step 2 (has 1 command)
    shell: pwsh
    run: Get-ScriptAnalyzerRule                                # Command 3

# Answer: 3 PowerShell commands total
# (NOT 2 steps — count the actual commands!)
```

⚠️ **Common Trap**
- Don't count STEPS
- Count individual COMMANDS inside steps
- A step with `run: |` can have multiple commands

🧠 **Exam Tip:** Read carefully — count commands, not steps

🔗 [Official Docs](https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners/about-github-hosted-runners)

---

## 24. 🧩 Marketplace Requirement: Public Repo

📌 **What is it?**  
→ To publish action to Marketplace, repo MUST be public

🎯 **Why do we use it?**  
→ Marketplace actions are for everyone to use

🛠 **Requirements Checklist**
```
✅ Repository must be PUBLIC
✅ Only ONE action per repository
✅ action.yml in ROOT directory
✅ Unique name on Marketplace
```

⚠️ **Common Trap**
- ❌ Private repo = cannot publish to Marketplace
- ❌ Multiple actions in repo = cannot publish
- ✅ Public + Single action + Root metadata = OK

🧠 **Exam Tip:** Marketplace = PUBLIC repo only

🔗 [Official Docs](https://docs.github.com/en/actions/creating-actions/publishing-actions-in-github-marketplace)

---

## 25. 🧩 Metadata File Location (`action.yml`)

📌 **What is it?**  
→ The `action.yml` file MUST be in repository ROOT for Marketplace

🎯 **Why do we use it?**  
→ GitHub looks for it in root directory

🛠 **Correct Structure**
```
my-action/
├── action.yml        ✅ In ROOT
├── index.js
├── package.json
└── README.md
```

```
my-action/
├── src/
│   └── action.yml    ❌ In subdirectory (won't work for Marketplace)
└── README.md
```

⚠️ **Common Trap**
- ❌ Subdirectory = won't work for Marketplace publishing
- ✅ Root directory = required for Marketplace

🧠 **Exam Tip:** Marketplace publishing = `action.yml` must be in ROOT

🔗 [Official Docs](https://docs.github.com/en/actions/creating-actions/metadata-syntax-for-github-actions)

---

## 26. 🧩 Unique Naming Rules in Marketplace

📌 **What is it?**  
→ Your action name must be unique and follow rules

🎯 **Why do we use it?**  
→ Avoid conflicts with existing names

🛠 **Naming Rules**
| ✅ Allowed | ❌ NOT Allowed |
|-----------|----------------|
| Unique name | Name matching existing Marketplace action |
| | Name matching Marketplace category |
| | Name matching GitHub username/org |
| | Name matching GitHub feature |

⚠️ **Common Trap**
- Can't name your action "github" (reserved)
- Can't name it after a category like "deployment"
- Must be unique across entire Marketplace

🧠 **Exam Tip:** Name must NOT match: existing action, category, user, org, or GitHub feature

🔗 [Official Docs](https://docs.github.com/en/actions/creating-actions/publishing-actions-in-github-marketplace)

---

## 27. 🧩 Marketplace Category Selection

📌 **What is it?**  
→ When publishing, you select a category for your action

🎯 **Why do we use it?**  
→ Helps users find your action

🛠 **Example Categories**
- Continuous Integration
- Deployment
- Testing
- Code Quality
- Security
- Utilities
- Monitoring

⚠️ **Common Trap**
- Category is selected DURING publishing
- 2FA (two-factor authentication) IS required to publish
- Action name must NOT match category name

🧠 **Exam Tip:** Publishing requires: 2FA + category selection

🔗 [Official Docs](https://docs.github.com/en/actions/creating-actions/publishing-actions-in-github-marketplace)

---

## 28. 🧩 Scoped Installation Tokens

📌 **What is it?**  
→ Temporary token GitHub creates for runners  
→ Used to download actions from private/internal repos

🎯 **Why do we use it?**  
→ Secure way to access private actions  
→ No need for manual authentication

🛠 **How It Works**
```
1. Workflow starts
2. GitHub auto-creates scoped installation token
3. Token has READ access only
4. Runner uses token to download actions
5. Token expires after 1 hour
```

⚠️ **Common Trap**
- ❌ NOT a personal access token (PAT)
- ❌ NOT direct repository access
- ❌ NOT user credential prompt
- ✅ Auto-generated, read-only, expires in 1 hour

🧠 **Exam Tip:** Scoped token = auto-created, read-only, 1-hour expiry

🔗 [Official Docs](https://docs.github.com/en/actions/creating-actions/sharing-actions-and-workflows-with-your-organization)

---

## 29. 🧩 Token Expiry (1 Hour)

📌 **What is it?**  
→ Scoped installation tokens expire after 1 hour

🎯 **Why do we use it?**  
→ Security — limits exposure if token is compromised

🛠 **Timeline**
```
0:00 - Token created
0:30 - Action downloaded ✅
0:45 - Workflow still running ✅
1:00 - Token expires
1:01 - Token no longer works ❌
```

⚠️ **Common Trap**
- Token is for downloading actions, not for your scripts
- If workflow runs longer than 1 hour, token may expire

🧠 **Exam Tip:** Remember: 1 hour expiry

---

## 30. 🧩 Event Filtering Using `types`

📌 **What is it?**  
→ Filter which activity types trigger your workflow

🎯 **Why do we use it?**  
→ Run only for specific activities (not all)

🛠 **Example**
```yaml
on:
  issues:
    types: [opened, edited]       # Only these trigger
    # Ignores: closed, deleted, labeled, etc.

  pull_request:
    types: [opened, synchronize]  # Only these trigger
    # Ignores: closed, merged, etc.
```

⚠️ **Common Trap**
- ❌ `notTypes:` — does NOT exist
- ❌ `exclude:` — does NOT exist
- ✅ Only list what you WANT in `types:`

🧠 **Exam Tip:** To exclude events, just don't include them

🔗 [Official Docs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#onevent_nametypes)

---

## 31. 🧩 `on.branch_protection_rule.types`

📌 **What is it?**  
→ Filter branch protection rule events by type

🎯 **Why do we use it?**  
→ React only to specific changes (created/edited, not deleted)

🛠 **Example**
```yaml
# Run only when rules are created or edited
on:
  branch_protection_rule:
    types: [created, edited]      # ✅ Excludes 'deleted'

# Run only when rules are deleted
on:
  branch_protection_rule:
    types: [deleted]              # Only deletions
```

⚠️ **Common Trap**
- Available types: `created`, `edited`, `deleted`
- To exclude deleted: only include `[created, edited]`

🧠 **Exam Tip:** No `notTypes:` — just list what you want

🔗 [Official Docs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#onevent_nametypes)

---

## 32. 🧩 Action Metadata File Keys

📌 **What is it?**  
→ Keys you can use in `action.yml`

🎯 **Why do we use it?**  
→ Define your action's interface

🛠 **Common Keys**
```yaml
name: 'My Action'                    # Required
description: 'What it does'          # Required
author: 'Your Name'                  # Optional

inputs:                              # Optional
  my-input:
    description: 'Input description'
    required: true
    default: 'default-value'

outputs:                             # Optional
  my-output:
    description: 'Output description'

runs:                                # Required
  using: 'node20'
  main: 'index.js'

branding:                            # Optional (for Marketplace)
  icon: 'check-circle'
  color: 'green'
```

⚠️ **Common Trap**
- `name`, `description`, `runs` are REQUIRED
- `inputs`, `outputs`, `branding` are OPTIONAL

🧠 **Exam Tip:** Required = name + description + runs

🔗 [Official Docs](https://docs.github.com/en/actions/creating-actions/metadata-syntax-for-github-actions)

---

## 33. 🧩 `outputs` in Custom Actions

📌 **What is it?**  
→ Data your action produces that other steps can use

🎯 **Why do we use it?**  
→ Share results with subsequent steps/jobs

🛠 **Example**
```yaml
# action.yml
outputs:
  result:
    description: 'The calculation result'
  status:
    description: 'Success or failure'
```

```yaml
# In workflow
- name: Run my action
  id: myaction
  uses: ./my-action

- name: Use output
  run: echo "Result was ${{ steps.myaction.outputs.result }}"
```

⚠️ **Common Trap**
- ❌ `environment` — NOT for passing data
- ❌ `runs` — defines HOW action runs
- ❌ `description` — just describes the action
- ✅ `outputs` — for passing data

🧠 **Exam Tip:** Pass data between steps → use `outputs`

🔗 [Official Docs](https://docs.github.com/en/actions/creating-actions/metadata-syntax-for-github-actions)

---

## 34. 🧩 Passing Data Between Steps

📌 **What is it?**  
→ Use outputs to share data from one step to another

🎯 **Why do we use it?**  
→ Step 2 needs result from Step 1

🛠 **Example**
```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Set output
        id: step1
        run: echo "version=1.0.0" >> $GITHUB_OUTPUT

      - name: Use output
        run: echo "Version is ${{ steps.step1.outputs.version }}"
```

⚠️ **Common Trap**
- Must use `id:` on the step that sets output
- Access with `${{ steps.STEP_ID.outputs.OUTPUT_NAME }}`

🧠 **Exam Tip:** Steps need `id:` to share outputs

---

## 35. 🧩 Required Files for Custom Action

📌 **What is it?**  
→ Files needed to create a custom action

🎯 **Why do we use it?**  
→ Know what to include in your action repo

🛠 **Required Files by Type**
```
# JavaScript Action
├── action.yml          ✅ Required
├── index.js            ✅ Required (entry point)
├── node_modules/       ✅ Required (or use ncc to bundle)
└── package.json        ✅ Required

# Docker Action
├── action.yml          ✅ Required
├── Dockerfile          ✅ Required
└── entrypoint.sh       ✅ Required (or similar)

# Composite Action
└── action.yml          ✅ Required (contains all steps)
```

⚠️ **Common Trap**
- `action.yml` is ALWAYS required (any action type)
- JS needs entry file + dependencies
- Docker needs Dockerfile + entrypoint

🧠 **Exam Tip:** All actions need `action.yml` at minimum

---

## 36. 🧩 Using Secrets as Environment Variables

📌 **What is it?**  
→ Make secrets available as environment variables in steps

🎯 **Why do we use it?**  
→ Scripts can read secrets from `$ENV_VAR`

🛠 **Example**
```yaml
steps:
  - name: Deploy
    env:
      API_KEY: ${{ secrets.API_KEY }}
      DB_PASSWORD: ${{ secrets.DB_PASSWORD }}
    run: |
      ./deploy.sh
      # Script uses $API_KEY and $DB_PASSWORD
```

⚠️ **Common Trap**
- Don't pass secrets as command arguments (visible in logs)
- Use env vars instead

🧠 **Exam Tip:** Secrets → env vars → script reads env

---

## 37. 🧩 Correct Syntax: `${{ secrets.NAME }}`

📌 **What is it?**  
→ The exact syntax to access secrets

🎯 **Why do we use it?**  
→ Exam tests if you know the correct syntax

🛠 **Correct vs Wrong**
```yaml
# ✅ CORRECT
env:
  KEY: ${{ secrets.API_KEY }}

# ❌ WRONG - missing 'secrets.'
env:
  KEY: ${{ API_KEY }}

# ❌ WRONG - extra 'environment'
env:
  KEY: ${{ secrets.environment.API_KEY }}

# ❌ WRONG - plain text
env:
  KEY: API_KEY
```

⚠️ **Common Trap**
- Must have `secrets.` prefix
- Case sensitive: `secrets.API_KEY` not `Secrets.API_KEY`

🧠 **Exam Tip:** Always: `${{ secrets.SECRET_NAME }}`

🔗 [Official Docs](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions)

---

## 38. 🧩 Why NOT Pass Secrets via CLI

📌 **What is it?**  
→ Don't put secrets directly in command line arguments

🎯 **Why do we use it?**  
→ CLI commands can appear in:
  - Process lists (`ps aux`)
  - System logs
  - Audit events

🛠 **Example**
```yaml
# ❌ BAD - visible in process list and logs
- run: ./deploy.sh --password=${{ secrets.PASS }}

# ✅ GOOD - hidden in environment
- env:
    PASS: ${{ secrets.PASS }}
  run: ./deploy.sh   # script reads $PASS internally
```

⚠️ **Common Trap**
- ❌ "CLI auto-redacts secrets" — FALSE!
- ❌ "Passing via CLI is safe" — FALSE!
- CLI arguments are NOT automatically hidden

🧠 **Exam Tip:** Secrets via CLI = visible to others = bad

🔗 [Official Docs](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions#using-secrets-in-a-workflow)

---

## 39. 🧩 Command Line Exposure Risk

📌 **What is it?**  
→ Command line arguments can be seen by:
  - Other users on same system
  - System logs
  - Security audit tools

🎯 **Why do we use it?**  
→ Understand WHY env vars are safer

🛠 **Example Risk**
```bash
# If someone runs 'ps aux' while your command runs:
user   12345  ./deploy.sh --password=MySecretPass123

# They can see your password!
```

⚠️ **Common Trap**
- Even on GitHub runners, logs can expose secrets
- Always use environment variables for secrets

🧠 **Exam Tip:** CLI exposure = other users can see your secrets

---

## 40. 🧩 Custom Labels for Self-Hosted Runners

📌 **What is it?**  
→ Tags you add to identify runner capabilities

🎯 **Why do we use it?**  
→ **Route jobs** to specific runners based on needs

🛠 **Example**
```yaml
jobs:
  gpu-task:
    runs-on: [self-hosted, linux, gpu]   # needs GPU
    steps:
      - run: python train_model.py

  arm-task:
    runs-on: [self-hosted, linux, arm64] # needs ARM
    steps:
      - run: ./build-arm.sh
```

⚠️ **Common Trap**
- ❌ NOT for code readability
- ❌ NOT for security enhancement
- ❌ NOT for naming jobs
- ✅ For ROUTING jobs to correct runners

🧠 **Exam Tip:** Labels = routing, not security or names

🔗 [Official Docs](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/using-self-hosted-runners-in-a-workflow#using-custom-labels-to-route-jobs)

---

## 41. 🧩 Routing Jobs Using Labels

📌 **What is it?**  
→ Match `runs-on` labels with runner labels

🎯 **Why do we use it?**  
→ Ensure jobs run on correct runners

🛠 **Example**
```yaml
# Job needs ALL these labels
runs-on: [self-hosted, linux, x64, gpu]

# Runner must have ALL labels to be selected:
# - self-hosted ✅
# - linux ✅
# - x64 ✅
# - gpu ✅
```

⚠️ **Common Trap**
- ALL labels must match (AND logic, not OR)
- If runner missing any label, job won't run there

🧠 **Exam Tip:** All labels must match = AND logic

---

## 42. 🧩 Proxy Config for Runners (`https_proxy`)

📌 **What is it?**  
→ Environment variable for runners behind a firewall

🎯 **Why do we use it?**  
→ Let runner communicate with GitHub through proxy

🛠 **Example**
```bash
# Set on the runner machine
export https_proxy=http://proxy.company.com:8080
export http_proxy=http://proxy.company.com:8080
export no_proxy=localhost,127.0.0.1
```

⚠️ **Common Trap**
- ❌ `proxy_server` — NOT valid
- ❌ `network_proxy` — NOT valid
- ❌ `outbound` — NOT valid
- ✅ `https_proxy` — CORRECT (lowercase!)

🧠 **Exam Tip:** Proxy variable = `https_proxy`

🔗 [Official Docs](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/using-a-proxy-server-with-self-hosted-runners)

---

## 43. 🧩 GitHub-Hosted vs Self-Hosted Runners

📌 **What is it?**  
→ Two types of machines that run your workflows

| Feature | GitHub-Hosted | Self-Hosted |
|---------|--------------|-------------|
| Managed by | GitHub | You |
| Cost | Included/Pay per minute | Your hardware |
| Setup | Zero | Manual |
| Customization | Limited | Full control |
| OS options | Ubuntu, Windows, macOS | Any |

🎯 **Why do we use it?**  
→ Choose based on needs

⚠️ **Common Trap**
- GitHub-hosted: clean environment every time
- Self-hosted: may have cached data (faster but needs cleanup)

🧠 **Exam Tip:** GitHub-hosted = managed | Self-hosted = your control

🔗 [Official Docs](https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners/about-github-hosted-runners)

---

## 44. 🧩 When to Mix Both Runners

📌 **What is it?**  
→ Use GitHub-hosted AND self-hosted in same workflow

🎯 **Why do we use it?**  
→ **Resource-intensive tasks** on self-hosted  
→ Simple tasks on GitHub-hosted

🛠 **Example**
```yaml
jobs:
  lint:
    runs-on: ubuntu-latest        # GitHub-hosted (simple)
    steps:
      - run: npm run lint

  train-ml:
    runs-on: [self-hosted, gpu]   # Self-hosted (needs GPU)
    steps:
      - run: python train.py

  deploy:
    runs-on: ubuntu-latest        # GitHub-hosted (simple)
    needs: [lint, train-ml]
    steps:
      - run: ./deploy.sh
```

⚠️ **Common Trap**
- ❌ NOT for "highest security"
- ❌ NOT for "personal projects"
- ❌ NOT for "short-lived jobs"
- ✅ For resource-intensive tasks (GPU, memory, etc.)

🧠 **Exam Tip:** Mix runners = balance cost + performance

🔗 [Official Docs](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/about-self-hosted-runners)

---

## 45. 🧩 Runner Groups (Enterprise Cost Control)

📌 **What is it?**  
→ Organize runners into groups with access controls

🎯 **Why do we use it?**  
→ **Chargeback model**: Each team pays for their runners  
→ Prevent dev using prod runners

🛠 **Example**
```
Organization
├── Runner Group: "Production"
│   ├── prod-runner-1
│   └── prod-runner-2
│   └── Access: Production repos only
│
└── Runner Group: "Development"
    ├── dev-runner-1
    └── dev-runner-2
    └── Access: Development repos only
```

⚠️ **Common Trap**
- **Labels** = routing (which runner)
- **Groups** = access control (who can use)
- ❌ `runner sets` — does NOT exist

🧠 **Exam Tip:** Labels = routing | Groups = access control

🔗 [Official Docs](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/managing-access-to-self-hosted-runners-using-groups)

---

## 46. 🧩 Artifact Retention

📌 **What is it?**  
→ How long GitHub keeps your uploaded artifacts

🎯 **Why do we use it?**  
→ Control storage and keep important files

🛠 **Default Retention**
```
Public repos: 90 days
Private repos: 90 days (can be configured)
```

⚠️ **Common Trap**
- ❌ "Cannot customize retention" — FALSE!
- ✅ Can customize per artifact, repo, org, or enterprise

🧠 **Exam Tip:** Retention IS customizable at multiple levels

🔗 [Official Docs](https://docs.github.com/en/actions/using-workflows/storing-workflow-data-as-artifacts)

---

## 47. 🧩 `actions/upload-artifact` Usage

📌 **What is it?**  
→ Official action to save files from workflow

🎯 **Why do we use it?**  
→ Keep build outputs, test results, logs

🛠 **Example**
```yaml
- name: Build
  run: npm run build

- name: Upload build
  uses: actions/upload-artifact@v4
  with:
    name: build-output
    path: dist/
```

⚠️ **Common Trap**
- `path` is required
- `name` defaults to "artifact" if not specified
- Can upload files or folders

🧠 **Exam Tip:** Upload artifact = `actions/upload-artifact`

---

## 48. 🧩 Custom Retention Days

📌 **What is it?**  
→ Set how long to keep specific artifacts

🎯 **Why do we use it?**  
→ Important artifacts: keep longer  
→ Temp artifacts: keep shorter (save storage)

🛠 **Example**
```yaml
# Keep for 5 days
- uses: actions/upload-artifact@v4
  with:
    name: temp-logs
    path: logs/
    retention-days: 5

# Keep for 30 days
- uses: actions/upload-artifact@v4
  with:
    name: release-build
    path: dist/
    retention-days: 30
```

⚠️ **Common Trap**
- Can't exceed org/enterprise max retention
- `retention-days` is per-artifact setting

🧠 **Exam Tip:** Use `retention-days` in upload-artifact

---

## 49. 🧩 YAML `on` Keyword

📌 **What is it?**  
→ The keyword that defines WHEN workflow runs

🎯 **Why do we use it?**  
→ Without `on:`, workflow won't know when to trigger

🛠 **Example**
```yaml
on:
  push:                    # On push
  pull_request:            # On PR
  workflow_dispatch:       # Manual trigger
  schedule:                # Cron schedule
    - cron: '0 0 * * *'
```

⚠️ **Common Trap**
- ❌ `trigger:` — does NOT exist
- ❌ `event:` — does NOT exist
- ❌ `workflow:` — for name, not triggers
- ✅ `on:` — ONLY valid keyword

🧠 **Exam Tip:** Only `on:` is valid for triggers

🔗 [Official Docs](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows)

---

## 50. 🧩 Workflow Triggers (push, issues, discussion)

📌 **What is it?**  
→ Events that can trigger workflows

🎯 **Why do we use it?**  
→ Automate based on what happens

🛠 **Common Triggers**
```yaml
on:
  # Code events
  push:
  pull_request:
  
  # Issue events
  issues:
    types: [opened, edited, closed]
  
  # Discussion events
  discussion:
    types: [created, answered]
  
  # Manual
  workflow_dispatch:
  
  # Scheduled
  schedule:
    - cron: '0 0 * * *'
  
  # Other workflows
  workflow_run:
    workflows: ["Build"]
    types: [completed]
```

⚠️ **Common Trap**
- Different events have different `types`
- Some events don't have types (like `push`)

🧠 **Exam Tip:** Know common events: push, PR, issues, schedule

---

## 51. 🧩 Scheduled Workflow Minimum Time (5 min)

📌 **What is it?**  
→ Shortest interval for scheduled workflows is 5 minutes

🎯 **Why do we use it?**  
→ Can't schedule more frequently than every 5 minutes

🛠 **Example**
```yaml
on:
  schedule:
    # ✅ Every 5 minutes (minimum)
    - cron: '*/5 * * * *'
    
    # ❌ Every 1 minute (will be throttled)
    - cron: '* * * * *'
```

⚠️ **Common Trap**
- Can schedule every 5 min, but runs may be delayed during high load
- Peak times may have delays

🧠 **Exam Tip:** Minimum schedule interval = 5 minutes

---

## 52. 🧩 Matrix Strategy

📌 **What is it?**  
→ Create multiple jobs from combinations of values

🎯 **Why do we use it?**  
→ Test on multiple OS, versions, etc. without duplicate code

🛠 **Example**
```yaml
jobs:
  test:
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]
        node: [18, 20]
    runs-on: ${{ matrix.os }}
    steps:
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node }}
      - run: npm test
```

⚠️ **Common Trap**
- Each combination = separate job
- Values are accessed with `${{ matrix.KEY }}`

🧠 **Exam Tip:** Matrix creates job per combination

🔗 [Official Docs](https://docs.github.com/en/actions/using-jobs/using-a-matrix-for-your-jobs)

---

## 53. 🧩 Job Count = Combinations Logic

📌 **What is it?**  
→ Total jobs = **multiply** all matrix axes

🎯 **Why do we use it?**  
→ Exam asks "how many jobs?"

🛠 **Example**
```yaml
strategy:
  matrix:
    os: [ubuntu, windows, macos]   # 3 values
    node: [18, 20]                  # 2 values
    
# Total = 3 × 2 = 6 jobs
```

```yaml
strategy:
  matrix:
    animal: [cat, dog, bear]       # 3 values
    color: [black, brown]          # 2 values
    
# Total = 3 × 2 = 6 jobs
```

⚠️ **Common Trap**
- Don't ADD values — MULTIPLY them
- `[a,b,c] × [x,y] = 3 × 2 = 6`

🧠 **Exam Tip:** Jobs = axis1 × axis2 × axis3...

---

## 54. 🧩 Workflow Templates

📌 **What is it?**  
→ Pre-made workflows your org can reuse

🎯 **Why do we use it?**  
→ Standardize automation across repos  
→ New repos get consistent CI/CD

🛠 **Setup**
```
.github repository (org level)
└── workflow-templates/
    ├── my-ci.yml
    └── my-ci.properties.json
```

⚠️ **Common Trap**
- Must be in `.github` repo of organization
- Must be in `workflow-templates/` folder
- Need properties file for metadata

🧠 **Exam Tip:** Templates = org's `.github` repo + `workflow-templates/`

🔗 [Official Docs](https://docs.github.com/en/actions/using-workflows/creating-starter-workflows-for-your-organization)

---

## 55. 🧩 Reusing Automation in Org

📌 **What is it?**  
→ Share workflows across organization repos

🎯 **Why do we use it?**  
→ Consistency  
→ Less duplication  
→ Easier maintenance

🛠 **Methods**
```yaml
# 1. Workflow Templates (starter workflows)
# Stored in org's .github repo

# 2. Reusable Workflows (call other workflows)
jobs:
  call-workflow:
    uses: my-org/shared-workflows/.github/workflows/ci.yml@main

# 3. Composite Actions (bundle steps)
- uses: my-org/my-action@v1
```

⚠️ **Common Trap**
- ❌ Naming conventions alone don't ensure reuse
- ❌ GitHub wiki is documentation, not automation
- ❌ Contribution guidelines are for collaboration rules
- ✅ Workflow templates = actual reuse mechanism

🧠 **Exam Tip:** Enterprise reuse = workflow templates

---

## 56. 🧩 Workflow Badges (Private Repo Limitation)

📌 **What is it?**  
→ Status badges showing pass/fail

🎯 **Why do we use it?**  
→ Show build status on README

🛠 **Public vs Private**
```markdown
# Public repo - badge works everywhere
![CI](https://github.com/user/repo/actions/workflows/ci.yml/badge.svg)

# Private repo - badge only works for authenticated users
# External embedding is BLOCKED
```

⚠️ **Common Trap**
- ❌ "Badge visibility disabled by default" — NOT the reason
- ✅ Security: prevent external embedding from unauthorized sources

🧠 **Exam Tip:** Private badges blocked = security feature

---

## 57. 🧩 Where to View PR Workflow Status

📌 **What is it?**  
→ Multiple places to see if PR checks passed/failed

🎯 **Why do we use it?**  
→ Know where to look for status

🛠 **Locations**
```
1. PR page → Checks section (bottom)
2. PR page → Conversation tab → Status checks
3. Actions tab → Click workflow run
4. Commits → Status icon next to commit
```

⚠️ **Common Trap**
- All these show same info, different views
- "Checks" tab shows detailed results

🧠 **Exam Tip:** PR status visible in: PR page, Checks tab, Actions tab

---

## 58. 🧩 Filtering Failed Runs (`failure`)

📌 **What is it?**  
→ Filter workflow runs to show only failures

🎯 **Why do we use it?**  
→ Quickly find what's broken

🛠 **How to Filter**
```
Actions tab → Status dropdown → Select "failure"

Or use URL:
https://github.com/user/repo/actions?query=is%3Afailure
```

⚠️ **Common Trap**
- Can filter by: success, failure, cancelled, skipped
- Filter applies to the run status, not job status

🧠 **Exam Tip:** Filter failed = status dropdown or query

---

## 59. 🧩 Approval Timeout (30 Days → Fail)

📌 **What is it?**  
→ If environment approval isn't given within 30 days, deployment fails

🎯 **Why do we use it?**  
→ Prevents stale deployments waiting forever

🛠 **How it Works**
```
1. Workflow reaches deployment to protected environment
2. Waits for approval
3. Day 30: Still no approval
4. Workflow automatically fails
```

⚠️ **Common Trap**
- 30 days is the MAXIMUM wait time
- After timeout, must re-run workflow

🧠 **Exam Tip:** Approval timeout = 30 days max

---

## 60. 🧩 Docker Action Inputs Using `args`

📌 **What is it?**  
→ Pass inputs to Docker container via `args`

🎯 **Why do we use it?**  
→ Docker entrypoint receives these as command line arguments

🛠 **Example**
```yaml
# action.yml
inputs:
  name:
    description: 'Name to greet'
    required: true
runs:
  using: 'docker'
  image: 'Dockerfile'
  args:
    - ${{ inputs.name }}
```

```dockerfile
# Dockerfile
FROM alpine
COPY entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
```

```bash
# entrypoint.sh
#!/bin/sh
echo "Hello $1!"   # $1 receives the 'name' input
```

⚠️ **Common Trap**
- `args` passes to ENTRYPOINT
- Order matters — first arg is `$1`, second is `$2`, etc.

🧠 **Exam Tip:** Docker inputs via `args` → become positional arguments

---

## 📝 Master Quick Reference

| # | Concept | Key Point |
|---|---------|-----------|
| 1 | `needs:` | Sequential job order |
| 2 | Parallel/Sequential | No needs = parallel, has needs = sequential |
| 3 | Concurrency vs Dependency | `concurrency:` = runs, `needs:` = job order |
| 4 | Docker action | Custom OS + tools, slower |
| 5 | JS vs Docker vs Composite | JS=fast, Docker=isolated, Composite=bundle |
| 6-7 | Composite | Bundle steps, no isolation |
| 8-9 | Scripts | `chmod +x`, store in repo |
| 10-14 | Env vars | Step > Job > Workflow, use variables |
| 15-20 | Versioning | SHA most secure, tags can move |
| 21-22 | GitHub Packages | npm/NuGet/Maven/Gradle/Docker ✅, RPM ❌ |
| 23 | Command count | Count commands, not steps |
| 24-27 | Marketplace | Public + 1 action + root + unique + 2FA |
| 28-29 | Scoped tokens | Auto, read-only, 1 hour expiry |
| 30-31 | Event filtering | Use `types:`, no `notTypes:` |
| 32-35 | Action metadata | `action.yml` required, `outputs` for data |
| 36-39 | Secrets | `${{ secrets.NAME }}`, never CLI |
| 40-45 | Runners | Labels=routing, Groups=access, `https_proxy` |
| 46-48 | Artifacts | `retention-days` customizable |
| 49-51 | Triggers | `on:` only, 5 min minimum schedule |
| 52-53 | Matrix | Multiply axes for job count |
| 54-55 | Templates | Org's `.github/workflow-templates/` |
| 56-58 | Status | Private badges blocked, filter by failure |
| 59 | Approval | 30 days max timeout |
| 60 | Docker args | Inputs become positional arguments |

---

> 🎯 **Final Study Tip:** Focus on ⚠️ Common Traps — exams love testing what something is **NOT**!

Good luck with your test! 🚀
