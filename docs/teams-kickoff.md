# Echo Plus for Teams — kickoff (2026-10-26)

John confirmed the project is a go. This is the starting breakdown for
milestone 1 in `docs/teams-plan.md`. Build split per the plan: Muse
(web shell, sign-in, orgs/teams/admin, web chat, agents in chats),
Hark (calls/meetings), Grok (lead/review).

## Step 1, in buildable pieces

1. **Web-boot pass (first). DONE 2026-10-26 (echo-plus PR #55).** The
   existing app already had an Expo web target (`web` script, react-dom,
   app.json web section), so the web shell grows in the same codebase
   and reuses `src/lib`. The export failure's real cause was smaller
   than expected: `react-native-web` was never installed (that was the
   resolution failure at `src/lib/calls/background.ts`), and
   expo-sqlite's web build needed its documented Metro setup (`.wasm`
   asset + COOP/COEP headers). No platform-split code changes were
   needed — the codebase already lazy-loads phone-only modules and
   keeps them off web. Export exits 0; a local browser eyeball of
   sign-in + inbox is the last verification (Grok's machine).
2. **Orgs/teams data model. WRITTEN 2026-10-26 (echo-plus PR #55).**
   Migration `20261105000100_teams_orgs.sql` (for Grok to run by hand —
   standing rule: Muse never runs SQL), with its own test suite
   (server `teamsOrgs.test.js`, and the full server suite still
   green). Tables: `orgs` (incl. the channel privacy setting below),
   `org_members` (role: owner/admin/member), `org_invites` (by work
   email — accepting links the account whose sign-in email matches,
   no token links), `teams`, and `team_members`. All changes go
   through checked functions, mirroring the group tables: admins
   invite/remove/promote, the owner transfers or deletes the org,
   removal cuts access at once (team rows included).
3. **Company sign-in.** Layered on existing accounts: an admin creates
   an org and invites by work email; accepting creates/links the
   person's Echo Plus account to the org. SSO is a later item (per the
   plan), not milestone 1.
4. Then milestone 1 continues: channels in the web chat (piece by
   piece, reusing group/relay code), then one agent per person in a
   chat (the agent system already exists).

## Decided 2026-10-26 (folded into step 1)

- **Channel privacy is an org setting** (`orgs.channels_visibility` in
  the orgs migration), fully private by default: channels are
  end-to-end encrypted like the app, and admins manage membership but
  cannot read messages. An org admin may switch the org to
  company-visible, which lets the org create clearly labeled channels
  the company can read/search/audit — the label is mandatory, never
  silent. Private chats stay encrypted either way. (This settles the
  earlier A-vs-B question as "A by default, B as an explicit org
  choice"; Grok is confirming with John.)
- **One login.** The same Echo Plus account signs in on the phone app
  and on Teams web. Org membership is a row naming that account; there
  are no separate work accounts to create or sync.

## Not in milestone 1

Calls/meetings (Hark, later step), file sharing, calendar, recording,
SSO — per the plan's build order.
