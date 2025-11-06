# OpenCode Complete Guide

A comprehensive guide to OpenCode - an AI coding agent built for the terminal.

---

## Table of Contents

1. [What is OpenCode?](#what-is-opencode)
2. [OpenCode vs Cursor](#opencode-vs-cursor)
3. [Understanding `/init` Command](#understanding-init-command)
4. [OpenCode Agents Explained](#opencode-agents-explained)
5. [Usage Examples](#usage-examples)
6. [Key Features](#key-features)
7. [Custom Commands](#custom-commands)
8. [Tools System](#tools-system)
9. [Rules & Instructions](#rules-and-instructions)
10. [Sharing Conversations](#sharing-conversations)

---

## What is OpenCode?

OpenCode is an AI coding agent that runs in your terminal. It's designed to help you write code, understand codebases, fix bugs, and build features - all from the command line.

**Key Characteristics:**
- Terminal-native (TUI - Terminal User Interface)
- Works with any LLM provider (Anthropic, OpenAI, Google, etc.)
- Git-integrated for undo/redo functionality
- Highly customizable with agents, commands, and tools
- Can be used alongside your existing IDE

---

## OpenCode vs Cursor

### Why Use OpenCode Over Cursor?

| Feature | OpenCode | Cursor |
|---------|----------|--------|
| **Environment** | Terminal-based | IDE (VS Code fork) |
| **Integration** | Works with ANY editor | Must use Cursor IDE |
| **Workflow** | Terminal-centric developers | GUI-centric developers |
| **LLM Providers** | Any provider (Anthropic, OpenAI, Google, local models) | Limited to supported providers |
| **Git Integration** | Built-in undo/redo with Git | File-level history |
| **Agents System** | Multiple specialized agents (Build, Plan, custom) | Single AI assistant |
| **Customization** | Highly configurable (agents, tools, commands) | Limited customization |
| **Sharing** | Share full conversation sessions | Share code snippets |
| **Cost** | Pay-per-use with your API keys | Subscription-based |
| **Remote Work** | SSH-friendly, terminal-based | Requires GUI, heavier |

### When to Use OpenCode

**Choose OpenCode if you:**
- Prefer terminal-based workflows
- Want to keep your existing editor (Vim, Neovim, Emacs, VS Code, etc.)
- Need to work over SSH or on remote servers
- Want full control over which LLM provider you use
- Prefer pay-per-use pricing with your own API keys
- Need specialized agents for different tasks (planning vs building)
- Want Git-integrated undo/redo functionality
- Value lightweight, fast tooling

**Choose Cursor if you:**
- Prefer an integrated IDE experience
- Want GUI-based code editing with AI assistance
- Are comfortable with VS Code
- Don't want to manage API keys
- Prefer a subscription model

---

## Understanding `/init` Command

### What Does `/init` Do?

The `/init` command creates an `AGENTS.md` file in your project root. This is **NOT** like `git init` - it doesn't initialize version control.

**What `/init` Actually Does:**

1. **Scans Your Project**: Analyzes your codebase structure, file types, frameworks used
2. **Generates Context**: Creates a markdown file with project-specific information
3. **Creates AGENTS.md**: Writes project guidelines for the AI to follow
4. **Improves AI Understanding**: Helps OpenCode understand your project's conventions

### Example: Before and After `/init`

**Before `/init`:**
```
You: "Add authentication to the API"
OpenCode: *searches blindly, might miss your auth patterns*
```

**After `/init`:**
```
You: "Add authentication to the API"
OpenCode: *knows you use JWT tokens, understands your middleware pattern,
          knows where auth functions live, follows your coding style*
```

### What Gets Generated in AGENTS.md

Example `AGENTS.md` file:

```markdown
# My Web App Project

This is a Next.js 14 application using TypeScript, Tailwind CSS, and Prisma ORM.

## Project Structure
- `app/` - Next.js app router pages and layouts
- `components/` - Reusable React components
- `lib/` - Utility functions and shared code
- `prisma/` - Database schema and migrations

## Code Standards
- Use TypeScript with strict mode
- React components use function syntax with hooks
- API routes follow RESTful conventions
- Database queries use Prisma client

## Authentication
- Uses NextAuth.js with JWT strategy
- Auth middleware in `lib/auth.ts`
- Protected routes check for valid session

## Testing
- Jest for unit tests
- Playwright for E2E tests
- Run with `npm test`
```

### Why Use `/init`?

1. **Better Context**: AI understands your project conventions
2. **Consistent Code**: Follows your patterns instead of making assumptions
3. **Team Alignment**: Share conventions with teammates (commit AGENTS.md to Git)
4. **Faster Development**: Less explanation needed in prompts
5. **Project Memory**: AI remembers project structure across sessions

### When to Run `/init`

- **First time** using OpenCode in a project
- After **major refactors** or structural changes
- When **adding new frameworks** or significant dependencies
- After **onboarding new team members** (ensures consistency)

---

## OpenCode Agents Explained

### What Are Agents?

Agents are specialized AI assistants with different capabilities and restrictions. Think of them as different "modes" with specific purposes.

### Types of Agents

#### 1. Primary Agents
Primary agents are the main assistants you interact with. Switch between them with **Tab** key.

**Built-in Primary Agents:**

##### Build Agent (Default)
```json
{
  "mode": "primary",
  "tools": {
    "write": true,
    "edit": true,
    "bash": true,
    "read": true
  }
}
```
- **Purpose**: Full development work
- **Capabilities**: Can read, write, edit files, and run commands
- **When to Use**: Building features, fixing bugs, refactoring

##### Plan Agent
```json
{
  "mode": "primary",
  "tools": {
    "write": false,
    "edit": false,
    "bash": false,
    "read": true
  }
}
```
- **Purpose**: Analysis and planning without making changes
- **Capabilities**: Can only read files, cannot modify anything
- **When to Use**: Code review, suggesting changes, understanding code

**Switching Agents:**
```bash
# Press Tab to cycle between Build and Plan agents
<Tab>

# You'll see an indicator in the bottom-right corner
[Build Mode] or [Plan Mode]
```

#### 2. Subagents
Subagents are specialized workers that primary agents can invoke for specific tasks.

**Built-in Subagent:**

##### General Agent
```json
{
  "mode": "subagent",
  "description": "Researches complex questions, searches code"
}
```
- **Purpose**: Multi-step research and complex searches
- **Invocation**: Automatically or via `@general mention`
- **When to Use**: Finding specific code patterns, complex investigations

**Using Subagents:**
```bash
# Manually invoke a subagent
@general help me find all authentication middleware in this codebase

# Or let the primary agent invoke it automatically
Where are all the API error handlers implemented?
# (Primary agent might invoke general subagent for complex search)
```

### Creating Custom Agents

#### Example 1: Documentation Writer Agent

**~/.config/opencode/agent/docs-writer.md:**
```markdown
---
description: Writes and maintains project documentation
mode: subagent
tools:
  bash: false
  write: true
  edit: true
---

You are a technical writer. Create clear, comprehensive documentation.

Focus on:
- Clear explanations
- Proper structure
- Code examples
- User-friendly language
```

**Usage:**
```bash
@docs-writer Update the README with installation instructions
```

#### Example 2: Security Auditor Agent

**~/.config/opencode/agent/security.md:**
```markdown
---
description: Performs security audits and identifies vulnerabilities
mode: subagent
temperature: 0.1
tools:
  write: false
  edit: false
---

You are a security expert. Focus on identifying potential security issues.

Look for:
- Input validation vulnerabilities
- Authentication and authorization flaws
- Data exposure risks
- Dependency vulnerabilities
- Configuration security issues
```

**Usage:**
```bash
@security Audit the authentication system for vulnerabilities
```

#### Example 3: Fast Planning Agent

**opencode.json:**
```json
{
  "agent": {
    "fast-plan": {
      "description": "Quick planning with a faster model",
      "mode": "primary",
      "model": "anthropic/claude-haiku-4-20250514",
      "temperature": 0.2,
      "tools": {
        "write": false,
        "edit": false,
        "bash": false
      }
    }
  }
}
```

### Agent Workflow Example

**Complete Feature Development:**

```bash
# Step 1: Switch to Plan mode
<Tab>

# Step 2: Ask for a plan
User: I want to add user profile pages with edit functionality.
      Use the same design patterns as our dashboard pages.

Plan Agent: Here's my implementation plan:
            1. Create profile page component at app/profile/[id]/page.tsx
            2. Add profile edit form component
            3. Create API route for profile updates
            4. Add validation using Zod schema
            5. Update navigation to include profile link

# Step 3: Iterate on the plan
User: Also add avatar upload functionality

Plan Agent: Updated plan:
            1-5. (same as before)
            6. Add file upload component
            7. Create avatar storage using your existing S3 setup
            8. Add image optimization

# Step 4: Switch to Build mode
<Tab>

# Step 5: Execute the plan
User: Looks good! Go ahead and implement it.

Build Agent: *starts implementing the feature step by step*
```

---

## Usage Examples

### Example 1: Understanding a Codebase

```bash
# General question about the codebase
Give me an overview of how this application works

# Ask about specific files (use @ for fuzzy search)
How is authentication handled in @src/middleware/auth.ts?

# Understand a specific feature
Explain how the payment processing flow works

# Find implementation details
Where is the user registration logic implemented?
```

### Example 2: Adding Features

#### Simple Feature (Direct Build)
```bash
# Switch to Build mode if not already
<Tab> # (until you see [Build Mode])

# Request the feature with context
Add a dark mode toggle to the settings page.
Use the same toggle component we use in @components/ui/switch.tsx
Store the preference in local storage like we do for other settings.
```

#### Complex Feature (Plan First)
```bash
# Step 1: Switch to Plan mode
<Tab>

# Step 2: Request a detailed plan
I need to implement a real-time notification system.
Users should see notifications in a dropdown in the navbar.
Notifications should persist in the database.
Use WebSockets for real-time updates.
[Drag and drop a design mockup image]

# Step 3: Review and iterate
Plan Agent: *provides detailed plan*

User: Looks good, but let's use Server-Sent Events instead of WebSockets
      since we only need server-to-client communication.

Plan Agent: *updates plan with SSE approach*

# Step 4: Switch to Build mode and execute
<Tab>
Implement this plan!

Build Agent: *starts building feature*
```

### Example 3: Debugging

```bash
# Report a bug with context
The login form isn't working. When I submit credentials,
I get a 500 error. Check @app/api/auth/login/route.ts

# Let OpenCode investigate
Why am I getting a CORS error when calling the API from localhost:3000?

# Debug with logs
!npm run dev
Run the dev server and look at the error logs. Then fix the issue.
```

### Example 4: Refactoring

```bash
# Request refactoring with specific guidelines
Refactor @lib/database.ts to use a connection pool instead of
creating a new connection for each query.
Maintain the same API so we don't break existing code.

# Break down large files
The @components/Dashboard.tsx file is too large.
Break it into smaller components following our component structure
in @components/analytics/.
```

### Example 5: Testing

```bash
# Add tests for a feature
Add unit tests for @lib/validation.ts
Use Jest and follow the testing patterns in @tests/lib/auth.test.ts

# Run tests and fix failures
!npm test
Fix any failing tests
```

### Example 6: Code Review

```bash
# Switch to Plan mode for review
<Tab>

# Request review
Review the changes in @src/api/users.ts
Check for security issues, performance problems, and code quality.
Don't make any changes, just provide feedback.

# Invoke security agent
@security Audit the new authentication middleware for vulnerabilities
```

### Example 7: Documentation

```bash
# Invoke docs writer agent
@docs-writer Create API documentation for all routes in @app/api/

# Update existing docs
Update the CONTRIBUTING.md with our new testing requirements
and code style guidelines.
```

### Example 8: Using Undo/Redo

```bash
# Request a change
Refactor the error handling in @lib/api-client.ts

# Oops, didn't like the changes
/undo

# This reverts file changes via Git and shows your original message again
# Now you can refine your request
Refactor the error handling in @lib/api-client.ts
But keep the existing error types, just improve the retry logic.

# If you change your mind again
/redo  # Restores the previous changes
```

### Example 9: Running Commands

```bash
# Run a command and let OpenCode see the output
!npm install lodash
!git status

# Chain commands
!npm run build && npm run test

# Let OpenCode react to command output
!npm run lint
Fix any linting errors that were reported
```

### Example 10: Referencing Multiple Files

```bash
# Use @ to reference multiple files
I want to add a new payment method.
Look at how we handle credit cards in @lib/payments/credit-card.ts
and implement PayPal following the same pattern.
Create the new file at @lib/payments/paypal.ts
```

---

## Key Features

### 1. File References with `@`

Fuzzy search for files in your prompts:

```bash
# Single file
Explain @src/utils/validation.ts

# Multiple files
Refactor @components/Button.tsx to match the style of @components/Input.tsx

# Works with any command
How does @api/users.ts interact with @lib/database.ts?
```

### 2. Bash Command Execution with `!`

Run commands and include output in conversation:

```bash
# View output
!ls -la src/

# Run scripts
!npm run build

# Git operations
!git log --oneline -10

# Check environment
!node --version
!npm list
```

### 3. Undo/Redo System

Git-based time travel for your coding session:

```bash
# Undo last message and all its changes
/undo

# Your original message is shown again, you can edit it
# File changes are reverted using Git

# Redo if needed
/redo

# Can undo multiple times
/undo
/undo
/undo
```

**How it works:**
- Requires a Git repository
- Creates hidden commits for change tracking
- Reverts files to previous state
- Shows your original prompt for editing

### 4. Session Management

```bash
# Start a new session
/new

# List and resume previous sessions
/sessions

# Each session maintains its own context
```

### 5. Model Switching

```bash
# List available models
/models

# See which providers and models you can use
# Switch models in your config or per-agent
```

### 6. Theme Customization

```bash
# List available themes
/themes

# Configure in opencode.json
{
  "theme": "dracula"
}
```

### 7. Sharing Conversations

```bash
# Share current conversation
/share
# Copies public URL to clipboard

# Unshare
/unshare

# Enable auto-share in config
{
  "share": "auto"  # or "manual" (default) or "disabled"
}
```

### 8. External Editor

```bash
# Open external editor for long prompts
/editor

# Uses your $EDITOR environment variable
# Useful for writing detailed requirements

# Export conversation to view/edit
/export
```

---

## Custom Commands

### What Are Custom Commands?

Reusable slash commands for repetitive tasks. Like Git aliases but for AI prompts.

### Creating Custom Commands

#### Method 1: Markdown Files

**.opencode/command/test.md:**
```markdown
---
description: Run tests with coverage
agent: build
model: anthropic/claude-3-5-sonnet-20241022
---

Run the full test suite with coverage report and show any failures.
Focus on the failing tests and suggest fixes.
```

**Usage:**
```bash
/test
```

#### Method 2: JSON Config

**opencode.json:**
```json
{
  "command": {
    "test": {
      "template": "Run the full test suite with coverage report.",
      "description": "Run tests with coverage",
      "agent": "build"
    }
  }
}
```

### Custom Command Examples

#### 1. Component Generator

**.opencode/command/component.md:**
```markdown
---
description: Create a new React component
---

Create a new React component named $ARGUMENTS with TypeScript.
Include:
- Proper TypeScript types
- Props interface
- Export statement
- JSDoc comments

Follow the component structure in @components/ui/
Place it in components/ui/$ARGUMENTS.tsx
```

**Usage:**
```bash
/component Button
# Creates components/ui/Button.tsx
```

#### 2. Git Review

**.opencode/command/review-changes.md:**
```markdown
---
description: Review recent git changes
agent: plan
---

Recent git commits:
!`git log --oneline -10`

Changed files:
!`git diff --name-only HEAD~5`

Review these changes and suggest:
1. Any potential bugs
2. Code quality improvements
3. Security concerns
4. Performance optimizations
```

**Usage:**
```bash
/review-changes
```

#### 3. Deploy Checker

**.opencode/command/deploy-check.md:**
```markdown
---
description: Pre-deployment checks
---

Run pre-deployment checks:

!`npm run lint`
!`npm run type-check`
!`npm run test`
!`npm run build`

Analyze the results and confirm if the app is ready to deploy.
List any issues that need to be fixed.
```

**Usage:**
```bash
/deploy-check
```

#### 4. Documentation Generator

**.opencode/command/generate-docs.md:**
```markdown
---
description: Generate API documentation
agent: docs-writer
subtask: true
---

Generate comprehensive API documentation for:
$ARGUMENTS

Include:
- Endpoint descriptions
- Request/response examples
- Error codes
- Authentication requirements

Follow the format in @docs/api/example.md
```

**Usage:**
```bash
/generate-docs @app/api/users/route.ts
```

#### 5. Security Audit

**.opencode/command/security-audit.md:**
```markdown
---
description: Run security audit
agent: security
subtask: true
---

Perform a security audit on $ARGUMENTS

Check for:
- SQL injection vulnerabilities
- XSS vulnerabilities
- Authentication issues
- Authorization bypass
- Sensitive data exposure
- CSRF vulnerabilities

Provide a detailed report with severity ratings.
```

**Usage:**
```bash
/security-audit @api/
```

### Advanced Command Features

#### Using Arguments

**Commands can accept positional arguments:**

```markdown
---
description: Create files
---

Create a file named $1 in directory $2 with content: $3
```

```bash
/create-file config.json src '{"key": "value"}'
# $1 = config.json
# $2 = src
# $3 = {"key": "value"}
```

#### Command Output Injection

**Include bash command output in prompts:**

```markdown
---
description: Analyze dependencies
---

Current dependencies:
!`npm list --depth=0`

Analyze these dependencies and:
1. Identify outdated packages
2. Suggest security updates
3. Recommend performance improvements
```

#### File References in Commands

```markdown
---
description: Compare implementations
---

Compare the implementation in @$ARGUMENTS
with the pattern in @lib/patterns/standard.ts

Suggest improvements to align with our standard pattern.
```

```bash
/compare @services/auth.ts
```

---

## Tools System

### What Are Tools?

Tools are capabilities the AI can use to interact with your codebase and environment.

### Built-in Tools

| Tool | Purpose | Default |
|------|---------|---------|
| `bash` | Execute shell commands | Enabled |
| `edit` | Modify existing files | Enabled |
| `write` | Create new files | Enabled |
| `read` | Read file contents | Enabled |
| `grep` | Search file contents | Enabled |
| `glob` | Find files by pattern | Enabled |
| `list` | List directory contents | Enabled |
| `webfetch` | Fetch web content | Enabled |
| `todowrite` | Manage todo lists | Enabled |
| `todoread` | Read todo lists | Enabled |

### Configuring Tools

#### Global Configuration

**opencode.json:**
```json
{
  "tools": {
    "write": true,
    "bash": true,
    "webfetch": true
  }
}
```

#### Per-Agent Configuration

**opencode.json:**
```json
{
  "tools": {
    "write": true,
    "bash": true
  },
  "agent": {
    "plan": {
      "tools": {
        "write": false,
        "bash": false
      }
    },
    "readonly": {
      "tools": {
        "write": false,
        "edit": false,
        "bash": false
      }
    }
  }
}
```

### Tool Permissions

Control which actions require approval:

**opencode.json:**
```json
{
  "permission": {
    "edit": "ask",     // Ask before editing
    "bash": {
      "git push": "ask",      // Ask before pushing
      "git *": "allow",       // Allow other git commands
      "rm -rf": "deny",       // Never allow
      "*": "ask"              // Ask for everything else
    },
    "webfetch": "allow"
  }
}
```

**Permission Levels:**
- `"allow"` - Execute without asking
- `"ask"` - Prompt for approval
- `"deny"` - Block execution

### Tool Examples

#### Restricting Dangerous Operations

```json
{
  "permission": {
    "bash": {
      "rm -rf *": "deny",
      "npm publish": "ask",
      "git push": "ask",
      "*": "allow"
    }
  }
}
```

#### Creating a Safe Planning Agent

```json
{
  "agent": {
    "safe-plan": {
      "description": "Planning with no file modifications",
      "mode": "primary",
      "tools": {
        "write": false,
        "edit": false,
        "bash": false,
        "read": true,
        "grep": true,
        "glob": true
      }
    }
  }
}
```

---

## Rules and Instructions

### AGENTS.md File

Project-specific instructions that help OpenCode understand your codebase.

**Example AGENTS.md:**

```markdown
# E-Commerce Platform

Full-stack Next.js 14 application with TypeScript, Prisma, and Stripe.

## Project Structure
- `app/` - Next.js App Router (pages, layouts, API routes)
- `components/` - React components
  - `components/ui/` - Reusable UI components
  - `components/features/` - Feature-specific components
- `lib/` - Utility functions and shared logic
- `prisma/` - Database schema and migrations
- `public/` - Static assets

## Tech Stack
- **Frontend**: Next.js 14, React 18, TypeScript, Tailwind CSS
- **Backend**: Next.js API Routes, Prisma ORM
- **Database**: PostgreSQL
- **Authentication**: NextAuth.js with JWT
- **Payments**: Stripe
- **Deployment**: Vercel

## Code Standards

### TypeScript
- Use strict mode
- Explicit return types for functions
- No `any` types - use `unknown` and type guards
- Use interfaces for object shapes

### React Components
- Function components with TypeScript
- Use React hooks (useState, useEffect, etc.)
- Custom hooks in `lib/hooks/`
- Prop types defined as interfaces

### File Naming
- Components: PascalCase (e.g., `UserProfile.tsx`)
- Utilities: camelCase (e.g., `formatCurrency.ts`)
- API routes: kebab-case (e.g., `create-order.ts`)

### API Routes
- RESTful conventions
- Proper HTTP status codes
- Error handling middleware in `lib/api/errors.ts`
- Input validation using Zod schemas

## Database
- Prisma ORM for all database operations
- Migrations in `prisma/migrations/`
- Run `npm run db:push` to update schema
- Run `npm run db:seed` to seed development data

## Authentication
- NextAuth.js with JWT strategy
- Session middleware in `lib/auth.ts`
- Protected routes use `getServerSession()`
- API routes check auth with `requireAuth()` middleware

## Testing
- Jest for unit tests
- React Testing Library for component tests
- Playwright for E2E tests
- Test files co-located with source: `*.test.ts`
- Run tests with `npm test`
- Run E2E with `npm run test:e2e`

## Styling
- Tailwind CSS for all styling
- Shared theme in `tailwind.config.js`
- Custom components in `components/ui/` using class-variance-authority
- Mobile-first responsive design

## Error Handling
- API errors use custom `ApiError` class
- Client-side errors logged to error boundary
- User-facing errors show toast notifications

## Performance
- Use Next.js Image component for images
- Lazy load components where appropriate
- Database queries optimized with Prisma includes
- API routes cached with Next.js caching headers

## Deployment
- Environment variables in `.env.local` (development)
- Production env vars in Vercel dashboard
- Database migrations run automatically on deploy
- Run `npm run build` to test production build locally
```

### Global Rules

**~/.config/opencode/AGENTS.md:**
```markdown
# Personal Coding Preferences

## Code Style
- I prefer functional programming patterns
- Use descriptive variable names, avoid abbreviations
- Add comments for complex logic
- Always include error handling

## Communication
- Explain your reasoning before making changes
- Ask clarifying questions if requirements are ambiguous
- Suggest improvements when you see opportunities

## Testing
- Always write tests for new features
- Maintain or improve test coverage
- Include edge cases in tests
```

### Instructions Array

Reference external documentation:

**opencode.json:**
```json
{
  "instructions": [
    "CONTRIBUTING.md",
    "docs/coding-standards.md",
    "docs/architecture.md",
    ".cursor/rules/*.md"
  ]
}
```

### Combining Rules

OpenCode combines rules from multiple sources:

1. Global rules (~/.config/opencode/AGENTS.md)
2. Project rules (./AGENTS.md)
3. Instructions array files
4. Agent-specific prompts

**Priority:** Agent prompts > Instructions > Project rules > Global rules

---

## Sharing Conversations

### Why Share?

- Get help from teammates
- Document problem-solving approaches
- Create examples for onboarding
- Debug issues collaboratively

### Sharing Methods

#### Manual Sharing (Default)

```bash
# Share when you want
/share

# Copies URL like: https://opencode.ai/s/abc123
# Share this link with your team
```

#### Auto-Sharing

**opencode.json:**
```json
{
  "share": "auto"
}
```

Every conversation automatically gets a shareable link.

#### Disable Sharing

**opencode.json:**
```json
{
  "share": "disabled"
}
```

Useful for sensitive projects or compliance requirements.

### Privacy Considerations

**What Gets Shared:**
- Full conversation history
- All messages and responses
- Session metadata

**What Doesn't Get Shared:**
- Your actual source code files (unless pasted in messages)
- Environment variables
- API keys
- Local file system access

**Best Practices:**
- Review conversations before sharing
- Don't share sessions with sensitive data
- Unshare when collaboration is complete
- For proprietary work, disable sharing entirely

### Unsharing

```bash
# Remove shared conversation
/unshare

# This deletes the conversation from servers
# Link becomes invalid
```

---

## Complete Workflow Example

Here's a full example of using OpenCode to build a feature:

```bash
# 1. Start OpenCode in your project
cd ~/projects/my-app
opencode

# 2. Initialize the project (first time only)
/init

# 3. Switch to Plan mode
<Tab>

# 4. Request a feature plan
I want to add a comment system to blog posts.
Requirements:
- Users can add comments to posts
- Comments have replies (nested comments)
- Display in threaded view
- Real-time updates when new comments arrive

Look at our existing post system in @app/blog/[slug]/page.tsx
for reference on our design patterns.

[Drag and drop mockup.png]

# 5. Review the plan
Plan Agent: Here's my implementation plan:

1. Database Schema
   - Create Comment model in Prisma schema
   - Add parent_id for nested comments
   - Include user_id and post_id foreign keys

2. API Routes
   - POST /api/comments - Create comment
   - GET /api/comments/[postId] - Fetch comments
   - DELETE /api/comments/[id] - Delete comment

3. Components
   - CommentList - Display threaded comments
   - CommentItem - Individual comment with replies
   - CommentForm - Input form for new comments

4. Real-time Updates
   - Use Server-Sent Events
   - SSE endpoint at /api/comments/stream
   - Client subscribes to updates

5. Features
   - Markdown support in comments
   - Edit/delete own comments
   - Admin can delete any comment
   - Pagination for long threads

# 6. Ask for clarifications
User: Looks good! But use Server-Sent Events only in production.
      For development, use polling every 5 seconds.
      Also add rate limiting on the comment API.

Plan Agent: *updates plan with polling fallback and rate limiting*

# 7. Switch to Build mode and execute
<Tab>

User: Perfect! Go ahead and implement this.

Build Agent: I'll implement the comment system. Let me start by updating
             the Prisma schema...

[AI starts implementing step by step]

# 8. Run migrations
Build Agent: I've updated the schema. Let me run the migration:
!`npm run prisma:migrate:dev`

# 9. Test the implementation
Build Agent: Implementation complete. Let me start the dev server
             to verify everything works:

!`npm run dev`

# 10. Manual testing, find an issue
User: The nested comments aren't indenting properly.

Build Agent: Let me check the styling in CommentItem.tsx...
             *fixes the issue*

# 11. Add tests
User: Add tests for the comment API routes.
      Follow our testing pattern in @app/api/__tests__/posts.test.ts

Build Agent: *creates comprehensive tests*

# 12. Run tests
!`npm test`

Build Agent: All tests passing! The comment system is ready.

# 13. Share the session with your team
/share

# Link copied: https://opencode.ai/s/xyz789
# Send this to your team for review

# 14. Create documentation
@docs-writer Document the new comment system in docs/features/comments.md
Include API endpoints, component usage, and configuration options.

# 15. Security audit
@security Audit the comment system for vulnerabilities,
especially around XSS, SQL injection, and authorization.

Security Agent: *provides security audit report*
Found potential XSS vulnerability in comment rendering.
Comments should be sanitized before display.

# 16. Fix security issues
User: Fix the XSS vulnerability mentioned in the audit.

Build Agent: *adds proper sanitization*

# 17. Final review
User: <Tab> (switch to Plan mode)
      Review all the changes we made today and create a summary.

Plan Agent: *provides comprehensive summary of changes*

# 18. Commit the changes
User: <Tab> (switch to Build mode)
      Create a git commit with an appropriate message.

Build Agent:
!`git add .`
!`git commit -m "feat: Add threaded comment system with real-time updates

- Add Comment model to Prisma schema
- Implement comment API routes with rate limiting
- Create CommentList and CommentForm components
- Add SSE for production, polling for development
- Include comprehensive tests
- Add XSS protection with comment sanitization"`
```

---

## Pro Tips

### 1. Use Plan Mode First for Complex Tasks

Don't jump straight into building complex features. Review the plan first.

```bash
<Tab>  # Switch to Plan mode
# Request plan
# Review and iterate
<Tab>  # Switch to Build mode
# Execute
```

### 2. Provide Context with @

Always reference related files to give context:

```bash
# Bad
Add authentication to the dashboard

# Good
Add authentication to @app/dashboard/page.tsx
Use the same pattern as @app/profile/page.tsx
with the auth middleware from @lib/auth.ts
```

### 3. Use Custom Commands for Repetitive Tasks

Create commands for things you do often:

```bash
/test
/review-changes
/security-audit
/deploy-check
```

### 4. Leverage Agents for Different Purposes

```bash
@general    # Complex searches
@security   # Security audits
@docs-writer # Documentation
@plan       # Code review
```

### 5. Commit AGENTS.md to Git

Share project context with your team by committing `AGENTS.md` to version control.

### 6. Use Undo Liberally

Don't be afraid to experiment. You can always undo:

```bash
# Try something
Refactor this code

# Don't like it?
/undo

# Try a different approach
```

### 7. Break Down Large Tasks

```bash
# Instead of:
Build a complete e-commerce checkout system

# Do:
1. First, let's create the shopping cart component
2. <after that's done> Now add the checkout form
3. <after that's done> Now integrate Stripe payment
4. <after that's done> Now add order confirmation
```

### 8. Let OpenCode See Command Output

```bash
# Let OpenCode see errors
!npm run build

# Then it can fix them
Fix the TypeScript errors from the build output
```

### 9. Use Images for Design References

Drag and drop design mockups into the terminal:

```bash
Create a pricing page that looks like this:
[mockup.png]

Match the style of our existing pages.
```

### 10. Create Specialized Agents

Make your workflow more efficient with specialized agents:

```bash
# Create agents for:
- Code review
- Security audits  
- Documentation
- Performance optimization
- Testing
```

---

## Conclusion

OpenCode is a powerful terminal-based AI coding assistant that gives you:

✅ **Freedom**: Use any LLM provider, any editor
✅ **Control**: Specialized agents, custom commands, tool permissions
✅ **Integration**: Git-based undo/redo, terminal-native workflow
✅ **Flexibility**: Works over SSH, lightweight, highly customizable
✅ **Collaboration**: Share conversations with your team

It's perfect for developers who prefer terminal workflows, want fine-grained control over their AI assistant, and value integration with their existing tools.

**Get Started:**
```bash
# Install
curl -fsSL https://opencode.ai/install | bash

# Configure
opencode auth login

# Start coding
cd your-project
opencode
/init
```

**Learn More:**
- Docs: https://opencode.ai/docs
- GitHub: https://github.com/sst/opencode
- Discord: https://opencode.ai/discord

---

*Created with OpenCode - the AI coding agent for terminal workflows*
