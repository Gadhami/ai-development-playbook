# Setup Guide

## Option 1: Direct Copy (Simplest)

1. Copy the entire `.github/` folder to your project root.
2. Pick a profile from `profiles/` and copy it to `.github/copilot-instructions.md`.
3. Edit the `<!-- CONFIGURE -->` sections with your project details.
4. Commit and push.

```bash
# Example: setting up a .NET backend project
cp -r /path/to/ai-dev-playbook/.github/ /path/to/your-project/.github/
cp /path/to/ai-dev-playbook/profiles/backend-dotnet.md /path/to/your-project/.github/copilot-instructions.md
```

## Option 2: Git Submodule (Auto-Updating)

Add this repo as a submodule, then symlink or copy the files:

```bash
cd /path/to/your-project

# Add as submodule
git submodule add https://github.com/Gadhami/ai-dev-playbook .github/ai-playbook

# Copy files (repeat after pulling submodule updates)
cp -r .github/ai-playbook/.github/instructions/ .github/instructions/
cp -r .github/ai-playbook/.github/agents/ .github/agents/
cp -r .github/ai-playbook/.github/prompts/ .github/prompts/
```

To update:
```bash
git submodule update --remote .github/ai-playbook
# Re-copy the shared files (your copilot-instructions.md is preserved)
cp -r .github/ai-playbook/.github/instructions/ .github/instructions/
cp -r .github/ai-playbook/.github/agents/ .github/agents/
cp -r .github/ai-playbook/.github/prompts/ .github/prompts/
```

## Option 3: Manual Sync

Pull the latest from the template repo and copy what you need:

```bash
cd /path/to/ai-dev-playbook
git pull

# Sync to your project (preserves your copilot-instructions.md)
cp -r .github/instructions/ /path/to/your-project/.github/instructions/
cp -r .github/agents/ /path/to/your-project/.github/agents/
cp -r .github/prompts/ /path/to/your-project/.github/prompts/
```

## After Setup

1. Open your project in VS Code.
2. GitHub Copilot will automatically load:
   - `.github/copilot-instructions.md` — for every conversation.
   - `.github/instructions/*.instructions.md` — based on file type (via `applyTo` patterns).
   - `.github/agents/*.agent.md` — as custom agents (invoke with `@agent-name`).
   - `.github/prompts/*.prompt.md` — as reusable prompts (invoke with `/prompt-name`).
3. No additional configuration needed.

## Customizing copilot-instructions.md

The profile files are starting points. You should customize:

1. **Project metadata** — Name, repo URL, specific tech stack versions.
2. **Architecture details** — If your project doesn't use Clean Architecture, adjust the architecture section.
3. **Test framework** — Match what your project actually uses (xUnit, NUnit, MSTest, Jest, Karma).
4. **Company-specific standards** — Add any org-level policies (see [hierarchy.md](hierarchy.md)).
5. **Product-specific context** — Add domain terminology, key business rules, or data model notes.
