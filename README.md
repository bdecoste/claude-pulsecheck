# claude-pulsecheck

Automation that drafts a weekly PulseCheck check-in from evidence indexed across
a user's connected data sources (Google Drive, Slack, Gmail, Jira, calendar, meeting
notes, GitHub, etc.), then saves the draft into PulseCheck for the user to review
and submit.

## What it is

- A [Claude Code cloud routine](https://claude.ai/code/routines) that fires on a
  cron schedule (e.g. Fridays 2pm PT).
- Uses the [Super](https://api.super.work) MCP as the primary evidence source
  (it indexes across the user's connected tools).
- Writes to the PulseCheck MCP via `save_checkin_draft` — the user reviews and
  submits from the PulseCheck UI.

The routine prompt is `routine_prompt.md`. It is embedded inline in the routine's
`job_config.ccr.events[0].data.message.content` at creation time — the cloud
agent does not clone this repo. This repo is the source of truth for iterating
on the prompt; changes are applied by re-running `RemoteTrigger` with
`action: "update"` and the new content.

## Requirements

- A claude.ai account with the following MCP connectors attached and authenticated:
  - **Super** (`api.super.work/mcp`)
  - **PulseCheck** (or whatever your organization's weekly check-in tool is,
    with a matching MCP)
- At least one cloud environment configured for routines.

## Non-negotiable field rules (from the PulseCheck MCP guide)

- `priorities` is a **manager-only** field and FORBIDDEN for ICs. The routine
  never fills it.
- `accomplishments` and `upcoming` are required for ICs.
- `blockers` is optional and only filled when a real blocker surfaces in the
  evidence.
- Check-ins are visible to peers, manager, and reports — the routine keeps
  content broad-audience appropriate.

## Files

- `routine_prompt.md` — the prompt the cloud agent runs each week (templated
  with `{USER_NAME}`, `{USER_EMAIL}`, `{COMPANY}`, `{COMPANY_DOMAIN}` placeholders).
- `.gitignore` — excludes local Claude Code settings.

## How to adapt for yourself

1. Fill in the placeholders in `routine_prompt.md` with your name, email, and
   company details.
2. Connect Super and your weekly-check-in MCP to your claude.ai account.
3. In Claude Code, run `/schedule` (or invoke the `schedule` skill), create a
   routine, paste the filled-in prompt, wire in the two MCP connections, and
   set the cron to whenever you want the draft to land.
