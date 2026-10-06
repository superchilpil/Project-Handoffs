# GhostChat HANDOFF — AI CONTINUITY RECORD
FORMAT=AI_ONLY; HUMAN_READABILITY=NONREQUIRED; DENSE_SHORTHAND_ALLOWED; PRIORITY=MAX_CONTEXT/TOKEN_EFFICIENCY
LAST_RECONSTRUCTED=2026-10-05
IMPORTANT=Historical+intent context; compare current source before claiming implementation state.

[IDENTITY]
repo=https://github.com/superchilpil/ghost-chat
fork_of=https://github.com/Enubia/ghost-chat
owner=superchilpil;branch=main;language=Go;framework=Wails_v3
frontend=React+TypeScript+Zustand+CSS_Modules
platforms=Windows+macOS
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
STATUS=source now contains live logging wiring, but runtime success MUST be verified before declaring fixed

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

[UPDATER]
startup CheckForUpdate -> update:available
InstallUpdate for installed Windows builds; updater handoff then process exits
historical bug=update icon did nothing
STATUS=verify current UI/runtime; never infer from control presence

[AUTH]
preferred UX="Sign in with Google"; no user-created GhostChat username/password
Twitch=current keychain-backed token manager
YouTube auth/API=current source/user decision; self-build README requires YOUTUBE_API_KEY; public release embeds release API config
do not invent credentials policy

[BUILD/PACKAGING]
prebuilt Windows user should not need Git/Go/Node/pnpm/Wails
Windows WebView2 assumed included Windows10/11
dev=Go1.25+;Node20+;pnpm;Wails3 CLI
repo currently has build.bat,Taskfile.yml,.github workflows,build/,installer docs/settings
historical builder ZIP requirement=complete source+build material; no Git clone/download
failed=empty/incomplete ZIP; "Ghost Chat is missing or incomplete"; Go not on PATH
installable product > source-only archive
external toolchain only if legally/practically unavoidable; explicit requirement, never silently assume PATH
verify archive before claiming self-contained/build success

[HISTORICAL_REJECTED]
incomplete ZIP=rejected
Git-dependent build package=rejected
assume Go installed=rejected
post-stream-only chat logging=rejected
OBS integration=rejected/out-of-scope
Do not repeat without explicit reversal.

[KNOWN_OPEN_VERIFY]
1 updater icon/runtime
2 Browse button/runtime
3 chat-log path readability
4 prove live logging runtime
5 prove auto-live all 3 platforms + manual connection isolation
6 prove YouTube StreamList/Innertube fallback
7 prove persistence channels/AutoConnect/log path
8 verify genuinely complete Windows package
9 keep OBS untouched

[CURRENT_SOURCE_MAP]
app.go: App{auth,clients,config,connectionState,connectionTransport,autoOwned,liveMonitorCancel,chatLog,window state}
NewApp=>chatlog logger; onMessage logs every ChatMessage
wireClients=>Twitch/YouTube/Kick; connected/disconnected wrapped for logging/transport
ServiceStartup=>restore Twitch auth; start live monitor; updater check
UpdateConfig=>persist config; chat-log enable state; vanish hotkey
Connect=>Twitch/Kick channel persistence; YouTube manual input persistence; automatic YT URL does not overwrite saved channel
monitor=startLiveMonitor->pollLivePlatforms->pollTwitchLive/pollKickLive/pollYouTubeLive->applyLiveState
SelectChatLogDirectory=native directory picker; stale path validation
InstallUpdate=exists
root=current includes build.bat,go.mod/go.sum,Taskfile.yml,app.go,main.go,internal/,frontend/,build/,docs/,installer docs/settings

[USER_PREFS]
direct_fix>long_explanation
verify before claim
preserve working behavior
Windows=>build.bat/equivalent
significant user-visible change=>README/release notes as appropriate
handoff=manual anytime
AI-only dense shorthand preferred
do not make user re-explain known context
current user decisions override stale historical goals

[CONTINUITY]
Read before asking user to explain. Combine with current repo/source and recent conversation. Historical != proof. Explicit negative requirements are binding until user reverses. Significant state change=>update handoff. Full transcript may be unavailable; maximize recoverable context without inventing.
