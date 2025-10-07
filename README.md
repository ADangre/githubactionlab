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
