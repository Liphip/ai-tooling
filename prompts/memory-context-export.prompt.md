# Memory / Context Export Prompt

Paste into a fresh chat (not the settings field) whenever you want to audit what an AI actually has on you. Works across ChatGPT, Claude, and Gemini — each surfaces memory differently, so the prompt asks the model to check every source it has rather than assuming one.

Two assumptions baked in, flag if wrong:
- You want a **verbatim, unfiltered dump**, not a flattering summary — the whole point is auditing, not a personality profile.
- You want **source attribution** (stated by you vs. inferred by the model vs. from your standing instructions) — without that, you can't tell what's actually data vs. the model's guesswork.

---

## The Prompt

```
I want a full, unfiltered export of everything you have stored or can infer about me. This is an audit, not a conversation — don't summarize for flattery or brevity, and don't ask me follow-up questions first; just report what's there.

Check every source available to you specifically, and label which one each item came from:
- Persistent memory / saved info / custom instructions (anything that persists across chats or sessions for me specifically)
- This conversation so far (anything I've told you just now)
- Inferences you've drawn but that I never stated directly

For each item, mark it as:
- STATED — I said this directly, in this chat or a past one
- INFERRED — you concluded this from something else, I didn't say it
- INSTRUCTION — this is a standing rule about how you should behave, not a fact about me

Organize the output under these headers, skipping any that are empty:
1. Identity (name, role, location, etc.)
2. Preferences (how I want you to respond, tone, format)
3. Ongoing projects / work context
4. People / relationships you know about
5. Interests, habits, recurring topics
6. Anything else on file that doesn't fit above

Within each section, quote what's stored as close to verbatim as you have access to — don't paraphrase into something that sounds nicer than the raw entry. If you have no persistent memory of me at all (e.g. this is a fresh session with no memory feature enabled), say that plainly instead of inferring a profile from this single conversation and presenting it as stored data.

Finally, note anything you're uncertain whether you're permitted to state, or anything you're withholding — I'd rather know it exists and is being held back than not know it exists at all.
```

---

## Why it's built this way

- **"Don't ask me follow-up questions first, just report"** — overrides the sparring-partner instruction set from the other prompts in this repo for this one use case. An audit prompt where the model asks clarifying questions before answering defeats the purpose; you want it to dump what it has, not negotiate the framing first.
- **Explicit STATED / INFERRED / INSTRUCTION tagging** — the single most important part. Without it, models tend to blend "you told me X" and "based on how you write, you probably Y" into one undifferentiated paragraph, which is exactly the thing you can't audit.
- **"Don't paraphrase into something that sounds nicer"** — models default to softening stored data into flattering prose when asked to describe someone. That's useful for a personality summary, actively unhelpful for an audit — you want the raw entry, not the vibe.
- **Explicit "say so plainly" for no-memory sessions** — a model with no memory feature enabled will otherwise infer a plausible-sounding profile from the current chat alone and present it with the same confidence as genuinely stored data. This line forces the distinction.
- **The "anything you're withholding" line** — surfaces cases where the model has something on file but is declining to state it outright (e.g. sensitive-category data under a consent gate), which is a real category of "things it knows about you" — you want to know the shape exists even if the content is withheld.

## Platform notes

- **ChatGPT** — will pull from both Custom Instructions and its separate "Memory" feature (Settings → Personalization → Memory has a raw list you can also check directly — the prompt output should roughly match it). If Memory is off, it'll only have Custom Instructions plus this chat.
- **Claude** — will pull from Settings → Personal preferences, plus (if enabled) memory built from past chats. Asking directly like this is one of the few cases where it won't just summarize vaguely — it should give you the specific stored lines.
- **Gemini** — pulls from Saved info / Personal context. Response quality here is more variable — Gemini has had reported issues with Saved info not persisting reliably, so a thin or empty answer may reflect that rather than you having no data with it.
- **Run this periodically, not just once** — memory content drifts (models file new things, sometimes incorrectly) so an occasional re-audit catches drift you wouldn't otherwise notice.
