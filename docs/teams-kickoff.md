# Echo Plus for Teams — kickoff (2026-10-26)

John confirmed the project is a go. This is the starting breakdown for
milestone 1 in `docs/teams-plan.md`. Build split per the plan: Muse
(web shell, sign-in, orgs/teams/admin, web chat, agents in chats),
Hark (calls/meetings), Grok (lead/review).

## Step 1, in buildable pieces

1. **Web-boot pass (first).** The existing app already has an Expo web
   target (`web` script, react-dom, app.json web section), so the web
   shell grows in the same codebase and reuses `src/lib`. First check
   run 2026-10-26: `expo export --platform web` fails — the phone-only
   call background module (`src/lib/calls/background.ts`, pulled in by
   the root `index.ts`) breaks the web bundle. Piece 1 is the
   platform-split pass: phone-only modules (call background, push
   tasks, notification listeners) get web-safe entry points, until the
   app exports for web and the sign-in screen + inbox render in a
   browser signed into an existing account.
2. **Orgs/teams data model.** New tables alongside the existing ones:
   `orgs`, `org_members` (person, org, role: owner/admin/member,
   status: invited/active/removed), and `teams` under an org. Written
   as a migration for Grok to run by hand (standing rule: Muse never
   runs SQL).
3. **Company sign-in.** Layered on existing accounts: an admin creates
   an org and invites by work email; accepting creates/links the
   person's Echo Plus account to the org. SSO is a later item (per the
   plan), not milestone 1.
4. Then milestone 1 continues: channels in the web chat (piece by
   piece, reusing group/relay code), then one agent per person in a
   chat (the agent system already exists).

## Decision needed from John before the channels data model

Channels privacy, per the plan's honest catch:

- **Option A — encrypted like the app.** Channels are end-to-end
  encrypted; admins manage membership but cannot read messages.
  Strongest privacy story, matches the app's promise; no company-wide
  search or admin audit of message content.
- **Option B — a company-visible channel type.** Admins can create
  clearly labeled channels the company can read/search/audit;
  private chats stay encrypted. More of what businesses expect from
  Teams/Slack, but two privacy models to explain and keep separate.

Kickoff recommendation: A as the default, B as an explicit,
admin-created, clearly labeled channel type later — never silently.

## Not in milestone 1

Calls/meetings (Hark, later step), file sharing, calendar, recording,
SSO — per the plan's build order.
