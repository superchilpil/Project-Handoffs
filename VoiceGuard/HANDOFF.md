# VoiceGuard HANDOFF — AI CONTINUITY RECORD
FORMAT=AI_ONLY; HUMAN_READABILITY=NONREQUIRED; DENSE_SHORTHAND_ALLOWED; PRIORITY=MAX_CONTEXT/TOKEN_EFFICIENCY
LAST_RECONSTRUCTED=2026-10-05
IMPORTANT=Historical+intent context; verify source/runtime before claiming implementation state.

[IDENTITY]
repo=https://github.com/superchilpil/VoiceGuard
branch=main
publisher=Jack The Gooner
known_release=6.7.8
central_handoff=https://github.com/superchilpil/Project-Handoffs
platform=Windows
stack=C#/.NET8 WinForms x64 self-contained
purpose=voice-chat profanity filter: mic capture->delay->Whisper detection->censor/replacement->virtual mic

[CORE_AUDIO]
idle=live passthrough
VG_trigger held=private delayed capture/filter
release=>drain delayed queue; release game PTT; return live
game PTT=untouched direct-unfiltered bypass ONLY idle
filter/drain=>no direct bypass
filter=speech only; soundboard excluded
timing=must continue/drain after PTT release; no truncation
historical delay≈2-3s
routing=Voicemeeter Banana/VB-Cable
WDK=avoid

[WHISPER]
Whisper.net1.9.1;model=small.en;historical model≈465MB
historical rolling Window=1.0s Step=0.5s
known issues=recognition lag; sometimes one recognition/needed second PTT
aliases=Whisper correction; example "bag it"->"faggot"
diagnostics=timestamps/transcripts/aliases/confidence/timing where available

[SOUNDBOARD]
local playback=synchronized delayed timeline, not immediate
start≈currentEngine.DelaySeconds+0.020s
soundboard=in delayed timeline
PTT held through complete delayed transmission
local+transmitted start sync required
replacement/censor audio=delayed path
separate VoiceGuard WAV converter desired; common audio->WAV; must not alter/break VoiceGuard

[UI/OVERLAY]
titlebar=purple
version only; no Stage
tray option visible
persistent Check for updates
overlay=only when started
positions=4 corners+4 side-middle
modes=text|LED
LED green=live;red=filter/analyze/drain;purple=soundboard;yellow=direct bypass;gray=stopped
shape=circle;solid center->radial transparent edge;no pie fill;no purple/magenta rim
historical tuning≈75% size+more transparency
lower-left logo=too large; latest known feedback after audio fix: audio now works as intended, logo still same size/needs smaller (~75% target). OPEN unless source/user proves fixed.

[UPDATER]
startup latest-release API
newer=>Update to X.Y.Z
asset=VoiceGuard_Setup_X.Y.Z.exe
flow=temp download->validate->stop engine->elevated installer->exit
recent_commit=d7facbf6456502f69071ab9ce557fbe8548bd12f
legacy install risk=versioned dirs; possible installer /DIR="<AppContext.BaseDirectory>"
never assume fixed

[BUILD]
tfm=net8.0-windows;WinForms;x64/win-x64;self-contained
PublishSingleFile=false;PublishTrimmed=false
packages=NAudio2.2.1;Whisper.net1.9.1;Whisper.net.Runtime1.9.1;Whisper.net.Runtime.OpenVino1.9.1;OpenVINO.runtime.win2024.4.0.1
build=BUILD_INSTALLER.bat exists for local/manual convenience only;PRIMARY_BUILD=GitHub_Actions;LOCAL_PC_BUILD=NOT_REQUIRED;Windows project build script is NOT a mandatory requirement
publish verifies VoiceGuard.exe + runtimes\win-x64\whisper.dll;ggml-whisper.dll;ggml-base-whisper.dll;ggml-cpu-whisper.dll
installer=VoiceGuard_Installer.iss;ProgramFiles\VoiceGuard;x64/admin;recursive publish;VoiceGuard_Setup_X.Y.Z.exe

[RELEASE]
workflow=.github/workflows/windows-installer.yml
trigger=workflow_dispatch
release_version=exact MAJOR.MINOR.PATCH or blank=>patch++
draft_release=bool
flow=version fields->commit/push->vX.Y.Z tag->build installer->artifact->release
notes=RELEASE_NOTES.md;missing=>fail;existing tags never overwrite;concise user-facing
history=Inno discovery;ProgramFiles(x86) PowerShell parse;GH token auth;one-job/tag behavior
6.7.6=in-app updater + soundboard timing sync
future significant user-visible change=>update notes automatically

[HISTORICAL_DECISIONS]
speech-only
soundboard excluded
standalone WAV converter
Voicemeeter Banana
VB-Cable acceptable
WDK avoided
historical OS=Windows NT10.0.26200.0
historical .NET9 SDK; current baseline .NET8
OpenVINO/native DLL packaging sensitive

[KNOWN_OPEN_VERIFY]
1 lower-left logo size
2 updater legacy install dir
3 soundboard alias persistence: fixed config-load omission by restoring config.SoundboardAliases after SoundboardPhrases are loaded; commit=458a78e31cbb87cf56d0a7c52e6549b9c8b0cf01; runtime/build verification pending
4 preserve PTT drain timing
5 preserve soundboard sync
6 preserve overlay semantics/geometry
7 verify all build/test/release claims

[USER_PREFS]
direct implementation>long explanation
no unverified claims
edit GitHub directly when available
Windows=>build.bat/equivalent
release-worthy=>RELEASE_NOTES.md
targeted fix=>preserve working behavior
handoff=manual anytime
AI-only dense shorthand preferred


[UI_REDESIGN_6.7.8_TO_7.0.0]
active_branch=ui/jtg-rgb-redesign
main=DO_NOT_MERGE_OR_MODIFY_UNTIL_USER_EXPLICITLY_CONFIRMS_FINAL_UI
current_ui_status=running on user's Windows PC; user is reviewing Audio dropdown/slider colors and Settings toggle sizing
latest_ui_commit=477e3335713920193f3d7b7b6032f42eb8f39ea2
latest_build_run=https://github.com/superchilpil/VoiceGuard/actions/runs/38176348643
latest_build_status=GitHub Actions build+publish success; artifact is available in that run; runtime/visual verification remains user's responsibility
latest changes=custom purple Audio dropdown buttons replacing native white arrows; custom-drawn purple volume slider; explicit compact Settings toggle size/position
user's last feedback=prior build still showed white dropdown button, native volume slider, and oversized/clipped Settings toggles; latest commit attempts direct custom drawing/replacement rather than only changing native colors
must_verify=launch latest artifact; check no startup exception; Audio input/output dropdown arrows purple; slider track/thumb use VoiceGuard palette; both startup/tray toggles compact, same size, not clipped; confirm other pages/audio behavior unchanged
next_action=wait for user visual confirmation and any further corrections; do not merge/release before explicit confirmation
release_plan=after user confirms final fixes, merge ui/jtg-rgb-redesign into main and ship release 7.0.0 via .github/workflows/windows-installer.yml
release_notes_for_7.0.0=only a brief high-level UI overhaul statement; avoid listing individual UI changes or technical details (e.g. "UI overhaul for a cleaner, more consistent VoiceGuard experience.")
release_notes_source=RELEASE_NOTES.md; curate the 7.0.0 release notes to the brief UI-overhaul wording when preparing the release; do not accidentally publish the accumulated testing-branch changelog as detailed 7.0.0 notes
release_gate=explicit user approval after visual testing; keep main/stable release untouched until then

[CONTINUITY]
Use before asking user to re-explain. Current source beats stale history for implementation state; current user decisions beat old requirements. Rejected approaches stay rejected. Significant state changes=>update. Full transcript may be unavailable; never fabricate.
