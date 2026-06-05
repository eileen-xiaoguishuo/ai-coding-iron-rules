<!-- language switch -->
[简体中文](./README.md) · **English**

# Iron Rules for Coding with AI

> *Translated from the Chinese original. By **Eileen** · from 小鬼说.*

> I'm **Eileen** — a 20-year product veteran, owner of a sports event-systems company, not a trained programmer.
> With AI (Claude Code) running in parallel across many windows, I co-create products that are live in the real world.
>
> These are the **iron rules** distilled from my "AI co-creation diary" — not a tutorial, a battlefield.

Every rule here comes from a real failure: I trusted the AI's "looks right,"
went in circles, and finally nailed the lesson into a rule so I'd never repeat it.
If you also code with AI, I hope these save you the detours I took.

> 💬 Got your own war stories or disagree with one? Come to [Discussions](../../discussions).

---

## Contents

- [1. Mindset: how to "trust" AI](#1-mindset-how-to-trust-ai)
- [2. How to direct the AI](#2-how-to-direct-the-ai)
- [3. Debugging: where to look first](#3-debugging-where-to-look-first)
- [4. Shipping: before you say "it's done"](#4-shipping-before-you-say-its-done)
- [5. Data & security](#5-data--security)

---

## 1. Mindset: how to "trust" AI

### 1. Speak with evidence, don't speculate
**The trap**: The AI said "should be fixed now" / "probably a cache issue." I believed it, ran it, everything broke — round and round we went.
**The rule**: Back every claim with evidence — run the command, read the log, open the file, actually reproduce it. **Verify before** you say "done." No bare claims like "I think / probably / should be / looks like."
**Why**: AI is brilliant at answers that *sound* right. Its confidence is not fact. This is the root of every other rule.

### 2. A right result ≠ a right process — make it show its work
**The trap**: The AI handed me a result that looked fine. I checked one spot, it passed, I relaxed — and a whole chain after it was wrong. It had been right by accident.
**The rule**: When the AI gives a result, make it state three things up front: **what it used / what it based that on / how it verified** — especially when there were multiple options (which field, which file, which endpoint).
**Why**: "Accidentally correct" hides the wrong implementation until it's bitten you in ten places. Only a transparent process is debuggable.

### 3. Your domain intuition beats the AI's "sounds reasonable"
**The trap**: The AI reasoned its way to a conclusion from the data. My gut said "impossible," but a pile of "maybe / could be" talked me out of it. My gut was right.
**The rule**: When your knowledge of the domain says "something's off," make the AI **stop speculating immediately** — don't let it keep hunting for reasons it "might" be true. Go back to facts.
**Why**: The AI sees code and data patterns; you see the real world. On facts about your business, your memory > its "plausible guess."

---

## 2. How to direct the AI

### 4. The moment an instruction is vague, ask for options — don't let it guess
**The trap**: I said "optimize this" / "simplify it" / "delete that." The AI picked a direction on its own, finished, and it wasn't what I wanted. Wasted work.
**The rule**: For fuzzy instructions (take down / tweak / delete / align / clean up…), make the AI **stop and list 2–4 concrete options** for you to pick before it touches anything.
**Why**: Given ambiguity, the AI will run with whatever it thinks is most likely — and you pay for the wrong guess. Aligning first is the fastest path.

### 5. Beware the AI's bias toward automation — check the real frequency first
**The trap**: For a task I run a few times a *week*, the AI eagerly set me up a job that runs every *hour*. Pure waste.
**The rule**: When it defaults to "automate it / add a cache / add an abstraction," ask: **what's the real frequency / scale?** For low-frequency work, a manual button is plenty.
**Why**: AI is trained to favor solutions that *look* complete, so it over-engineers. Good trade-offs follow real need, not whichever option is "more thorough."

### 6. Want the truth? Make it play red team
**The trap**: I asked "is this plan any good?" and it almost always said "Great!" — then I found the gaping holes myself.
**The rule**: When you want a review, demand it explicitly: "poke holes, don't agree with me, give me at least 3 concrete objections, name the weakest link." In a discussion phase, say "don't act yet."
**Why**: By default the AI leans toward pleasing you and pushing the task forward. If you don't ask for an adversary, you won't hear the real problems.

---

## 3. Debugging: where to look first

### 7. "My change didn't take effect" — first confirm you're testing the new version
**The trap**: I changed the code, it didn't take effect, I suspected the code for ages — turns out I was testing a stale build / an old process / the wrong environment.
**The rule**: When "the behavior didn't change," **the first step isn't reading code — it's confirming you're testing the new version**: Is the build artifact's timestamp fresh? Is it the new process? Which environment are you hitting?
**Why**: More than half of "the change didn't work" illusions are you validating against something stale. Rule that out first and save endless dead-end debugging.

### 8. Don't bury yourself in code — check the runtime first
**The trap**: UI didn't render / data was wrong / an API errored, and I dove straight into reading the code logic for ages.
**The rule**: For these symptoms, **check the runtime state first** — logs (reload timestamps), the actual data, the browser Network panel, curl the endpoint directly.
**Why**: The symptom usually isn't in the code you assume it's in. The runtime truth is ten times faster than reading code cold.

### 9. "Parsed 0 records" — first look at what the raw data actually looks like
**The trap**: An import/parse reported "0 records, nothing matched." I suspected the business logic — but the raw data wasn't shaped the way I assumed (it was encoded, the real header was on a different row, the column names were variants).
**The rule**: When a parse comes back empty, **dump one raw record and look at it first**: Is it encoded? Which row is the real header? Are the field names variants? Never let `except: continue` silently swallow parse errors.
**Why**: When the source is dirty, suspecting the logic is chasing the wrong thing. See the input clearly before you discuss processing.

### 10. Don't trust surface signals (status codes / mirrors / caches)
**The trap**: I used a status code to decide whether an endpoint existed — but the auth layer intercepted before routing, returning the same code whether or not it existed. It fooled me.
**The rule**: Check the **authoritative source that's actually in effect**, not side signals that go stale or get rewritten by middle layers (status codes, local mirrors, caches, what the UI shows).
**Why**: Surface signals often disagree with the underlying truth. When you're stuck, go back to the authoritative data.

---

## 4. Shipping: before you say "it's done"

### 11. Before you say "it's done," verify it yourself once
**The trap**: The AI (and I) said "ready to test," and the user clicked it into a white screen / an error. I'd turned them into my test machine.
**The rule**: Before handing off, **walk the real flow yourself**. For front-end especially: actually open it, no blank screen, the key path is clickable. "Tests pass" at the API layer ≠ pass from the user's point of view.
**Why**: An unverified "done" is gambling with someone else's time. Verifying once costs far less than making them hit the bug and come back.

### 12. "Works on mine" ≠ "works on theirs"
**The trap**: A user's screenshot showed a problem (a seam / misalignment / wrong display); I couldn't reproduce it locally no matter what. We talked past each other.
**The rule**: When you can't reproduce, **match the user's real environment** — their screen zoom level, device, browser. For visual bugs, zoom into the pixels with a tool; don't rely on "looks fine to me."
**Why**: Zoom, DPI, and device differences make the same code render differently on each side. The user's view is the source of truth.

### 13. The default for a dangerous action must be "do nothing"
**The trap**: A merge / delete / bulk-edit tool defaulted to "execute," and a slip of the hand caused a mistake.
**The rule**: For dangerous actions — delete, merge, dedup, bulk-edit — **the default must be no-op**; require an explicit opt-in each time before it runs.
**Why**: An accidental delete should require real effort to trigger. Making destructive actions opt-in is insurance for your future self.

---

## 5. Data & security

### 14. Clean external data before it enters your store — dirty source, dirty everything
**The trap**: I loaded an external table (Excel / scraped data) straight into the database; the dirty data contaminated a whole swath, and once it was in, fixing it was whack-a-mole.
**The rule**: **Before** treating external data as an authoritative source, produce a "health report" and clean it (merged cells, broken formats, typos, absurd values). Make ingestion idempotent — re-runs only correct, never duplicate.
**Why**: A dirty source means everything downstream is dirty, and you can't trace which cell caused it. Stopping it at the door is far cheaper than cleaning the house afterward.

### 15. Comparing strings across sources? Normalize first (whitespace / case)
**The trap**: Two datasets matched by name wouldn't line up at all — they differed by a single space / letter case, judged "no match" by strict equality, and a whole batch was dropped.
**The rule**: Before comparing strings across systems, **always normalize**: strip whitespace, unify case, then compare. Don't use bare `==`.
**Why**: Stray whitespace and case differences in external data are extremely common. When a comparison stalls, nine times out of ten it's missing normalization.

### 16. Secrets & keys: never a default value, never in the repo
**The trap**: A key was read as "use a default if not set"; that default secret got pushed to the repo with the code and sat there for over a month unnoticed.
**The rule**: Keys / passwords / tokens must **fail loud if unset** — never a fallback default. Before making any code public, scan it for embedded credentials.
**Why**: Defaults quietly become production config; a key in a public repo may be scraped even after you delete it. **If you're about to share code on GitHub, burn this one into your memory.**

---

## About

These rules come from real-project failures, **desensitized** — all business specifics removed, only the universal parts kept.
Licensed under [CC BY 4.0](./LICENSE): reuse, quote, and remix freely — **just credit "Eileen · 小鬼说."**

If one helped, or you disagree / have your own iron rule, come to [Discussions](../../discussions).
