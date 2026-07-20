# Create mode — first run of the day

Build the full note, then create it with `create_document`: title `Daily Note - {Month D, YYYY}`, tag `daily-note`, and `library_id` for the "Daily Notes" library if the user has one. Pass `collapsed_sections` for the News and Pre-Briefings headings — they're bulky, and collapsing keeps the note scannable without losing anything.

Consult the memory note throughout: it can add, remove, or reshape sections, and holds the specifics (weather locations, news interests, Linear scope). Where the memory note conflicts with this file, the memory note wins.

## Section order and sourcing

### Notes to Claude
Starts nearly empty — a one-line invitation:
> Leave instructions here and I'll act on them next time I update this note (about every 30 minutes). Say "always" or "from now on" and I'll remember it for future days.

If yesterday's note had unprocessed instructions (lines not marked ✓), carry them here unmarked so they still get handled.

### TODO
Find the most recent prior daily note via `search_documents` with `tags: ["daily-note"]`, sorted by recency — don't rely on title search, which gets flaky once many `Daily Note -` docs exist. Look back up to a week; skip weekends/gaps gracefully. (Very first run ever: if the user has a predecessor briefing system — e.g. "Morning Brief" docs — it's fine to seed carry-over todos from the latest of those instead.) Carry over unchecked todos verbatim; drop ones that clearly expired ("prep for Tuesday's board meeting" after Tuesday); add new ones implied by today's inputs — explicit asks of the user found in email, Slack, or Linear. Use markdown task checkboxes (`- [ ]`) so the user can check items off. Link each carried or discovered item to its source.

Scanning for asks: in email, look at the last 2-3 days of the inbox and ignore automated digests/notification mail — you're after review requests and direct human asks. In Slack, search for mentions of the user and recent DMs, excluding bot traffic (`to:me` alone mostly returns bot noise).

### Weather
For each location in the memory note (if none: one line pointing the user to add locations to Daily Note Memory, and skip). Today + short outlook for the week. Flag extreme heat, storms, or anything that changes plans. Use web search.

### News
Top stories today with source links, filtered by the interest list in the memory note. Flag anything relevant to the user's work context (also in memory). A handful of items, one line each.

### Calendar
Today's events from the calendar connector, in the user's timezone (trust event offsets, not labels). Flag conflicts, events awaiting a response ("needsAction"), and time ambiguities. Include video links inline.

### Linear — Needs Attention
Scan project status updates and issues from the past 7 days scoped per the memory note (default: teams/projects the user belongs to). List only items with explicit asks of the user, blockers needing a decision/name/budget, or notable status changes — slips, launches, resolved blockers. Link each. Note which can be cleared in today's meetings.

### Pre-Briefings
For each real meeting today (skip personal items): the video/dial-in link; relevant docs from Notion, Linear, Drive, TK; prior context from Granola — last related meeting, open items, owners. End each with one line: what to walk in with or decide. If today has no meetings, one line saying so.

## Writing it

Assemble the whole body as markdown, headings as `##`. Keep the entire note comfortably readable — if a section runs long, tighten it rather than splitting it. After creating, no further action; update runs take it from here.
