# Project Handoffs

This is a personal repository used to store project handoff files for continuity between ChatGPT conversations.

The repository contains project context, decisions, current status, known issues, build information, and other details needed to continue a project without re-explaining everything from scratch.

> **Privacy note:** This repository is personal and the handoff files are intended for my own use. The repository is public only because that is currently required by the available workflow/tooling. Do not treat the contents as a public project documentation repository.


## Handoff Instructions for Future ChatGPT Iterations

This repository is the canonical continuity archive for these projects. If this repository or one of its handoff files is provided to a future ChatGPT iteration, treat the handoff as project context that should be used before asking the user to re-explain the project.

- The handoff is intended to preserve as much project context as possible when a previous conversation cannot be continued.
- Use the relevant project handoff together with the current project repository/source. The handoff describes history, requirements, decisions, failures, known issues, and preferences; the source/repository represents the current implementation.
- Do not discard historical requirements simply because they are not obvious from the current source.
- Do not assume a previously reported bug is fixed unless the current implementation verifies it.
- Do not repeat previously rejected approaches without a reason.
- Preserve important working behavior when making targeted fixes.
- Never claim a build, test, release, or feature works unless it was actually verified.
- When significant project work is completed, update the appropriate handoff so the next iteration has the newest context.
- The user may manually request a handoff at any time. When they do, capture all available relevant information rather than producing only a short summary.
- A true complete historical ChatGPT transcript may not be available; in that case, maximize the handoff with every relevant detail available from the current conversation, retained project context, repository state, prior decisions, failed attempts, requirements, and known issues.
- These instructions are intentionally redundant so they can be reintroduced to a future ChatGPT iteration if necessary.
