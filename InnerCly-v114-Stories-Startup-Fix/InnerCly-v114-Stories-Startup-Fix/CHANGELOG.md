## v114 — Story and Home initialization recovery

- Fixed a scope error in the standalone account-header synchronization helper: it referenced `runtime`, `esc`, and `accountBadgeMarkup` from another script callback's lexical scope. This could throw during authenticated startup and prevent Home/Stories initialization from finishing.
- The helper now uses `window.__INNERCLY_REALTIME__`, `window.__INNERCLY_ACCOUNT_BADGE_MARKUP__`, and its own HTML escaping function.
- Story creation, playback, rendering, camera/capture flow, and Story CSS were not edited. The v40 and v80 master backups remain unchanged.

## v113 — Home feed refresh reliability

- Fixed an overlapping-request race in `refreshPosts`: the newest request started no longer cancels an older successful response if the newest request fails. Once a newer response successfully applies, older responses are ignored.
- Malformed feed responses are treated as errors rather than valid empty feeds, preventing cached posts from being cleared by an incomplete payload.
- Initial Home feed fetch retries twice after short delays while cached content remains visible, reducing the chance that a brief startup/network race leaves the feed incomplete.
- Exposed the canonical refresh function to existing feature integration helpers.
- No changes to the Stories renderer or upper app layout. The v40/v80 master backups are unchanged.

## v112 — Session-hint recovery and Home feed reload
- Fixed a concrete stale-session hint risk: the page bootstrap previously read `sessionStorage` before `localStorage`, while the auth flow writes `localStorage` first. If a stale tab hint survived a failed sessionStorage update, the page could repeatedly select the wrong state namespace after reload and leave the Home feed stale or incomplete.
- The early bootstrap and session-hint reader now compare both stores and prefer the newest timestamped hint; older hints fall back to localStorage.
- If the durable hint write succeeds but writing sessionStorage fails, the app removes the stale tab hint when possible.
- Added no changes to the Stories renderer or the upper app layout. v40/v80 master backups are unchanged.

## v111 — Authenticated startup blank-screen recovery

- Fixed a concrete recovery flaw carried in v109/v110: if `startRuntime()` set the realtime `ready` flag and then threw before a view was activated, the recovery path skipped its retry, hid the sign-in overlay, and reported success. That could leave the app looking empty.
- Recovery now retries authenticated runtime activation even when `ready` is already true, and makes the existing Home view visible if startup leaves every `.view` inactive.
- If authentication is valid but rendering still has a problem, the user stays signed in and gets a visible notice instead of an empty screen or a false request to sign in again.
- The Stories renderer and upper app layout were not changed by this patch; v40/v80 master backups are unchanged.

## v110 — Consistent build identification

- Corrected the stale sign-in-screen label from `INNERCLY BUILD · V107` to `INNERCLY BUILD · V110`. The previous v109 package already reported v109 through its API and launcher, but the leftover visible label made it look like v107 was opening.
- Aligned the browser title, API build values, launcher-required build, launch URL cache-buster, and launcher status messages to v110.
- Carried forward the v109 session-restoration handling and other app features without intentional changes to their behavior.
- Preserved the v40 and v80 master backups byte-for-byte.

## v109 — Session restoration error handling

- Fixed a startup-path bug that could treat a client-side app initialization exception as a failed saved login, even when `/api/auth/me` had already confirmed the server session.
- Added limited retries for transient connection/server errors during session restoration.
- A real HTTP 401 still opens the sign-in prompt. Other HTTP errors now show their status and do not claim the session was lost or ask the user to sign in as though the password were wrong.
- Kept changes limited to session restoration messaging/flow and version identifiers; feature layout and behavior elsewhere were not intentionally changed.
- v40 and v80 backup files were carried forward unchanged.

## v108 — Stable local session origin

- Traced the repeated-login path in the saved v107 project. The server session is stored in an HttpOnly cookie scoped to the exact local host and port; the launcher previously could silently choose a different port when the remembered port was occupied, making the saved cookie unavailable at the new address.
- The launcher now keeps the remembered local port stable and warns instead of silently switching to a different origin when an unrelated process occupies it. If an InnerCly server is detected, existing safe replacement behavior is retained.
- No login UI, feed, Stories, badges, or social features were changed. v40 and v80 backup files are preserved unchanged.

## v107 — Session restoration and immediate Home feed

- Fixed local HTTP cookie security by basing the cookie Secure attribute on the actual request origin; loopback HTTP does not receive an unusable Secure cookie.
- Start server-session validation while the existing intro animation is playing so Home/feed loads in parallel.
- Check that the newly issued server cookie authenticates successfully before forcing the account-state reload; display a clear error when cookie persistence cannot be confirmed.
- Render the current account's compact cached feed immediately, then replace it with the fresh server response. Large inline media is excluded from the cache to avoid browser storage exhaustion.
- Kept header/top-navigation markup and app layout structure unchanged. Badge management from v105 remains present.
- Preserved v40 and v80 master backups byte-for-byte.

## v105 — Restore session persistence and instant Home feed

- Kept the v104 global badge rendering and dedicated badge-management page.
- Local launcher now reuses a running v105 server only when it confirms local-development cookie settings; it replaces a misconfigured server that would issue Secure cookies over HTTP.
- Authentication restoration no longer lets a transient second check reopen the login overlay after a session is already active.
- Home is selected after session restoration; the feed placeholder renders immediately and the post list appears as soon as the posts API returns, without waiting for unrelated sections.
- Preserved the existing header/navigation structure and all original features.
- Left v40 and v80 backup files unchanged.

## v104 — Active badges displayed across InnerCly

- Added a shared account-name badge formatter used across posts, comments, friend lists and requests, followers, Discover, profiles, group/event owners, group/direct/marketplace/delivery chats, Marketplace sellers, moderation safety lists, and owner role/badge search results.
- Stories now retain badge data from the API and display active badges on story cards.
- Standardized active-badge serialization and included it in story, owner user-search, blocked-user, and muted-user responses so those surfaces have the data needed to display badges.
- Kept the dedicated Creator Verification & Badges page and the existing Roles & Permissions controls.
- Kept v40 and v80 master backup files unchanged.

## v104 badge-visibility additions

- Covered top account name/header refresh after sign-in, profile updates, and an Owner granting a badge to their own account.
- Added safe self-account badge rendering to the legacy local feed comments, Marketplace seller/order cards, and local seller/order chat labels.
- Added active badge data to moderation report targets and reporters so the Safety UI can render them.

## Validation

- Python syntax checked.
- Inline JavaScript syntax checked.
- ZIP integrity checked.
- Isolated API badge-grant smoke test passed in v104.
