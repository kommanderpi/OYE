# Set Up Your Projects Workspace

## 1. Create a Projects folder

Find your main **user/home folder** on your computer — the same general location that contains folders such as:

* Documents
* Downloads
* Pictures / Photos

Create a new folder called:

```text
Projects
```

**Do not put this folder on your Desktop.**

---

## 2. Create your project directories

Inside `Projects`, create three folders:

```text
Projects/
├── TM_PE/
├── TM_OD/
└── Digital Twin/
```

These will contain your three course projects.

---

## 3. Create your VS Code workspace

Open **VS Code**.

Open the entire `Projects` folder using **Open Folder**.

Your `Projects` directory should now be the root of your VS Code workspace.

Investigate how to **save your VS Code workspace**, and save it so that you can easily return to this setup.

---

# 4. Set Up Your Coding Agents

We are going to use four different roles:

**Explorer**
Investigates and understands the project before changes are made.

**Builder**
Writes and modifies code.

**Reviewer**
Checks the Builder's work for mistakes, problems, and unnecessary complexity.

**Documenter**
Explains what was built and keeps the project documentation up to date.

Our general workflow will be:

```text
EXPLORE → BUILD → REVIEW → DOCUMENT
```

The setup is different depending on whether you are using **Codex** or **Claude Code**.

---

# OPTION A — CODEX

Codex separates two kinds of instructions:

```text
AGENTS.md
```

tells Codex **how we want it to work**.

```text
~/.codex/config.toml
```

and the agent configuration files tell Codex **which agents exist and which models they should use**.

---

## A1. Find the Codex folder

When Codex is installed, it automatically creates this folder in your **user/home folder**:

```text
~/.codex/
```

You do **not** need to create `.codex` yourself. This folder does **not** go inside `Projects`. On macOS, its full path looks like `/Users/your-name/.codex`. On Windows, it is inside `C:\Users\your-name`. The shortcut `~` means your user/home folder, so this guide writes the location as `~/.codex`.

Inside the existing `.codex` folder, create an `agents` folder if it is not already there:

```text
~/.codex/agents/
```

Your structure should begin to look like this:

```text
Your home folder/
├── .codex/
│   ├── config.toml
│   └── agents/
│       ├── explorer.toml
│       ├── builder.toml
│       ├── reviewer.toml
│       └── documenter.toml
│
└── Projects/
    ├── AGENTS.md
    ├── TM_PE/
    ├── TM_OD/
    └── Digital Twin/
```

---

## A2. Open the Codex user configuration

Open this file, or create it inside the existing `.codex` folder if it is not there yet:

```text
~/.codex/config.toml
```

### How to find `config.toml`

The `.codex` folder starts with a dot, which means your computer may treat it as a **hidden folder**. It is in your user/home folder, alongside folders such as `Documents`, `Downloads`, and `Projects`. It is **not** inside `Projects` or one of the course project folders.

From the Codex IDE extension, select the **gear icon** in the top-right corner, then select **Codex Settings → Open config.toml**.

The easiest way to open the file in VS Code is from a terminal:

```bash
code ~/.codex/config.toml
```

On Windows PowerShell, use:

```powershell
code "$HOME\.codex\config.toml"
```

If the `code` command is unavailable, open VS Code, select **File → Open File**, and navigate to the file manually.

To check for the folder from a macOS or Linux terminal, list the hidden files in your home folder:

```bash
ls -la ~
```

You should see `.codex` in the output. On macOS, you can also open Finder, press **Command+Shift+G**, type `~/.codex`, and press **Enter**. Press **Command+Shift+.** if you want Finder to show other hidden files.

On Windows, enter `%USERPROFILE%\.codex` in File Explorer's address bar. If needed, select **View → Show → Hidden items**.

If `.codex` exists but `config.toml` does not, create `config.toml` inside the existing `.codex` folder. Be careful that the file is not accidentally saved as `config.toml.txt`. If the `.codex` folder itself is missing, confirm that Codex is installed and has been opened; do not create the folder inside `Projects`.

Add:

```toml
# Default model for the main Codex agent
model = "gpt-5.6-sol"
model_reasoning_effort = "high"

[agents]
enabled = true
max_concurrent_threads_per_session = 4

[agents.explorer]
description = "Investigates the project, code, dependencies, and possible approaches before changes are made."
config_file = "./agents/explorer.toml"

[agents.builder]
description = "Implements and modifies the project."
config_file = "./agents/builder.toml"

[agents.reviewer]
description = "Reviews implementations for errors, bugs, unnecessary complexity, and missing requirements."
config_file = "./agents/reviewer.toml"

[agents.documenter]
description = "Documents how the project works, how it is structured, and how to use it."
config_file = "./agents/documenter.toml"
```

The paths in `config_file` are **relative to this `config.toml` file**. Codex explicitly supports this role → configuration-file structure.

---

# A3. Configure each Codex agent

Each agent gets a different model depending on the kind of work it is doing.

## Explorer

Create:

```text
~/.codex/agents/explorer.toml
```

Add:

```toml
model = "gpt-5.6-luna"
model_reasoning_effort = "low"

model_instructions = """
Explore the relevant project before changes are made.

Inspect files, folders, dependencies, libraries, documentation, and existing code.

Understand how the project currently works.

Identify possible approaches and report what you discover.

Do not make substantial changes unless explicitly asked.
"""
```

The Explorer is doing relatively lightweight investigation, so we use a fast model with low reasoning effort.

---

## Builder

Create:

```text
~/.codex/agents/builder.toml
```

Add:

```toml
model = "gpt-5.6-terra"
model_reasoning_effort = "medium"

model_instructions = """
Implement the requested work.

Create and edit files as necessary.

Follow the existing project structure.

Prefer simple, readable, maintainable solutions.

Do not modify unrelated parts of the project.

Run appropriate tests or checks when possible.
"""
```

The Builder needs stronger coding ability but will generally be doing well-defined implementation work.

---

## Reviewer

Create:

```text
~/.codex/agents/reviewer.toml
```

Add:

```toml
model = "gpt-5.6-sol"
model_reasoning_effort = "high"

model_instructions = """
Review work that has already been created or modified.

Look for bugs, errors, missing requirements, unclear logic, and unnecessary complexity.

Check whether the implementation actually satisfies the task.

Consider edge cases and things that may break.

Explain problems clearly and suggest specific improvements.

Do not modify the implementation unless explicitly asked.
"""
```

The Reviewer gets the strongest model and higher reasoning because its job is to critically evaluate the work rather than simply produce more code.

---

## Documenter

Create:

```text
~/.codex/agents/documenter.toml
```

Add:

```toml
model = "gpt-5.6-luna"
model_reasoning_effort = "low"

model_instructions = """
Read the completed project and document how it works.

Explain:

- what the project does
- how it is structured
- how to run it
- important dependencies
- important decisions or limitations

Keep documentation concise and understandable.

Update existing documentation instead of creating duplicate documentation whenever possible.
"""
```

Documentation generally does not require the same level of computational reasoning as implementation or review.

OpenAI currently describes **GPT-5.6 Sol** as its flagship option for complex reasoning and coding, **Terra** as a balance of intelligence and cost, and **Luna** as the lightweight/high-volume option.

---

# A4. Create `AGENTS.md`

Now create:

```text
Projects/AGENTS.md
```

Add:

```markdown
# Project Agent Workflow

This workspace contains three projects:

- TM_PE
- TM_OD
- Digital Twin

Use the specialized agents defined for this workspace.

## Explorer

Use the Explorer when beginning a new task or whenever the project is not yet understood.

The Explorer should investigate before substantial changes are made.

## Builder

Use the Builder once the task and existing project structure are understood.

The Builder is responsible for implementing the solution.

## Reviewer

After substantial implementation work, use the Reviewer to independently inspect the result.

The Reviewer should identify problems rather than simply agreeing with the Builder.

## Documenter

Once the implementation has been reviewed, use the Documenter to update the project's documentation.

# Workflow

For substantial tasks, generally work in this order:

1. Explore
2. Build
3. Review
4. Document

Do not ask one agent to perform all four roles when the work can reasonably be delegated.
```

`AGENTS.md` provides the **shared project instructions**.

The `.codex` files provide the **actual agent and model configuration**.

---

## A5. Editing the Codex configuration later

If you want to change how the agents work, open:

```text
~/.codex/config.toml
```

If you want to change the model or behavior of one particular agent, edit its file:

```text
~/.codex/agents/explorer.toml
~/.codex/agents/builder.toml
~/.codex/agents/reviewer.toml
~/.codex/agents/documenter.toml
```

For example, changing:

```toml
model = "gpt-5.6-terra"
model_reasoning_effort = "medium"
```

to:

```toml
model = "gpt-5.6-sol"
model_reasoning_effort = "high"
```

would give that particular agent more reasoning capacity.

**Do not blindly increase every agent to the largest model.** Part of the exercise is learning to match computational resources to the kind of work being performed.

---

# OPTION B — CLAUDE CODE

Claude Code uses project-level subagents stored in:

```text
.claude/agents/
```

Create:

```text
Projects/
├── .claude/
│   └── agents/
│       ├── explorer.md
│       ├── builder.md
│       ├── reviewer.md
│       └── documenter.md
│
├── TM_PE/
├── TM_OD/
└── Digital Twin/
```

---

## Explorer

Create:

```text
.claude/agents/explorer.md
```

Add:

```markdown
---
name: explorer
description: Investigates the project before changes are made.
---

Explore the relevant project before proposing or making changes.

Inspect files, folders, dependencies, documentation, and existing code.

Understand how the project currently works.

Identify possible approaches and report what you discover.

Do not make substantial changes unless explicitly asked.
```

---

## Builder

Create:

```text
.claude/agents/builder.md
```

Add:

```markdown
---
name: builder
description: Builds and modifies the project.
---

Implement the requested work.

Create and edit files as necessary.

Follow the existing project structure.

Prefer simple, readable solutions.

Avoid modifying unrelated parts of the project.

Test your work when possible.
```

---

## Reviewer

Create:

```text
.claude/agents/reviewer.md
```

Add:

```markdown
---
name: reviewer
description: Reviews completed work for errors and improvements.
---

Review existing work before changing it.

Look for bugs, errors, unclear logic, unnecessary complexity, and missing requirements.

Check whether the solution actually satisfies the task.

Explain any problems you find and suggest specific improvements.

Do not modify the implementation unless explicitly asked.
```

---

## Documenter

Create:

```text
.claude/agents/documenter.md
```

Add:

```markdown
---
name: documenter
description: Creates and maintains project documentation.
---

Read the project and document how it works.

Explain:

- what the project does
- how the project is structured
- how to run it
- important dependencies
- important decisions or limitations

Update existing documentation rather than creating duplicates whenever possible.
```

---

# Final Structure

## If you use Codex

```text
Your home folder/
├── .codex/
│   ├── config.toml
│   └── agents/
│       ├── explorer.toml
│       ├── builder.toml
│       ├── reviewer.toml
│       └── documenter.toml
│
└── Projects/
    ├── AGENTS.md
    ├── TM_PE/
    ├── TM_OD/
    └── Digital Twin/
```

## If you use Claude Code

```text
Projects/
├── .claude/
│   └── agents/
│       ├── explorer.md
│       ├── builder.md
│       ├── reviewer.md
│       └── documenter.md
│
├── TM_PE/
├── TM_OD/
└── Digital Twin/
```

# Remember

The goal is **not simply to use an AI coding tool**.

The goal is to begin thinking about computational work as a sequence of different responsibilities:

```text
EXPLORE
   ↓
BUILD
   ↓
REVIEW
   ↓
DOCUMENT
```

Different agents can have different instructions, different responsibilities, and different amounts of computational power.
