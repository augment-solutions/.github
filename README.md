# Augment Solutions - Org-Wide GitHub Actions

Organization-wide GitHub configuration and reusable workflow templates for AI-powered automation across all `augment-solutions` repositories.

## 🚀 Available Workflows

### 1. Spec Generation (`auggie-spec.yml`)
**Purpose:** Automatically generate technical specifications from GitHub issues using Augment AI.

**Triggers:**
- Add the `auggieSpec` label to an issue
- Comment `/auggieApproveSpec` on an issue (legacy support)

**What it does:**
- Analyzes the issue description and comments
- Generates a comprehensive technical specification
- Posts the spec as a comment on the issue
- Adds a 👀 reaction when processing starts
- Adds a 🚀 reaction when complete

**Use case:** Convert feature requests or bug reports into detailed technical specifications before implementation.

---

### 2. Implementation Plan (`auggie-plan.yml`)
**Purpose:** Generate structured implementation plans with step-by-step tasks.

**Triggers:**
- Add the `auggieImplementPlan` label to an issue
- Comment `/auggieImplementPlan` on an issue

**What it does:**
- Analyzes the issue and all comments
- Creates a detailed implementation plan with tasks
- Posts the plan as a comment
- Adds `auggie-in-progress` label during processing
- Replaces with `plan-ready` label when complete
- Adds a 🚀 reaction when complete

**Use case:** Break down complex features into actionable implementation steps.

---

### 3. Direct Implementation (`auggie-implement-direct.yml`)
**Purpose:** Directly implement issue requirements and create a pull request.

**Triggers:**
- Add the `auggieImplementDirect` label to an issue
- Comment `/auggieImplementDirect` on an issue

**What it does:**
- Creates a new branch from the issue
- Implements the requested changes
- Creates a pull request with the implementation
- Adds `auggie-in-progress` label during processing
- Adds a 🚀 reaction when complete

**Branch naming:** `auggie/issue-{number}-{title-slug}`

**Use case:** Automate implementation of well-defined issues or small features.

---

### 4. Test Generation (`auggie-test-generate.yml`)
**Purpose:** Auto-generate unit tests for pull request changes.

**Triggers:**
- Comment `/auggieTestGenerate` on a pull request

**What it does:**
- Analyzes the PR changes
- Generates appropriate unit tests
- Commits tests to the PR branch
- Adds a 🚀 reaction to the trigger comment

**Use case:** Quickly generate test coverage for new code changes.

---

### 5. Build Triage (`auggie-triage.yml`)
**Purpose:** Analyze CI/CD failures and create fix pull requests.

**Triggers:**
- Manual workflow dispatch from Actions tab

**What it does:**
- Analyzes failed workflow run logs
- Identifies the root cause
- Creates a fix branch
- Implements the fix
- Creates a PR with the solution
- Posts a workflow summary with links

**Branch naming:** `auggie/fix-{service}-{short-sha}`

**Use case:** Automate debugging and fixing of CI/CD pipeline failures.

---

## 📋 Quick Start

### Prerequisites

1. **Required Secrets**

   Add these secrets to your repository (Settings → Secrets and variables → Actions):

   - `AUGMENT_SESSION_AUTH` - **Required for all workflows**
     - Augment authentication token for AI agent access

   - `GH_TOKEN` - **Required for some workflows**
     - GitHub Personal Access Token with `repo` and `workflow` permissions
     - Used by: `auggie-triage.yml`, `auggie-test-generate.yml`

   - `SLACK_WEBHOOK_URL` - **Optional**
     - For Slack notifications (currently only in triage workflow)

2. **Required Labels**

   Create these labels in your repository (Issues → Labels → New label):

   | Label | Color | Description | Used By |
   |-------|-------|-------------|---------|
   | `auggieSpec` | `#0E8A16` | Trigger spec generation | Spec Generation |
   | `auggieImplementPlan` | `#1D76DB` | Trigger implementation planning | Implementation Plan |
   | `auggieImplementDirect` | `#5319E7` | Trigger direct implementation | Direct Implementation |
   | `auggie-in-progress` | `#FBCA04` | AI agent is working | Plan, Implementation |
   | `plan-ready` | `#0E8A16` | Implementation plan is ready | Implementation Plan |

### Setup Steps

1. **Choose a workflow template** from the `workflow-templates/` directory

2. **Copy to your repository**
   ```bash
   cp workflow-templates/auggie-spec.yml .github/workflows/
   ```

3. **Customize the template** (see Customization section below)

4. **Add required secrets** to your repository settings

5. **Create required labels** in your repository

6. **Test the workflow** by triggering it on a test issue/PR

---

## 📁 Repository Structure

```
.github/
├── README.md                              # This file
├── workflows/
│   └── org-*.yml                          # Reusable workflow definitions (called by templates)
└── workflow-templates/
    ├── README.md                          # Template-specific documentation
    ├── auggie-spec.yml                    # Spec generation caller template
    ├── auggie-plan.yml                    # Implementation plan caller template
    ├── auggie-plan.properties.json        # Plan workflow metadata
    ├── auggie-implement-direct.yml        # Direct implementation caller template
    ├── auggie-test-generate.yml           # Test generation caller template
    └── auggie-triage.yml                  # Build triage caller template
```

**Key concepts:**
- **Caller templates** (`workflow-templates/*.yml`): Copy these to your repo and customize
- **Reusable workflows** (`workflows/org-*.yml`): Centralized logic, called by templates
- **Properties files** (`.properties.json`): Metadata for GitHub's workflow template UI

---

## 🔧 Customization

All workflow templates support customization through clearly marked sections. Here are the common customization points:

### 1. Trigger Labels and Commands

**Change the trigger label:**
```yaml
# In auggie-spec.yml, line 48
const TRIGGER_LABEL = 'auggieSpec';  # Change to your preferred label
```

**Change the slash command:**
```yaml
# In auggie-spec.yml, line 49
const TRIGGER_COMMAND = '/auggieApproveSpec';  # Change to your preferred command
```

### 2. Branch Naming

**For implementation workflows:**
```yaml
# In auggie-implement-direct.yml, line 122
const branchName = `auggie/issue-${issue.number}-${slug}`;  # Customize prefix
```

**For triage workflow:**
```yaml
# In auggie-triage.yml
branch_prefix: 'auggie/fix'  # Change when triggering the workflow
```

### 3. Custom Instructions

Add project-specific context to help the AI understand your codebase:

```yaml
custom_instructions: |
  ## Project Context
  - This project uses TypeScript with strict mode enabled
  - Follow the repository's existing error handling patterns
  - All new features must include unit tests using Jest
  - API changes must maintain backward compatibility

  ## Coding Standards
  - Use functional components with hooks (React)
  - Prefer composition over inheritance
  - Follow the existing file structure in src/

  ## Testing Requirements
  - Minimum 80% code coverage
  - Include both unit and integration tests
  - Mock external API calls
```

### 4. AI Model Selection

Specify a different AI model if needed:

```yaml
# In any workflow template
model: 'claude-3-5-sonnet-20241022'  # or other available models
```

### 5. Test Framework Configuration

**For test generation workflow:**
```yaml
test_framework: 'pytest'      # Options: pytest, jest, go, mocha, etc.
test_directory: 'tests/'      # Where to place generated tests
```

---

## 📚 Usage Examples

### Example 1: Generate a Spec for a New Feature

1. Create an issue with your feature request:
   ```
   Title: Add user authentication

   Description:
   We need to add JWT-based authentication to the API.
   Users should be able to login with email/password.
   ```

2. Add the `auggieSpec` label to the issue

3. Wait for the AI to generate a technical specification (usually 2-5 minutes)

4. Review the spec in the issue comments

5. Iterate by adding comments with clarifications if needed

### Example 2: Create an Implementation Plan

1. Start with an issue (with or without a spec)

2. Add the `auggieImplementPlan` label

3. The AI will:
   - Analyze the issue and comments
   - Break down the work into tasks
   - Identify dependencies
   - Suggest an implementation order

4. Review the plan and adjust as needed

5. Use the plan to guide manual implementation or trigger direct implementation

### Example 3: Direct Implementation

1. Create a well-defined issue:
   ```
   Title: Fix login button alignment

   Description:
   The login button on the homepage is misaligned.
   It should be centered horizontally in its container.
   File: src/components/LoginButton.tsx
   ```

2. Add the `auggieImplementDirect` label

3. The AI will:
   - Create a branch: `auggie/issue-123-fix-login-button-alignment`
   - Make the necessary changes
   - Create a PR with the fix

4. Review the PR and merge if acceptable

### Example 4: Generate Tests for a PR

1. Create a PR with your changes

2. Comment `/auggieTestGenerate` on the PR

3. The AI will:
   - Analyze your code changes
   - Generate appropriate unit tests
   - Commit the tests to your PR branch

4. Review the generated tests and adjust as needed

### Example 5: Triage a Failed Build

1. Note the workflow run ID of a failed build (e.g., `12345678`)

2. Go to Actions → Augment Build Triage → Run workflow

3. Fill in the inputs:
   ```
   Workflow Run ID: 12345678
   Failed Services: api-service (optional)
   Source Branch: main (optional)
   ```

4. The AI will:
   - Analyze the failure logs
   - Create a fix branch
   - Implement the fix
   - Create a PR

5. Review and merge the fix PR

---

## 🛠️ Troubleshooting

### Common Issues

#### 1. Workflow doesn't trigger

**Problem:** Added label but workflow didn't run

**Solutions:**
- ✅ Verify the workflow file is in `.github/workflows/` (not `workflow-templates/`)
- ✅ Check that the label name matches exactly (case-sensitive)
- ✅ Ensure the workflow file has correct YAML syntax
- ✅ Check Actions tab for any workflow errors

#### 2. Authentication errors

**Problem:** `Error: Authentication failed` or `401 Unauthorized`

**Solutions:**
- ✅ Verify `AUGMENT_SESSION_AUTH` secret is set in repository settings
- ✅ Check that the secret value is correct (no extra spaces)
- ✅ Ensure the secret hasn't expired
- ✅ For `GH_TOKEN`, verify it has `repo` and `workflow` permissions

#### 3. Label not found errors

**Problem:** Workflow fails with "Label not found" error

**Solutions:**
- ✅ Create the required labels in your repository (see Quick Start section)
- ✅ Verify label names match exactly (case-sensitive)
- ✅ Check that labels exist before the workflow tries to add/remove them

#### 4. Branch creation fails

**Problem:** `Error: Branch already exists` or `Permission denied`

**Solutions:**
- ✅ Delete the existing branch if it's no longer needed
- ✅ Ensure the workflow has `contents: write` permission
- ✅ Check that branch protection rules aren't blocking the workflow
- ✅ Verify the repository isn't archived or read-only

#### 5. AI agent produces unexpected results

**Problem:** Generated code/spec doesn't match expectations

**Solutions:**
- ✅ Add more detailed `custom_instructions` with project context
- ✅ Provide clearer issue descriptions with examples
- ✅ Include relevant code snippets or file paths in the issue
- ✅ Add comments to the issue with clarifications
- ✅ Try a different AI model if available

#### 6. Workflow runs but produces no output

**Problem:** Workflow completes but no comment/PR is created

**Solutions:**
- ✅ Check the workflow run logs in the Actions tab
- ✅ Verify the reusable workflow is accessible (not in a private repo)
- ✅ Ensure the workflow has necessary permissions (`issues: write`, `pull-requests: write`)
- ✅ Check for rate limiting issues with GitHub API

#### 7. Test generation fails

**Problem:** `/auggieTestGenerate` doesn't work on PR

**Solutions:**
- ✅ Verify you're commenting on a PR, not an issue
- ✅ Check that `GH_TOKEN` secret is set (required for test generation)
- ✅ Ensure the PR branch is accessible and not protected
- ✅ Verify the test framework is correctly specified

### Getting Help

If you encounter issues not covered here:

1. **Check workflow logs:** Go to Actions tab → Select the failed workflow → View logs
2. **Review reusable workflow:** Check the org-wide workflow definition for updates
3. **Test with minimal example:** Try the workflow on a simple test issue
4. **Contact support:** Reach out to the Augment Solutions team

---

## 🔄 Updating Workflows

To update to the latest version of a workflow:

1. **Check for updates** in this repository's `workflow-templates/` directory

2. **Review the changelog** (if available) for breaking changes

3. **Update your caller template:**
   ```bash
   # Backup your current version
   cp .github/workflows/auggie-spec.yml .github/workflows/auggie-spec.yml.backup

   # Copy the new version
   cp workflow-templates/auggie-spec.yml .github/workflows/
   ```

4. **Restore your customizations** from the backup file

5. **Test the updated workflow** on a test issue/PR

---

## 📖 Additional Resources

- **Workflow Templates Directory:** [`workflow-templates/`](./workflow-templates/)
- **Template-Specific Documentation:** [`workflow-templates/README.md`](./workflow-templates/README.md)
- **Augment Documentation:** [Augment Code Docs](https://docs.augmentcode.com)
- **GitHub Actions Documentation:** [GitHub Actions](https://docs.github.com/en/actions)

---

## 🤝 Contributing

To add a new workflow template:

1. Create the reusable workflow in `.github/workflows/org-*.yml`
2. Create the caller template in `workflow-templates/`
3. Add comprehensive comments explaining customization options
4. Update this README with documentation for the new workflow
5. Create a `.properties.json` file for GitHub's template UI (optional)
6. Test the template in a sample repository
7. Submit a pull request

---

## 📝 License

This repository is maintained by Augment Solutions for use across the organization.

