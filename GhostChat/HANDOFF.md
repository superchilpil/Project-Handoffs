# GhostChat HANDOFF — AI CONTINUITY RECORD
FORMAT=AI_ONLY; HUMAN_READABILITY=NONREQUIRED; DENSE_SHORTHAND_ALLOWED; PRIORITY=MAX_CONTEXT/TOKEN_EFFICIENCY
LAST_RECONSTRUCTED=2026-10-06
IMPORTANT=Historical+intent context; compare current source before claiming implementation state.

[IDENTITY]
repo=https://github.com/superchilpil/ghost-chat
fork_of=https://github.com/Enubia/ghost-chat
owner=superchilpil;branch=main;language=Go;framework=Wails_v3
frontend=React+TypeScript+Zustand+CSS_Modules
platforms=Windows official release; macOS source-build support only
central_handoff=https://github.com/superchilpil/Project-Handoffs
purpose=transparent always-on-top desktop chat overlay; unified Twitch+YouTube+Kick
priority=practical/installable Windows streaming chat overlay

[CRITICAL_SCOPE]
OBS=OUT_OF_SCOPE;NOT_REQUIRED;DO_NOT_MODIFY;NO_DEPENDENCY;NO_FEATURE;NO_README_GOAL;NO_RELEASE_NOTE_ITEM
GhostChat remains independent of OBS.
Preserve Twitch/Kick while improving YouTube.
This explicitly supersedes older handoff text that incorrectly listed OBS as a goal.

[CORE]
multi_platform=Twitch IRC+YouTube Live Chat+Kick Pusher
ChatClient=Connect(input),Disconnect(); connecting while connected => disconnect/reconnect
ChatMessage=platform-neutral backend->frontend chat:message
MessageFragment=plain/emote-image; Twitch offsets converted client-side
MessageFilter=intake-time; rejected permanently; config changes non-retroactive
vanish=transparency+click-through hotkey
themes=built-in/custom
emotes=Twitch/BTTV/FFZ/7TV/YouTube/Kick
badges=platform-specific
YT_events=SuperChat+membership
fade=configurable
filters=bots/commands/users
i18n=en-US/de-DE

[AUTO_LIVE]
background monitor + auto chat connect, per-platform independent
poll=Twitch+YouTube+Kick independently; one service failure must not block others
interval=5s/10s/15s/30s/1m/2m/5m; runtime clamp 5..300; historical/default≈15s
per_platform=AutoConnect
live=>auto connect; autoOwned=true; emit auto-connected; optional auto-show/focus+vanish
not_live=>disconnect ONLY autoOwned; emit auto-disconnected; optional hide tray
manual connections=never disrupt
Twitch channel persists
Kick channel persists
YouTube channel/handle/ID persists; temporary automatic broadcast URL MUST NOT overwrite saved input
YouTube live=>current broadcast video URL
settings=persist

[YOUTUBE_CONNECTION_STABILITY]
2026-10-05/06 recent work focused on duplicate/replay protection and connection recovery.
backend_dedupe=internal/chat/youtube/client.go seenIDs map; markSeen(id) bounded cache; reset on Connect; applies to StreamList and Innertube messages
frontend_dedupe=frontend/src/components/Chat/Chat.tsx seenMessageIdsRef keyed platform:id; bounded relative to MAX_MESSAGES
commit=34c4b3b14c77f1278f944c5a02a2838d4fb800f1 backend Innertube replay dedupe
commit=04d10a68ad7e1d9977720da4afd0b16c2c1e0ae8 frontend replay guard
commit=48dd43a7bdefd6d3f1ae042c3aa53c4fb68252c4 YouTube recovery resilience
recovery=streamLoop retains pageToken across transient gRPC failures; exponential backoff up to 60s; Innertube pollLoop backoff/rebootstrap; rebootstrap after maxFailuresBeforeReboot=8 except rate-limit/auth-stale paths
rate_limit_backoff=30s base up to 5m
HTTP_timeout=targeted increase was attempted; verify exact current newHTTPClient value before claiming
original_user_symptom=messages repeated, then disappeared/reappeared with increasing gaps; connection recovery was strengthened
OPEN_VERIFY=runtime stream test still required; if duplicates persist, consider preserving seenIDs across reconnects or replacing reset/5000-entry behavior with a time-windowed cache
release_notes_NEXT_RELEASE=connection fixes ONLY; do not mention vanish-position persistence, chat logging, UI changes, or unrelated work in that release's notes
intended_release_note_points=improved YouTube connection resilience; greater tolerance for temporary network/API failures; longer request timeout if verified; improved recovery/reconnection; replayed-message protection during recovery

[RECENT_DECISIONS/WORK]
2026-10-03:
- OBS untouched; explicitly no OBS work going forward
- poll YouTube/Twitch/Kick live status
- auto-connect each live chat
- saved Twitch/Kick usernames/channels persist
- persistent channel configuration
- 15s independent live monitor
- auto connect/disconnect without disrupting manual connections
- Twitch IRC preserved
- Kick Pusher preserved
- YouTube StreamList low-latency transport + Innertube fallback
- vendored protobuf for YouTube transport
- unified ChatMessage mapping
- preferred end-user auth UX="Sign in with Google"; no user-created credentials
- a no-login/public-chat/API-key alternative was discussed; final policy must follow current source/user decision, do not invent
- release build can embed YouTube API key via buildconfig ldflags; current README says self-builds require YOUTUBE_API_KEY; prebuilt releases embed release config
- Twitch auth=keychain token store
- Twitch live monitor uses access token when logged in

[CHAT_LOGGING]
required=continuous DURING stream; NOT post-stream-only
one session log can contain all connected platforms
directory=user-selected
messages=archived on receipt; remain even if moderator later deletes
metadata=stream title when available; start/end; prefixes Y/T/K; emoji descriptors
current source=NewApp->chatlog.NewLogger; every onMessage=>chatLog.Message
chat:connected=>chatLog.Connect; chat:disconnected=>Disconnect; shutdown=>Close
toggle=enable while connected => Connect current platforms; disable=>Close
stream title=resolved after connection
folder picker=SelectChatLogDirectory; stale/nonexistent saved dir ignored so native picker still opens
HISTORICAL_BUGS=Browse did nothing; path field hard to read; user reported no logging; user explicitly requires live availability
STATUS=source contains live logging wiring; runtime success MUST be verified before declaring fixed

[UI/SETTINGS]
persistent=settings/window position+size/channels/AutoConnect/themes/chat-log settings/etc
tray=open/close/center/vanish/config-folder/quit
minimize_to_tray
auto_show_on_live
live_poll_interval
chat_log_enable/path
YouTube API key
Twitch account/auth
path field=readable/editable
Browse=must work
settings changes must not regress channels/auth/logging
vanish_position_persistence=manual vanish saves X/Y; PositionSaved distinguishes valid 0,0; auto-show live must not overwrite manual vanish position; verify startup restore in current main.go before claiming complete

[UPDATER]
startup CheckForUpdate -> update:available
InstallUpdate for installed Windows builds; updater handoff then process exits
historical bug=update icon did nothing
current fix=TitleBar awaits InstallUpdate, shows Updating…, logs failure, falls back to opening update URL, disables button while updating
commits=a63adbd5c62afc90a22d4ef08027707e2cef83f8;da88cc2408d79b6707001b3c04de0151a1b023cb
STATUS=runtime install/update still requires verification

[AUTH]
preferred UX="Sign in with Google"; no user-created GhostChat username/password
Twitch=current keychain-backed token manager
YouTube auth/API=current source/user decision; self-build README requires YOUTUBE_API_KEY; public release embeds release API config
do not invent credentials policy

[BUILD/PACKAGING]
prebuilt Windows user should not need Git/Go/Node/pnpm/Wails
Windows WebView2 assumed included Windows10/11
dev=Go1.25+;Node20+;pnpm;Wails3 CLI
repo has build.bat,Taskfile.yml,.github workflows,build/,installer docs/settings
historical builder ZIP requirement=complete source+build material; no Git clone/download
failed=empty/incomplete ZIP; "Ghost Chat is missing or incomplete"; Go not on PATH
installable product > source-only archive
external toolchain only if legally/practically unavoidable; explicit requirement, never silently assume PATH
verify archive before claiming self-contained/build success
GitHub Actions is primary executable build/release path; local build.bat is optional developer convenience

[RELEASE_WORKFLOW]
workflow=.github/workflows/release.yml
dispatch=workflow_dispatch; version override; release-candidate toggle
official_release_target=Windows only; Windows portable EXE+NSIS installer; latest.yml generated; no macOS release artifacts/manifests
macOS=users may build from source; not part of official release workflow
release_build=YouTube API key embedded via ldflags
NEXT_RELEASE_NOTES=ONLY connection fixes from [YOUTUBE_CONNECTION_STABILITY]; explicitly exclude vanish persistence/chat logging/UI/unrelated work
Do not edit release notes preemptively unless user asks/build is being prepared.

[HISTORICAL_REJECTED]
incomplete ZIP=rejected
Git-dependent build package=rejected
assume Go installed=rejected
post-stream-only chat logging=rejected
OBS integration=rejected/out-of-scope
Do not repeat without explicit reversal.

[MAJOR_BUILD_RELEASE_CHANGES]
2026-10-06:
- Windows release workflow is the ONLY official release target; macOS release job/artifacts/manifest generation removed. macOS remains source-buildable only.
- release=.github/workflows/release.yml now builds Windows portable+NSIS only and create-release depends only on Windows.
- YouTube release secrets remain injected for final linker/build steps, but MUST NOT flow into binding-generation task variables/checksum labels.
- build/Taskfile.yml generate:bindings now consumes BINDING_FLAGS instead of BUILD_FLAGS.
- build/windows/Taskfile.yml defines secret-free BINDING_FLAGS for binding generation while BUILD_FLAGS retains final release ldflags/secrets for go build.
- This fixes GitHub Actions Windows failure where masked secret values became '*' in .task/checksum filenames, producing Windows invalid-filename errors.
- removed obsolete dummy latest-mac.yml generation from Windows-only release workflow.
commits=aa74ccc4e0700600b1d16e1a9e1e1d59e266e9a6;d707b9729c26639e5b1199dd15eeaf17d9967885;eef582484c9b548dab7f1d14d2b89024f17ad95e

[KNOWN_OPEN_VERIFY]
1 YouTube connection stability under long real stream
2 if duplicates persist, inspect seenIDs lifecycle/reset behavior and frontend remount/fade behavior
3 updater icon/runtime
4 Browse button/runtime
5 prove live logging runtime
6 prove auto-live all 3 platforms + manual connection isolation
7 prove YouTube StreamList/Innertube fallback
8 prove persistence channels/AutoConnect/log path
9 verify genuinely complete Windows package
10 keep OBS untouched
11 verify vanish-position startup restore before claiming complete

[CURRENT_SOURCE_MAP]
app.go=App{auth,clients,config,connectionState,connectionTransport,autoOwned,liveMonitorCancel,chatLog,window state}
NewApp=>chatlog logger; onMessage logs every ChatMessage
wireClients=>Twitch/YouTube/Kick; connected/disconnected wrapped for logging/transport
chat:connected=>chatLog.Connect(platform,"")
ServiceStartup=>restore Twitch auth; start live monitor; updater check
UpdateConfig=>persist config; chat-log enable state; vanish hotkey
Connect=>Twitch/Kick channel persistence; YouTube manual input persistence; automatic YT URL does not overwrite saved channel; resolve chat-log stream title
monitor=startLiveMonitor->pollLivePlatforms->pollTwitchLive/pollKickLive/pollYouTubeLive->applyLiveState
SelectChatLogDirectory=native directory picker; stale path validation; AttachToWindow
InstallUpdate=exists; frontend now handles errors
internal/chat/youtube/client.go=StreamList+Innertube; seenIDs/markSeen; backoff/rebootstrap
frontend/src/components/Chat/Chat.tsx=frontend replay guard; MAX_MESSAGES=500
frontend/src/components/Settings/GeneralSettings.tsx=chat log settings; readable path input; browse
frontend/src/index.css=.chat-log-location-input
internal/chatlog/logger.go=session logger; service history+active tracking; title/start/end/emoji descriptors
root=current includes build.bat,go.mod/go.sum,Taskfile.yml,app.go,main.go,internal/,frontend/,build/,docs/,installer docs/settings

[USER_PREFS]
direct_fix>long_explanation
verify before claim
preserve working behavior
BUILD_STRATEGY=GitHub_Actions_primary;LOCAL_PC_BUILD=NOT_REQUIRED;build.bat optional developer convenience only
significant user-visible change=>README/release notes as appropriate
release notes=concise,user-focused;next release connection-fixes-only unless user changes scope
handoff=manual anytime
AI-only dense shorthand preferred
do not make user re-explain known context
current user decisions override stale historical goals

[CONTINUITY]
Read before asking user to explain. Combine with current repo/source and recent conversation. Historical != proof. Explicit negative requirements are binding until user reverses. Significant state change=>update corresponding project handoff. Full transcript may be unavailable; maximize recoverable context without inventing.
