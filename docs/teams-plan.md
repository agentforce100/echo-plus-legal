# Echo Plus for Teams — plan (docs only, no code)

A web version of Echo Plus for businesses, on the Echo Plus website.
It has everything a team chat app needs, plus everything Echo already
does, plus one thing Teams does not have: every person can bring their
own AI agent into chats and meetings.

## The feature list, in plain words

**Team chat, like Teams:**

- Team chat and channels: a channel per team, project, or topic, with
  threaded replies so side conversations do not drown the main thread.
- 1:1 and group chats, the same as in the Echo Plus app.
- 1:1 and group calls, and meetings with screen share, a waiting room,
  raise-hand, and a recording / transcript the organiser controls.
- File sharing: drag a file into a chat or channel, preview it, and
  find it later from one files list per channel.
- Calendar: meetings, availability, and reminders, in one place, with
  invites that join straight into a call.
- Company sign-in: people sign in with their work account, not a
  personal one. An admin invites the company, not each person one by one.
- Org and admin controls: who belongs to which team, who is an admin,
  who can create channels, who can invite guests, and a way to remove
  someone and cut their access at once. Guests (clients, contractors)
  see only the channels they are invited to.

**Everything from Echo, in the same place:**

- Reply help in your voice: drafts that sound like you, not generic.
- Tone buttons, Translate, and Fix typos on any message.
- Reply rescue: help when a reply is late or awkward.
- Talk, don't type: dictate a reply instead of typing it.

**The most important part — your own agent in every chat and meeting:**

- Every person can add their own AI agent to a chat, channel, or
  meeting — the same agent model Echo Plus already has (an agent
  account owned by a person, run from their own setup).
- In a chat, the agent can answer questions, fetch what its owner
  asked it to watch for, and draft replies for its owner to approve.
- In a meeting, the agent can listen, take notes, list decisions and
  action items, and answer questions its owner asks it.
- An agent acts only for its owner. It never sends for someone else,
  and nothing it drafts sends without its owner's approval — the same
  never-send-without-approval rule as the app.
- Every agent is clearly labeled as an agent, with who owns it, in the
  member list, the message header, and the meeting participant list.
- Opt-outs, per person and per meeting: anyone can hide agents from
  their own view, a meeting organiser can run an agent-free meeting,
  and an admin can turn agents off for a whole company. An agent that
  is opted out of a meeting does not listen, take notes, or join.
- What an agent heard and wrote is visible to its owner, and meeting
  notes are shared only where the organiser says they go.

## MVP vs. later

**MVP — the smallest version a real company can use:**

1. Web chat: 1:1, group, and channel chat with threads.
2. Company sign-in, orgs, teams, and admin controls (invite, remove,
   guest limits).
3. File sharing in chat and channels.
4. Calendar + meetings: schedule, join, screen share, no recording yet.
5. Echo reply help, tone, translate, and typo fixes on the web.
6. One agent per person in chats (labeled, owner-only, opt-out).
   Meeting notes by an agent come only if the organiser allows them.

**Later — after the MVP is used by real teams:**

- Meeting recording and transcripts, with agent summaries.
- Agent in meetings as a full participant (answers live, not just notes).
- Talk, don't type and Reply rescue on the web.
- Advanced admin: data retention rules, audit log, single sign-on
  (SSO) for large companies, and guest expiry.
- Channel apps and integrations (shared docs, task lists).
- Offline / low-bandwidth mode for the web app.

## Rough build order

1. Web shell + company sign-in + orgs/teams/admin. Nothing else
   works without this, and it is where the access rules live.
2. Web chat + channels + threads, reusing the app's chat logic.
3. File sharing on the web.
4. Calendar + calls/meetings with screen share.
5. Echo reply help on the web (drafts, tone, translate, typos).
6. Agents in chats, then agents taking meeting notes.
7. Recording, transcripts, SSO, and the later admin list.

## What we can reuse from the app and Supabase

- The Echo Plus app (Expo/React Native + TypeScript): chat, groups,
  calls, and Echo reply logic — the web version reuses the same
  message, group, and Echo code where it fits, with a web screen
  layer instead of phone screens.
- Supabase: accounts, the message relay, groups, agents, calls, and
  media storage (the existing migrations for profiles, relay,
  groups, agents, calls, and media). The agent system is already
  built: agent accounts, owner controls, agent badges, and limits.
- Edge Functions: `echo-draft` (Echo AI drafts), `agent-auth`,
  `echo-media` handling, and push — adapted for web, not rewritten.
- The Echo AI provider setup (drafts on tap, with usage limits).

Two honest catches, to decide before building, not after:

- Echo Plus today is end-to-end encrypted and the server only
  relays messages. Business features like company-wide search,
  admin audit logs, and server-side meeting notes do not fit that
  model as-is. The plan needs an explicit choice per feature:
  keep it end-to-end encrypted, or make it a clearly labeled
  company-visible feature (e.g. a channel an admin can read).
- Web sign-in and company accounts are new: the app signs in by
  username, so company sign-in, SSO, and guest handling are new
  work, not a reuse.

## Rough costs

These are planning guesses, not quotes. The main costs are people
first, services second.

- Building: the bulk of the cost. A small team (web + backend +
  design) for the MVP, then ongoing work for meetings, agents,
  and admin. This dwarfs the service bills below.
- Supabase: grows with companies and messages. Starts small
  (hobby/pro tier range), then database, storage for files,
  and realtime as usage grows. File storage is the line to watch.
- AI (Echo drafts + agent notes/answers): charged per use, per
  person. This is the main running cost that scales with use,
  so per-company or per-seat pricing needs to cover heavy users.
- Calls/meetings: call/media relay and screen-share bandwidth,
  which grow with meeting minutes. Recording storage adds on top.
- Web hosting for the site/app: small compared with the above.

Pricing hint from that shape: charge per seat, with agent-heavy
use as a higher tier or an add-on, so the AI and meeting costs
do not outrun the subscription.
