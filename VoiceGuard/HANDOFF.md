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
validation=latest commit build run https://github.com/superchilpil/VoiceGuard/actions/runs/39133791698 succeeded on 7595a57f6956a6b647bf3018ce835788c8f6997f; artifact=VoiceGuard-Test-win-x64; artifact_id=12139545079; expiry=2026-11-01; isolation patches keep VTT outside filter queue state, timing watermarks, and censor audio history
next=check latest workflow; provide test artifact; user runtime-test mic capture/transcription/confirm-send/cancel and ensure existing PTT/filter/soundboard behavior remains unchanged; keep isolated; no merge to main unless explicitly approved

[SESSION_UPDATE_2026-10-18_FINAL]
latest_commit=7595a57f6956a6b647bf3018ce835788c8f6997f
build=success;run=https://github.com/superchilpil/VoiceGuard/actions/runs/39133791698;artifact=VoiceGuard-Test-win-x64;artifact_id=12139545079
next=user runtime test; keep branch isolated; no main merge; inspect failures/feedback before additional changes


[SESSION_UPDATE_2026-10-18_LIVE_DICTATION_REVISION]
active_work_branch=feature/voice-to-text-test
latest_commit=ad3c37167f0c6b9bfd61ef97af533bda05b9fa2d
feature=changed the experimental interaction to live game-chat dictation; first activation captures the foreground game, presses configured chat-open key (default Enter), and starts mic capture; Whisper rolling snapshots update typed chat text live with common-prefix diffing to correct without duplicating; a single activation begins a short 375ms disambiguation window then stops capture and waits for final Whisper result before pressing Enter to send; second activation inside that window cancels, backspaces VoiceGuard-entered text, and presses Esc; Escape also cancels; no staged transcript/Send button flow
implementation=VoiceToTextForm.cs handles target focus, incremental text correction, final submit, and cancel; SpeechDetector.cs queues rolling 1.5s snapshots, coalesces stale queued interim snapshots, prioritizes profanity/filter work, and only surfaces final failures; MainForm.cs wires live transcript callback
validation=GitHub Actions run https://github.com/superchilpil/VoiceGuard/actions/runs/39134471747 succeeded on latest commit; artifact=VoiceGuard-Test-win-x64; artifact_id=12139402063; size=108749868 bytes; expiry=2026-11-01
safety=experimental branch only; no changes merged to main or released; user should test foreground targeting, live corrections, single-press send, double-tap/escape cancellation, and existing PTT/filter/soundboard behavior
next=wait for artifact upload completion, share VoiceGuard-Test-win-x64 from the latest Actions run; test carefully in a game with no sensitive chat; revise only on this branch based on runtime feedback


[SESSION_UPDATE_2026-10-18_NAV_AND_ESCAPE]
active_work_branch=feature/voice-to-text-test
latest_commit=44bb1755593b8f6e9cb953aa1b51350252eadab0
ui=added dedicated Speech to Text left-nav page in MainForm.cs, with dark/purple design language, configurable dictation activation and game chat-open keys, saved settings, usage instructions, and button to open the live dictation runtime window; removed duplicate launcher from general Settings page
escape_behavior=VoiceToTextForm.cs Escape global hook calls CancelSession(false,false); this stops/cancels current recognition generation and resets local session state without sending backspaces or injecting Escape; physical Escape passes through to game; double-tap remains clear typed text plus send Escape
build=workflow run https://github.com/superchilpil/VoiceGuard/actions/runs/39135666649 on commit 44bb1755593b8f6e9cb953aa1b51350252eadab0 was still in progress when recorded; verify final status/artifact before giving download link
next=verify latest workflow build; if success provide VoiceGuard-Test-win-x64; user test nav page/config persistence, live dictation, Escape cancel passthrough, double-tap clear/close, and existing PTT/filter/delay/soundboard

[SESSION_UPDATE_2026-10-18_HOTKEY_AND_NAV_REVISION]
active_work_branch=feature/voice-to-text-test
latest_commit=ce307445a0207f57b13931320c836ba3bd19409f
navigation=Speech to Text moved directly above Settings; Settings is pinned to the bottom of the sidebar; page visibility and column mapping updated to match the new order
dictation_ui=removed the Open Live Dictation Window button; intended workflow is live transcription typed directly into the foreground game chat with no visible secondary window
hotkey_fix=MainForm now creates a hidden VoiceToTextForm controller and starts global activation/Escape hooks during app load after persisted key settings are loaded; key changes rebuild hooks immediately; controller hooks are disposed on real app exit
safety_fix=reject VoiceGuard main window as a dictation target; verify capture can start before injecting the game's chat-open key, avoiding stray Enter/Escape when VoiceGuard engine or Whisper is not ready; physical Escape still cancels without backspacing or injecting Escape; double-tap cancellation still clears VoiceGuard-entered text and closes chat
validation=source changes pushed to feature branch; check latest GitHub Actions build for commit ce307445a0207f57b13931320c836ba3bd19409f before sharing a new test artifact; earlier successful artifact predates these changes and must not be reused
next=verify build; if it fails, inspect logs and fix only feature/voice-to-text-test; user should test hotkey with VoiceGuard started and game focused, live text, send/double-tap cancel/Escape, key persistence, nav order, and unchanged PTT/filter/delay/soundboard; no merge to main without explicit approval

[SESSION_UPDATE_2026-10-18_STT_OVERLAY_BLUE]
active_work_branch=feature/voice-to-text-test
latest_mainform_sha=8e2546990c8afbf60ad44c1f554e72f6ad7373b3
latest_voicetotext_sha=a77dd7eb9b346fd5cb4476e378a36d83920f1a3e
latest_modeoverlay_sha=a94ab42d6d8abc716a76f5b987f7da1fb766f6d5
feature=when live speech-to-text recording begins, MainForm receives an active callback and changes mode to SPEECH TO TEXT; ModeOverlayForm maps SPEECH TO TEXT / DICTATION to DodgerBlue for both Text style lettering and LED gradient; overlay returns to LIVE after successful send or cancellation; remains blue while final transcription is being processed
validation=source checked for callback wiring and blue color mapping; build not yet verified; inspect latest feature branch Actions workflow and only share an artifact built from these latest source commits
next=verify GitHub Actions build, then test Text and LED overlay styles during live dictation, ensure blue indicator ends on send/cancel, and confirm other modes retain existing colors; do not merge to main without explicit user approval

