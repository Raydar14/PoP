# Be333.app ↔ Power over Porn — Integration Spec

*How the recovery app launches a meditation session in Be333.app and syncs the result back.*

Status: **Design draft v0.1.** Engineering spec for the meditation coping-skill integration (`docs/app-spec.md` §7.9c).
Written assuming **you own/control Be333.app** (recommended path). A limited third-party fallback is in §8.

---

## 1. Goals

1. From inside Power over Porn (PoP), the user starts a timed meditation — the timer runs in **Be333.app**.
2. When the session finishes, its **summary** (duration, completion, timestamp) flows back into PoP and is stored with the user's recovery data, so practice can be measured against urges.
3. Everything is **opt-in, revocable, read-only-from-PoP's-view, and minimal** — only session summaries, never audio or anything the user writes in Be333.

Two problems to solve: **(A) launch** a session, and **(B) sync** the result back. They're independent — you can ship A first.

---

## 2. Recommended architecture (you own Be333.app)

```
 PoP app  ──(1) deep link: start session──▶  Be333.app
    ▲                                            │
    │                                     (user meditates)
    │                                            │
    │  ◀─(2a) return deep link (quick) ──────────┤
    │                                            │
 PoP API ◀─(2b) server webhook: session.completed (source of truth)
    │
    └─(3) optional: GET /v1/sessions to backfill/reconcile
```

- **Account link once** via OAuth 2.0 (PKCE) so the two accounts are associated.
- **Launch** each session with a deep link / universal link.
- **Sync** authoritatively via a **server-to-server webhook** from Be333 → PoP (survives the user closing either app). The return deep link is a nice-to-have for instant UI feedback.

---

## 3. Account linking (OAuth 2.0 + PKCE)

Be333.app acts as the OAuth **provider**; PoP is the client. PKCE (no client secret on device).

**Authorization request** (PoP opens this in an in-app browser / ASWebAuthenticationSession):
```
GET https://be333.app/oauth/authorize
  ?response_type=code
  &client_id=pop_mobile
  &redirect_uri=powerover://oauth/be333
  &scope=sessions.read
  &state=<csrf>
  &code_challenge=<S256>
  &code_challenge_method=S256
```

**Scopes** (least privilege): `sessions.read` only. No profile, no content, no write.

**Token exchange** (PoP backend or app):
```
POST https://be333.app/oauth/token
  grant_type=authorization_code
  code=<code>
  redirect_uri=powerover://oauth/be333
  client_id=pop_mobile
  code_verifier=<verifier>
→ { access_token, refresh_token, expires_in, token_type: "Bearer" }
```

- Store refresh token in the device secure enclave / Keychain / Keystore (or PoP backend if server-side sync).
- **Revoke:** `POST /oauth/revoke` when the user taps "Unlink" (and PoP deletes stored Be333 data if the user chooses).

---

## 4. Launching a session (deep link)

PoP sends the user to Be333 with the chosen duration and a correlation id.

**Custom scheme** (works app-to-app when Be333 is installed):
```
be333://session/start
  ?duration=300            # seconds
  &type=calm               # reset | calm | deep (optional preset)
  &source=pop
  &session_ref=<uuid>      # PoP-generated correlation id
  &return=powerover://be333/return
```

**Universal / App Link** (preferred — falls back to web if the app isn't installed):
```
https://be333.app/s?duration=300&type=calm&source=pop&session_ref=<uuid>&return=powerover%3A%2F%2Fbe333%2Freturn
```

Behavior on the Be333 side:
- Parse params, start the timer at `duration`.
- Persist `session_ref` + the linked PoP user with the session record.
- If not installed → the universal-link URL renders the Be333 web timer (PWA), same params.

> `session_ref` is the glue: PoP generates it, passes it in, and matches it when the completion comes back — so a session is never double-counted or mis-attributed.

---

## 5. Syncing the result back

### 5a. Webhook (authoritative — recommended)
When a session ends, Be333 POSTs to PoP's registered endpoint:

```
POST https://api.poweroverporn.app/v1/integrations/be333/webhook
Content-Type: application/json
X-Be333-Signature: t=<unix>,v1=<hmac_sha256(secret, t + "." + rawBody)>

{
  "event": "session.completed",
  "id": "evt_9f2...",                 // idempotency key
  "created_at": "2026-07-31T03:12:40Z",
  "data": {
    "session_ref": "<uuid from launch, if present>",
    "be333_session_id": "sess_abc123",
    "pop_user_id": "<mapped via the linked account>",
    "started_at": "2026-07-31T03:07:10Z",
    "ended_at":   "2026-07-31T03:12:40Z",
    "duration_sec": 330,
    "planned_sec": 300,
    "completed": true,                // false if ended early
    "type": "calm"
  }
}
```

- **Verify** `X-Be333-Signature` (HMAC-SHA256 over `timestamp.rawBody` with a shared secret); reject if the timestamp is stale (>5 min) or the signature fails.
- **Idempotent**: dedupe on `id` (and/or `be333_session_id`) — safe to retry.
- Be333 should **retry** with backoff on non-2xx (e.g., 5 attempts over ~24h).

### 5b. Return deep link (instant UI, non-authoritative)
On finish, Be333 also bounces the user back:
```
powerover://be333/return?session_ref=<uuid>&completed=1&duration=330
```
PoP shows an immediate "Nice — 5 minutes logged" toast, then reconciles with the webhook when it lands. Never treat the client return as the source of truth (it can be lost if the user swipes away).

### 5c. Pull/backfill (reconciliation)
```
GET https://be333.app/v1/sessions?since=<iso8601>
Authorization: Bearer <access_token>
→ { "sessions": [ { be333_session_id, started_at, ended_at, duration_sec, completed, type } ] }
```
Run on app-foreground to catch anything the webhook missed (offline device, etc.).

---

## 6. What PoP stores (data model)

```
MeditationSession {
  id                // PoP id
  source: "be333"
  be333_session_id
  started_at, ended_at
  duration_sec, planned_sec
  completed: bool
  type: "reset" | "calm" | "deep"
  created_at
}
```
Rolls up into: weekly minutes, session count, day-streak, and the "practice vs. urge intensity" correlation. **Never** stores audio, guidance text, or anything authored in Be333.

---

## 7. Security & privacy

- **Least privilege:** `sessions.read` scope only; summary fields only.
- **Transport:** TLS everywhere; HMAC-signed webhooks; short-lived access tokens + rotating refresh.
- **Storage:** tokens in Keychain/Keystore; session data encrypted at rest; on-device-first where feasible.
- **User control:** unlink instantly revokes tokens and (optionally) deletes synced sessions.
- **Data mapping:** the PoP↔Be333 user link is the only PII crossing; keep the mapping server-side, not in deep-link URLs.
- **Compliance:** treat meditation-with-recovery-context as sensitive; if any of it reaches Dr. Powers' practice, scope a BAA (see app-spec §9/§11).

---

## 8. Fallback if Be333.app is third-party (not owned)

If you can't add OAuth/webhooks to Be333:
- **Launch only** via universal link (§4) — no `pop_user_id`, rely on the **return deep link** (§5b) for a best-effort client-side log.
- Accept that some sessions won't sync (return link lost) and label the stat "approximate."
- Or: manual "I meditated" logging in PoP as a stopgap.
This is strictly worse; owning the Be333 side unlocks reliable sync.

---

## 9. Rollout

| Phase | Scope |
|---|---|
| **MVP** | Universal-link launch + return deep link (§4, §5b). Instant "logged" UX, no backend needed. |
| **v1** | OAuth account link (§3) + webhook sync (§5a) → reliable, authoritative logging + correlations. |
| **v1.1** | Pull/backfill reconciliation (§5c); unlink + data deletion; retry/monitoring. |

---

## 10. What Be333.app needs to build (checklist)

- [ ] OAuth 2.0 authorize + token + revoke endpoints; register `pop_mobile` client + `powerover://oauth/be333` redirect; scope `sessions.read`.
- [ ] Universal link + custom scheme handler for `…/s?duration&type&source&session_ref&return` that starts a timed session and stores `session_ref` + linked user.
- [ ] On completion: fire `session.completed` **webhook** (HMAC-signed, retried) to PoP, and bounce the user back via the `return` deep link.
- [ ] `GET /v1/sessions?since=` read endpoint.
- [ ] Shared webhook secret + a way to rotate it.

*(PoP builds: the connect/consent UI — already prototyped —, token storage, webhook receiver + verification, session store, and the correlation insights.)*
