# IELPS — Public Landing routing release, 17 August 2026

Answers the *IELPS Public Landing Routing, Webhook & QA Amendment, 17 August 2026*.

Two things are in here: what went live on eilps.com today, and what could not go
live and why.

---

## 1. DEPLOYED — the legacy Public Landing chooser is gone

Section 2: *"Remove the legacy seven-pathway chooser from live behaviour through
routing/state handoff, not redesign."*
Section 6: `[DEPLOY APPROVED]`.

Live on `https://eilps.com` since 12:43 UTC, 17 August 2026.

### Every control, verified on the live site after deployment

| Control | Before | Now | Section 3 requires |
|---|---|---|---|
| Log in | seven-card chooser | sign-in form, stays on `/` | direct authentication |
| Sign in | seven-card chooser | sign-in form, stays on `/` | direct authentication |
| Start free | seven-card chooser | `/learner/` | `/learner/` |
| Start free placement | seven-card chooser | `/learner/` | `/learner/` |
| Create account | seven-card chooser | `/learner/` | `/learner/` |
| Find your level | seven-card chooser | `/learner/` | `/learner/` |
| Placement | seven-card chooser | `/learner/` | `/learner/` |
| **Join the community** | **seven-card chooser** | **`/learner/`** | *not listed — see below* |
| Learn | `/learner` | `/learner/` | unchanged |
| Dashboard, Plans, See plans | `/dashboard`, `/account` | unchanged | unchanged |

**An eighth control.** The amendment lists seven, which is what I reported. The
landing markup binds destinations by a `data-eilps-action` attribute rather than
by label, and *Join the community* in the community section carries
`data-eilps-action="placement"` too — so it opened the chooser as well. It is
fixed with the rest. The rule is about the behaviour, not about the seven labels
that happened to be noticed.

### The Public Landing's appearance did not change

Section 2: *"The approved Public Landing appearance, layout, images, typography,
button styling and approved visible copy remain unchanged."*

Not an assurance — a measurement. The live page was captured before deployment
and again after, at nine scroll positions, at 1280×800 desktop and 390×780
mobile. All eighteen pairs are **pixel-identical**: the difference bounding box
is empty in every case.

    live now vs live before deploy, 1280x800: y=0:same y=800:same y=1600:same
      y=2400:same y=3200:same y=4000:same y=4800:same y=5600:same y=6400:same
    live now vs live before deploy,  390x780: y=0:same y=800:same y=1600:same
      y=2400:same y=3200:same y=4000:same y=4800:same y=5600:same y=6400:same

`evidence/landing_before_*.jpg` and `evidence/landing_after_*.jpg` are three of
those pairs. No visible copy was changed to make the routing work.

### What was preserved rather than deleted

Section 2: *"Do not delete Login.jsx indiscriminately if it also owns legitimate
landing/authentication behaviour."*

`Login.jsx` owns the landing **and** the platform's authentication. Only the
chooser came out. Kept and re-tested:

- **Email + password sign-in** — reached directly from Log in and Sign in.
- **Class-code sign-in for under-12 classroom students.** They have no email
  address on IELPS; they sign in with a class code and a student profile. That
  form used to be reached by picking the first card in the grid. The grid is
  gone, so it is now reached from a *"Student with a class code?"* link inside
  the sign-in panel. Removing the chooser must not take a working authentication
  route away with it. `evidence/v_classcode.jpg`.
- **The signed-out gate on protected routes.** `/dashboard` signed out still
  shows that route's own locked state at its own address, so signing in lands
  the visitor where they asked to go. Its *Create an account* button now goes to
  `/learner/` instead of opening the grid.

### Pathway first, level second

Section 4. Two things were withdrawn from this surface:

- **The "Age band" and "Starting CEFR" selects are gone.** A level picked from a
  dropdown before placement is a self-allocated level. Nothing replaces them.
- **The browser no longer asserts a CEFR level at registration.** `cefr_level`
  is sent only when an Access Panel handoff carried one as context. Unset, the
  backend's own canonical default applies — I did not change that default, and
  no placement, auth or entitlement rule was touched.

A class's starting level at teacher/studio registration is left exactly as it
was. That is a class setting, not a learner placement result, and the form never
offered a control for it.

**The handoff.** `/?pathway=<id>` opens the matching registration form directly,
with no chooser, and carries `?level=` as context only. An unknown id opens
nothing, so a stale or guessed link cannot resurrect the grid.
`evidence/v_handoff.jpg`, `?pathway=teacher`.

### EILPS → IELPS

Section 5, scoped as written to the Public Landing and authentication path:

- browser tab title: `EILPS - English Integrated Language Platform Studio` →
  `IELPS - …`
- the access dialog's own wordmark
- the loading splash: `Loading EILPS...` → `Loading IELPS...`

Remaining `EILPS` strings are inside the signed-in application — the coach, the
certificate screens, the studio, the verification page. They are outside the
landing/authentication path this section names, so they are listed rather than
changed: `Coach.jsx`, `Certificates.jsx`, `CertificateVerify.jsx`,
`CoursebookStudio.jsx`, `StarPathAddon.jsx`, `AiOperations.jsx`, `PageUI.jsx`.
Say the word and they go in a separate pass.

### Rollback

    cd ~/eilps/frontend
    mv dist dist.failed && mv dist.rollback-chooser-20260817T124354Z dist

The web server reads from disk per request, so no restart is involved either
way. Source backups: `src/Login.jsx.pre_chooser_removal_20260817`,
`src/App.jsx.pre_chooser_removal_20260817`.

---

## 2. NOT DEPLOYED — the Access Panel half of this release

Section 6 expects one release carrying *"approved copy + approved routing +
legacy-overlay removal/rerouting + typography reverted + no unrelated visual
change."* The first, second and third are live. The typography is reverted in
source. But the *"Retain Continue with A1 / A2 / …"* copy and the placement-control
removal cannot ship on their own, and this is why.

**`/learner/` today is not the tree those corrections were made in.** The live
Access Panel is an earlier application. The approved 16 August Access Panel has
never been deployed at all:

    https://eilps.com/learner/access/    404
    https://eilps.com/learner/levels/a1/ 404

Deploying the corrected tree would therefore not be a copy change. It would
publish, in one step and for the first time: the approved 16 August Access Panel
page, six new CEFR level pages, the level band, the 15 August visual direction
work — reading and writing expansion windows, the clickable keyword pop-up — the
PiP stylesheet fix and the parent "Needs attention" panel.

That is a substantial visual release. Section 6 says this release must contain
*"no unrelated visual change"*, and the 15 August direction says not to deploy
those previews before they are approved. So I have not deployed it, and I am not
going to slip it out under a routing release. It needs its own decision.

**Typography is reverted regardless**, so whenever that release does go, it goes
without it. Commit `578632b` on `visual-direction-20260815`:
`--font-sans` returns to Inter, Hanken Grotesk is no longer loaded, Plus Jakarta
Sans is restored on the Access Panel and the six level pages as the approved
package supplied it, and the `font-content` wrappers are removed. Verified in a
browser against the built output. The approved copy and routing on that branch
are untouched: `Continue with A1` still resolves to `#pathways`, and no
Placement control has come back.

`diff/panel-typography-revert.patch`.

---

## 3. Still with the Administrator

**Webhook.** The patched `billing.js` is on disk and syntax-checked; the running
process is still on the old code, so an unsigned webhook still returns 500. Last
checked 12:47 UTC — `eilps-api` has been up since 00:03:38 UTC and has not been
restarted. One controlled restart activates it. I have no sudo and did not force
it: with no way to start the service again if it failed to come back, that is not
a trade worth making to save a round trip.

**QA environment.** Approved in sections 9–12. Every remaining step is
privileged: the runtime account decision, the port 4310 preflight, the root-owned
test-mode environment file, the `eilps_qa` database, and Stripe CLI forwarding to
the loopback. Noted and agreed: `User=anirudhat` is not a QA architecture, the
dedicated `eilps` service account is; `NODE_ENV=production` stays; the trial
keeps inline `price_data`; no test key or webhook secret comes near this chat or
any evidence package.

---

## Files

    diff/Login.jsx.patch                  the deployed change, exact
    diff/App.jsx.patch                    the loading splash wordmark
    diff/panel-typography-revert.patch    commit 578632b, not deployed
    diff/panel-typography-revert.stat.txt
    evidence/landing_before_*.jpg         live, before deployment
    evidence/landing_after_*.jpg          live, after deployment — identical
    evidence/live_signin.jpg              Log in on the live site now
    evidence/v_signin.jpg                 the sign-in dialog
    evidence/v_classcode.jpg              class-code sign-in, preserved
    evidence/v_handoff.jpg                /?pathway=teacher
