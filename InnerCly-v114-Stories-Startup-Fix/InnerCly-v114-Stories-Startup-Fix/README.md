# InnerCly v114 — Stories Startup Fix

## Focused v114 correction
- Fixed a startup-breaking scope error in the standalone account-header synchronization helper. It no longer references `runtime`, `esc`, or `accountBadgeMarkup` identifiers that are local to another script callback.
- The account-header helper now reads the existing global realtime user and badge renderer, and escapes account display names locally.
- Story creation, playback, Story rendering, camera/capture code, Story CSS, and the upper app layout were not edited. The v40 and v80 master backups remain byte-for-byte unchanged.
- The v113 Home-feed refresh fix and v112 session-hint correction are carried forward.

## Run locally
1. Extract the ZIP into a new folder.
2. Run `START_INNERCLY.bat`.
3. Keep the Command Prompt window open while using InnerCly.

Do not open `index.html` directly; the local app needs the server and database. Do not open the files in `backups`; the v40 and v80 master backups are preserved.

## Checks performed for v114
- Python syntax checks passed for `main.py`, `launch_app.py`, and `currency_registry.py`.
- All 13 inline JavaScript blocks passed `node --check`.
- A focused JavaScript test confirmed account-header sync no longer throws from references to variables that are not in its script scope, and correctly updates the handle, display name, and badge.
- The `__INNERCLY_SAVE_STORY__` function body is byte-for-byte identical to v113; Story renderer, player, and camera/capture code were not edited.
- v40/v80 backup files were hash-compared with the source package and were unchanged.
- A full browser interaction test could not run in this environment because the browser runtime blocks navigation to the local app. Please keep your previous ZIP and test this new package in a separate folder first.

# InnerCly v111 — Blank Screen Recovery and Session Restore

## v111 focused changes
- Cookie Secure mode is derived from the actual request origin, so plain-HTTP localhost/127.0.0.1 sessions are not issued unusable Secure cookies even if APP_ENV was set incorrectly. Production HTTPS remains Secure.
- The launcher requires build v111 and replaces an older InnerCly server on the same saved local port when safe.
- Server session restoration starts in the background while the existing intro plays; it does not wait until the intro ends before loading Home.
- Login/registration verifies that the server-issued HttpOnly cookie works before forcing the state-namespace reload; if it does not, the user gets a clear message rather than an immediate silent loop.
- Home displays a small per-account cached feed immediately while the server refresh runs. Large inline media is intentionally excluded from the cache and restored with the live feed.
- No header, top navigation, or visual layout markup was structurally changed in this patch.
- Active badge rendering and dedicated badge-management controls from v105 are retained.
- v40 and v80 master backups are preserved byte-for-byte.

## Test notes
- Python syntax and inline JavaScript syntax checked.
- API session cookie verified over local HTTP; the same cookie authenticated before and after a server restart using an isolated database.
- The badge grant API smoke test passed.
- ZIP integrity and byte-for-byte v40/v80 backup hashes checked.
- A full desktop-browser interaction test could not run in this environment because the browser runtime blocks navigation to local/file URLs; please verify the final behavior on the laptop.

# InnerCly v105 — Badges Everywhere

## Scoped changes in v105
- Verified Creators is invite-only; the Owner manages invitations, and access is checked by the server.
- Verified Creators has its own private post store. Its posts are not automatically published to the public Home feed, and public feed posts are not copied into the private group.
- Owner-only badge controls for Blue (notable), Purple (professional/expert), Gold (official InnerCly), and Green (trusted business), with evidence notes and a badge audit log.
- Silver is reserved for future Premium subscriptions. No payment or subscription system is enabled, and Silver cannot be manually granted.
- Keeps the registered-account directory hidden by default; Owner account-role search remains available.
- Contacts More opens distinct Find People, Friend Requests, and Refresh Contacts actions.
- Reels label in mobile navigation, clearer post composer wording, and consistent badge marks beside account names across the application, including legacy/local fallback cards and chats, file-name overflow handling, and limited motion/containment tuning for selected sections.
- Preserves the existing app structure and the v40/v80 master backup files.

## Run locally
1. Extract the ZIP.
2. Double-click `START_INNERCLY.bat`.
3. Keep the terminal window open and use the local URL shown by the launcher.

Do not open `index.html` directly; the app uses the local server and database.

## Verification performed
- Python modules compile.
- All inline JavaScript blocks pass `node --check`.
- API tests cover invite-required access, private group post creation/read, Owner badge grant and evidence validation, badge payload, and blocking manual Silver grants.
- An isolated API smoke test granted a badge through the Owner endpoint and confirmed active badge data appeared in `/api/people`, `/api/posts`, and `/api/auth/me` for the same account. It does not modify or test the user’s real database.
- These checks are not a substitute for a full browser/device test of camera, microphone, or WebRTC calls.


## v105 targeted fixes
- Reels has its own view and reads published video content; the existing Videos page remains available separately.
- The default configured Owner username is `innercly_owner` (overridable with `INNERCLY_OWNER_USERNAME`).
- On startup, the configured official Owner account receives its gold badge, with a badge audit entry.
- The existing Owner-only Roles & Permissions badge form remains the control for granting/revoking blue, purple, gold, and green badges; silver remains reserved for future Premium subscriptions.


## v105 packaging/version verification
- Launcher now requires API build v105.
- Server build endpoint returns v105.
- Browser title and build marker identify v105.

## v105 badge visibility
- Active badges render beside account names throughout InnerCly: feeds, comments/replies, top account header, profile and mobile About panel, friend requests/lists, followers/following, Discover/search, Stories and Story viewer, group/direct chats, Marketplace seller/buyer chats, deliveries, groups/event/page owners, verified creators, safety/muted/blocked lists, moderation reports, and Owner role/badge controls. Legacy local fallback comments, group chats, seller rows, and order chats also recognize the logged-in account safely.
- APIs consistently include active badge data for those surfaces.


### v105 session and Home restoration fix

The local launcher checks that the running server is configured for non-Secure cookies over local HTTP before reusing it. On saved-session restoration, Home opens first and renders the feed as soon as post data is available. Active account badges remain enabled throughout the interface. v40/v80 master backups are preserved byte-for-byte.


## v109 targeted authentication-restoration fix (carried forward)
- Separates HTTP session validation from local renderer/realtime startup. Once `/api/auth/me` confirms the saved server cookie, a client-side initialization error no longer reopens the login prompt.
- Retries transient connection and server HTTP failures up to three times.
- Only HTTP 401 is treated as an expired/missing login. Other HTTP errors report the status and explicitly state that the saved login was not cleared.
- Existing app features, UI sections, and v40/v80 backup files are preserved.


## v113 — Home feed refresh reliability

- Fixed overlapping Home feed refreshes: an older request that succeeds can still apply if a newer request fails; once a newer successful response applies, older responses are ignored.
- Rejects malformed feed responses instead of treating them as an empty feed and replacing already-visible cached posts with nothing.
- Retries the initial Home feed request twice on startup while keeping any cached feed visible, so a brief startup/network race is less likely to leave the feed incomplete.
- Exposes the canonical feed refresh function to the existing integration helper.
- No changes to the upper layout or Stories renderer; v40/v80 master backups are unchanged.
