# Immediate ChatGPT Handoff Trigger

## Purpose

This file is a **handoff trigger**, not normal project documentation.

If this Markdown file is provided to a ChatGPT instance, **immediately begin a project handoff workflow**. Do not wait for the user to separately ask for a handoff.

The purpose is to transfer as much useful project context as possible from the current ChatGPT conversation/context into the project's persistent handoff system so another ChatGPT conversation can continue the work without requiring the user to re-explain the project.

---

## FORMAT / STORAGE
Handoff files are AI-only continuity data; human readability is not required. Prefer dense shorthand, abbreviations, key=value records, compact lists/tables, and other token-efficient structures when they preserve meaning. Optimize for maximum recoverable context per token, not presentation.

## Immediate instructions

When this file is provided:

1. **Treat the user's current conversation and available project context as the source of truth for the handoff.**
2. Identify the project being worked on and locate its existing handoff file in this repository.
3. Read the existing project handoff before writing anything so historical requirements and previous decisions are preserved.
4. If the project's source repository is available, inspect the current repository state as well. The handoff describes history and intent; the source repository represents the current implementation.
5. **Immediately update the project's handoff file** with all relevant information available from the current conversation/context.
6. Do not produce only a short summary. Capture as much useful continuity information as practical.
7. Preserve:
   - Project purpose and goals
   - Current implementation/state
   - Current version/release information
   - Features and requirements
   - User preferences
   - Design decisions
   - Architecture and important technical details
   - Known bugs and unresolved issues
   - Failed approaches and approaches the user rejected
   - Build/package/install requirements
   - Release workflow
   - Important file names, paths, settings, and configuration
   - Important historical context that could affect future implementation
   - What was most recently being worked on
   - Clear next steps
9. Do **not** invent missing details. If something cannot be recovered, mark it as unknown rather than guessing.
10. Do not claim that a build, test, release, feature, or fix succeeded unless it was actually verified.
11. Do not erase historical requirements simply because they are not obvious in the current source.
12. Do not repeat approaches that the user already rejected unless there is a specific reason to reconsider them.
13. Preserve working behavior when documenting or continuing a targeted fix.
14. If the current project handoff already contains information that remains correct, retain it rather than replacing it with a shorter summary.
15. Add newly discovered information and update stale information where the current conversation/source proves it has changed.
16. If the project handoff is missing, create it in the appropriate project folder.
17. After completing the handoff, give the user a concise confirmation stating what handoff file was updated and the commit/change made. Do not claim more than was actually done.

---

## Handling incomplete conversation history

A complete raw ChatGPT transcript may not be available.

That is expected.

When the full transcript is unavailable, reconstruct the handoff from every source that is actually available, including:

- The current conversation
- Retained project context/memory supplied to the ChatGPT instance
- Existing project handoff files
- Current project source/repository state
- Repository history when available
- Existing release notes
- Existing build scripts/configuration
- Previously recorded bugs, requirements, and rejected approaches

The goal is **maximum useful continuity**, not a minimal summary.

Never pretend that a reconstructed handoff is a complete transcript.

---

## Handoff repository rules

This repository is the persistent continuity archive for the user's projects.

Project-specific handoffs belong in their own folders, for example:

- `VoiceGuard/HANDOFF.md`
- `GhostChat/HANDOFF.md`

Do not put detailed project history, workflow instructions, or large continuity records in the repository root README.

The root README should remain intentionally minimal so that it exposes as little forward-facing information about this repository as reasonably possible.

This repository is personal. It is currently public only because that is required by the available workflow/tooling; it should not be treated as public project documentation.

---

## Future project continuity

The user may provide this file at any point and expect an immediate handoff, including when a conversation is getting long or when they are moving work to a new ChatGPT conversation.

The handoff process should therefore be treated as an explicit workflow trigger:

**File provided -> inspect existing handoff -> collect all available context -> update/create project handoff -> verify the write -> report completion.**

Do not make the user explain the handoff procedure again.

---

## Important user workflow preferences

- Prefer direct implementation and fixes over lengthy explanations when the necessary tools are available.
- Never claim something was built, tested, released, fixed, or verified unless it actually was.
- For Windows projects, include a `build.bat` or equivalent one-command build script by default.
- Preserve existing working behavior when fixing a specific issue.
- Update user-facing release notes when significant user-visible changes are made.
- Keep release notes concise and user-focused.
- When a significant project state change occurs, update the corresponding project handoff.
- The user can manually request a handoff at any time.
