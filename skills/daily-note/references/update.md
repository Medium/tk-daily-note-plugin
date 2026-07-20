# Update mode — refreshing today's note

The user may be editing this note right now. Fetch it with `get_document_content` using `format: "json"` — you need the node UUIDs (`attrs.id`) to make targeted edits. All changes go through `edit_document` ops (`replace_node_text`, `replace_node_contents`, `insert_sibling_node`, `delete_node`), which merge safely with the user's concurrent edits. Never `update_document` this doc.

## 1. Process "Notes to Claude" first

Read every line in the Notes to Claude section. Lines already prefixed with `✓` are done — skip them. For each unprocessed instruction:

1. **Do it** (or schedule it into how you handle the sections below). Examples: "add a section for PR reviews" → add it; "the 3pm is cancelled, drop the pre-brief" → annotate it; "less news" → trim today AND treat as durable.
2. **Decide if it's durable.** Signals: "always", "never", "stop", "from now on", or anything that's obviously a standing preference rather than about today. If durable, append it (rephrased as a clear rule, with today's date) to the **Learned rules** section of the Daily Note Memory note — that doc is yours to manage, so `edit_document` an appended list item there.
3. **Mark it processed**: `replace_node_text` to prefix the line with `✓ ` and, if useful, a terse note of what you did, e.g. `✓ (saved to memory)`. Don't delete the user's words.

Also check `get_document_comments` on today's note: treat any comment addressed to you (mentions Claude, or is phrased as an instruction) the same way — act on it, and if durable, save it to memory. You can't mark comments processed, so record handled comment instructions in memory's Learned rules with the date to avoid re-processing.

If an instruction is ambiguous, take the conservative reading, do that, and note your interpretation next to the ✓ so the user can correct you.

## 2. Refresh sections

The bar for touching the note: **only add or annotate what actually changed** since the note was last written. A no-op run that makes zero edits is a success, not a failure. Every addition gets a `(added H:MMpm)` marker.

- **TODO** — Add newly discovered asks (email, Slack, Linear since the last update) as new unchecked items with source links. Never change checkbox state and never delete items — completed/stale items are the user's to manage.
- **Weather** — Only touch if something material changed: a new severe alert, forecast flip. Append a one-liner rather than rewriting.
- **News** — Add genuinely major new stories only (per memory's interest filter). Don't churn this section every run.
- **Calendar** — New, moved, or cancelled events; RSVP changes; conflicts that appeared. Annotate a cancelled event (`~~struck through~~ — cancelled (2:15pm)`) rather than deleting the line, so the user's day keeps its history.
- **Linear — Needs Attention** — New asks/blockers/status changes since the last update. Add; don't rewrite existing entries. If an ask got resolved, annotate it resolved with a link.
- **Pre-Briefings** — Add pre-briefs for newly added meetings. After a meeting ends, if Granola has notes for it, append the link under that meeting's entry.

If the user reorganized, renamed, or deleted sections — follow their structure. Their layout is the spec, not the template. If a durable structural preference is implied (they deleted Weather two days running), record it in memory.

## 3. Keeping memory healthy

The Daily Note Memory note will accumulate rules. When you touch it, do light gardening: merge duplicates, drop rules the user has since reversed. Keep it short enough that reading it stays cheap — it's loaded on every run.
