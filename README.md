In a huge corporation, a senior frontend developer gets a ticket:

> "Change the text on the button from 'Submit' to 'Send'. 2 story points."

He smiles.
"Finally, something simple."

Sprint 1:
He opens the codebase and finds five different button components:

* `Button`
* `PrimaryButton`
* `PrimaryButtonV2`
* `ButtonDeprecated`
* `SmartButton` (which is neither smart nor a button, just a div with feelings)

So obviously he does the only reasonable thing: he starts an RFC titled:

> "Global Strategic Button Unification Initiative (Phase 1 of N)"

Sprint 2:
He creates a new package in the monorepo:

`@company/frontend-interactive-click-surface`

Inside it, he adds a new component:

```ts
type ButtonVariant = "primary" | "secondary" | "tertiary" | "ghost" | "sendButLikeInASubtleWay";

type ButtonLabel<T extends string> = T & { __brand: "ButtonLabel" };

interface ButtonProps<TLabel extends string> {
  label: ButtonLabel<TLabel>;
  onClick: () => Promise<void>;
  trackingId?: `${Uppercase<TLabel>}_CLICKED`;
}
```

He has not yet changed the word "Submit".
But he has created a type that can generate a GDPR breach just from the length of the error message.

Sprint 3:
Design wants in. They schedule a "Button Alignment Workshop". 17 people attend.
Outcome: same button, new Figma file.

Compliance wants in. They ask if the button text "Send" is legally distinct from "Submit" in Switzerland.
Outcome: new JIRA ticket.

Security wants in. They ask if clicking "Send" could be interpreted as consent to something.
Outcome: another JIRA ticket and a Confluence page nobody will ever read.

Still no one has changed the actual text.

Sprint 4:
He introduces a hook:

```ts
function useButtonController<TLabel extends string>(props: ButtonProps<TLabel>) {
  // currently just returns props
  return props;
}
```

Now the button does exactly what it did before, but through a hook, which makes it "future proof".
PR: 143 files changed, 0 user-facing differences, 58 snapshot updates.

Code review comments:

* "Can we make this generic?" (it already is)
* "Can we extract this to a shared package?" (it already is)
* "Nit: typo in the JSDoc." (there is no JSDoc)

Sprint 5:
Product Manager finally asks:
"So... did we change 'Submit' to 'Send'?"

Developer, slightly offended:
"Not yet. First I had to build the **infrastructure** for changing it."

He explains the architecture diagram he drew in Excalidraw:

* Top left: "User"
* Top right: "Button"
* Between them: 9 layers of abstraction labeled "Platform"

Sprint 6:
User research comes back:

* 50% of users prefer "Submit"
* 50% prefer "Send"
* 30% thought the button was a banner ad
* Nobody knows how they got into this application in the first place

So they decide to A/B test it.

He implements:

```tsx
const label = useExperiment("send_vs_submit") === "treatment"
  ? "Send"
  : "Submit";
```

But of course he cannot just write `"Send"` and `"Submit"`. That would be insane.

So he adds a `ButtonCopy` enum in a shared `i18n` package, adds translation keys for 27 locales, creates a fallback for Klingon, and opens a localization ticket for "Pirate English" just in case marketing ever wants it.

Sprint 7:
Everything is ready.

* New package
* New hook
* New experiment
* New translations
* 3 RFCs
* 2 architecture diagrams
* 1 Loom video no one watched past minute 2

He finally opens the actual PR that changes the visible text.

**Diff in the legacy form component:**

```diff
- <PrimaryButton label="Submit" />
+ <InteractiveClickSurface
+   label={resolvedLabel}
+   onClick={handleSubmitAsync}
/>
```

QA tests it in staging:

* "Looks the same."
* "Works the same."
* "Why did we touch 200 files again?"

Release day:
He deploys behind a feature flag.
They agree to turn it on gradually, "to monitor impact".

2 months later, the flag is still off.

End of year performance review:

Manager:
"So, what did you ship this year?"

Developer:
"Technically, nothing visible. But I created a fully type-safe, experiment-ready, localization-aware, analytics-compliant, design-system-aligned button platform that will let **anyone** change 'Submit' to 'Send' in under 5 minutes."

Manager types for a moment, then reads his summary:

> "Strengths:
>
> * Great collaborator across teams
> * Strong focus on long-term platforms
>
> Development areas:
>
> * Needs to increase direct user impact
>
> Rating: Meets expectations."

He leaves the meeting, opens JIRA, and finds a new ticket assigned to him:

> "Story: Change text on second button from 'Cancel' to 'Back'.
> Estimate: 2 story points."

He cracks his knuckles, opens a new RFC, and whispers to himself:

> "This time, we do it right. We start with a monorepo-wide `@company/navigation-intent-surface`..."

Somewhere in the backend, a junior engineer changes `"Submit"` to `"Send"` in a single line and deploys to production.

No RFC.
No feature flag.
No TypeScript.

It just works.

He fails his security review.

