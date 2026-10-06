# VoiceGuard Project Handoff

## Repository
- https://github.com/superchilpil/VoiceGuard
- Branch: main
- Known release: 6.7.6

## Purpose
Windows C#/.NET voice-chat profanity filter using delayed microphone capture, Whisper speech recognition, and configurable censorship/replacement audio.

## Core behavior
- Idle: live microphone passthrough.
- Hold VoiceGuard trigger: private delayed capture/filtering.
- Release trigger: drain delayed queue, release game PTT, return to live passthrough.
- Normal game PTT remains an unfiltered bypass while VoiceGuard is idle.
- Direct bypass is unavailable while filtering/draining.
- Designed for virtual-mic routing such as Voicemeeter/VB-Cable.

## Recognition
- Whisper.net; Whisper small.en has been used.
- Rolling recognition has used 1.0s windows / 0.5s steps.
- Timing/lag is an ongoing tuning area.
- Filter speech, not unrelated soundboard playback.

## Soundboard
- Local headset playback is synchronized to configured VoiceGuard delay.
- Soundboard PTT remains held through complete delayed transmission.
- Soundboard is inserted into the delayed timeline rather than playing immediately.

## UI / overlay
- Purple title bar; show only version number, not Stage.
- Keep Minimize to system tray visible.
- Overlay only when VoiceGuard is started.
- Eight positions: four corners plus four side-center positions.
- Text and circular LED modes.
- LED: green=live, red=filtering/analyzing/draining, purple=soundboard, yellow=direct bypass, gray=stopped.
- LED target: circular solid center with radial fade to transparent edges; avoid magenta fringe/pie fill.
- Latest LED tuning reduced size to about 75% and increased transparency.

## Updater
- Persistent Check for updates button.
- Startup check for newer GitHub release.
- Newer release changes button to Update to X.Y.Z.
- Downloads matching VoiceGuard_Setup_X.Y.Z.exe, stops engine, launches installer elevated, exits app.
- Possible legacy-install edge case: may need installer launched with /DIR="<AppContext.BaseDirectory>".

## Build / release
- .NET 8 WinForms, net8.0-windows, x64, win-x64, self-contained.
- PublishSingleFile=false, PublishTrimmed=false.
- NAudio 2.2.1; Whisper.net 1.9.1 and related runtime packages.
- BUILD_INSTALLER.bat publishes, verifies VoiceGuard.exe and Whisper native DLLs, then runs Inno Setup.
- .github/workflows/windows-installer.yml supports manual release_version and draft_release inputs.
- RELEASE_NOTES.md is the persistent user-facing release-note source and should be updated for significant release-worthy changes.

## User preferences
- Prefer direct edits/fixes.
- Never claim compile/test/release success unless verified.
- Include a build script in Windows projects.
- Keep release notes concise and user-facing.
- Avoid unrelated redesigns when fixing a specific issue.
