# Open-loop item schema

Every item the scanner produces — and everything `board/index.html` renders — is a JSON object with these fields.

## Fields

| Field | Type | Required | Description |
|---|---|---|---|
| `id` | string | yes | Stable unique id, e.g. `loop-2026-09-28-001`. The scanner must reuse the same id for the same open loop across scans so items don't duplicate. |
| `title` | string | yes | Short, actionable title. Verb-first: "Reply to…", "Finish…", "Book…". |
| `priority` | string | yes | One of `urgent`, `high`, `normal`, `low`. See `prompts/prioritization.md`. |
| `status` | string | yes | One of `open`, `waiting` (blocked on someone/something else), `snoozed` (deliberately deferred), `shipped` (done — confirmed by the user). The board moves `shipped` items to the shipped log instead of hiding them. |
| `source_chat` | string | yes | Human-readable name of the conversation the item came from, e.g. `"Job search"`. Shown on the card so the user can jump back to context. |
| `source_fingerprint` | string | yes | Machine key for dedup, e.g. the chat's id + a topic hash. Same loop seen in a later scan must produce the same fingerprint. |
| `note` | string | no | One or two sentences of context: what's blocking, what's next, why it matters. |
| `detail` | string | no | Longer context for the Focus view: background, what was tried, what "done" looks like. |
| `why_urgent` | string | no | One sentence: why the scanner ranked it at this urgency. Shown in the Focus view so the user can agree or override. |
| `due` | string | no | `YYYY-MM-DD` deadline, when one exists. |
| `snooze_until` | string | no | `YYYY-MM-DD` comeback date. Set when the user snoozes; the scan should not surface the item before this date. |
| `shipped_at` | string | no | `YYYY-MM-DD` date the user confirmed shipped. |
| `shipped_note` | string | no | What the user actually did, in their words. Proof, not a checkbox. |

## Example

```json
{
  "id": "loop-2026-09-28-001",
  "title": "Reply to landlord about lease renewal",
  "priority": "high",
  "status": "waiting",
  "source_chat": "Personal",
  "source_fingerprint": "chat-9f31::lease-renewal",
  "note": "Waiting on their answer about the parking spot. Nudge if no reply by Thursday.",
  "due": "2026-10-03"
}
```

## Rules for producers

1. **Never invent items.** Every item traces to a real, observed unfinished thread.
2. **Never duplicate.** Match `source_fingerprint` against existing items first; update the existing item instead of creating a new one.
3. **`shipped` is sacred.** Only mark shipped when the user confirms they acted. "Produced" is not "shipped."
4. **Keep titles actionable.** If you can't phrase it as a next action, it's not an open loop — it's a note. Leave notes out.
