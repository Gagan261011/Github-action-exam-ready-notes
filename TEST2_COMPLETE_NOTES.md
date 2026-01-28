# 📘 GitHub Actions – Complete Concept Notes (Test 2 Preparation)

> **Last Updated:** January 28, 2026  
> **Source:** Test2.txt  
> **Total Concepts:** 60

---

## 🔹 How to Use These Notes

1. **Read each concept** → Understand the definition and purpose first
2. **Study the example** → See how it works in real YAML
3. **Focus on traps & tips** → These are what exams test!

---

## 📑 Clickable Table of Contents

| # | Concept | # | Concept |
|---|---------|---|---------|
| 1 | [Exit codes in Docker actions](#1--exit-codes-in-docker-actions) | 31 | [Workflow commands purpose](#31--workflow-commands-purpose) |
| 2 | [Meaning of exit 1](#2--meaning-of-exit-1) | 32 | [Action metadata YAML syntax](#32--action-metadata-yaml-syntax) |
| 3 | [Marketplace purpose](#3--marketplace-purpose) | 33 | [Custom action types](#33--custom-action-types) |
| 4 | [Publishing action to Marketplace](#4--publishing-action-to-marketplace-release) | 34 | [Storing secrets larger than 48KB](#34--storing-secrets-larger-than-48kb) |
| 5 | [Static IP large runners for allowlist](#5--static-ip-large-runners-for-allowlist) | 35 | [Approval/rejection in workflows](#35--approvalrejection-in-workflows) |
| 6 | [Workflow trigger for feature branch](#6--workflow-trigger-for-feature-branch) | 36 | [Viewing workflow logs locations](#36--viewing-workflow-logs-locations) |
| 7 | [Artifact expiration via API](#7--artifact-expiration-via-api) | 37 | [JavaScript actions vs traditional Node.js](#37--javascript-actions-vs-traditional-nodejs) |
| 8 | [Central repo for reusable workflows](#8--central-repo-for-reusable-workflows) | 38 | [Matrix strategy definition](#38--matrix-strategy-definition) |
| 9 | [branches + paths filter logic](#9--branches--paths-filter-logic) | 39 | [Workflow templates storage location](#39--workflow-templates-storage-location) |
| 10 | [Configuring secrets for template workflows](#10--configuring-secrets-for-template-workflows) | 40 | [Action distribution best practices](#40--action-distribution-best-practices) |
| 11 | [JavaScript action metadata (main vs steps)](#11--javascript-action-metadata-main-vs-steps) | 41 | [Required workflow components](#41--required-workflow-components) |
| 12 | [GitHub-hosted vs self-hosted runners](#12--github-hosted-vs-self-hosted-runners) | 42 | [Job dependencies with needs](#42--job-dependencies-with-needs) |
| 13 | [Events → workflows → jobs → steps flow](#13--events--workflows--jobs--steps-flow) | 43 | [Secrets auto-redaction in logs](#43--secrets-auto-redaction-in-logs) |
| 14 | [Organization secrets with repo policy](#14--organization-secrets-with-repo-policy) | 44 | [Schedule workflows with cron](#44--schedule-workflows-with-cron) |
| 15 | [Accessing workflow logs via API](#15--accessing-workflow-logs-via-api) | 45 | [Runner groups default assignment](#45--runner-groups-default-assignment) |
| 16 | [Cache action behavior on cache miss](#16--cache-action-behavior-on-cache-miss) | 46 | [github.event context property](#46--githubevent-context-property) |
| 17 | [Dockerfile case sensitivity](#17--dockerfile-case-sensitivity) | 47 | [Deployment steps in YAML](#47--deployment-steps-in-yaml) |
| 18 | [Missing on: keyword issue](#18--missing-on-keyword-issue) | 48 | [Set up job recorded info](#48--set-up-job-recorded-info) |
| 19 | [Viewing workflow file from failed run](#19--viewing-workflow-file-from-failed-run) | 49 | [Exit code interpretation](#49--exit-code-interpretation) |
| 20 | [continue-on-error purpose](#20--continue-on-error-purpose) | 50 | [Workflow commands for annotations](#50--workflow-commands-for-annotations) |
| 21 | [Environment protection rules](#21--environment-protection-rules) | 51 | [Jobs and steps purpose](#51--jobs-and-steps-purpose) |
| 22 | [Verifying actions before use](#22--verifying-actions-before-use) | 52 | [Sharing custom Docker action](#52--sharing-custom-docker-action) |
| 23 | [Custom labels cumulative logic](#23--custom-labels-cumulative-logic) | 53 | [on: keyword for triggers](#53--on-keyword-for-triggers) |
| 24 | [Action release tag best practices](#24--action-release-tag-best-practices) | 54 | [Hosting public actions separately](#54--hosting-public-actions-separately) |
| 25 | [Using private action from private repo](#25--using-private-action-from-private-repo) | 55 | [Runner groups for access control](#55--runner-groups-for-access-control) |
| 26 | [Debug logging enablement](#26--debug-logging-enablement) | 56 | [Jobs section purpose](#56--jobs-section-purpose) |
| 27 | [Sharing actions privately via repo](#27--sharing-actions-privately-via-repo) | 57 | [Custom environment variables location](#57--custom-environment-variables-location) |
| 28 | [Env variable precedence](#28--env-variable-precedence-workflowjobstep) | 58 | [Artifact download access level](#58--artifact-download-access-level) |
| 29 | [Default variable RUNNER_OS](#29--default-variable-runner_os) | 59 | [GITHUB_TOKEN expiration](#59--github_token-expiration) |
| 30 | [Docker actions require Linux runners](#30--docker-actions-require-linux-runners) | 60 | [Publishing npm to GitHub Packages](#60--publishing-npm-to-github-packages) |

---

# 📚 All 60 Concepts

---

## 1. 📦 Exit codes in Docker actions

📌 **What is it?**  
→ Exit codes tell GitHub Actions if a Docker action succeeded (0) or failed (non-zero)

🎯 **Why we use it?**  
→ To signal success/failure from Docker container actions back to the workflow

🛠 **Example**
```bash
# In entrypoint.sh
if [ "$ERROR" = "true" ]; then
  echo "Task failed!"
  exit 1  # Failure
fi
exit 0  # Success
```

⚠️ **Common Trap**  
→ Forgetting that ANY non-zero exit code = failure, not just `exit 1`

🧠 **Exam Tip**  
→ Exit code 0 = success, Exit code 1 (or any non-zero) = failure

🔗 [Docs](https://docs.github.com/en/actions/creating-actions/setting-exit-codes-for-actions)

---

## 2. 📦 Meaning of `exit 1`

📌 **What is it?**  
→ `exit 1` is a command that terminates a script with a failure status code

🎯 **Why we use it?**  
→ To indicate that a task has failed and the workflow should stop or mark as failed

🛠 **Example**
```bash
if [ -z "$API_KEY" ]; then
  echo "Error: API_KEY not set"
  exit 1
fi
```

⚠️ **Common Trap**  
→ Confusing `exit 1` with success - it's ALWAYS a failure indicator

🧠 **Exam Tip**  
→ When you see `exit 1` in code, it means "failure exit code is being set"

🔗 [Docs](https://docs.github.com/en/actions/creating-actions/setting-exit-codes-for-actions#setting-a-failure-exit-code-in-a-docker-container-action)

---

## 3. 📦 Marketplace purpose

📌 **What is it?**  
→ GitHub Marketplace is a platform for publishing and sharing GitHub Actions

🎯 **Why we use it?**  
→ To discover, share, and distribute reusable actions with the community

🛠 **Example**
```yaml
# Using a Marketplace action
steps:
  - uses: actions/checkout@v4  # From Marketplace
```

⚠️ **Common Trap**  
→ Thinking Marketplace manages workflows or executes steps - it only SHARES actions

🧠 **Exam Tip**  
→ Marketplace = platform for publishing/sharing actions (not execution!)

🔗 [Docs](https://docs.github.com/en/actions/creating-actions/publishing-actions-in-github-marketplace)

---

## 4. 📦 Publishing action to Marketplace (release)

📌 **What is it?**  
→ Process of making your action available on GitHub Marketplace by creating a release

🎯 **Why we use it?**  
→ To share your custom action with the GitHub community

🛠 **Steps to publish:**
1. Add action.yml to repository root
2. Draft a new release
3. Select "Publish this Action to GitHub Marketplace"
4. Choose categories
5. Set version tag
6. Publish release

⚠️ **Common Trap**  
→ If "Publish" checkbox is disabled, you need to accept the GitHub Marketplace Developer Agreement

🧠 **Exam Tip**  
→ Publishing = tag as new release + publish (NOT creating branches or merging PRs)

🔗 [Docs](https://docs.github.com/en/actions/creating-actions/publishing-actions-in-github-marketplace)

---

## 5. 📦 Static IP large runners for allowlist

📌 **What is it?**  
→ Large runners with static IP addresses that can be added to IP allowlists

🎯 **Why we use it?**  
→ When internal resources require IP allowlists for security, large runners provide predictable IPs

🛠 **Example**
```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest-16-cores  # Large runner with static IP
```

⚠️ **Common Trap**  
→ Standard GitHub-hosted runners have dynamic IPs - only LARGE runners have static IPs

🧠 **Exam Tip**  
→ For IP allowlist requirements → use large runners with static IP ranges

🔗 [Docs](https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners/about-github-hosted-runners#ip-addresses)

---

## 6. 📦 Workflow trigger for feature branch

📌 **What is it?**  
→ Configuration to trigger workflows only when pushing to feature branches

🎯 **Why we use it?**  
→ To run CI/CD only on specific branch patterns like `feature/*`

🛠 **Example**
```yaml
on:
  push:
    branches:
      - 'feature/*'  # Triggers on feature/login, feature/api, etc.
```

⚠️ **Common Trap**  
→ Using `types: feature` is WRONG - branches don't have "types"
→ Using `on: commit` is WRONG - use `on: push`

🧠 **Exam Tip**  
→ Correct syntax: `on: push: branches: ['feature/*']`

🔗 [Docs](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows)

---

## 7. 📦 Artifact expiration via API

📌 **What is it?**  
→ Using GitHub API to find when an artifact will expire (via `expires_at` field)

🎯 **Why we use it?**  
→ To programmatically check artifact expiration dates for management purposes

🛠 **Example**
```bash
curl -L \
  -H "Authorization: Bearer $TOKEN" \
  https://api.github.com/repos/OWNER/REPO/actions/artifacts
# Response includes "expires_at" field
```

⚠️ **Common Trap**  
→ Expiration date is NOT shown in the "Actions" tab or "Artifacts" tab - only via API

🧠 **Exam Tip**  
→ To find artifact expiration → use API (check `expires_at` value)

🔗 [Docs](https://docs.github.com/en/actions/using-workflows/storing-workflow-data-as-artifacts)

---

## 8. 📦 Central repo for reusable workflows

📌 **What is it?**  
→ A dedicated repository to store and manage all reusable workflows for an organization

🎯 **Why we use it?**  
→ Better organization, easier tracking, standardization, and centralized version control

🛠 **Example**
```yaml
# In project repo, calling reusable workflow from central repo
jobs:
  deploy:
    uses: myorg/workflows/.github/workflows/deploy.yml@v1
```

⚠️ **Common Trap**  
→ Don't scatter reusable workflows across individual project repos - centralize them!

🧠 **Exam Tip**  
→ Best practice = create dedicated repository for reusable workflows

🔗 [Docs](https://docs.github.com/en/actions/creating-actions/sharing-actions-and-workflows-from-your-private-repository)

---

## 9. 📦 branches + paths filter logic

📌 **What is it?**  
→ When both `branches` and `paths` filters are defined, BOTH must be satisfied for workflow to run

🎯 **Why we use it?**  
→ To create precise triggers that require specific branch AND specific file changes

🛠 **Example**
```yaml
on:
  push:
    branches:
      - main
    paths:
      - 'src/**'
# Only runs if push is to main AND changes are in src/
```

⚠️ **Common Trap**  
→ It's NOT an OR condition - BOTH filters must match!

🧠 **Exam Tip**  
→ branches + paths = AND logic (both must be satisfied)

🔗 [Docs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#onpull_requestpull_request_targetbranchesbranches-ignore)

---

## 10. 📦 Configuring secrets for template workflows

📌 **What is it?**  
→ When using templated workflows with `${{ secrets.token }}`, you must create that secret first

🎯 **Why we use it?**  
→ Template workflows reference secrets that need to exist in the repository/organization

🛠 **Example**
```yaml
# Template uses this:
env:
  API_KEY: ${{ secrets.API_KEY }}

# You must create secret named "API_KEY" in your repo settings
```

⚠️ **Common Trap**  
→ Don't replace `${{ secrets.token }}` with actual value - that exposes the secret!
→ Don't remove the secret reference - create the secret instead

🧠 **Exam Tip**  
→ Before using template with secrets → create those secrets in your repo/org

🔗 [Docs](https://docs.github.com/en/actions/learn-github-actions/using-starter-workflows)

---

## 11. 📦 JavaScript action metadata (`main` vs `steps`)

📌 **What is it?**  
→ JavaScript actions use `main` argument (not `steps`) to specify the entry point file

🎯 **Why we use it?**  
→ To tell GitHub which JavaScript file to execute as the action

🛠 **Example**
```yaml
# CORRECT - action.yml for JavaScript action
runs:
  using: 'node20'
  main: 'main.js'  # ✅ Correct

# WRONG
runs:
  using: 'node12'
  steps: 'main.js'  # ❌ Wrong - steps is not valid
```

⚠️ **Common Trap**  
→ Using `steps:` instead of `main:` - JavaScript actions don't use `steps`

🧠 **Exam Tip**  
→ JavaScript action requires: `using` (node version) + `main` (entry file)

🔗 [Docs](https://docs.github.com/en/actions/creating-actions/creating-a-javascript-action)

---

## 12. 📦 GitHub-hosted vs self-hosted runners

📌 **What is it?**  
→ GitHub-hosted = managed by GitHub, ephemeral | Self-hosted = managed by you, customizable

🎯 **Why we use it?**  
→ GitHub-hosted: minimal management, cost-effective | Self-hosted: full control, private network access

🛠 **Comparison**
| Feature | GitHub-hosted | Self-hosted |
|---------|--------------|-------------|
| Management | By GitHub | By you |
| Environment | Fresh each job | Persistent (optional) |
| Cost | Free minutes included | Your infrastructure |
| Network | Public | Private network access |

⚠️ **Common Trap**  
→ Self-hosted requires more maintenance but offers more control

🧠 **Exam Tip**  
→ For cost-effective + minimal management → GitHub-hosted runners

🔗 [Docs](https://docs.github.com/en/actions/using-github-hosted-runners/about-github-hosted-runners/about-github-hosted-runners)

---

## 13. 📦 Events → workflows → jobs → steps flow

📌 **What is it?**  
→ The execution hierarchy: Event triggers Workflow → executes Jobs → contains Steps

🎯 **Why we use it?**  
→ Understanding this flow is fundamental to GitHub Actions

🛠 **Visual Flow**
```
EVENT (push, PR, schedule)
    ↓
WORKFLOW (defined in .yml file)
    ↓
JOB(S) (run on runners, can be parallel)
    ↓
STEP(S) (individual tasks/actions)
```

⚠️ **Common Trap**  
→ Jobs don't initiate runs - EVENTS trigger workflows which execute jobs

🧠 **Exam Tip**  
→ Event → Workflow → Job(s) → Step(s) (memorize this order!)

🔗 [Docs](https://docs.github.com/en/actions/using-workflows/about-workflows)

---

## 14. 📦 Organization secrets with repo policy

📌 **What is it?**  
→ Organization-level secrets that can be restricted to specific repositories via access policy

🎯 **Why we use it?**  
→ To securely share secrets across multiple repos while limiting access to authorized ones

🛠 **Example**
```yaml
# In workflow - using org secret
env:
  API_KEY: ${{ secrets.ORG_API_KEY }}
```

**Setup:** Org Settings → Secrets → Create secret → Set repository access policy

⚠️ **Common Trap**  
→ Just creating org secret isn't enough - must configure access policy for repos

🧠 **Exam Tip**  
→ Organization secret + access policy = secure sharing to specific repos only

🔗 [Docs](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions#creating-secrets-for-an-organization)

---

## 15. 📦 Accessing workflow logs via API

📌 **What is it?**  
→ Using GitHub API to programmatically download workflow run logs

🎯 **Why we use it?**  
→ To automate log retrieval for analysis, auditing, or debugging

🛠 **Example**
```bash
curl -L \
  -H "Authorization: Bearer $TOKEN" \
  -H "Accept: application/vnd.github+json" \
  https://api.github.com/repos/OWNER/REPO/actions/runs/RUN_ID/logs
```

**Required info:** owner, repo, run_id

⚠️ **Common Trap**  
→ You need `run_id`, NOT `job_id` to get workflow logs
→ Auth token is NOT required for public repos

🧠 **Exam Tip**  
→ To access logs via API: need owner + repo + run_id

🔗 [Docs](https://docs.github.com/en/rest/actions/workflow-runs?apiVersion=2022-11-28#download-workflow-run-logs)

---

## 16. 📦 Cache action behavior on cache miss

📌 **What is it?**  
→ When cache key doesn't match (cache miss), workflow continues and creates new cache if job succeeds

🎯 **Why we use it?**  
→ To understand caching behavior and plan for first-run scenarios

🛠 **Example**
```yaml
- uses: actions/cache@v3
  with:
    path: ~/.npm
    key: npm-${{ hashFiles('**/package-lock.json') }}
# If no cache found → continues → creates cache after job success
```

⚠️ **Common Trap**  
→ Cache miss does NOT fail or terminate the workflow

🧠 **Exam Tip**  
→ Cache miss → job continues → new cache created if job succeeds

🔗 [Docs](https://docs.github.com/en/actions/using-workflows/caching-dependencies-to-speed-up-workflows#managing-caches)

---

## 17. 📦 Dockerfile case sensitivity

📌 **What is it?**  
→ The filename must be exactly `Dockerfile` (capital D, lowercase rest)

🎯 **Why we use it?**  
→ Docker requires this exact filename for automatic detection

🛠 **Example**
```
✅ Correct: Dockerfile
❌ Wrong: dockerfile
❌ Wrong: DockerFile
❌ Wrong: DOCKERFILE
```

⚠️ **Common Trap**  
→ Using `dockerfile` (all lowercase) will cause Docker container action to fail

🧠 **Exam Tip**  
→ If Docker action fails, check: Is it `Dockerfile` (capital D)?

🔗 [Docs](https://docs.github.com/en/actions/creating-actions/creating-a-docker-container-action)

---

## 18. 📦 Missing `on:` keyword issue

📌 **What is it?**  
→ Without `on:` keyword, workflow has no triggers and will never run

🎯 **Why we use it?**  
→ `on:` specifies WHAT events trigger the workflow (required!)

🛠 **Example**
```yaml
# ❌ WRONG - missing on:
name: My Workflow
jobs:
  build:
    runs-on: ubuntu-latest

# ✅ CORRECT
name: My Workflow
on: push  # <-- Required!
jobs:
  build:
    runs-on: ubuntu-latest
```

⚠️ **Common Trap**  
→ Workflow seems valid but never runs = probably missing `on:`

🧠 **Exam Tip**  
→ Workflow not running? First check: Is `on:` keyword present?

🔗 [Docs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)

---

## 19. 📦 Viewing workflow file from failed run

📌 **What is it?**  
→ The "View workflow file" option in a failed run's menu shows the workflow YAML

🎯 **Why we use it?**  
→ To quickly access the workflow configuration when troubleshooting failures

🛠 **How to access:**
1. Go to Actions tab
2. Click on failed run
3. Click "..." menu
4. Select "View workflow file"

⚠️ **Common Trap**  
→ "View raw logs" shows execution logs, NOT the workflow file
→ "Download log archive" downloads logs, NOT workflow file

🧠 **Exam Tip**  
→ To view workflow file from failed run → "View workflow file" option

🔗 [Docs](https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/viewing-workflow-run-history)

---

## 20. 📦 `continue-on-error` purpose

📌 **What is it?**  
→ Setting that allows workflow to continue even if a specific step fails

🎯 **Why we use it?**  
→ For non-critical steps where failure shouldn't stop the entire workflow

🛠 **Example**
```yaml
steps:
  - name: Optional lint check
    run: npm run lint
    continue-on-error: true  # Workflow continues even if lint fails
  
  - name: Critical build
    run: npm run build  # This runs regardless of lint result
```

⚠️ **Common Trap**  
→ It doesn't retry steps or specify conditions - just allows continuation after failure

🧠 **Exam Tip**  
→ `continue-on-error: true` = workflow continues even if step fails

🔗 [Docs](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions#jobsjob_idcontinue-on-error)

---

## 21. 📦 Environment protection rules

📌 **What is it?**  
→ Rules that must pass before jobs targeting an environment can start

🎯 **Why we use it?**  
→ To add approval gates, wait timers, or branch restrictions for deployments

🛠 **Example**
```yaml
jobs:
  deploy:
    environment: production  # Subject to protection rules
    runs-on: ubuntu-latest
```

**Protection rules:** Required reviewers, wait timer, branch restrictions

⚠️ **Common Trap**  
→ If ANY protection rule fails, job is skipped
→ If job is REJECTED, entire workflow fails

🧠 **Exam Tip**  
→ Jobs won't start until ALL protection rules pass | Rejected = workflow fails

🔗 [Docs](https://docs.github.com/en/actions/deployment/targeting-different-environments/using-environments-for-deployment)

---

## 22. 📦 Verifying actions before use

📌 **What is it?**  
→ Steps to validate an action's trustworthiness before using it in workflows

🎯 **Why we use it?**  
→ Security best practice to avoid malicious or buggy actions

🛠 **Verification checklist:**
1. ✅ Review action.yml file (check what it actually does)
2. ✅ Check if action is in GitHub Marketplace
3. ✅ Check if action is VERIFIED in Marketplace (blue badge)

⚠️ **Common Trap**  
→ GitHub stars are NOT a reliable indicator of trustworthiness

🧠 **Exam Tip**  
→ Verify actions by: reviewing action.yml + checking Marketplace + checking verified status

🔗 [Docs](https://learn.microsoft.com/en-us/training/modules/github-actions-automate-tasks/2-github-actions-automate-development-tasks)

---

## 23. 📦 Custom labels cumulative logic

📌 **What is it?**  
→ Self-hosted runner must have ALL specified labels to be eligible for a job

🎯 **Why we use it?**  
→ To precisely route jobs to runners with specific capabilities

🛠 **Example**
```yaml
jobs:
  build:
    runs-on: [self-hosted, linux, gpu]
    # Runner must have ALL THREE labels: self-hosted AND linux AND gpu
```

⚠️ **Common Trap**  
→ Labels are NOT "any match" (OR) - they are "all must match" (AND)

🧠 **Exam Tip**  
→ Custom labels are cumulative = runner needs ALL assigned labels

🔗 [Docs](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/using-self-hosted-runners-in-a-workflow#using-custom-labels-to-route-jobs)

---

## 24. 📦 Action release tag best practices

📌 **What is it?**  
→ Guidelines for tagging and versioning GitHub Action releases

🎯 **Why we use it?**  
→ To manage releases properly and help users reference stable versions

🛠 **Best practices:**
```
✅ Use semantic versioning (v1.0.0, v1.0.1, v2.0.0)
✅ Create releases on release branches, not main
✅ Move major version tag (v1) to current release
✅ New major version (v2) for breaking changes
✅ Use beta tags for unstable releases (v2-beta)
```

⚠️ **Common Trap**  
→ Don't create releases directly on main without validation

🧠 **Exam Tip**  
→ Breaking changes = new major version (v1 → v2)

🔗 [Docs](https://docs.github.com/en/actions/creating-actions/releasing-and-maintaining-actions)

---

## 25. 📦 Using private action from private repo

📌 **What is it?**  
→ Allowing workflows to access actions stored in other private repositories

🎯 **Why we use it?**  
→ To use custom internal actions without making them public

🛠 **Example**
```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: my-org/private-action@v1  # Private repo action
```

**Setup:** Configure repository settings to allow access from other private repos

⚠️ **Common Trap**  
→ Don't copy action code into each repo - configure access permissions instead

🧠 **Exam Tip**  
→ Private action access = grant workflow access to the private repo containing action

🔗 [Docs](https://docs.github.com/en/actions/creating-actions/sharing-actions-and-workflows-from-your-private-repository)

---

## 26. 📦 Debug logging enablement

📌 **What is it?**  
→ Enabling verbose logging by setting `ACTIONS_STEP_DEBUG` to `true`

🎯 **Why we use it?**  
→ To get more detailed logs when troubleshooting workflow issues

🛠 **How to enable:**
1. Go to repo Settings → Secrets and variables → Actions
2. Create secret OR variable named `ACTIONS_STEP_DEBUG`
3. Set value to `true`

⚠️ **Common Trap**  
→ Can be set as secret OR variable - if both exist, SECRET takes precedence

🧠 **Exam Tip**  
→ Enable debug logging: set `ACTIONS_STEP_DEBUG` = `true` (secret takes precedence over variable)

🔗 [Docs](https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/enabling-debug-logging)

---

## 27. 📦 Sharing actions privately via repo

📌 **What is it?**  
→ Storing custom actions in a private repository with access control

🎯 **Why we use it?**  
→ To share internal actions across teams while maintaining privacy

🛠 **Example**
```yaml
# Team A's workflow can use action from Team B's private repo
- uses: myorg/internal-actions/deploy@v1
```

**Setup:** Store in private repo → Configure access control for specific repos

⚠️ **Common Trap**  
→ Don't use public repos for sensitive internal actions
→ Don't copy action code to every repo (duplication)

🧠 **Exam Tip**  
→ Share privately = private repo + access control (NOT public repo with restrictions)

🔗 [Docs](https://docs.github.com/en/actions/creating-actions/sharing-actions-and-workflows-with-your-organization)

---

## 28. 📦 Env variable precedence (workflow/job/step)

📌 **What is it?**  
→ Environment variable at STEP level overrides JOB level, which overrides WORKFLOW level

🎯 **Why we use it?**  
→ To understand which value is used when same variable is defined at multiple levels

🛠 **Example**
```yaml
env:
  NAME: 'Workflow Level'  # Lowest priority

jobs:
  build:
    env:
      NAME: 'Job Level'  # Medium priority
    steps:
      - run: echo "$NAME"
        env:
          NAME: 'Step Level'  # Highest priority - THIS WINS!
# Output: "Step Level"
```

⚠️ **Common Trap**  
→ Most specific (step) wins, not the first defined (workflow)

🧠 **Exam Tip**  
→ Precedence: Step > Job > Workflow (step is most specific = highest priority)

🔗 [Docs](https://docs.github.com/en/actions/using-workflows/disabling-and-enabling-a-workflow)

---

## 29. 📦 Default variable RUNNER_OS

📌 **What is it?**  
→ Default environment variable containing the runner's operating system (Linux, Windows, macOS)

🎯 **Why we use it?**  
→ To write conditional logic based on the runner's OS

🛠 **Example**
```yaml
steps:
  - name: OS-specific command
    run: |
      if [ "$RUNNER_OS" == "Linux" ]; then
        echo "Running on Linux"
      fi
```

⚠️ **Common Trap**  
→ `RUNNER_OS` is for OS | `RUNNER_ARCH` is for architecture (X86, ARM, etc.)
→ `GITHUB_RUNNER_OS` does NOT exist!

🧠 **Exam Tip**  
→ OS = `RUNNER_OS` | Architecture = `RUNNER_ARCH`

🔗 [Docs](https://docs.github.com/en/actions/learn-github-actions/variables#default-environment-variables)

---

## 30. 📦 Docker actions require Linux runners

📌 **What is it?**  
→ Docker container actions can ONLY run on Linux-based runners

🎯 **Why we use it?**  
→ Understanding this limitation when planning workflows with Docker actions

🛠 **Example**
```yaml
jobs:
  docker-job:
    runs-on: ubuntu-latest  # ✅ Must be Linux
    steps:
      - uses: my-docker-action@v1
```

⚠️ **Common Trap**  
→ Docker actions will FAIL on Windows or macOS runners

🧠 **Exam Tip**  
→ Docker container action + Windows = will NOT work (Linux only!)

🔗 [Docs](https://docs.github.com/en/actions/creating-actions/about-custom-actions#docker-container-actions)

---

## 31. 📦 Adding PATH using `$GITHUB_PATH`

📌 **What is it?**  
→ Workflow command to add directories to PATH for subsequent steps

🎯 **Why we use it?**  
→ To make executables in custom directories available to later steps

🛠 **Example**
```yaml
steps:
  - name: Add to PATH
    run: echo "/tmp/mytools" >> $GITHUB_PATH
  
  - name: Use tool
    run: mytool --version  # Now accessible!
```

⚠️ **Common Trap**  
→ `$GITHUB_ENV` = set variables | `$GITHUB_PATH` = add to PATH
→ `$GITHUB_OUTPUT` = step outputs | `$GITHUB_STEP_SUMMARY` = summaries

🧠 **Exam Tip**  
→ Add to PATH: `echo "/path" >> $GITHUB_PATH`

🔗 [Docs](https://docs.github.com/en/actions/using-workflows/workflow-commands-for-github-actions)

---

## 32. 📦 Treat env variables as case sensitive

📌 **What is it?**  
→ Best practice to treat ALL environment variables as case-sensitive

🎯 **Why we use it?**  
→ Ensures consistency across different OS and shells

🛠 **Example**
```yaml
env:
  API_KEY: 'secret'  # Use consistent casing

steps:
  - run: echo "$API_KEY"  # ✅ Matches exactly
  - run: echo "$api_key"  # ❌ May not work on some systems
```

⚠️ **Common Trap**  
→ `API_KEY` and `api_key` might be different on some systems

🧠 **Exam Tip**  
→ Always treat env variables as case-sensitive (even if OS allows otherwise)

🔗 [Docs](https://docs.github.com/en/actions/using-workflows/workflow-commands-for-github-actions)

---

## 33. 📦 Publishing npm package to GitHub Packages

📌 **What is it?**  
→ Workflow configuration to publish Node.js packages to GitHub Packages registry

🎯 **Why we use it?**  
→ To automatically publish npm packages when a release is created

🛠 **Example**
```yaml
name: Publish Package
on:
  release:
    types: [published]
jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20.x'
          registry-url: 'https://npm.pkg.github.com/'  # Required!
      - run: npm ci
      - run: npm publish
        env:
          NODE_AUTH_TOKEN: ${{ secrets.GITHUB_TOKEN }}  # Required!
```

⚠️ **Common Trap**  
→ Missing `registry-url` = won't publish to GitHub Packages
→ Missing `NODE_AUTH_TOKEN` = authentication failure

🧠 **Exam Tip**  
→ GitHub Packages npm publish needs: registry-url + NODE_AUTH_TOKEN

🔗 [Docs](https://docs.github.com/en/actions/publishing-packages/publishing-nodejs-packages)

---

## 34. 📦 Secrets exposure risk via CLI

📌 **What is it?**  
→ GitHub automatically redacts secrets in workflow logs, replacing them with ***

🎯 **Why we use it?**  
→ Automatic security feature to prevent accidental secret exposure

🛠 **Example**
```yaml
steps:
  - run: echo "Token: ${{ secrets.API_TOKEN }}"
# Log output: "Token: ***" (automatically redacted)
```

⚠️ **Common Trap**  
→ Redaction happens automatically - no manual action needed

🧠 **Exam Tip**  
→ Secrets in logs = automatically replaced with *** (redacted)

🔗 [Docs](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions#redacting-secrets-from-workflow-run-logs)

---

## 35. 📦 Action metadata required files

📌 **What is it?**  
→ All actions REQUIRE a metadata file named `action.yml` or `action.yaml`

🎯 **Why we use it?**  
→ Defines inputs, outputs, and run configuration for the action

🛠 **Example**
```yaml
# action.yml (required)
name: 'My Action'
description: 'Does something useful'
inputs:
  name:
    description: 'Your name'
    required: true
runs:
  using: 'node20'
  main: 'index.js'
```

⚠️ **Common Trap**  
→ File MUST be named `action.yml` or `action.yaml` (not `actions.yml`)

🧠 **Exam Tip**  
→ Every action needs: action.yml (or action.yaml) in YAML syntax

🔗 [Docs](https://docs.github.com/en/actions/creating-actions/metadata-syntax-for-github-actions)

---

## 36. 📦 Workflow status badge limitations

📌 **What is it?**  
→ Status badges show workflow pass/fail state; can be customized with event/branch params

🎯 **Why we use it?**  
→ To display workflow status in README files or documentation

🛠 **Example**
```markdown
![Build Status](https://github.com/OWNER/REPO/actions/workflows/ci.yml/badge.svg)

# Filter by event:
![Build](https://github.com/OWNER/REPO/actions/workflows/ci.yml/badge.svg?event=push)

# Filter by branch:
![Build](https://github.com/OWNER/REPO/actions/workflows/ci.yml/badge.svg?branch=main)
```

⚠️ **Common Trap**  
→ For private repos, badges may not display to unauthenticated users

🧠 **Exam Tip**  
→ Filter by event: add `?event=push` to badge URL

🔗 [Docs](https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/adding-a-workflow-status-badge)

---

## 37. 📦 Matrix job combinations

📌 **What is it?**  
→ Using `strategy.matrix` to run jobs with multiple configurations automatically

🎯 **Why we use it?**  
→ To test across multiple OS, language versions, or configurations

🛠 **Example**
```yaml
jobs:
  test:
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest]
        node: [16, 18, 20]
    runs-on: ${{ matrix.os }}
    steps:
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node }}
# Creates 6 jobs: 2 OS × 3 node versions
```

⚠️ **Common Trap**  
→ Matrix is defined under `strategy`, not directly in the job

🧠 **Exam Tip**  
→ Access matrix values: `${{ matrix.property }}` (e.g., `matrix.os`, `matrix.node`)

🔗 [Docs](https://docs.github.com/en/actions/using-jobs/using-a-matrix-for-your-jobs)

---

## 38. 📦 Workflow templates reuse

📌 **What is it?**  
→ Organization-defined starter workflows stored in `.github` repo

🎯 **Why we use it?**  
→ To provide standardized workflow templates across the organization

🛠 **Storage location:**
```
.github (repository)
└── workflow-templates/
    ├── my-workflow.yml
    └── my-workflow.properties.json
```

⚠️ **Common Trap**  
→ Must be in `.github` repo's `workflow-templates` directory
→ NOT in `.github/workflow-templates` (that's wrong!)

🧠 **Exam Tip**  
→ Templates go in: `.github` repo → `workflow-templates/` directory

🔗 [Docs](https://docs.github.com/en/actions/using-workflows/creating-starter-workflows-for-your-organization)

---

## 39. 📦 Scheduled workflow min interval

📌 **What is it?**  
→ Using cron syntax to schedule workflows at specific times

🎯 **Why we use it?**  
→ For automated tasks like nightly builds, cleanup jobs, reports

🛠 **Example**
```yaml
on:
  schedule:
    - cron: '0 0 * * 1-5'  # Weekdays at midnight UTC

# Cron format: minute hour day month day-of-week
# 0 0 * * 1-5 = Mon-Fri at 00:00 UTC
```

⚠️ **Common Trap**  
→ No `on: schedule: weekdays` syntax - must use cron expression

🧠 **Exam Tip**  
→ Schedule workflows: use `on: schedule: cron` syntax

🔗 [Docs](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#schedule)

---

## 40. 📦 Viewing PR workflow status locations

📌 **What is it?**  
→ Two places to view workflow status for pull requests

🎯 **Why we use it?**  
→ To check if PR checks passed or failed

🛠 **Locations:**
1. **Actions tab** - Full workflow run list and details
2. **Checks tab** - On the specific PR page

⚠️ **Common Trap**  
→ "Insights" tab shows analytics, NOT workflow logs
→ "Issues" tab is for issue tracking, NOT workflows

🧠 **Exam Tip**  
→ PR workflow logs visible in: Actions tab + Checks tab (on PR)

🔗 [Docs](https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/using-workflow-run-logs)

---

## 41. 📦 Using `${{ secrets.NAME }}` correctly

📌 **What is it?**  
→ Syntax to access secrets stored in GitHub repository/organization

🎯 **Why we use it?**  
→ To securely use sensitive values without hardcoding them

🛠 **Example**
```yaml
steps:
  - name: Deploy
    run: ./deploy.sh
    env:
      API_KEY: ${{ secrets.API_KEY }}
      DB_PASSWORD: ${{ secrets.DB_PASSWORD }}
```

⚠️ **Common Trap**  
→ Secret must exist in repo/org settings BEFORE using in workflow
→ Secrets are automatically redacted in logs

🧠 **Exam Tip**  
→ Access secrets: `${{ secrets.SECRET_NAME }}`

🔗 [Docs](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions#using-secrets-in-a-workflow)

---

## 42. 📦 Passing outputs between steps

📌 **What is it?**  
→ Using `$GITHUB_OUTPUT` to pass data from one step to another

🎯 **Why we use it?**  
→ To share results/values between steps in a job

🛠 **Example**
```yaml
steps:
  - name: Set output
    id: step1
    run: echo "version=1.0.0" >> $GITHUB_OUTPUT
  
  - name: Use output
    run: echo "Version is ${{ steps.step1.outputs.version }}"
```

⚠️ **Common Trap**  
→ Step needs an `id` to reference its outputs
→ Use `steps.<step_id>.outputs.<name>` syntax

🧠 **Exam Tip**  
→ Set output: `echo "key=value" >> $GITHUB_OUTPUT` | Use: `steps.id.outputs.key`

🔗 [Docs](https://docs.github.com/en/actions/using-workflows/workflow-commands-for-github-actions#setting-an-output-parameter)

---

## 43. 📦 Artifact retention customization

📌 **What is it?**  
→ Configuring how long artifacts are kept before automatic deletion

🎯 **Why we use it?**  
→ To manage storage and comply with retention policies

🛠 **Example**
```yaml
- uses: actions/upload-artifact@v4
  with:
    name: my-artifact
    path: ./build
    retention-days: 5  # Keep for 5 days only
```

⚠️ **Common Trap**  
→ Once deleted, artifacts CANNOT be restored
→ Default retention varies by repo settings

🧠 **Exam Tip**  
→ Deleted artifacts cannot be restored | Write access needed to delete

🔗 [Docs](https://docs.github.com/en/actions/managing-workflow-runs/removing-workflow-artifacts)

---

## 44. 📦 Runner groups purpose

📌 **What is it?**  
→ Collections of self-hosted runners with security boundaries and access policies

🎯 **Why we use it?**  
→ To control which repositories/organizations can use specific runners

🛠 **Example workflow:**
1. Create runner group "production-runners"
2. Add specific runners to group
3. Set access policy: only "production" repos can use

⚠️ **Common Trap**  
→ New runners are assigned to DEFAULT group, must be moved manually

🧠 **Exam Tip**  
→ Runner groups = security boundary + access control for self-hosted runners

🔗 [Docs](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/managing-access-to-self-hosted-runners-using-groups)

---

## 45. 📦 Proxy configuration for runners

📌 **What is it?**  
→ Self-hosted runners need outbound HTTPS connectivity to GitHub using long polling

🎯 **Why we use it?**  
→ Understanding network requirements for self-hosted runner setup

🛠 **How it works:**
- Runner opens HTTPS connection to GitHub
- Uses 50-second long poll for job assignments
- NO inbound connections from GitHub required

⚠️ **Common Trap**  
→ You don't need to allow inbound connections FROM GitHub
→ Only outbound connectivity TO GitHub is required

🧠 **Exam Tip**  
→ Self-hosted runners need: outbound HTTPS long polling to GitHub

🔗 [Docs](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/about-self-hosted-runners#communication-between-self-hosted-runners-and-github)

---

## 46. 📦 Docker action inputs via args

📌 **What is it?**  
→ Environment variables for Docker actions are created from input names in UPPERCASE with INPUT_ prefix

🎯 **Why we use it?**  
→ Understanding how inputs are passed to Docker containers

🛠 **Example**
```yaml
# action.yml
inputs:
  my_name:
    description: 'Name to greet'

# Inside Docker container, accessible as:
# $INPUT_MY_NAME (uppercase, spaces → underscores)
```

⚠️ **Common Trap**  
→ Input `my_name` becomes `INPUT_MY_NAME` (not `INPUT_my_name`)

🧠 **Exam Tip**  
→ Docker inputs: converted to UPPERCASE + INPUT_ prefix + spaces→underscores

🔗 [Docs](https://docs.github.com/en/actions/creating-actions/metadata-syntax-for-github-actions#example-specifying-inputs)

---

## 47. 📦 Filtering failed workflow runs

📌 **What is it?**  
→ Ability to filter and view only failed workflow runs in the Actions tab

🎯 **Why we use it?**  
→ To quickly find and troubleshoot failed runs

🛠 **How to filter:**
1. Go to Actions tab
2. Use filter dropdown or search
3. Select "Failure" status

⚠️ **Common Trap**  
→ Expand each step to search within logs (collapsed steps not included in search)

🧠 **Exam Tip**  
→ To search logs: expand steps first (collapsed steps aren't searched)

🔗 [Docs](https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/using-workflow-run-logs)

---

## 48. 📦 Approval timeout rules

📌 **What is it?**  
→ Environment protection rules can require approval before jobs run

🎯 **Why we use it?**  
→ To add human verification before critical deployments

🛠 **What happens:**
- Job waits for approval
- If APPROVED → job runs
- If REJECTED → workflow FAILS

⚠️ **Common Trap**  
→ Rejection doesn't skip the job - it FAILS the entire workflow

🧠 **Exam Tip**  
→ Job rejected = workflow fails (not skipped!)

🔗 [Docs](https://docs.github.com/en/actions/managing-workflow-runs/reviewing-deployments)

---

## 49. 📦 Workflow triggers list

📌 **What is it?**  
→ Common events that can trigger workflows

🎯 **Why we use it?**  
→ To configure when workflows should run

🛠 **Common triggers:**
```yaml
on:
  push:                    # Code pushed
  pull_request:            # PR opened/updated
  release:                 # Release published
  schedule:                # Cron schedule
  workflow_dispatch:       # Manual trigger
  issue_comment:           # Comment on issue
  check_suite:             # Check suite completed
```

⚠️ **Common Trap**  
→ `check_suite` only triggers on DEFAULT branch
→ `on: commit` doesn't exist - use `on: push`

🧠 **Exam Tip**  
→ Manual trigger = `workflow_dispatch` | PR trigger = `pull_request`

🔗 [Docs](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows)

---

## 50. 📦 PowerShell steps count logic

📌 **What is it?**  
→ Each `run:` command counts as one step, regardless of commands inside

🎯 **Why we use it?**  
→ Understanding how steps are counted in workflows

🛠 **Example**
```yaml
steps:
  - run: echo "Step 1"          # Count: 1 step
  
  - run: |                       # Count: 1 step (multi-line)
      echo "Line 1"
      echo "Line 2"
      echo "Line 3"

# Total: 2 steps (not 4)
```

⚠️ **Common Trap**  
→ Multi-line `run` is still ONE step, not multiple steps

🧠 **Exam Tip**  
→ Each `- run:` or `- uses:` = 1 step (regardless of content)

🔗 [Docs](https://docs.github.com/en/actions/learn-github-actions/understanding-github-actions#jobs)

---

## 51. 📦 Variables vs hardcoded paths

📌 **What is it?**  
→ Using variables and environment settings instead of hardcoded values

🎯 **Why we use it?**  
→ For flexibility, maintainability, and security

🛠 **Example**
```yaml
# ❌ Bad - hardcoded
- run: /opt/tools/v1.2/deploy.sh

# ✅ Good - using variables
env:
  TOOL_VERSION: '1.2'
- run: /opt/tools/v${{ env.TOOL_VERSION }}/deploy.sh
```

⚠️ **Common Trap**  
→ Hardcoded paths are hard to maintain and update

🧠 **Exam Tip**  
→ Use variables/env for paths and values, not hardcoded strings

🔗 [Docs](https://docs.github.com/en/actions/learn-github-actions/variables)

---

## 52. 📦 Commit SHA versioning safety

📌 **What is it?**  
→ Using full commit SHA to reference actions for maximum security

🎯 **Why we use it?**  
→ SHA is immutable - tags/branches can be moved or changed

🛠 **Example**
```yaml
# Less secure - tag can be moved
- uses: actions/checkout@v4

# Most secure - immutable SHA
- uses: actions/checkout@b4ffde65f46336ab88eb53be808477a3936bae11
```

⚠️ **Common Trap**  
→ Tags (v1, v2) can be moved to different commits

🧠 **Exam Tip**  
→ For maximum security: use full commit SHA (immutable reference)

🔗 [Docs](https://docs.github.com/en/actions/creating-actions/releasing-and-maintaining-actions)

---

## 53. 📦 Marketplace public repo requirement

📌 **What is it?**  
→ Actions published to Marketplace must be in PUBLIC repositories

🎯 **Why we use it?**  
→ Marketplace is for sharing with the community

🛠 **Requirements:**
- Repository must be public
- Must have action.yml/action.yaml
- Accept Marketplace Developer Agreement

⚠️ **Common Trap**  
→ Private repos can share actions internally but NOT on Marketplace

🧠 **Exam Tip**  
→ GitHub Marketplace = public repos only

🔗 [Docs](https://docs.github.com/en/actions/creating-actions/publishing-actions-in-github-marketplace)

---

## 54. 📦 Metadata file location

📌 **What is it?**  
→ The action.yml file must be in the repository ROOT for Marketplace actions

🎯 **Why we use it?**  
→ GitHub looks for metadata file at root to recognize it as an action

🛠 **Structure:**
```
my-action-repo/
├── action.yml       # Required at root
├── index.js         # Action code
├── package.json
└── README.md
```

⚠️ **Common Trap**  
→ Putting action.yml in a subdirectory won't work for Marketplace publishing

🧠 **Exam Tip**  
→ action.yml must be in repository ROOT directory

🔗 [Docs](https://docs.github.com/en/actions/creating-actions/publishing-actions-in-github-marketplace)

---

## 55. 📦 Secrets context usage

📌 **What is it?**  
→ The `secrets` context contains all secrets available to the workflow

🎯 **Why we use it?**  
→ To access repository, organization, and environment secrets

🛠 **Example**
```yaml
env:
  TOKEN: ${{ secrets.GITHUB_TOKEN }}     # Auto-provided
  API_KEY: ${{ secrets.API_KEY }}        # Custom secret
  ORG_SECRET: ${{ secrets.ORG_SECRET }}  # Organization secret
```

⚠️ **Common Trap**  
→ Secrets must be created BEFORE they can be referenced in workflows

🧠 **Exam Tip**  
→ Access secrets via: `${{ secrets.NAME }}` context

🔗 [Docs](https://docs.github.com/en/actions/security-guides/using-secrets-in-github-actions)

---

## 56. 📦 Runner label routing

📌 **What is it?**  
→ Using labels to categorize runners and route jobs to specific runners

🎯 **Why we use it?**  
→ To direct jobs to runners with specific capabilities (OS, hardware, etc.)

🛠 **Example**
```yaml
jobs:
  gpu-job:
    runs-on: [self-hosted, linux, gpu]  # Route to GPU runner
  
  arm-job:
    runs-on: [self-hosted, linux, arm64]  # Route to ARM runner
```

⚠️ **Common Trap**  
→ All labels must match (AND logic), not just one (OR logic)

🧠 **Exam Tip**  
→ Runner labels = categorize runners | `runs-on` = select by labels

🔗 [Docs](https://docs.github.com/en/actions/hosting-your-own-runners/managing-self-hosted-runners/using-labels-with-self-hosted-runners)

---

## 57. 📦 Workflow badges private repo issue

📌 **What is it?**  
→ Status badges for private repos may not display to unauthenticated users

🎯 **Why we use it?**  
→ Understanding badge visibility limitations

🛠 **Example**
```markdown
<!-- May not show for private repo viewers without access -->
![Status](https://github.com/private-org/private-repo/actions/workflows/ci.yml/badge.svg)
```

⚠️ **Common Trap**  
→ Badge works in public repos but may fail for private repo external viewers

🧠 **Exam Tip**  
→ Private repo badges = visibility issues for external viewers

🔗 [Docs](https://docs.github.com/en/actions/monitoring-and-troubleshooting-workflows/adding-a-workflow-status-badge)

---

## 58. 📦 Event type filtering

📌 **What is it?**  
→ Using `types` keyword to filter specific activity types within an event

🎯 **Why we use it?**  
→ To trigger workflow only on specific event activities

🛠 **Example**
```yaml
on:
  release:
    types: [published]  # Only when release is published
  
  issues:
    types: [opened, labeled]  # Only when issue opened or labeled
  
  pull_request:
    types: [opened, synchronize, reopened]
```

⚠️ **Common Trap**  
→ Use `types:` (not `event:` or `when:`) to filter activity types

🧠 **Exam Tip**  
→ Filter event activities: use `types:` keyword (not `event:` or `when:`)

🔗 [Docs](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows)

---

## 59. 📦 Workflow_call reusable workflow concept

📌 **What is it?**  
→ Event that allows a workflow to be called by another workflow

🎯 **Why we use it?**  
→ To create reusable workflows that can be shared across repositories

🛠 **Example**
```yaml
# Reusable workflow (called.yml)
on:
  workflow_call:
    inputs:
      environment:
        required: true
        type: string

# Calling workflow
jobs:
  deploy:
    uses: myorg/workflows/.github/workflows/called.yml@main
    with:
      environment: 'production'
```

⚠️ **Common Trap**  
→ Reusable workflows need `on: workflow_call` trigger

🧠 **Exam Tip**  
→ Reusable workflow = triggered by `workflow_call` event

🔗 [Docs](https://docs.github.com/en/actions/using-workflows/reusing-workflows)

---

## 60. 📦 Node publish with registry-url

📌 **What is it?**  
→ The `registry-url` parameter in setup-node specifies the package registry

🎯 **Why we use it?**  
→ To publish npm packages to GitHub Packages instead of npmjs.com

🛠 **Example**
```yaml
- uses: actions/setup-node@v4
  with:
    node-version: '20.x'
    registry-url: 'https://npm.pkg.github.com/'  # GitHub Packages
    
- run: npm publish
  env:
    NODE_AUTH_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

**Registry URLs:**
- GitHub Packages: `https://npm.pkg.github.com/`
- npm public: `https://registry.npmjs.org/`

⚠️ **Common Trap**  
→ Without `registry-url`, publish goes to default npm registry (not GitHub)

🧠 **Exam Tip**  
→ GitHub Packages npm: set `registry-url: 'https://npm.pkg.github.com/'`

🔗 [Docs](https://docs.github.com/en/actions/publishing-packages/publishing-nodejs-packages)

---

# 🎯 Quick Reference Tables

## Secret Scope Levels
| Level | Scope | Access Control |
|-------|-------|---------------|
| Organization | All/selected repos | Policy-based |
| Repository | Single repo | Repo settings |
| Environment | Specific environment | Environment rules |

## Environment Variables Precedence
| Priority | Level | Overrides |
|----------|-------|-----------|
| Highest | Step | Job, Workflow |
| Medium | Job | Workflow |
| Lowest | Workflow | Nothing |

## Action Types
| Type | Runtime | Platform |
|------|---------|----------|
| JavaScript | Node.js | Any runner |
| Docker | Container | Linux only |
| Composite | Mixed | Any runner |

## Runner Comparison
| Feature | GitHub-hosted | Self-hosted |
|---------|--------------|-------------|
| Maintenance | GitHub | You |
| Environment | Fresh/ephemeral | Persistent (optional) |
| Cost | Free minutes | Your infrastructure |
| Network | Public | Private network |

---

# ✅ Final Exam Checklist

- [ ] Exit codes: 0 = success, non-zero = failure
- [ ] Marketplace = sharing platform, not execution
- [ ] branches + paths = AND logic (both must match)
- [ ] JavaScript action uses `main:` not `steps:`
- [ ] Docker actions = Linux runners ONLY
- [ ] Env precedence: Step > Job > Workflow
- [ ] Debug logging: `ACTIONS_STEP_DEBUG` = true
- [ ] Labels are cumulative (ALL must match)
- [ ] GITHUB_TOKEN expires: job finish OR 24 hours
- [ ] Secrets auto-redacted in logs
- [ ] Workflow files: `.github/workflows/`
- [ ] Templates: `.github` repo → `workflow-templates/`

---

**Good luck on your exam! 🚀**
