# GhostChat Project Handoff

## Canonical repositories / identity
- User fork: https://github.com/superchilpil/ghost-chat
- User GitHub: superchilpil
- Central handoff repository: https://github.com/superchilpil/Project-Handoffs

## Project purpose
GhostChat is a Windows desktop chat/stream utility. The user's goal is a practical installable build with persistent settings, reliable live chat logging, useful OBS integration, and a self-contained build/package workflow.

## Core user goals
- Make GhostChat installable on Windows.
- Make settings persist between launches.
- Make the application useful alongside OBS and streaming.
- Provide chat logs that can be referenced while a stream is still running.
- Keep the build process simple enough to use with a build.bat/one-command script.
- Avoid requiring the user to install Git merely to build a supplied package.
- A builder ZIP should contain the complete source and required build material rather than a placeholder that expects to clone the repository later.

## Packaging history / failures to avoid
Previous attempts at producing a GhostChat ZIP repeatedly failed because:
- The ZIP did not actually contain the complete source.
- The package expected Git to be installed or source to be downloaded separately.
- The user reported "Ghost Chat is missing or incomplete."
- The user reported that Go was not installed/on PATH.
The next packaging workflow should therefore explicitly verify that the archive contains the actual source tree and all dependencies/build prerequisites that can legally and practically be bundled.

## OBS / streaming integration
- OBS integration is an ongoing feature goal.
- The user wanted OBS-related functionality included in the project's feature list/README and release notes when appropriate.
- Streaming workflow improvements should prioritize reliable, immediately useful behavior over unnecessary complexity.

## Update UI
- An in-app update icon was reported as doing nothing.
- This was an active bug/feature investigation and should be considered unresolved unless the current repository state proves otherwise.
- Do not assume the update control works just because it exists.

## Chat logging
This is a major requirement and a known unresolved area.
- Chat Log Browse button was reported as not working.
- The text box showing the chat-log folder location was difficult to read and needs usable contrast/readability.
- User explicitly asked whether chat logs are generated only after the stream ends.
- User does NOT want logs to exist only after the stream finishes.
- Desired behavior: chat should be logged continuously while the stream is running so the user can reference the log live.
- User subsequently reported that it was not logging anything at all.
- Therefore, live chat logging must be implemented and actually verified rather than merely creating a log file after shutdown.
- A useful implementation should make it clear where the active log is stored and ensure entries are flushed/available during the stream.

## Settings
- Settings persistence is a core requirement.
- Settings should survive application restarts.
- Folder/log path configuration should be readable and editable.
- Do not regress existing settings while fixing logging or UI.

## Build expectations
- Windows project.
- Include build.bat or equivalent one-command build automation by default.
- Installer/package should be self-contained enough that the user does not need Git to retrieve source.
- If Go or another compiler/runtime is required, either bundle an appropriate dependency/toolchain where licensing permits or make the build package clearly self-contained; do not silently assume it is installed on PATH.
- Never claim the package builds successfully without actually verifying the build.

## Current known issues / unfinished work
1. Verify and fix the in-app update icon/control.
2. Fix Chat Log Browse.
3. Improve readability of the chat-log folder location field.
4. Implement/verify continuous live chat logging.
5. Confirm logs are readable while a stream is actively running.
6. Continue OBS integration work and keep README/release notes synchronized when user-visible features are added.
7. Produce a genuinely complete, self-contained Windows build/package rather than a source-incomplete ZIP.

## Historical context
- User previously requested a ZIP that could build GhostChat on their PC with a build.bat.
- User wanted the ZIP to pull source from their GitHub fork, but then clarified that the ZIP must itself contain everything needed and must not require downloading from Git.
- Multiple iterations were rejected because the supplied ZIP was empty or lacked source.
- User explicitly asked for an installable version rather than just a source package.
- The project has therefore been treated as both a source/build packaging task and a usable streaming application task.

## User working preferences
- Prefer direct implementation/fixes rather than long explanations.
- Do not claim something works unless it has actually been verified.
- Preserve settings across launches.
- Include build automation in Windows projects.
- When making a significant user-visible feature/fix, update project documentation/release notes.
- If a supplied build/archive is incomplete, fix the package itself rather than asking the user to install additional tools unless there is no practical alternative.
- User may manually request a handoff at any time.

## Next-chat starting instructions
Before making assumptions, inspect the current GitHub repository state because this handoff contains historical context and known requirements, while the repository contains the latest implementation. Treat unresolved issues above as unresolved until verified in the current source.
