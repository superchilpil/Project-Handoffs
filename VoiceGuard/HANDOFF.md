# VoiceGuard Project Handoff

FORMAT=AI_ONLY; HUMAN_READABILITY=NONREQUIRED; DENSE_SHORTHAND_ALLOWED; PRIORITY=MAX_CONTEXT/TOKEN_EFFICIENCY

## Canonical repositories / identity
- Main project: https://github.com/superchilpil/VoiceGuard
- Branch: main
- Publisher/branding: Jack The Gooner
- Known release at time of this handoff: 6.7.6
- Central handoff repository: https://github.com/superchilpil/Project-Handoffs

## Project purpose
VoiceGuard is a Windows desktop profanity filter for voice chat. It captures microphone audio, delays transmission, uses Whisper speech recognition to detect configured words/phrases, and censors offending sections with silence or user-defined replacement audio. It is intended to sit between the user's microphone/game PTT workflow and a virtual microphone such as Voicemeeter/VB-Cable.

## Fundamental operating model
- Idle state = live microphone passthrough.
- Holding the separate VoiceGuard trigger starts private delayed capture/filtering.
- Releasing the VoiceGuard trigger drains the delayed queue, releases the game PTT, and returns to live passthrough.
- The normal game PTT key is intentionally left untouched so it can provide a direct, unfiltered bypass when the VoiceGuard engine is idle.
- Direct bypass is not available while VoiceGuard is actively filtering or draining.
- VoiceGuard should censor speech, not soundboard audio.
- User wants the audio pipeline to preserve the intended timing rather than cutting off audio immediately when PTT is released.

## Speech recognition / Whisper
- Uses Whisper.net.
- Whisper small.en has been used as the recognition model.
- Model download previously discussed as roughly 465 MB.
- Recognition worker has used rolling windows with Window=1.0s and Step=0.5s.
- Recognition lag/detection timing has been a recurring tuning issue.
- Previous logs showed cases where recognition happened only once or required another PTT press; timing/window behavior has therefore been important.
- User uses transcription aliases to compensate for Whisper misrecognitions.
- Example previously configured alias: "bag it" -> "faggot".
- Diagnostics/logging include timestamps, recognition output, aliases, confidence/timing information where available.

## Audio / soundboard behavior
- Local headset soundboard playback is synchronized to the configured VoiceGuard delay instead of starting immediately.
- Current implementation starts local playback using approximately currentEngine.DelaySeconds + 0.020 seconds.
- Soundboard audio is represented in the delayed timeline rather than bypassing the delay.
- Soundboard PTT remains held for the complete delayed transmission.
- User explicitly wants local and transmitted soundboard audio to start in sync.
- Replacement/censor sounds are part of the delayed output path.
- User previously wanted a standalone VoiceGuard-specific audio converter that converts common audio formats to WAV without changing/breaking VoiceGuard.

## UI / branding
- Purple title bar.
- Do not show "Stage"; title/version should show only the version number.
- Keep "Minimize to system tray" visible.
- Header includes an always-visible "Check for updates" button.
- User prefers practical UI changes without unrelated redesign.
- A lower-left logo was repeatedly considered too large; latest requested direction was to reduce it to roughly 75% of its previous size.

## Status overlay
- Overlay is shown only when VoiceGuard is started.
- Settings allow enable/disable and position.
- Eight positions: four corners and the middle of each side.
- Supports text or LED status.
- LED status meanings:
  - Green = live microphone passthrough
  - Red = filtering / analyzing / draining
  - Purple = soundboard playback
  - Yellow = direct PTT bypass
  - Gray = stopped/inactive
- Desired LED shape is circular, with a solid center fading radially to transparent edges.
- Avoid pie-shaped fills and purple/magenta fringe/rims.
- Latest visual tuning reduced LED size to about 75% of the previous size and increased transparency.
- The overlay's purpose is status feedback, not decorative animation.

## In-app updater
- MainForm contains a persistent "Check for updates" button.
- VoiceGuard checks GitHub's latest-release API at startup.
- If a newer release exists, the button changes to "Update to X.Y.Z".
- Updater downloads the matching installer asset named like VoiceGuard_Setup_X.Y.Z.exe to a temporary folder.
- It validates that the installer exists and is reasonably sized, stops the engine if active, launches the installer elevated, then exits VoiceGuard.
- Updater-related implementation was recently changed around commit d7facbf6456502f69071ab9ce557fbe8548bd12f.
- Potential legacy-install edge case: older installs may live in versioned directories. If an updater downloads/launches the installer but fails to replace the existing application, investigate launching the installer with /DIR="<AppContext.BaseDirectory>".

## Build configuration
- Target framework: net8.0-windows.
- Windows Forms.
- x64 / win-x64.
- Self-contained.
- PublishSingleFile=false.
- PublishTrimmed=false.
- Main packages:
  - NAudio 2.2.1
  - Whisper.net 1.9.1
  - Whisper.net.Runtime 1.9.1
  - Whisper.net.Runtime.OpenVino 1.9.1
  - OpenVINO.runtime.win 2024.4.0.1
- User expects Windows projects to include a one-command build script such as build.bat.

## Installer
- BUILD_INSTALLER.bat cleans publish/installer, runs dotnet publish, verifies VoiceGuard.exe, verifies Whisper native DLLs, and runs Inno Setup.
- Expected Whisper native files include:
  - publish\runtimes\win-x64\whisper.dll
  - publish\runtimes\win-x64\ggml-whisper.dll
  - publish\runtimes\win-x64\ggml-base-whisper.dll
  - publish\runtimes\win-x64\ggml-cpu-whisper.dll
- VoiceGuard_Installer.iss:
  - App name VoiceGuard
  - Publisher Jack The Gooner
  - Default install directory under Program Files\VoiceGuard
  - x64/admin
  - Includes publish directory recursively
  - Output installer named VoiceGuard_Setup_X.Y.Z.exe

## Automated release workflow
File: .github/workflows/windows-installer.yml
- Normal release path is manual workflow_dispatch.
- release_version input accepts exact MAJOR.MINOR.PATCH, e.g. 6.7.7; blank means automatically increment patch.
- draft_release boolean creates a GitHub Release as a draft for testing.
- Workflow updates Version, AssemblyVersion and FileVersion, commits/pushes the version bump, creates/pushes the matching vX.Y.Z tag, builds the Windows installer, uploads the artifact, and creates the GitHub Release.
- RELEASE_NOTES.md is the persistent source of user-facing release notes.
- Workflow must fail clearly if RELEASE_NOTES.md is missing.
- Exact existing tags should not be overwritten.
- Do not replace user-facing release notes with generic autogenerated changelog text.
- Important workflow history: fixes included Inno Setup discovery, PowerShell ProgramFiles(x86) parsing, GitHub token authentication, and one-job/tag behavior.
- Potential future issue: if tag-push triggering causes duplicate/unintended workflow runs, consider using workflow_dispatch as the sole release trigger.

## Release notes
- RELEASE_NOTES.md should be updated whenever a significant user-visible feature/fix is made.
- Notes should be concise and user-facing, not generic technical changelog prose.
- 6.7.6 included:
  - In-app update checking/updating.
  - Soundboard timing fix so local headset playback is synchronized with configured VoiceGuard delay.
- User does not want to have to remind the assistant to update release notes for future release-worthy changes.

## Historical project requirements / ideas
- VoiceGuard originally centered on a 2–3 second delayed PTT pipeline with Whisper detection.
- User wanted filtering to apply only to speech and not soundboard audio.
- User wanted a standalone WAV converter specifically for VoiceGuard.
- User uses Voicemeeter Banana and wanted VoiceGuard's virtual microphone available for routing.
- VB-Cable is an acceptable alternative to a custom WDK virtual audio driver.
- User has specifically avoided requiring WDK for the virtual-mic solution.
- Previous recognition/debug logs included OS Microsoft Windows NT 10.0.26200.0 and .NET 9 SDK details during earlier development; the current VoiceGuard project baseline is documented above as .NET 8 unless the repository says otherwise.
- Earlier builds used Whisper/OpenVINO runtime components and had runtime/native-DLL packaging concerns.

## Current known concerns
- Keep PTT drain/timing behavior intact when changing audio timing.
- Avoid breaking working soundboard synchronization while modifying the engine.
- Be careful with updater install-directory behavior for existing installations.
- Preserve overlay visual requirements and status-color semantics.
- Verify changes rather than assuming a build succeeded.

## User working preferences
- Prefer direct implementation/fixes over lengthy explanations.
- Do not claim compile/test/release success unless actually performed and verified.
- For GitHub source changes, edit the repository directly when the required tool access exists.
- Include a build.bat/equivalent in Windows projects.
- When a fix or feature is release-worthy, update RELEASE_NOTES.md as part of the code change.
- Preserve existing working behavior when fixing a specific issue.
- User may manually request a project handoff at any time.

## Handoff Continuity Instructions

This section is intentionally redundant so a future ChatGPT iteration can recover the handoff workflow even if the surrounding conversation is unavailable.

Treat this file as the project's continuity record. Use it before asking the user to re-explain the project. Combine this historical context with the current GitHub repository/source, because the handoff describes why decisions were made while the repository describes the latest implementation.

When updating or continuing this project:
- Preserve the requirements, decisions, and working behavior documented here.
- Treat listed unresolved issues as unresolved until the current source proves otherwise.
- Do not repeat previously failed or rejected approaches without a clear reason.
- Never claim code, builds, tests, installers, releases, or features work unless actually verified.
- Prefer direct implementation/fixes when repository access permits.
- When a significant feature or fix changes the project state, update this handoff with the new state and any important reasoning.
- If the user manually asks for a handoff, capture all available project information, not merely a short summary.
- A complete historical chat transcript may not be available. In that case, preserve every relevant detail available from current conversation context, retained project context, repository history, requirements, decisions, failed attempts, known issues, and user preferences.
