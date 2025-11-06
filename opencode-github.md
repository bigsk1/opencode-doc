# OpenCode GitHub Integration Guide

OpenCode brings powerful AI coding assistance directly into your GitHub workflow. With the `/opencode` command, you can leverage AI to analyze issues, implement features, fix bugs, and review pull requests - all within GitHub's familiar interface.

## What is OpenCode?

OpenCode is an AI coding agent that runs securely in your GitHub Actions environment, giving you access to advanced code analysis, implementation, and review capabilities directly from GitHub issues and pull requests.

## Getting Started

### Installation

To set up OpenCode in your repository, run this command in a local clone of your GitHub repo:

```bash
opencode github install
```

This interactive setup will:
1. Install the GitHub app
2. Create the necessary workflow file
3. Set up required secrets

### Manual Setup

If you prefer manual setup:

1. **Install the GitHub App**: Visit [github.com/apps/opencode-agent](https://github.com/apps/opencode-agent) and install it on your repository.

2. **Add the Workflow**: Create `.github/workflows/opencode.yml`:

```yaml
name: opencode
on:
  issue_comment:
    types: [created]

jobs:
  opencode:
    if: |
      contains(github.event.comment.body, '/oc') ||
      contains(github.event.comment.body, '/opencode')
    runs-on: ubuntu-latest
    permissions:
      id-token: write
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4
        with:
          fetch-depth: 1
      - name: Run opencode
        uses: sst/opencode/github@latest
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        with:
          model: anthropic/claude-sonnet-4-20250514
```

3. **Configure Secrets**: In your repository settings, add your API keys under "Secrets and variables" → "Actions".

### Configuration Options

- `model`: The AI model to use (required). Format: `provider/model`
- `share`: Whether to share OpenCode sessions (default: true for public repos)
- `token`: Optional GitHub token for custom authentication

## Using OpenCode in Issues

### Analyze and Explain Issues

Get AI analysis of complex issues:

```
/opencode explain this issue
```

OpenCode will:
- Read the entire issue thread including all comments
- Analyze the problem context
- Provide a clear explanation of what's happening
- Suggest potential approaches

### Fix Issues Automatically

Have OpenCode implement fixes for you:

```
/opencode fix this
```

OpenCode will:
- Analyze the issue requirements
- Create a new branch
- Implement the necessary changes
- Open a pull request with the solution
- Include detailed commit messages and PR descriptions

### Request Specific Features

Ask for detailed feature implementations:

```
/opencode implement user authentication with OAuth support
```

### Investigate Problems

Get help with debugging:

```
/opencode investigate why the login form is not working properly
```

## Using OpenCode in Pull Requests

### Code Review and Feedback

Get AI-powered code review:

```
/opencode review this PR and suggest improvements
```

### Request Changes

Ask for specific modifications to the PR:

```
/opencode add error handling to the database connection logic
```

### Optimization Suggestions

Get performance and code quality improvements:

```
/opencode optimize this code for better performance
```

### Documentation

Request documentation additions:

```
/opencode add comprehensive comments and documentation to these functions
```

## Advanced Features

### Contextual Understanding

OpenCode has access to:
- Full repository context
- Issue and PR history
- Code relationships and dependencies
- Project structure and patterns

### Multi-file Operations

OpenCode can work across multiple files:

```
/opencode refactor the user authentication system across all related files
```

### Testing Integration

Request test implementation:

```
/opencode add unit tests for the new payment processing feature
```

### Database Operations

Handle database-related changes:

```
/opencode create a migration for the new user preferences table
```

## Best Practices

### Writing Effective Prompts

1. **Be Specific**: Provide clear context and requirements
   ```
   ❌ /opencode fix the bug
   ✅ /opencode fix the null pointer exception in UserController.java when processing empty email addresses
   ```

2. **Include Context**: Reference relevant files or components
   ```
   /opencode implement rate limiting similar to how it's done in the auth middleware
   ```

3. **Specify Requirements**: Include any constraints or preferences
   ```
   /opencode add logging using Winston, following the existing logging patterns in the project
   ```

### Security Considerations

- OpenCode runs in your GitHub Actions environment
- All operations are logged in the Actions run
- API keys are stored securely as GitHub Secrets
- Sessions can be disabled or restricted for sensitive repositories

### Session Management

OpenCode automatically creates shareable session links that include:
- Complete conversation history
- All code changes made
- Links to GitHub Actions runs
- Visual session cards for easy sharing

## Command Variations

OpenCode responds to both:
- `/opencode` - Full command
- `/oc` - Shorthand version

You can use natural language after either command:

```
/opencode can you help me understand why this test is failing?
/oc implement a dark mode toggle for the dashboard
```

## Troubleshooting

### Common Issues

1. **OpenCode doesn't respond**: Check that the GitHub app is installed and the workflow file exists
2. **API errors**: Verify your API keys are correctly set in repository secrets
3. **Permission errors**: Ensure the workflow has appropriate permissions

### Getting Help

- Check GitHub Actions logs for detailed error messages
- Visit the [OpenCode documentation](https://opencode.ai/docs)
- Join the [OpenCode Discord community](https://opencode.ai/discord)
- Report issues at [github.com/sst/opencode](https://github.com/sst/opencode)

## Example Workflows

### Bug Fix Workflow
1. User reports bug in an issue
2. Comment: `/opencode investigate and fix the reported bug`
3. OpenCode analyzes, creates branch, implements fix, opens PR
4. Review the PR and merge when ready

### Feature Implementation
1. Create issue describing new feature
2. Comment: `/opencode implement this feature following our existing patterns`
3. OpenCode creates implementation plan and executes it
4. Iterate with additional comments if needed

### Code Review Enhancement
1. Open a pull request
2. Comment: `/opencode review this code and suggest security improvements`
3. OpenCode analyzes and provides detailed feedback
4. Make improvements based on suggestions

## Integration with Development Workflow

OpenCode seamlessly integrates with your existing GitHub workflow:

- **Issues**: Triage, analysis, and automated fixes
- **Pull Requests**: Review, enhancement, and modification requests  
- **Actions**: Secure execution environment with full repository access
- **Branches**: Automatic branch creation for new implementations
- **Sharing**: Collaborative session links for team coordination

By leveraging OpenCode in GitHub, you can accelerate development, improve code quality, and maintain better project documentation - all while working within your familiar GitHub environment.