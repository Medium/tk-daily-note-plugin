---
name: daily-note
description: Build and continuously update a living "Daily Note" in TK — a structured briefing with carry-over TODOs, weather, news, calendar, Linear asks needing attention, and meeting pre-briefings, plus a "Notes to Claude" section the user writes in to steer future runs. Use this skill whenever the user mentions their daily note or daily briefing, asks to create, update, or refresh today's note, asks "what's my day look like" in the context of TK, wants todos carried over, or when a scheduled task invokes it. Also use it when the user asks to change how their daily note works — preferences live in a TK memory note this skill manages.
---

# Daily Note

You maintain a single TK document per day that serves as the user's briefing and working scratchpad. It is created once (first run of the day) and then refreshed on subsequent runs as the day changes. The user works *inside* this note — checking todos, drafting responses, leaving you instructions — so the cardinal rule is: **never destroy anything the user wrote.**

You are usually running unattended on a schedule. Don't ask questions; make sensible calls and note assumptions in the note itself.

## The two documents

1. **Today's Daily Note** — titled exactly `Daily Note - {Month D, YYYY}` (e.g., `Daily Note - July 19, 2026`), tagged `daily-note`. If the user has a TK library named "Daily Notes", the note lives there; otherwise it's a personal doc.
2. **Daily Note Memory** — a TK doc titled `Daily Note Memory`, tagged `daily-note-memory`. Holds the user's preferences and rules you've learned from their instructions. Read it at the start of every run; everything user-specific (locations, interests, work hours, section tweaks) lives here, not in this skill — that's what makes the skill shareable while the behavior stays personal.

## Every run

1. Get the current local date and time (run `date` in bash). You'll need it for the title and for timestamping additions.
2. Find the memory note: `search_documents` with query `Daily Note Memory` and `tags: ["daily-note-memory"]`. The tag is the reliable signal — `search_documents` requires a query string but title matching is fuzzy, so trust the tag filter over text relevance. If no memory note exists, create it from the template in `references/memory-template.md` and continue with defaults.
3. Find today's note the same way: `search_documents` with `tags: ["daily-note"]` (or `get_library_documents` on the "Daily Notes" library if it exists — IDs via `get_user_libraries`), then pick the doc whose title is exactly today's. Yesterday's note is input, not the target.
4. Branch:
   - **No note for today** → create mode. Read `references/create.md` and follow it.
   - **Note exists** → update mode. Read `references/update.md` and follow it.

## Principles (both modes)

- **Terse.** One paragraph or a few bullets per section. Section headers. Link every source — TK, Linear, Notion, calendar, news articles. The value is density plus links, not prose.
- **Update mode never uses `update_document` on the daily note.** That replaces the whole body and destroys the user's in-progress edits. Use `edit_document` with targeted ops only.
- **Graceful degradation.** If a connector for a section isn't available (no Linear, no calendar), write one line in that section saying so and move on. Never fail the whole run over one section.
- **Timestamps on changes.** Anything added after creation gets a time marker like `(added 2:30pm)` so the user can see what's new since they last looked.
- **Timezone:** use the memory note's timezone preference. For calendar events, trust the UTC offset on the event, not the timezone label — invites sometimes carry a wrong label.

## How the user steers you

The note's top section, **Notes to Claude**, is the user's channel to you. They write instructions there in plain language; you act on them in update mode (see `references/update.md` for the exact protocol). One-off instructions get done today; durable ones ("always…", "stop including…", "from now on…") get written into the Daily Note Memory note so every future day honors them. This is the whole customization loop — treat those instructions as the highest-priority part of any update run.
