# The One-Character Fix

A senior developer is debugging a production issue.

After 6 hours of investigation, she finds the bug:

```diff
- if (user.age == "18")
+ if (user.age === 18)
```

One character. Two, if you count the removed quotes.

She fixes it locally. Tests pass. She opens a PR.

**The PR description:**

> Fixes type coercion bug in age verification.

The diff: `+1 -1`

She expects a quick approval.

---

**Hour 1: The Review Begins**

Tech Lead: "Can you add a test for this?"

She adds a test:

```ts
it("should verify age correctly", () => {
  expect(verifyAge({ age: 18 })).toBe(true);
  expect(verifyAge({ age: "18" })).toBe(true);
});
```

Tech Lead: "Wait, should `"18"` return true?"

She pauses.

She has no idea.

She checks the product requirements document from 2019.

It says: "User must be 18 or older."

It does not specify whether "18" is a valid representation of 18.

She schedules a meeting with Product.

---

**Hour 3: The Requirements Meeting**

Product Manager: "What do you mean 'is the string 18 the same as the number 18'?"

She tries to explain JavaScript type coercion.

Product Manager: "So the computer doesn't know the difference between a number and a word?"

She: "Not... exactly."

Product Manager: "Can you just make it work like it should?"

She: "How should it work?"

Product Manager: "Like... normal?"

She opens LinkedIn.
She closes LinkedIn.

She writes in the ticket: "PM confirmed: age should work normally."

---

**Hour 5: The Philosophy Emerges**

She returns to her test.

The fundamental question: if the bug has existed for 4 years and no one noticed, was it a bug?

She checks the logs.

2.3 million users have been age-verified with `==` instead of `===`.

She checks the analytics.

All of them passed.

She checks the database.

Every single `user.age` is already stored as a number.

The bug was impossible to trigger.

The string `"18"` could never have reached this code path.

She stares at her diff.

She is fixing a bug that cannot exist.

She pushes the fix anyway.

---

**Hour 7: The Senior Senior Developer**

A Staff Engineer reviews the PR.

Comment: "I see you're using `===`. Have you considered that strict equality might break backwards compatibility?"

She: "Backwards compatibility with what?"

Staff Engineer: "With the existing behavior."

She: "The existing behavior is a bug."

Staff Engineer: "Is it though? If it's been working for 4 years..."

She mutes Slack.

---

**Hour 9: Security Weighs In**

Security Engineer: "This touches age verification. We'll need a security review."

She: "It's one character."

Security Engineer: "The Morris Worm was one function call."

She cannot argue with this.

She schedules a security review for next Thursday.

---

**Hour 14: The Security Review**

Security Engineer: "Walk me through the threat model."

She: "Someone might pass a string instead of a number."

Security Engineer: "And what would happen?"

She: "JavaScript would... coerce it."

Security Engineer: "To what?"

She: "To... the correct value."

Security Engineer: "So the bug... fixes itself?"

She: "Technically, yes."

Security Engineer: "Then what are we fixing?"

She: "The principle of the thing."

Security Engineer approves the PR.

Notes: "Low risk. Bug is theoretical. Developer is principled."

---

**Hour 16: The Architect Appears**

A Principal Engineer comments on the PR:

> "I appreciate the fix, but I'm concerned about the broader pattern. If `user.age` can be either a string or a number, we have a type consistency problem. Have you considered introducing a schema validation layer?"

She replies: "It's always a number. It's stored as a number. It's transmitted as a number. It has been a number since 2019."

Principal Engineer: "But it *could* be a string."

She: "It can't."

Principal Engineer: "Theoretically."

She adds a comment to her code:

```ts
// user.age is always a number.
// It has been a number since 2019.
// If you are reading this in the future and it is a string,
// something has gone very wrong.
// God help you.
```

The Principal Engineer approves.

---

**Hour 20: The Test Coverage Debate**

CI fails.

Test coverage has dropped from 83.4% to 83.2%.

She added a test. Coverage dropped.

She investigates.

Her test covers the fixed line, but now the old code path is no longer covered because it no longer exists.

She needs to add a test for code that she deleted.

She writes:

```ts
it("should have never existed", () => {
  // This test exists only to satisfy coverage.
  // It tests nothing.
  // It covers a ghost.
  expect(true).toBe(true);
});
```

Coverage: 83.4%.

CI passes.

---

**Hour 23: QA Has Questions**

QA Engineer: "I'm testing this fix on staging, but I can't reproduce the original bug."

She: "That's because it was impossible to trigger."

QA: "Then how do I verify the fix works?"

She: "You can't."

QA: "So how do I close the ticket?"

She: "Believe."

QA marks the ticket as "Verified (on faith)."

---

**Hour 26: The Final Approval**

The PR has:

* 4 approvals
* 47 comments
* 1 security review
* 3 meetings
* 1 philosophical crisis
* 1 test that tests nothing

She merges.

The deployment succeeds.

Monitoring shows no change.

Because nothing could have changed.

The bug was a ghost.

The fix was an exorcism.

---

**Epilogue: The Postmortem**

Manager: "Great work on the age verification bug. Can you write a postmortem?"

She writes:

> **Incident Summary:**
> A type coercion bug existed in the age verification system for 4 years.
>
> **Impact:**
> None. The bug was impossible to trigger.
>
> **Root Cause:**
> A developer in 2019 used `==` instead of `===`.
>
> **Resolution:**
> Added one character.
>
> **Lessons Learned:**
> Some bugs are only bugs in principle. We fixed this one anyway, because we are professionals, and because someone might ask "why is this `==` instead of `===`?" in a code review someday, and we didn't want to have to explain JavaScript type coercion again.
>
> **Action Items:**
> - [ ] Discuss whether theoretical bugs require postmortems (meeting scheduled, 47 attendees)
> - [ ] Add ESLint rule to prevent `==` (blocked by 3 legacy modules that rely on type coercion "intentionally")
> - [ ] Consider TypeScript (we've been considering TypeScript since 2017)

---

**Two Weeks Later**

A junior developer opens a new PR:

```diff
- if (user.role == "admin")
+ if (user.role === "admin")
```

PR description: "Quick fix, should be simple."

She sees the notification.

She smiles.

She adds herself as a reviewer.

She types:

> "Have you considered the backwards compatibility implications?"

Somewhere, a Staff Engineer nods in approval.

The cycle continues.

---

**Post-Credits Scene**

The original developer from 2019 — now a VP of Engineering at a different company — sees a LinkedIn post:

> "Just spent 26 hours getting a one-character fix through code review. The bug was impossible to trigger. AMA."

He likes the post.

He doesn't comment.

He knows.

He was the one who wrote `==` in the first place.

He did it on purpose.

Not because he didn't know about `===`.

But because he knew, one day, a future developer would find it.

And they would learn.

They would learn what he learned, many years ago:

**The code is not the hard part.**

---

*The end.*

*Or is it?*

*Check your codebase.*

*Search for `==`.*

*Someone left it there for you.*

*They're waiting to see what you'll do.*

