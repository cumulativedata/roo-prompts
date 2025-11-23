# roo-prompts

A collection of AI agent prompts and commands for software development workflows.

## Structure

### commands/
Slash commands that guide AI agents through specific development processes.

- [commit.md](commands/commit.md) - Multi-step workflow for creating well-informed git commits
- [spec_writing.md](commands/spec_writing.md) - Interactive specification document generation following RFC 2119 conventions
- [architecture_writing.md](commands/architecture_writing.md) - Practical architecture blueprint generation from specifications

### system/
System-level prompts that configure AI agent behavior.

- [system-prompt-code-brief-no_browser](system/system-prompt-code-brief-no_browser) - Expert developer persona with tool usage policies and markdown linking rules

## Usage

These prompts are designed for AI coding assistants like Roo (VS Code plugin) and Claude Code.

To use in your project:

1. Create a `.roo` folder in your project root:
   ```bash
   mkdir .roo
   ```

2. Link the system prompt:
   ```bash
   ln -s /path/to/roo-prompts/system/system-prompt-code-brief-no_browser .roo/system-prompt-code
   ```

3. Link the commands folder:
   ```bash
   ln -s /path/to/roo-prompts/commands .roo/commands
   ```

The AI agent will automatically detect and use these prompts. Commands become available as slash commands (e.g., `/commit`, `/spec_writing`, `/architecture_writing`), and the system prompt configures agent behavior.
