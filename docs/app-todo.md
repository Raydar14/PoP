# App — things I need from you (Alex / Dr. Powers)

I built the prototype and spec autonomously overnight. These are the decisions and inputs **only you can make** — none of them block reviewing the prototype, but they shape the real build.

## Decide (quick calls)
- [ ] **App name** — "Power over Porn" is long for a home-screen label. Want a short app name (e.g., "PoP", "Rewire", "Powers Method")? Note: a discreet name doubles as stealth mode.
- [ ] **Platform priority** — my recommendation is a **PWA MVP first** (cheap to validate, reuses this prototype) then a native iOS/Android app. Agree, or go straight to native?
- [ ] **Free vs paid line** — I recommend keeping the **SOS tool + basic tracking free forever**, program + insights on subscription. OK?
- [ ] **Tone check** — is "Days rewiring", "urges surfed", "relapse as data", "the 90-second wave" the language you want? Any terms to change?
- [ ] **Milestone names** — I used "Neural Reset (30 days)", "First 24 hours", etc. Your call on names/thresholds.

## Provide (content — the big one)
- [ ] **Program content** — the six phases are my outline. The actual lessons, exercises, scripts, and audio need to be **written and clinically approved by you.** This is the core IP; the app is the delivery vehicle.
- [ ] **Reassurance quotes / voice** — short daily lines in your voice (I placeholdered one).
- [ ] **Crisis resources** — confirm the right hotlines/resources to route to (defaulted to 988 US). Add international if you'll serve outside the US.
- [ ] **Trigger + emotion list** — confirm the HALT/emotion tags and trigger categories you want tracked.

## Business / legal (before any store launch)
- [ ] **Legal + policy review** — app stores scrutinize this category. Get counsel to review positioning (wellness vs. medical), disclaimers, and store compliance **before submission.**
- [ ] **Privacy stance** — confirm: no ad tracking, no data selling, encryption at rest, therapist-sharing only when the user opts in. (I've written the spec this way.)
- [ ] **HIPAA** — if the app sends anything to your practice, we likely need a BAA with the backend vendor. Flag whether therapist-facing features are in scope for v1.
- [ ] **Developer accounts** — Apple Developer ($99/yr) and Google Play ($25 once) if we go native.
- [ ] **Age gate** — confirm 18+.

## Optional / later
- [ ] Accountability ally feature — do you want partners/sponsors in v1, or later?
- [ ] Community — high moderation cost; I've scoped it for v2 with a safety plan. Keep it there?
- [ ] Clinician portal — want therapists to be able to assign homework / view reports?

---

### What's already done (see the PR)
- Interactive, on-brand prototype — 5 screens (Today, Program, SOS, Reflect, You): `app/prototype.html` + published Artifact link (in the chat).
- Comprehensive product spec: `docs/app-spec.md`.
- Everything uses the site's existing brand system (colors, fonts, the brain logo).

## Biofeedback / wearables (Signals) — new
- [ ] **Confirm the concept & tone** — OK to detect *late-night elevated-HR windows* and nudge, framed as support (opt-in, on-device, "logged as a win, not a slip")? Any part you want softer or off by default?
- [ ] **Which devices first?** I'd start with **Apple Watch/iPhone (HealthKit)** + **Oura**, then Whoop/Garmin/Fitbit/Health Connect.
- [ ] **API access & accounts** — Oura, Whoop, Garmin, Fitbit developer programs + OAuth; Apple HealthKit entitlement; Android Health Connect. Some require app review/approval.
- [ ] **Health data = PHI-adjacent** — legal/privacy review for storing/processing HR/HRV/sleep; on-device processing preferred; BAA if any of it reaches your practice.
- [ ] **Clinical framing** — you (Dr. Powers) to confirm how we describe stress/HRV and the "risk window" so it stays evidence-informed and non-alarmist.

## Meditation coping skill · Be333.app — new
- [ ] **Confirm the flow** — meditation is an opt-in coping skill; the app launches **Be333.app** for the timer and logs sessions back once connected. Good?
- [ ] **Be333.app integration** — what can Be333.app expose? Need either (a) a deep-link to start a timed session + a webhook/API to report completion, or (b) account linking (OAuth) to pull sessions. Who builds the Be333 side?
- [ ] **What to sync** — minutes, session length, streak, timestamp (to correlate with urges). Confirm.
- [ ] **Entry points** — Today "daily practice", the high-risk-window nudge, and an SOS "Meditate with Be333" action. OK?
