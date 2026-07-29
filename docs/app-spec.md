# Power over Porn — App Product Spec

*A brain-based, shame-free recovery companion that extends Dr. Raychel Powers' program between sessions.*

Status: **Design concept / v0.1** · Companion to the interactive prototype (`app/prototype.html`).
This document is a design and planning artifact — not a commitment to scope, timeline, or clinical claims. Clinical content and any therapeutic claims must be written and signed off by Dr. Powers.

---

## 0. Decisions adopted (v0.2)

- **Private-first, no sponsor/ally sharing** — removed peer data-sharing; the app keeps data on-device by default.
- **Cinematic first-run** — onboarding is an animated multi-step walkthrough (ambient motion, an animated urge-wave and habit-loop), not a static screen.
- **No emoji UI** — mood check-in uses a refined bar scale; milestones/settings use custom line icons.
- **PWA MVP first**, then native (validate cheaply, reuse the prototype).
- **SOS urge tool free forever** — safety is never paywalled.

Still open (see `docs/app-todo.md`): app name, program content, legal/store review.

## 1. Vision

The website converts people who are ready for help. The **app is where the work actually happens** — every day, in private, often at the exact moment an urge hits at 11pm.

The app's single job: **help someone rewire the habit loop, one urge and one day at a time, without shame.** It is a self-help companion, not a replacement for therapy, and it is explicit about that everywhere.

Three product pillars, drawn straight from the brand:

1. **Brain-based** — every feature is framed around how the brain actually forms and breaks habits (dopamine, cues, the 90-second urge wave). Progress is "rewiring," not "purity."
2. **Shame-free** — no streak-shaming, no red "you failed" screens. A slip is *data*, not a verdict. Language is warm, clinical, and second-person.
3. **Private by default** — used in vulnerable moments. App lock, stealth mode, local-first sensitive data, no ad tracking, ever.

---

## 2. Clinical grounding & safety (non-negotiable)

- The app **supports, does not replace** professional care. This disclaimer appears in onboarding, the footer of key screens, and settings.
- **Crisis routing:** a persistent path to crisis resources (e.g., 988 Suicide & Crisis Lifeline in the US, with locale-aware equivalents). If a check-in or journal signals self-harm risk, surface crisis resources immediately and prominently.
- **No diagnosis.** The onboarding "assessment" is a personalization tool and self-reflection aid, explicitly *not* a diagnostic instrument.
- **Evidence-informed methods only:** urge surfing (mindfulness-based relapse prevention), CBT thought records, HALT trigger awareness, habit-loop (cue–routine–reward) reframing, self-compassion. All lesson content authored/approved by Dr. Powers.
- **Sensitive-content safety:** the app never displays explicit material. The SOS tool is designed to de-escalate, never to test or expose.

---

## 3. Who it's for

| Persona | Situation | What they need from the app |
|---|---|---|
| **The self-starter** | Found the site, not in therapy yet, wants to try alone | Structured program, daily guidance, private tracking |
| **The client** | Working with Dr. Powers or another clinician | Between-session practice, homework, shareable reports |
| **The relapser** | Has tried and slipped many times; carries shame | Shame-free framing, strong SOS, "relapse as data" |
| **The supporter** | Partner/ally of someone recovering | Accountability role, gentle visibility (opt-in by the user) |

Primary design target: **the self-starter and the client.**

---

## 4. Design principles

1. **The red button is always reachable.** SOS is the center tab on every screen — an urge can't wait for navigation.
2. **Summary before detail.** Home leads with the one number that matters (days rewiring) and today's single focus.
3. **State in form, not just number.** Urge level, mood, and consistency use color + shape (sage = steady, amber = noticing, red = strong) so status reads at a glance.
4. **Committed dark theme.** This is a deliberate single-theme choice: the app is used privately, frequently at night. Light theme is out of scope for v1.
5. **Warm, plain language.** "How strong is the pull right now?" not "Log craving intensity."

---

## 5. Brand & UX system

Inherited directly from the website (`index.html` `:root`):

| Token | Value | Use |
|---|---|---|
| `--ink` | `#0C0A0B` | App background |
| `--carbon` / `--carbon-2` | `#15100F` / `#1D1618` | Cards, surfaces |
| `--bone` | `#F4EFEA` | Primary text, logo on dark |
| `--ash` / `--ash-dim` | `#9C9296` / `#6E666A` | Secondary/tertiary text |
| `--signal` / `--signal-bright` | `#C8161D` / `#E8242B` | Accent, SOS, key CTAs |
| `--ember` | `#5E0B0F` | Deep gradient anchor |
| `--steady` *(new)* | `#8FB39A` | Semantic "calm/positive" — a picked sage, distinct from the red accent |
| `--caution` *(new)* | `#D19A3E` | Semantic "medium urge" |

**Type:** Fraunces (serif display — big numerals, thesis lines, quotes) · Archivo / system grotesque (UI & body) · Spline Sans Mono (eyebrows, timers, data labels).

**Logo:** the hollow bone-outline brain (`assets/logo-onblack.png`) for all dark surfaces.

**Semantic color is separate from the accent.** Red = accent + urgency; sage/amber = wellbeing state. Never use red to mean "you failed."

---

## 6. Information architecture

Five-tab bottom nav (center = SOS, raised and always red):

```
Today    Program    ( SOS )    Reflect    You
```

- **Today** — daily home: progress, focus lesson, quick urge check, reassurance.
- **Program** — "The Recovery Model": six phases, lessons, exercises.
- **SOS** — full-screen in-the-moment urge tool.
- **Reflect** — daily check-in, journal, CBT thought records, insights.
- **You** — profile, progress/insights, milestones, accountability, settings.

---

## 7. Feature set (comprehensive)

### 7.1 Onboarding & assessment
- Warm intro to the brain-based, shame-free premise (3–4 screens).
- Self-reflection questionnaire → personalizes program pacing, trigger list, reminder timing. **Explicitly not diagnostic.**
- Set a starting point (today = Day 1; option to log a prior clean date).
- Choose a "why" (personal motivation, shown back during SOS).
- Privacy setup: app lock (PIN/biometric), optional stealth mode.
- Optional accountability ally invite.
- Notification preferences + quiet hours.

### 7.2 Today (home)
- Greeting + date; day count in the progress ring ("Days rewiring").
- Ring shows progress to the **next milestone**, not an infinite streak (reduces all-or-nothing pressure).
- Three stat tiles: urges surfed, check-ins, longest run.
- **Today's focus** — the next lesson/exercise, one tap to continue.
- **Right now** — one-tap urge check (Calm / Noticing / Strong) that logs and, if Strong, offers SOS.
- Daily reassurance quote (Dr. Powers voice).
- Persistent crisis line.

### 7.3 Program — "The Recovery Model"
Six phases (working outline — final content by Dr. Powers):
1. **Understand the brain** — why willpower keeps losing (dopamine, cues, the habit loop).
2. **Map your triggers** — HALT, cue-mapping, high-risk situations.
3. **Surf the urge** — the 90-second skill; build a personal SOS plan.
4. **Rewire the reward** — rebuild the dopamine baseline with real, earned rewards.
5. **Repair intimacy** — connection over compensation; desire, shame, relationships.
6. **Relapse as data** — bounce back without the spiral.

Each phase: lessons (text/audio/video), interactive exercises, worksheets, short knowledge checks, progress tracking, unlock logic (linear by default, with a "jump to what I need" option).

### 7.4 SOS — urge tool
The emotional core. Full-screen, calm, reachable from anywhere.
- Opening reframe: "This is an urge — not an emergency."
- **Guided breathing** orb (visual pacing; respects reduced-motion).
- **90-second wave timer** — the physiological urge crest.
- Action menu: **Reframe the thought**, **Play it forward** (how you'll feel in 20 min), **Text my ally**, **Log this urge**.
- Personalized reminder of the user's "why" and their surfed-urge count.
- "I rode it out" → logs a win (sage), returns gently to Today.
- Everything is de-escalation; nothing exposes or tests.

### 7.5 Reflect — check-in, journal, thought records
- **Daily check-in:** mood (5-point), HALT/emotion tags, urge level, wins.
- **Journal:** prompted (rotating, method-based) + free entry; private by default.
- **CBT thought record** (from Program): situation → automatic thought → reframe.
- **Inline insight:** surfaces patterns ("urges cluster ~11pm and when Lonely").
- Recent entries list with mood dots.

### 7.6 You — progress & insights
- Profile + plan.
- **Consistency heatmap** (last 8 weeks; sage = full day, red = slip — factual, not punitive).
- **Trigger patterns** (ranked bars: loneliness, late night, stress, boredom…).
- **Mood/urge trends** over time.
- **Milestones** (shame-free: "First 24 hours", "Triggers mapped", "10 urges surfed", "Neural Reset — 30 days").
- **Accountability:** ally card, weekly report sharing (ally and/or therapist).
- **Settings** (see 7.7).

### 7.7 Privacy, safety & settings
- **App lock:** PIN + biometric; auto-lock on background.
- **Stealth mode:** neutral app icon + name; hide preview content.
- **Reminders:** customizable, quiet hours, gentle tone.
- **Share report with therapist:** exportable PDF/summary (user-controlled).
- **Crisis resources:** always available.
- **Data controls:** export, delete account/data.

### 7.8 Accountability & community (phased)
- **No peer/sponsor data-sharing in v1.** Per design direction, the app is private-first: progress, journals, and urges stay with the user. We are intentionally *not* shipping "share your report with a sponsor/ally."
- If a peer-support model is added later, it must be strictly opt-in, minimal (a nudge, not raw data), and off by default.
- Optional **therapist report** is deferred and, if built, is user-initiated export only — never automatic.
- Moderated, anonymous group/community — **later phase**, high moderation cost; ship only with a safety plan.

### 7.9 Library (phased)
- Brain-science explainers, guided audio (urge surfing, sleep, self-compassion), articles by Dr. Powers.

### 7.10 Notifications & engagement
- Daily check-in nudge (respects quiet hours).
- "High-risk window" reminder based on the user's own trigger data (e.g., a supportive ping at 10:30pm).
- Milestone celebrations (calm, not confetti-spam).
- **Never** guilt-based ("You haven't opened the app…"). Supportive only.

---

## 8. Data model (high level)

| Entity | Key fields |
|---|---|
| **User** | id, created_at, plan, motivation ("why"), timezone, quiet_hours |
| **StreakState** | current_start_date, longest_run, milestones_earned[] |
| **UrgeLog** | timestamp, intensity (calm/notice/strong), triggers[], outcome (surfed/acted), context note |
| **CheckIn** | date, mood (1–5), halt_tags[], urge_level, wins |
| **JournalEntry** | timestamp, prompt_id?, body (encrypted), mood_tag |
| **ThoughtRecord** | timestamp, situation, automatic_thought, reframe |
| **LessonProgress** | phase_id, lesson_id, status, completed_at |
| **Ally** | contact ref, permissions (report/panic), status |
| **Report** | period, generated_at, shared_with[] |

Sensitive free-text (journal, notes) should be **encrypted at rest**; consider local-first storage with optional end-to-end-encrypted sync.

---

## 9. Privacy, security & compliance

- **Threat model:** the most likely "attacker" is someone physically near the user (partner, family). App lock + stealth mode are first-class, not settings afterthoughts.
- **Data minimization:** collect only what powers a feature. No third-party ad SDKs. No selling data — ever, stated plainly.
- **Encryption:** TLS in transit; encryption at rest for sensitive fields; biometric-gated access.
- **HIPAA:** if the app transmits data to Dr. Powers as a covered entity, treat therapist-sharing features as PHI and scope a BAA with any backend vendor. Otherwise, position clearly as a consumer wellness app and avoid PHI handling until reviewed by counsel.
- **App Store constraints:** Apple/Google have specific rules for this category — expect extra review for anything adult-adjacent. Content must be clearly clinical/supportive. **Legal + policy review required before submission.**
- **Age gate:** 18+.

---

## 10. Tech approach (recommendation)

- **Client:** React Native (Expo) for one codebase across iOS/Android; or a **PWA-first** MVP (the current prototype is HTML/CSS/JS and could evolve into an installable PWA to validate cheaply before native).
- **Local-first data** (SQLite / MMKV) with optional encrypted cloud sync (so the app is fully usable offline and private by default).
- **Backend (when needed):** managed auth, encrypted sync, report generation. Keep sensitive text E2E-encrypted where feasible.
- **Content:** lessons authored in a lightweight CMS so Dr. Powers can edit without a release.
- **Analytics:** privacy-preserving, aggregate only (e.g., self-hosted or a consent-gated, no-PII tool). Never track content of journals/urges off-device.

**Suggested build order:** PWA MVP (validate) → React Native app (scale) → accountability/community (safety-gated).

---

## 11. Monetization (options)

- **Freemium:** SOS tool + basic tracking free forever (safety shouldn't be paywalled); Program + insights + reports on subscription.
- **Subscription:** monthly/annual; annual discounted.
- **Clinician tie-in:** bundled with Dr. Powers' program; provider seats for therapists.
- Keep the **SOS urge tool free and unlocked always** — paywalling crisis-adjacent help is both unethical and bad for reputation.

---

## 12. Success metrics

- **North star:** urges surfed (acted-on-avoided) per active user per week.
- Retention: D1 / D7 / D30; % completing daily check-in.
- Program progression: lessons completed, phase completion rate.
- SOS efficacy: % of SOS sessions ending in "I rode it out."
- Wellbeing trend: mood/urge intensity trending down over time (self-reported).
- Guardrail: report and act on any signal the app increases shame or distress.

---

## 13. MVP scope → roadmap

**MVP (v1):**
- Onboarding + privacy setup (app lock)
- Today home + day/milestone tracking
- SOS urge tool (breathing, timer, reframe, log)
- Program: phases 1–3 with real content
- Reflect: daily check-in + journal
- You: basic progress + milestones
- Crisis resources

**v1.1:** Insights/patterns, trigger-based smart reminders, therapist report export, accountability ally (1:1).

**v2:** Full 6-phase program, Library (audio), community (with moderation/safety plan), clinician portal.

---

## 14. Open questions / decisions needed
See `docs/app-todo.md` — the list of things that need **your** input (name, content, business, legal, accounts).

---

## 15. Legal note

Power over Porn (app) is a self-help wellness companion. It does not diagnose, treat, or cure any condition and is not a substitute for professional medical or psychological care. All clinical framing and content require review and approval by a licensed professional (Dr. Raychel Powers, Psy.D.) before release. App-store submission requires prior legal and policy review.
