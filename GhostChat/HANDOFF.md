# GhostChat Project Handoff

## Repository
- User fork: https://github.com/superchilpil/ghost-chat

## Purpose
Windows desktop chat/stream utility. Goal: installable Windows build, persistent settings, reliable live chat logging, and OBS/streaming integration.

## Build / packaging requirements
- Installable Windows package.
- Include build.bat or equivalent one-command build script.
- Builder ZIP must contain complete source needed to build; do not rely on Git being installed or on cloning/downloading source.
- Previous packaging attempts failed because source was missing/incomplete.
- Earlier errors included Git not being installed/on PATH and missing GhostChat source.

## Current work / known issues
- OBS integration is an ongoing goal.
- In-app update icon was reported as doing nothing and was being debugged.
- Chat Log Browse button was not working.
- Chat-log folder location text box was difficult to read.
- User wants logs available while the stream is running, not only after the stream ends.
- User reported logging was not actually producing entries; live logging needs implementation and verification.

## User preferences
- Prefer direct implementation and fixes.
- Do not claim a feature works without verification.
- Preserve settings across launches.
- Keep build/package workflows self-contained.
- Include build automation by default for Windows projects.
