# The Dark Mode Incident

A frontend developer at a mid-sized startup receives a Slack message from the CEO:

> "Hey! Can we add dark mode? My eyes hurt at night. Should be quick right? 🌙"

She sighs.
"Finally, a fun one."

**Week 1:**
She opens the codebase. The styling situation is... diverse.

* 40% inline styles
* 30% CSS modules
* 20% a `styles.css` file with 4,200 lines and no comments
* 10% Tailwind classes someone added "just to try it"
* And one component styled entirely with `!important` because "it was the only way"

She does the sensible thing: schedules a "Styling Strategy Alignment" meeting.

**Week 2:**
Design gets involved. They deliver a Figma file called `Dark_Mode_v1_FINAL_v3_USE_THIS_ONE.fig`.

It contains:

* 47 shades of gray (legally distinct from 50)
* A color called `--dark-mode-black-but-not-too-black`
* Another called `--text-primary-dark-accessible-AAA-karen-approved`
* A sticky note: "Can we make the dark mode feel... premium?"

She asks what "premium dark" means.

Designer: "You know, like Spotify. But not legally Spotify."

**Week 3:**
She creates a proof of concept using CSS variables:

```css
:root {
  --bg-primary: #ffffff;
  --text-primary: #1a1a1a;
}

[data-theme="dark"] {
  --bg-primary: #1a1a1a;
  --text-primary: #ffffff;
}
```

It works.
It's elegant.
It will never ship.

**Week 4:**
The tech lead reviews the PR.

"This is nice, but what about system preferences? What if someone has dark mode on their OS but wants light mode in our app? What about scheduled switching? What about per-page themes? What about..."

She closes the laptop and stares at the wall for 11 minutes.

**Week 5:**
She builds a proper solution:

```tsx
interface ThemeContextValue {
  theme: 'light' | 'dark' | 'system' | 'scheduled' | 'per-page' | 'vibes';
  setTheme: (theme: Theme) => void;
  resolvedTheme: 'light' | 'dark';
  systemPreference: 'light' | 'dark' | 'no-preference' | 'confused';
  schedule: ThemeSchedule | null;
  isHydrated: boolean;
  isTransitioning: boolean;
  prefersReducedMotion: boolean;
  hasSufficientContrast: boolean;
  userHasExplicitPreference: boolean;
  lastManualOverride: Date | null;
}
```

The toggle button now has more state than most banking applications.

**Week 6:**
QA finds a bug.

> "When switching from dark to light mode while hovering over the dropdown menu during a page transition on Safari 14.2 on an iPad with low battery mode, the text briefly turns invisible."

She cannot reproduce it.
QA sends a 47-second screen recording.
She still cannot reproduce it.
QA insists.

She adds:

```css
.dropdown-text {
  color: inherit !important;
}

/* TODO: Remove after Safari 14 is dead */
/* Note from 2019: Remove after Safari 12 is dead */
/* Note from 2017: Remove after IE11 is dead */
```

**Week 7:**
Accessibility review happens.

Reviewer: "The contrast ratio of your secondary text in dark mode is 4.48:1. WCAG AA requires 4.5:1."

She adjusts the gray from `#888888` to `#8a8a8a`.

New contrast ratio: 4.52:1.

The color is now visually identical but legally distinct.

**Week 8:**
Marketing wants in.

"Can we track which mode users prefer? For analytics?"

She adds a single analytics event: `theme_changed`.

Marketing: "Can we also track how long they stay in each mode? And whether they switch back? And what they were doing when they switched? And their emotional state?"

She adds 14 more events and a 200-line analytics utility that will be broken by the next adblocker update.

**Week 9:**
Backend team notices the theme preference is stored in localStorage.

Backend lead: "Shouldn't this be in the database? What if they switch devices?"

She explains it's just a visual preference.

Backend lead: "But what about their EXPERIENCE? What about CONSISTENCY?"

Three meetings later, there's now a `PATCH /api/users/preferences/theme` endpoint, a database migration, a Redis cache layer, and a webhook that notifies other services when someone changes their theme.

The webhook has zero subscribers.

**Week 10:**
Legal reviews the feature.

> "Does dark mode constitute a 'new product experience' that requires updated Terms of Service consent? Also, is storing theme preference in a cookie GDPR compliant? Please advise."

She does not advise.
She takes PTO.

**Week 11:**
She returns. Someone has added a "sepia mode" while she was gone.

Nobody asked for sepia mode.
Nobody knows who added sepia mode.
Sepia mode has 3 bugs.

She fixes the bugs.
She does not question sepia mode.

**Week 12:**
Launch day.

The feature flag is enabled.
Dark mode goes live.

The CEO sends a Slack message:

> "Love it! Quick question though — can we make the dark mode... darker? It doesn't feel premium enough. Maybe like Spotify?"

She stares at the message.
She opens LinkedIn.
She closes LinkedIn.
She types: "Sure, I'll look into it."

**Week 52:**
A new developer joins the company.

New dev: "Hey, why does the theme system have 3,000 lines of code, a database table, a Redis cache, webhooks, 14 analytics events, and a 40-page Confluence document titled 'Dark Mode: A Historical Perspective'?"

She smiles.

"You want the short answer or the long answer?"

New dev: "Short."

She points at the corner of the screen.

"You see that moon icon? It toggles between light and dark."

New dev: "That's it?"

She nods.

"That's it."

Somewhere in a parallel universe, a developer just adds `@media (prefers-color-scheme: dark)` to a stylesheet and goes home early.

She doesn't get promoted.
But she does ship.

---

*Epilogue:*

Six months later, the CEO sends another Slack message:

> "Hey! Love dark mode. Quick thought — can we add a high-contrast mode for accessibility? Should be quick right? 🎨"

She looks at her keyboard.
She looks at the window.
She looks at the keyboard again.

She starts typing an RFC titled:

> "Unified Chromatic Accessibility Framework: A Platform Approach to Visual Preference Orchestration (Phase 1 of N)"

She has learned nothing.
She has learned everything.

The system has claimed another one.
