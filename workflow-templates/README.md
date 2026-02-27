# Workflow Templates

This directory contains GitHub Actions workflow templates that can be used in repositories across the organization.

## Available Templates

### `auggie-triage.yml` - Augment Build Triage

Automatically triage and fix failed CI builds using Augment AI.

**Use Case:** When a CI build fails, manually trigger this workflow to have an AI agent analyze the failure, create a fix branch, and submit a PR with the fix.

**Setup:**
1. Copy `auggie-triage.yml` to your repository's `.github/workflows/` directory
2. Add required secrets to your repository:
   - `AUGMENT_SESSION_AUTH` - Augment authentication token
   - `GH_TOKEN` - GitHub token with repo and workflow permissions
3. Optional: Add `SLACK_WEBHOOK_URL` secret for Slack notifications

**Usage:**
1. Go to Actions tab in your repository
2. Select "Augment Build Triage" workflow
3. Click "Run workflow"
4. Enter the failed workflow run ID
5. Optionally specify failed services and source branch
6. Click "Run workflow"

**Customization:**
- **Branch Prefix:** Change the prefix for fix branches (default: `auggie/fix`)
- **Custom Instructions:** Add repository-specific instructions for the AI agent
- **Model:** Specify a different AI model if needed
- **Slack Notifications:** Uncomment the Slack notification step to get alerts

**Outputs:**
- Creates a fix branch with the pattern: `{branch_prefix}-{service}-{short_sha}`
- Creates a PR with the fix
- Posts a workflow summary with links to the fix branch and PR

## How to Use These Templates

1. **Copy to your repository:** Copy the desired template file to your repository's `.github/workflows/` directory
2. **Configure secrets:** Add the required secrets to your repository settings
3. **Customize:** Modify the template to fit your repository's needs (see customization comments in each template)
4. **Test:** Trigger the workflow manually to ensure it works as expected

## Contributing

To add a new workflow template:
1. Create the template file in this directory
2. Add comprehensive comments explaining customization options
3. Update this README with documentation for the new template
4. Test the template in a sample repository before committing

