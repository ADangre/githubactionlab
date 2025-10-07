# Github action using React + Vite project
## Learn
The lab is on GitHub Actions to automate two key workflows: deploying a static site to GitHub Pages and integrating SonarCloud for code quality analysis. It covers repository setup, creating and configuring multiple workflows, managing permissions and secrets, and adding useful badges for visibility. The focus is on practical, reproducible steps you can apply to any project.

What you’ll learn
Automated GitHub Pages deployment: Configure a build job that produces an artifact and a deploy job that publishes your site to GitHub Pages using official actions, with correct permissions and triggers.

SonarCloud integration: Create a SonarCloud project, add the required properties and tokens, and wire an analysis workflow that runs on pushes and pull requests, enforcing quality gates before merging.

Secrets and permissions: Set repository secrets (e.g., Sonar token), enable Pages permissions (pages: write), and ensure Actions have the right scopes to build and deploy safely.

Workflow structure and triggers: Use separate jobs and workflows (build/test vs. deploy vs. quality analysis), and configure triggers for push and pull_request to keep CI predictable.

Badges and visibility: Add build, deploy, and SonarCloud badges to your README so contributors and reviewers can quickly see status and quality at a glance.

Practical tips and pitfalls
Use separate workflows for deploy and analysis to keep concerns isolated; this simplifies troubleshooting and permissions management.

Set required permissions on workflows (especially for Pages) and verify that the deploy job has access to the artifact from the build job.

Keep secrets in GitHub (Settings → Secrets and variables). Never hardcode tokens in code or workflow files.

Target the right branches for push/pull_request triggers so CI runs where it matters (e.g., feature branches for PRs, main for deploy).

Fail fast on quality gates with SonarCloud so low-quality changes don’t get merged—this is essential for maintainability.

Outcome
By the end, you’ll have a repo with:

A reliable CI/CD pipeline that builds and deploys your static site to GitHub Pages automatically.

A SonarCloud-integrated quality gate that analyzes code on every push and PR, with badges reflecting current status and coverage.

Clear, maintainable YAML workflows and a README that communicates build, deploy, and quality signals to collaborators and reviewers.


# Step-by-step guide to set up GitHub Actions for Pages deployment and SonarCloud integration

This guide mirrors the video’s workflow: automate deployment of a React app to GitHub Pages and integrate SonarCloud for continuous code quality checks. You’ll create multiple Actions workflows, configure permissions, add secrets, and wire badges so the project is transparent and easy to maintain. The example file names align with the reference repo’s workflows: deployreactapptogithubpages.yaml, sonarissues.yaml, test.yaml, and a sample greeting.yaml.

---

## Prerequisites

- **GitHub repository:** A React/Vite app or similar static site that builds to a dist folder.
- **Branch strategy:** A default branch (e.g., main) for production deploys.
- **GitHub Pages:** Project-level Pages enabled for the repo.
- **SonarCloud account:** Organization, project key, and a generated token.

---

## Repository and GitHub Pages setup

- **Initialize repo:** Create or push your React app code to GitHub. Ensure package.json has scripts like build and test.
- **Enable GitHub Pages:** In Settings → Pages, choose “Deploy from a branch” or “GitHub Actions” (recommended). Using Actions gives you controlled CI/CD and granular permissions.
- **Plan artifacts:** Your build process should output a production-ready folder (e.g., dist). Actions will upload and deploy that artifact.

---

## Create the deployment workflow for GitHub Pages

Here’s a clear breakdown of what this **React App CI/CD to GitHub Pages** workflow does:

Workflow file: Check .github/workflows/deployreactapptogithubpages.yaml

## 🔄 Trigger Conditions
- Runs automatically on:
  - Pushes to `main` branch
  - Pushes to any `feature/**` branch
- Ignores changes to:
  - Workflow files (`.github/workflows/*`)
  - `README.md`
- Can also be run manually (`workflow_dispatch`)

---

## 🧪 Job 1: **Test**
- **Environment:** Ubuntu latest
- **Steps:**
  1. **Checkout code** → pulls repo into runner
  2. **Install Node.js v20** → ensures consistent runtime
  3. **Cache dependencies** → speeds up builds by caching `~/.npm`
  4. **Install dependencies** → runs `npm ci` for clean install
  5. **Run tests** → executes `npm run test` to validate code

✅ Purpose: Ensure code is correct and tests pass before building.

---

## 🏗️ Job 2: **Build**
- **Depends on:** `test` job (only runs if tests succeed)
- **Environment:** Ubuntu latest
- **Steps:**
  1. Checkout code
  2. Install Node.js v20
  3. Cache dependencies
  4. Install dependencies
  5. **Build project** → runs `npm run build` to generate production-ready files
  6. **Upload artifact** → saves the `dist` folder as a Pages artifact (`github-pages`)

✅ Purpose: Produce a deployable build and store it as an artifact.

---

## 🚀 Job 3: **Deploy**
- **Depends on:** `build` job
- **Environment:** Ubuntu latest
- **Permissions:**
  - `pages: write` → required to publish to GitHub Pages
  - `id-token: write` → ensures secure deployment
- **Environment:** Deploys to `github-pages` environment
- **Steps:**
  - **Deploy to GitHub Pages** → uses `actions/deploy-pages@v4` with `GITHUB_TOKEN`

✅ Purpose: Publishes the built app to GitHub Pages automatically.

---

## 📌 Summary of Flow
1. **Code pushed** → triggers workflow (except trivial changes like README/workflows).  
2. **Tests run** → ensures code quality.  
3. **Build job** → compiles React app and uploads artifact.  
4. **Deploy job** → securely publishes the app to GitHub Pages.  

This ensures **continuous integration (CI)** (tests + build) and **continuous deployment (CD)** (auto-publish to Pages) for your React app.

---


---

## Set up SonarCloud and repository secrets

- **Create SonarCloud project:** Note the organization and project key.
- **Generate token:** Create a SONAR_TOKEN in SonarCloud.
- **Add GitHub secret:** In repo Settings → Secrets and variables → Actions, add SONAR_TOKEN.
- **Configure properties:** Add sonar-project.properties to the repo root:

```properties
sonar.projectKey=your-org_your-repo
sonar.organization=your-org
sonar.projectName=Your Repo
sonar.sourceEncoding=UTF-8
sonar.javascript.lcov.reportPaths=coverage/lcov.info
sonar.sources=src
sonar.exclusions=**/*.test.js,**/*.spec.js
```

- **Coverage integration:** Ensure tests generate coverage (e.g., npm run test -- --coverage) to feed lcov into SonarCloud.

---

## Create the SonarCloud analysis workflow

Save as .github/workflows/sonarissues.yaml. It analyzes on pushes and pull requests, so PRs surface issues before merge.



- **Quality gate enforcement:** Configure SonarCloud to fail the check when the quality gate fails, so PRs show a “failing” status until issues are fixed.
- **PR decoration:** pull-requests: read permission allows SonarCloud to annotate PRs.

---

## Optional: Basic test workflow

Add a small CI to run on all pushes/PRs. Save as .github/workflows/test.yaml.
- **Purpose:** Keeps changes safe by running lint and tests on every change, complementing SonarCloud analysis.
Here’s a structured summary of what this **Testing workflow** does:

---

## 🔄 Trigger Conditions
- Runs automatically on:
  - **Pushes** to the `main` branch  
- **Ignores changes** to:
  - Workflow files (`.github/workflows/*`)  
  - `README.md`  
- Can also be run manually (`workflow_dispatch`)  

---

## 🧪 Job 1: **Test**
- **Runner:** Ubuntu latest  
- **Steps:**
  1. **Checkout code** → pulls repo into runner  
  2. **Install Node.js v20** → ensures consistent runtime  
  3. **Print Node version** → confirms environment  
  4. **Cache npm dependencies** → speeds up installs by caching `~/.npm`  
  5. **Install dependencies** → runs `npm install`  
  6. **Run tests** → executes `npm run test`  
  7. **Build step (placeholder)** → echoes “Build application…”  
  8. **Build project** → runs `npm run build`  

✅ Purpose: Validate code correctness and build readiness before proceeding.

---

## 🏗️ Job 2: **Build**
- **Depends on:** `Test` job (runs only if tests succeed)  
- **Runner:** Ubuntu latest  
- **Steps:**
  1. Checkout code  
  2. Install Node.js v20  
  3. Cache npm dependencies  
  4. Install dependencies  
  5. **Build message** → echoes “build started…”  
  6. **Build project** → runs `npm run build`  
  7. **Upload artifact** → saves the `dist/` folder as `dist-package` for later jobs  
  8. **Build complete message** → echoes “Build complete!”  

✅ Purpose: Produce a deployable build and make it available as an artifact.

---

## 🚀 Job 3: **Deploy**
- **Depends on:** `Build` job (runs only if build succeeds)  
- **Runner:** Ubuntu latest  
- **Steps:**
  1. **Download artifact** → retrieves `dist-package` from previous job  
  2. **Deploy message** → echoes “Deploy started…”  
  3. **Push to S3 bucket (placeholder)** → currently just echoes, but commented-out steps show how to configure AWS credentials and sync files to S3  
  4. **Deploy message** → echoes again (duplicate placeholder)  
  5. **Push to Azure server (placeholder)** → echoes deployment message  
  6. **Deploy complete message** → echoes “Deploy complete!”  

✅ Purpose: Deploy the built artifact to cloud targets (AWS S3, Azure, etc.). Currently placeholders, but ready for real deployment commands.

---

## 📌 Overall Flow
1. **Push to main** → triggers workflow.  
2. **Test job** → installs dependencies, runs tests, builds project.  
3. **Build job** → compiles project, uploads artifact.  
4. **Deploy job** → downloads artifact and deploys (AWS/Azure placeholders).  

This is a **CI/CD pipeline** with three stages: **Test → Build → Deploy**, ensuring only tested and successfully built code gets deployed.

## Optional: Greeting workflow for newcomers

A simple example that posts a log message on workflow_dispatch. Save as .github/workflows/greeting.yaml.



- **Use case:** Demonstrates manual triggers and basic job structure.

---

## Common pitfalls and fixes

- **Missing permissions:** If deploy fails, ensure permissions include pages: write and id-token: write.
- **Wrong artifact path:** Verify the upload path matches your build output (e.g., dist).
- **Branch triggers:** Deploy only on main; use PR triggers for analysis to prevent unreviewed deploys.
- **Secrets not set:** Sonar scan will fail without SONAR_TOKEN in repo secrets.
- **Insufficient fetch depth:** Sonar PR analysis can be incomplete with shallow clones; use fetch-depth: 0.

---

## What success looks like

- **Automated Pages deploys:** Every push to main builds and publishes the site.
- **Quality gate in PRs:** SonarCloud annotates code smells, bugs, and coverage; PRs show pass/fail status.
- **Clear signals:** README badges display deploy status and quality gate, helping reviewers and users trust the repo.

> Sources: 
