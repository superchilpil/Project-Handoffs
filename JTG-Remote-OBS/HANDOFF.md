# JTG Remote OBS — AI Continuity Handoff
FORMAT=AI_CONTINUITY; DENSE_SHORTHAND; SOURCE_OF_TRUTH=current_user_decisions+source_repo; VERIFY_BEFORE_CLAIM
LAST_UPDATED=2026-11-13
SOURCE_REPO=https://github.com/superchilpil/JTG-Remote-OBS
HANDOFF_REPO=https://github.com/superchilpil/Project-Handoffs
BRANCH=main
STATUS=early_scaffold+first_OBS_WebSocket_client_in_progress; NO_VERIFIED_BUILD_OR_RUNTIME_TESTS_YET

[PROJECT_IDENTITY]
name=JTG Remote OBS
purpose=phone-first local-network remote control for dedicated OBS streaming PC + integrated YouTube Live workflow/chat
apps=Windows_companion(.NET9)+native_Android(Kotlin)
platform=YouTube primary
owner_context=user runs main gaming PC + dedicated streaming PC; existing NDI feed into streaming PC OBS already works
primary_UI=Android phone app
secondary_UI=optional Windows companion UI/OBS custom dock; chat dock is useful but secondary
source_repo=https://github.com/superchilpil/JTG-Remote-OBS
main_branch=main
project_docs=README.md;docs/ARCHITECTURE.md;docs/YOUTUBE-INTEGRATION.md;docs/API-CONTRACT.md;Windows/Companion/README.md

[TOP_LEVEL_GOALS]
1 Android app is the main user experience/control center.
2 Windows companion handles local OBS WebSocket, Google OAuth/YouTube API, authenticated local API and fans out status/chat to phone + optional dock.
3 Phone should offer full stream workflow: start OBS encoder output; expose YouTube Manage Stream/Live Control Room experience for settings; confirm Go Live only after feed ready; distinct Stop Encoder vs End YouTube Broadcast.
4 Android full YouTube live chat, not merely viewer: display supported event types; send messages; moderation actions when authorized; filters/scroll controls/highlights/details.
5 Optional Windows OBS dock can expose status/chat, but do not let it become design priority or limit phone features.
6 Local-network-only remote access. No cloud relay, hosted backend, public endpoint, port forwarding, UPnP, cellular remote control.
7 Maintain distinction OBS outputActive vs YouTube broadcast lifeCycle/public-live status. OBS output active != YouTube publicly live.
8 NDI production feed already works; preserve current setup unchanged.

[PHYSICAL_ARCHITECTURE]
Main gaming PC:
- Owns scenes, transitions, overlays, microphone/audio mix, production setup.
- Sends finished program video to dedicated streaming PC through existing NDI.
Dedicated streaming PC:
- Existing OBS receives NDI input and works.
- JTG controls OBS streaming/encoder delivery and YouTube broadcast/chat/status only.
- No NDI integration, monitoring, setup, or configuration.
Android:
- Connects to Windows companion over same private LAN only.
Windows companion:
- Connects to OBS WebSocket on loopback (default ws://127.0.0.1:4455).
- Performs Google OAuth/API integration; stores refresh tokens locally using Windows DPAPI.
- Owns one upstream YouTube live chat connection and fans normalized events to Android and optional dock.
- Exposes LAN API only after pairing/TLS/device-auth security is implemented.
Network:
- OBS WebSocket remains loopback-only and password protected.
- Companion must bind only validated private LAN interface after security implementation.
- Pair with one-time QR code/secret and per-device revocable credentials; TLS; reject non-private peers.
- No internet exposure of companion or OBS.
- YouTube/Google internet traffic is allowed for OAuth/API only.

[ANDROID_REQUIREMENTS]
Native Kotlin Android app (NOT Flutter); current starter uses Android Views, not Compose.
Main screens/tabs expected:
- Dashboard: companion connectivity; OBS encoder output status/duration/reconnect; YouTube broadcast state; separate lifecycle controls; errors/health.
- Live Chat: realtime feed; event-specific cards; message composition/send; authorized moderation.
- Settings: pair/revoke device; companion address/connection; YouTube account status (OAuth on Windows); chat filters/preferences.
Streaming workflow:
- Start encoder output from phone (OBS StartStream).
- Wait for encoder/feed readiness and show status.
- Open/embed or launch YouTube Manage Stream / Live Control Room to adjust stream metadata/settings; exact embedding feasibility to be verified.
- Explicit Go Live confirmation when ready; use YouTube broadcast transition to testing/live as appropriate.
- Stop encoder output and End YouTube broadcast are distinct actions with explicit confirmation.
Chat:
- Ordinary messages, Super Chats, Super Stickers, membership events/messages, gifts, polls, supported system/moderation events per official API.
- Send chat message.
- Delete messages, timeout users and other moderation only if OAuth role/scope authorizes.
- Display avatar/profile image when available, channel name, badges, timestamp, paid amount, event context.
- Auto-scroll; pause on manual scroll; jump-to-latest; filters; mod/member highlighting.
- Reconnect/backoff; dedupe; preserve visible feed during transient interruptions.
- Phone is primary; feature parity should not be sacrificed for dock.
- Avoid undocumented scraping/Innertube; use official YouTube Live Streaming API.
Phone UI currently only a placeholder dashboard/chat/settings; no pairing/network implementation yet.

[WINDOWS_COMPANION_REQUIREMENTS]
- .NET 9 ASP.NET Core/Kestrel host.
- OBS WebSocket v5 client, challenge/salt auth, GetVersion/GetStreamStatus/GetStats and StartStream/StopStream; subscribe events where appropriate.
- Store OBS password outside source control (current bootstrap uses OBS_WEBSOCKET_PASSWORD env var).
- Google OAuth authorization handled on Windows; tokens DPAPI protected; never send refresh token to Android.
- YouTube broadcast selection/state/lifecycle; official API.
- YouTube Live Chat streamList preferred (low-latency server-streaming), reconnect with continuation token/backoff, normalize/dedupe events; insert/delete/moderation APIs as permitted.
- API endpoints/events documented in docs/API-CONTRACT.md.
- Optional OBS custom browser dock: status/chat. Current Dock is just HTML/CSS/JS shell; not yet registered/loaded by OBS.
- No NDI code/control/monitoring.
- Companion API currently loopback only; MUST NOT bind LAN before secure pairing/TLS/device auth.

[YOUTUBE_INTEGRATION]
Official API strategy from prior research:
- liveChatMessages.streamList preferred over polling list; server streaming, low latency, continuation token for reconnect; official API docs.
- liveChatMessages.insert to send; delete/moderation operations according to OAuth permissions.
- LiveChatMessage resource can include text, SuperChat, SuperSticker, membership, gift, poll and moderation/system events; map only supported types.
- Google OAuth scope minimization; verify exact scopes and role requirements before implementation.
- Tokens stay on Windows and DPAPI protect refresh token.
- API quota handling and streamList reconnection/error statuses need implementation/testing.
- Do not use undocumented endpoints/scraping for product core.
- YouTube Manage Stream UI: assess supported URL/embed/app launch; do not assume iframe embedding works due YouTube restrictions.

[SECURITY]
No secrets committed. Never ask user to paste Google/OBS credentials into chat.
OBS websocket only 127.0.0.1/localhost; default port 4455.
Companion bootstrap API currently http://127.0.0.1:47831 only. It is safe local scaffold, not LAN-ready.
Before LAN access: QR one-time pairing; per-device tokens, revocation; TLS cert provisioning/trust; private-interface bind; validate peer IP; rate limits; replay protection; auth on all control endpoints; audit sensitive actions.
No cloud, relay, UPnP, port-forward, public inbound endpoint, cellular remote.
DPAPI for YouTube refresh tokens.
Android must not contain Google client secret or refresh token.
Keep Go Live / End Broadcast explicit-confirmation UI.
No direct external exposure of OBS WebSocket.

[CURRENT_REPOSITORY_STATE]
Files committed on main; initial scaffold only; build/test outcomes not checked.
README.md: project goals/roles/security/architecture summary.
docs/ARCHITECTURE.md: topology, components, security, non-goals.
docs/YOUTUBE-INTEGRATION.md: OAuth, broadcast lifecycle, streamList/chat strategy.
docs/API-CONTRACT.md: REST endpoints and WebSocket event envelope/types.
Windows/Companion/JTG.RemoteOBS.Companion.csproj: net9.0-windows10.0.19041.0, ASP.NET Core SDK.
Windows/Companion/Program.cs: Kestrel loopback 127.0.0.1:47831; DI ObsWebSocketClient + hosted worker; health and OBS status endpoints; local POST start/stop endpoints. Not LAN accessible.
Windows/Companion/ObsWebSocketClient.cs: newly added OBS WebSocket v5 basic client; loopback host guard; challenge/salt SHA256 auth; identify; GetStreamStatus polling; StartStream/StopStream; serialized requests. Needs compile review, protocol review, events, stats, cancellation/reconnect testing. No verified build.
Windows/Companion/README.md: OBS WebSocket setup; env vars OBS_WEBSOCKET_HOST, OBS_WEBSOCKET_PORT, OBS_WEBSOCKET_PASSWORD; loopback API endpoints. Do not commit password.
Windows/Dock/index.html,dock.css,dock.js: basic status/chat shell polling local health; not yet actual OBS custom dock integration.
Android/settings.gradle.kts; Android/build.gradle.kts; Android/gradle.properties; Android/app/build.gradle.kts: AGP 8.9.2, Kotlin Android 2.1.20; compileSdk35/minSdk26/targetSdk35; Java17.
Android/app/src/main/AndroidManifest.xml: internet permission; backup false; cleartext traffic false.
Android/app/src/main/java/com/jtg/remoteobs/MainActivity.kt: native View UI, tabs Dashboard/Live Chat/Settings; pairing/chat/stream buttons disabled placeholders; no network integration.
.github/workflows/windows-build.yml: Windows .NET 9 build; workflow_dispatch + relevant pushes; build only, no installer/release.
.github/workflows/android-build.yml: Ubuntu JDK17 + Gradle 8.11.1; assembleDebug artifact; no wrapper checked in (workflow installs Gradle); no release signing.
.gitignore present.
Recent commit SHAs (chronological; current branch includes them):
a1fdd0297b6da3c0a928ae0039c69daa65001b74 README
a69a14d33484be5f2b347faba7791acc22702da0 architecture
1d9602e76b63d5e1a473638c5d35ddfea34fca32 youtube integration
bb295fb0d0038aa627431afd9cca34106be2b751 API contract
b4499533b2302be0c3a2f01f13a3208613cff169 csproj
0277d0c4a537577beafd14ef5a1165f2739218d6 Program bootstrap
24f0dca148bec2f0a9c20324ec6e89d4b1b0b9de appsettings
7e5a8911a3caf2fd15ca5c5b042c9641e80916d dock html
27934fa445bd60d6ac3e74566fe606cba1e33b6b dock css
261037e6a990412131faf0e659fc6ab3f474d49f6 dock js
f37dd1c12c0bd5c07c35473ad6fa19f2abee3964 Android settings
27999b7f5ed4b1704f42857e5026276c303f7367 Android root gradle
8144f0a7282f36b8bf0faafecbf2b6368cdb4a9c gradle properties
fa01507814d3cca25903ca676c58335b0ac68a7c Android app gradle
d6d861617d50810ad3aa17480f16da7579a0b805 manifest
d6b58fcac96969b05eef53bbf6bce882c3e1ef1c initial Android activity
01519f4ba8498a81274d5131fe8ce2365c3e512d Windows build workflow
c01d25a885430fd9187bbecfb7355ab8fd27c7d0 Android build workflow
c17e4c741fafa066ad21d852e3bca934ae44f8ec gitignore
97928dab844c9076e5fbae0fe480966ef2d692d add ObsWebSocketClient
ac74e979add5be2b96d6007b05d1289ed6a3e2b1 correct JSON message handling
debf4a75b9694a570dc40d098f2ecfd477890439 serialize requests/fix websocket receive buffer
16e0b633233e5cc8dd8d416118a981b81fbe35f9 wire companion host to OBS
5cf79543eb5e3aac56136506cc88c39f49479d7d document companion local setup
d95d43bcdd0d419692d85501b338d31cf6e79c98 phone-first Android navigation/dashboard/chat/settings shell
NOTE=SHAs above are commits returned by GitHub write tools; not necessarily full source review/build validation. Current main HEAD expected d95d43bc... unless later changes.

[KNOWN_IMPLEMENTATION_GAPS_AND_RISKS]
- OBS client not built or tested. Review C# compilation/API usage first.
- OBS ReceiveJsonAsync currently returns JsonNode; WebSocket fragmented messages supported with max 1MB guard.
- Current request loop ignores/discards unsolicited OBS events while awaiting response; polling status used instead. Implement proper reader loop/event dispatch before subscribing broadly.
- Current status poll every 2 sec; no GetStats yet.
- Start/stop API currently loopback unauthenticated; acceptable only because loopback-only bootstrap; do not expose LAN.
- OBS endpoint success currently means request accepted, not proof of stable encoder state; UI should wait for status.
- Need ensure API methods cannot race requests; _gate currently serializes status poll and controls.
- OBS authentication secret is environment variable; no secure settings UI or DPAPI for OBS password yet.
- Current host may start OBS worker and repeatedly fail if OBS absent; expected, but ensure logs/backoff sensible.
- No YouTube OAuth, broadcast management, live chat, DPAPI, Android API client, pairing/TLS, or QR code implemented.
- No custom OBS dock registration.
- Android nav shell is Android Views; user has not selected Jetpack Compose; don't migrate without reason.
- Android build workflow uses Gradle install and no wrapper; verify workflow.
- CI workflow results have not been queried/verified. Do not claim builds passed.
- No Windows installer or signed APK/release workflows yet.
- Manage Stream UI embedding capability not verified.
- REST API contract should evolve consistently with implementation.

[BUILD_RELEASE]
Windows current workflow=build-only; .NET9; no installer. Android current workflow=assembleDebug + artifact. Neither build has been verified in current work.
User preference from other projects: GitHub Actions primary for build/release; do not require user's PC to build; local build.bat optional only.
Future desired: Windows installer/release workflow and Android APK artifact/release workflow; only implement after build verified.
Windows target net9.0-windows10.0.19041.0; user's dev environment Windows, .NET9 SDK installed.
Android min SDK26; compile SDK35; JDK17.
No virtualization requirement; avoid adding virtualization dependencies.

[USER_CONTEXT_AND_PREFERENCES]
- Main streaming platform YouTube; may multi-stream later but current project YouTube-only.
- Gaming PC is production machine; dedicated streaming PC encodes and sends to YouTube.
- NDI between PCs is already working; do not touch.
- User wants integrated Android chat to avoid relying on GhostChat; GhostChat remains untouched and separate.
- User develops Windows apps and GitHub projects; expects direct implementation/commits when tools allow.
- JTG branding.
- Keep answers direct; report what actually committed and tested.
- No unsolicited task/reminder creation; user previously explicitly said wait for their input before creating tasks on another project.
- Current user explicitly clarified Android is focal point; Windows chat is optional secondary.
- Preserve all out-of-scope exclusions below.

[OUT_OF_SCOPE_DO_NOT_MODIFY]
- DO NOT modify existing NDI setup, add NDI monitoring, or build NDI controls.
- DO NOT add OBS scenes/sources/transitions/audio/mic/overlay/production controls. Main gaming PC owns production.
- DO NOT modify GhostChat or migrate it; separate project remains untouched.
- DO NOT make Windows dock the primary UI or constrain Android feature set to dock.
- DO NOT expose OBS WebSocket to LAN/internet.
- DO NOT add cloud relay, hosted backend, port forwarding, UPnP, public endpoint, or cellular control.
- DO NOT use undocumented YouTube scraping/Innertube as the primary implementation.
- DO NOT send/store Google refresh tokens or client secret on Android.
- DO NOT treat OBS outputActive as proof broadcast is publicly live.
- DO NOT merge Stop Encoder and End Broadcast into one ambiguous action.

[RECENT_WORK]
2026-11-13:
- User approved phone-first direction: Android is primary streaming control center; Windows companion is backend; Windows dock chat optional.
- User requested begin development.
- Added ObsWebSocketClient.cs, initial OBS v5 hello/identify auth and request/status/control operations, loopback guard, reconnect loop.
- Corrected JsonDocument/JsonNode handling and serialized status/control requests.
- Wired Program.cs DI hosted worker and endpoints.
- Added Windows/Companion/README.md local setup instructions.
- Reworked Android MainActivity into dashboard/live chat/settings shell, with disabled placeholders until pairing/security.
- No build/test run yet.
- User additionally requested a new handoff in Project-Handoffs; this file is that handoff.

[NEXT_STEPS_PRIORITY]
P0 verify current code:
1 Fetch/review current Program.cs and ObsWebSocketClient.cs; run Windows build via GitHub Actions or available CI; fix compilation issues. Do not claim success before run status.
2 Inspect OBS v5 protocol behavior; test with OBS installed on dedicated streaming PC; handle event frames and request responses with a single reader/pending-request correlation design. Add GetVersion/GetStats and explicit status.
3 Verify current OBS WebSocket password/env config; never commit secrets.
P1 complete Windows service foundation:
4 Build robust OBS connection/status lifecycle; handle reconnect, timeouts, clean shutdown, output status, stream errors; ensure controls idempotent/safe.
5 Implement local API contracts + typed status/event models; keep loopback-only until LAN auth complete.
6 Secure pairing/QR, per-device credentials/revocation, TLS, private network bind and peer checks before phone remote controls.
P2 YouTube:
7 OAuth authorization flow on Windows; DPAPI refresh token storage; minimal scopes.
8 broadcast discovery/selection and accurate lifecycle state; Go Live and End Broadcast distinct from OBS output.
9 implement streamList, reconnect/backoff, event normalization/dedupe, sending, moderation auth.
P3 Android-first:
10 implement pairing/connection screen and authenticated API client.
11 wire dashboard to live OBS + YouTube state.
12 build full chat timeline/composer/moderation/filter/scroll/highlighting/event rendering.
13 integrate YouTube Live Control Room via supported web/app flow; validate embedding limitations.
P4 polish:
14 optional OBS custom dock registration, status/chat only.
15 CI verify, Windows installer/release and Android APK workflow; do not claim until artifacts verified.

[CONTINUITY_RULE]
This handoff records goals/decisions and observed source state, not a verified release. Current user decisions override any stale future additions. Inspect repo before implementation; preserve NDI/GhostChat and phone-first direction. Significant changes should update this handoff. Never infer build success from workflow file or commit.
