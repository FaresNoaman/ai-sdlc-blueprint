# Claude Agent Prompts

This directory contains agent-specific prompt files for Claude Code when used with GitHub Spec Kit workflows.

## Purpose

These prompts provide context and instructions to Claude Code when it's invoked via GitHub Actions (e.g., for PR reviews or issue handling).

## Structure

```
claude/
├── README.md              # This file
├── pr-review.md           # Prompt for PR review automation
└── issue-to-pr.md         # Prompt for issue-to-PR automation
```

## Usage

These prompts are automatically loaded by the Spec Kit when Claude Code is invoked through GitHub Actions workflows.

### PR Review (claude-pr-review.yml)
When a PR is opened or updated, Claude Code uses the instructions in `pr-review.md` to:
- Check spec references
- Verify TDD compliance
- Identify security concerns
- Review architecture adherence

### Issue to PR (claude-issue-handler.yml)
When `@claude` is mentioned in an issue comment, Claude Code uses `issue-to-pr.md` to:
- Analyze the issue
- Create a feature branch
- Implement a solution following TDD
- Open a PR with appropriate spec references

## Customization

You can customize these prompts to enforce team-specific patterns, coding standards, or architectural decisions.

## Related Files
- `.specify/memory/constitution.md` - Project governance rules
- `CLAUDE.md` - Claude Code persistent context
- `.github/workflows/` - GitHub Actions workflows that invoke these prompts
