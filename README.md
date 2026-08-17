# Daily Note for TK

A Claude plugin that maintains a **living Daily Note** in [TK](https://tk.xyz): one document per day that Claude creates in the morning and keeps updating in place as your day changes — while you work inside it.

Each `Daily Note - {Month D, YYYY}` doc contains:

- **Notes to Claude** — write instructions here in plain language; the next update run acts on them. One-offs get done and checked off (✓); durable ones ("always…", "from now on…") are saved to a **Daily Note Memory** doc in TK that steers every future day.
- **TODO** — unchecked items carry over from yesterday; new asks are discovered from email, Slack, and Linear, each linked to its source.
- **Weather / News / Calendar / Linear — Needs Attention / Pre-Briefings** — terse, one paragraph or a few bullets each, every item linked. News and Pre-Briefings start collapsed.

The skill is *self-healing*: any run when today's note doesn't exist creates it; any run when it does exist refreshes it. Updates use TK's surgical `edit_document` operations, which merge safely with your live edits — Claude never rewrites the whole note, never touches your checkbox state, and never deletes what you wrote.

All personalization (timezone, weather locations, news interests, Linear scope, learned rules) lives in your private Daily Note Memory doc — not in the skill. The skill stays generic; your copy behaves like you.

## Prerequisites

- A **TK** account with the **TK MCP server** connected in Claude Cowork (or Claude Code).
- Optional but recommended connectors — each missing one just blanks its section gracefully: Google Calendar, Linear, Gmail, Slack, Granola.

## Install

**Claude Cowork:** open **Customize** in the left sidebar → **Plugins** tab → **+** in the Personal plugins section → **Add marketplace** → choose **Add from a repository** (*not* "Browse Anthropic sources" — that searches Anthropic's curated catalog only and won't find this repo) → paste `https://github.com/Medium/tk-daily-note-plugin` → install the **tk-daily-note** plugin.

(There's no longer a separate Cowork tab — chat and Cowork share the Home tab, and you switch between them with the selector in the bottom-left of the message box. Plugins install once and apply to your Cowork sessions on desktop, web, and mobile.)

You'll see a warning that plugins from a personal marketplace aren't reviewed by Anthropic — expected for any repo-hosted plugin. After installing, open the plugin to confirm the `daily-note` skill is listed and enabled.

**Claude Code:**

```
/plugin marketplace add Medium/tk-daily-note-plugin
/plugin install tk-daily-note@tk-daily-note
```

Update later by refreshing the marketplace — this is the point of distributing via a repo: the skill improves centrally, everyone's scheduled runs pick it up.

## Set up the schedule (one-time, per user)

The plugin delivers the skill; the recurring run is yours to create. In Cowork, just ask:

> Create a scheduled task that runs every 30 minutes on weekdays between 7am and 7pm. Its prompt: invoke the installed "daily-note" skill and follow it exactly; run unattended and never ask questions.

That's cron `*/30 7-18 * * 1-5`. Keep the schedule prompt thin — all logic belongs in the skill. After creating it, click **Run now** once to generate today's note and pre-approve the connector permissions so unattended runs never stall.

Optional first-run touches: create a private **"Daily Notes"** library in TK (the skill finds it by name; otherwise notes are personal docs tagged `daily-note`), and fill in weather locations in the Daily Note Memory doc after the first run seeds it.

## Repo layout

```
.claude-plugin/
  plugin.json          # plugin manifest
  marketplace.json     # marketplace manifest (this repo is its own marketplace)
skills/daily-note/
  SKILL.md             # invariants + every-run workflow (find memory note, find today's note, branch)
  references/
    create.md          # first-run-of-the-day: section templates and sourcing
    update.md          # refresh protocol: Notes-to-Claude handling, surgical edits, memory gardening
    memory-template.md # seed for the Daily Note Memory doc
README.md
```

## Provenance

Built from [Tony's Daily Note V2 user stories](https://tk.xyz/libraries/1f589782-2a2d-4900-8ed4-dc0d9f292ca4/notes/3a17f87f-ff98-4439-a954-c3b626f8ff3b) (TK Product and Architecture library — membership required). The design maps his stories: #1 the pre-populated briefing, #3 customization via the Notes-to-Claude → Memory loop. Story #2 ("record of your day") is a natural next section — PRs welcome.
