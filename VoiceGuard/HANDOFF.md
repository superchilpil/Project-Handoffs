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


[VOICEGUARD_POST_7.0.0_RELEASE_PROCESS]
latest_release=https://github.com/superchilpil/VoiceGuard/releases/tag/v7.0.0
latest_release_asset=https://github.com/superchilpil/VoiceGuard/releases/download/v7.0.0/VoiceGuard_Setup_7.0.0.exe
main_branch=main
testing_branch=ui/jtg-rgb-redesign
release_workflow=.github/workflows/windows-installer.yml
release_workflow_commit=769d302ca9db8a290e16ac21084f3ea4c1ab2900
versioning=manual workflow_dispatch; automatically increments patch from current csproj version; after patch reaches 9, next version resets patch to 0 and increments minor (7.0.9 -> 7.1.0); no push-tag trigger to avoid duplicate release runs
next_release_after_7.0.0=7.0.1
draft_release_input=workflow dispatch includes optional draft_release toggle
future_development_process=develop and test future UI/features on ui/jtg-rgb-redesign first; provide a Windows build artifact; user visually/runtime tests; fix feedback; only merge to main after explicit user approval; run release workflow only when user requests release
release_notes=keep user-facing notes concise and high-level unless user asks for detail
audio_engine_constraint=preserve audio engine and PTT passthrough behavior unless user explicitly requests changes

[CONTINUITY]
Use before asking user to re-explain. Current source beats stale history for implementation state; current user decisions beat old requirements. Rejected approaches stay rejected. Significant state changes=>update. Full transcript may be unavailable; never fabricate.


[SESSION_UPDATE_2026-10-14]
active_work_branch=fix/volume-sliders-test
latest_commit=131af2c7922838805eacd85c64b5fd9c004627d1
change=exclusive UI test-audio playback shared by replacement-sound and soundboard Test actions; newest click cancels active clip and supersedes older queued requests; cancellation-aware playback loop stops/disposes old player before next begins
volume_behavior=preserved item gain then master gain, independently; no live audio/PTT/delay pipeline changes
validation=GitHub Actions build-validation run https://github.com/superchilpil/VoiceGuard/actions/runs/38672745431 was queued at handoff update; verify completion before claiming build success
next=download VoiceGuard-Test-win-x64 artifact after successful workflow; user runtime-test rapid repeat clicks and switching between soundboard/replacement Test buttons; do not merge to main without explicit approval


[SESSION_UPDATE_2026-10-15]
main_commit=c4c6d2375c62084fbdbafaa4d4d9429c209a92b8
change=carried the tested item/master volume behavior plus exclusive UI test playback into main; no unrelated workflow changes merged
release_requested=yes
release_workflow=.github/workflows/windows-installer.yml; workflow_dispatch only; main workflow auto-increments 7.0.0 to 7.0.1; draft_release=false for public release
release_status=not yet dispatched; current GitHub connection exposes workflow-run reads/reruns but no workflow_dispatch action; user must click Run workflow on https://github.com/superchilpil/VoiceGuard/actions/workflows/windows-installer.yml
next=run release workflow on main, verify installer/release succeeds; user runtime-test rapid repeat and cross-button test playback
speech_to_text=weekend work planned; existing test branch feature/voice-to-text-test; keep speech-to-text work isolated from main unless user directs otherwise


[SESSION_UPDATE_2026-10-18]
active_work_branch=feature/voice-to-text-test
latest_commit=7595a57f6956a6b647bf3018ce835788c8f6997f
feature=experimental local Whisper voice-to-text; user opens Voice-to-Text from Settings; VoiceGuard must be running/model ready; activation key default F8; first press records from physical mic while engine idle, second stops and transcribes, third confirms/sends to the foreground game; Escape cancels; quick double-tap while recording cancels; transcript editable before send; chat-open key default Enter; max recording 20s
architecture=reuse AudioEngine physical mic capture while idle; separate VoiceToText PCM buffer and SpeechDetector queue item; transcription work yields to profanity/censor analysis; no second mic capture device; live passthrough is preserved; no automatic sending before confirmation
files=MainForm.cs;AudioEngine.cs;SpeechDetector.cs;VoiceToTextForm.cs;build-validation workflow now runs feature/** and fix/** and uploads VoiceGuard-Test-win-x64
validation=latest commit build run https://github.com/superchilpil/VoiceGuard/actions/runs/39133791698 in progress; prior build run for commit 0a58d73c5ab190aafdb0abb5181b20eca804acc6 succeeded; latest isolation patches keep VTT outside filter queue state, timing watermarks, and censor audio history; verify latest commit build before sharing artifact
next=check latest workflow; provide test artifact; user runtime-test mic capture/transcription/confirm-send/cancel and ensure existing PTT/filter/soundboard behavior remains unchanged; keep isolated; no merge to main unless explicitly approved
