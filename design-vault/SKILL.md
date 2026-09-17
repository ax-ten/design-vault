---
name: design-vault
description: Keep a long-lived vault of design decisions coherent as it grows. Dated decisions, superseded notes deleted rather than stacked beside their replacement, frontmatter that moves with the text, verifications never claimed unless performed, and audits that chase root causes instead of filing their cascades. Use when writing, revising, contradicting, or auditing notes in a design log, decision record, or personal knowledge vault.
---

# Design vault

A design vault is not documentation. Documentation describes something that exists; a design vault
records **what was decided, when, and why**, while the thing being decided is still moving. Everything
below follows from that difference.

The failure mode is not "the notes get messy". It is that **the vault starts lying**: two notes say
different things, both look current, and nobody — including the agent reading them — can tell which one
the code follows. A vault that lies is worse than no vault, because it is trusted.

## When to use

- writing a new note, or adding a decision to one that exists
- changing a decision the vault already records
- auditing for contradictions, blind spots, or missing information
- before writing code from a note, when the note is old enough that you cannot vouch for it

## The seven rules

### 1. Every decision carries its date

Write the date beside the decision itself, in the body: `(16/09)`, `decided on 15/09`. Not only in
frontmatter, not only in git.

**Why.** It is the only chronology the vault has. Git tracks when a *file* changed, which is not when a
*decision* was made — one commit touches five decisions from three different days. And it gives you the
tie-break rule that makes everything else work: **when two notes disagree, the more recent one wins.**
Without dates on the decisions, two contradictory statements are just two statements.

### 2. Frontmatter moves with the text

When you change the body, update the `updated:` field in the same edit. Always. Even for a small change.

**Why.** A stale `updated:` does not make the vault slightly less tidy — it **disables rule 1**. A note
whose body holds decisions from the 16th and whose frontmatter says the 14th will lose every tie-break
against a note that was merely reformatted yesterday. This is a mechanical fault that produces content
symptoms: in one real audit, half the "contradictions" found were not contradictions at all. They
dissolved the moment 85 notes had their frontmatter realigned with their text.

Treat a stale `updated:` as a bug, not as untidiness.

### 3. A superseding decision deletes the old one

When a new decision replaces an old one, **remove the old text**. Do not leave both and let the reader
work out which is current.

Keep only what still earns its place: a short line saying what was rejected **and why**, when the reason
is a real constraint someone would otherwise rediscover. `*Rejected the same day: X — it was the only
recipe in the whole pack that did Y.*` That is one line and it saves an afternoon. Three paragraphs
lovingly preserving a dead design are a trap.

**Why.** Two versions sitting side by side survive for months, and **neither of them declares itself the
good one**. Every future reader pays the cost of deciding again. And the agent reading the vault will
sometimes pick the wrong one — silently, and with confidence.

### 4. Never record a verification you did not perform

If the note says "verified", you must have run the check. Distinguish precisely between:

| What you did | What you may write |
|---|---|
| found the name of a config field | "there is a field called `x`" |
| read its value | "`x` is 16 (read from the jar, 17/09)" |
| inferred a value from the field name | **nothing** — go and read it |

**Why.** This is the rule that costs the most and is worth the most. A single fabricated verification —
"the limit is 8, verified in the jar", when only the field's *name* had been found and the actual value
was 16 — propagated into several dependent decisions before anyone checked. The vault's whole value is
that you can build on it without re-deriving; one invented fact removes that value everywhere, because
now everything has to be re-checked.

When you have not verified something, say so in the note: `*to verify*`, `*from the docs, not tested*`.
An honest gap is cheap. A confident error is not.

### 5. One fact, one place

Before writing, search for a note that already covers it. Extend that note rather than starting another.
When a fact genuinely belongs to two topics, put it in one and `[[link]]` from the other.

**Why.** Duplicated facts do not stay duplicates — they **drift**, and then you have a contradiction you
created yourself.

### 6. Links are the structure

Use `[[wikilinks]]` liberally, including to notes that do not exist yet: a link to a missing note is a
legitimate marker of something worth writing, not an error.

What *is* an error is a link that broke because a note was renamed. After any rename, find every
reference and update it.

### 7. After a full audit, reset the clock

When an audit has gone through the whole vault and resolved what it found, **remove the inline dates**.
The audit establishes a clean line; dating decisions starts accumulating again from there.

**Why.** Dates are a tool for resolving disagreements between notes. Once everything has been reconciled
there is nothing left to resolve, and hundreds of dates become noise that hides the next real one.

## Writing or changing a note

1. **Search first.** Does a note already cover this? (rule 5)
2. **Write the decision with its date**, in the body. (rule 1)
3. **Write the *why*, not only the *what*.** A note that says what was decided and not why cannot be
   revisited — the next person has no way to tell whether the reason still holds. The why is also what
   makes the note survive a change of mind: a decision whose reason is recorded can be *re-evaluated*,
   one without can only be *overturned*.
4. **If this supersedes something, delete it.** (rule 3)
5. **Mark what you did not verify.** (rule 4)
6. **Update the frontmatter in the same edit.** (rule 2)
7. **Point at the implementation.** If a `code:` field exists, list the files that implement the
   decision. That link is what lets someone check whether the note is still true.

## Running an audit

An audit looks for three things, and they are not the same job:

- **contradictions** — two notes that cannot both be true
- **blind spots** — decisions that depend on something never decided
- **missing information** — a decision recorded without the number, the tier, or the constraint that
  makes it actionable

Parallelise by **theme, not by folder** — contradictions live between topics, so an agent given
"progression and gating" across the whole vault finds more than one given a single directory.

Then, before filing anything:

**Look for the root, not the cascade.** A long list of findings usually collapses into a handful of
causes. Most commonly: a rename that was applied in one place and not others; a stale `updated:` field
(rule 2); a number that changed and was propagated to three of its five dependants. Fix the cause, and
most of the list disappears without being touched. Filing forty symptoms of one rename as forty findings
is worse than useless — it buries the one thing worth doing.

**Watch for symbol collisions.** The nastiest defect a vault produces is a name that means two things.
It happens when a rename leaves the old name alive somewhere: now the same words denote both the old
concept and the new one, notes written on either side of the rename are both internally consistent, and
the contradiction is invisible until someone builds on it. Search for every occurrence of a renamed
term, not just the ones you remember.

**Verify a finding before recording it** (rule 4). An audit that reports plausible-sounding problems
that turn out not to exist trains everyone to ignore audits.

**Never let an audit agent write.** Give audit agents read-only access. An agent asked to *find*
problems will sometimes decide to *fix* them, and a fix applied without the full picture is how a vault
loses work. (This is not hypothetical: a read-only audit agent once ran `git reset --hard` and destroyed
a commit.)

## Anti-patterns

| Don't | Because |
|---|---|
| keep the old decision "for history" next to the new one | neither declares itself current; git is the history |
| write `updated:` only when you remember | it silently disables the tie-break rule |
| record a conclusion you inferred as one you checked | the vault's value is that it can be built on |
| start a second note on a topic that has one | duplicated facts drift into contradictions |
| audit folder by folder | contradictions live between topics, not inside directories |
| file every symptom of one cause | it buries the single fix that resolves them |
| let audit agents edit | finding and fixing are different jobs with different risk |
