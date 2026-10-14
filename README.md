# Voice Bot Skills

A growing collection of custom AI voice bot skills.

## Skills

### `vb-resuing_routing.skill`

Adds **"resume from where the last call stopped"** to any node-based voice bot (collections, surveys, disclosures, onboarding). When a call drops midway, the next call to that customer picks up at the first unanswered node instead of restarting. It works entirely at the prompt level, with no platform code changes.

**How it works:** one integer variable, `resume_from_node`, and two halves that must both be built:

- **Router (reader):** a `NODE 0` in the Conversation Flow that runs right after identity confirmation. It reads `resume_from_node` and either starts fresh or plays a short recap and jumps to the saved node.
- **Computation (writer):** logic in the post-call analysis bucket (Bucket 6) that works out `last_completed_node` and writes `resume_from_node` for the next call.

Because `0` means a fresh call, **one bot handles both first calls and resumes**. With `0`, it behaves exactly like the original bot.

The skill also covers auditing and maintaining existing routing bots: checking lookup anchors, adding carve-outs for "ask X first" rules, staying under Bucket 6 word caps, and keeping the routing bot in sync with its base bot.

**Prerequisites**

- **Node-based bot:** the bot must have a clear, numbered node sequence, with an identity-confirmation step before any content.
- **Post-call analysis bucket (Bucket 6):** this is where `resume_from_node` gets computed.
- **Platform variable `resume_from_node`:**
  - The name must be exactly this. Variants like `resume_node` or `resumeFrom` are not supported.
  - It accepts **integers only**:
    - `0` means a fresh call.
    - `1` to `MAX` means resume at that node.
    - `MAX` means the call was completed.
  - Blank, null, non-numeric, or out-of-range values are treated as `0`, so the bot starts fresh. Values like `07`, `+7`, and `7.0` are read as `7`.
  - Register the variable with an **empty default**, so that "not passed" arrives as blank instead of blocking the call.
- **Platform/dialler support** (the prompt can't do these):
  - Store Bucket 6's `resume_from_node` output and pass it into the next call to that customer.
  - Only overwrite the stored value when `identity_confirmed = YES`. A failed resume call, such as voicemail, a third party, or no answer, outputs `0`, and saving that would erase the customer's real progress.
  - Treat `resume_from_node = MAX` as "completed, don't re-call."
  - Route all resume calls to the routing bot, not the base bot.
- **Reporting:** each call's analysis contains only that call's fields, so the reporting layer must merge the call chain into one record per customer.


### `language-telugu.skill`
— Enhances Telugu voice bots with natural language expressions and conversational phrasing for smoother, more engaging interactions with Telugu-speaking users.

### `language-tamil.skill`
Enhances Tamil voice bots with natural language expressions and conversational phrasing for smoother, more engaging interactions with Tamil-speaking users

### `language-kannada.skill`
 Enhances Kannada voice bots with natural language expressions and conversational phrasing for smoother, more engaging interactions with Kannada-speaking users.
