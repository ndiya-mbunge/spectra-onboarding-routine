# Automation run, scheduled task text (revised 9 October 2026)

Replaces `references/automation-run-task.md` in the client-onboarding skill. Skill files cannot be edited from inside a run (rule 2a), so Ndiya pastes this into the scheduled task and replaces the skill copy.

What changed, and why. Twenty passes on 8 Oct ended "nothing completed" for three reasons, all fixed in the tooling and now in the text below:

1. The Google Drive connector returns a stale cached snapshot and cannot write. Passes concluded the Zeal and Shipwright Registry rows did not exist, they did. Registry reads and writes now go through Make.
2. No run could call Instantly or SmartLead, because the Make passthrough scenarios take the API key as an input and a run must never hold a key (38b). Keys now sit in a Make data store and a scenario looks the key up itself.
3. A run that could not do something posted the same blocker list every hour. It now posts only when something changed.

## Tools this run uses

1. Registry read, live: Make scenario 9943488. `scenarios_run` with `{"range": "A1:L30"}`, `responsive: true`. Returns one line per row, pipe separated, columns A to L. Always use this, never the Drive connector, for any Registry fact.
2. Registry write: Make scenario 9943482. `scenarios_run` with `{"cell": "E10", "value": "Own account"}`. One cell per call, sequential, a run cannot overlap itself. Before any write, read the live Registry, find the client's row by name in column A, and build the cell address from that row. Never assume a row number. After the write, read the row back and confirm it.
3. Platform call: Make scenario 9943494. `scenarios_run` with `{"client": "<Registry client name>", "method": "get", "path": "/api/v2/campaigns?limit=100", "body": ""}`. The key is looked up inside Make from data store 194341 (record key is the Registry client name). The scenario returns only the platform's status and body, never the key. If the data store has no record for the client, the call fails: that is the "platform key does not exist yet" state, and 65, 70 and 77 wait for it. Where the platform is SmartLead the same store holds the key, the scenario currently targets Instantly only.
4. Browser queue: see below.

## Instructions

Run the client onboarding skill's automatic step check. For every active client in the Spectra Onboardings Asana project, read its parent task and its own Asana project, then check every step the skill marks automatic against what it actually depends on.

1. Read the live Registry first (tool 1). Treat it as the only source for what a Registry row holds. A Drive read that disagrees with it is the stale one.
2. Where a step's dependency is met and it has not run, execute it: 35a and the 47, 38a and 73 backfills, 57a once targets exist anywhere (a target Ndiya typed on the Registry or Dashboard is carried into the row, never questioned), 65, 70 and 77 once this client's key record exists in the data store, 73 BreakCold wiring, 79.
3. Closing the step's own Asana task is part of doing it, with a comment naming the object created or the value written. Never a human owned task on inference. Where the real system can be read (Registry, BreakCold, Instantly through tool 3, Slack), verification is allowed and closing on it is fine.
4. Registry fields describe the key situation (Platform, Sending account, Platform client ID), never the key value. Never write, read back or quote a key anywhere.
5. Do not write a Registry value you cannot source. A blank is correct when the source does not exist (for example Sending account when nobody has said whose Instantly account it is). Name the missing source and who holds it instead.
6. Compare the live BreakCold Client options with `breakcold_ids.md`. If anything is missing or wrong, never write the file: generate the full corrected file and flag it to Ndiya with the full content.

## Browser work

Some steps can only be done in Ndiya's browser (Claude project instructions and uploads, Instantly or SmartLead screens with no API, Underfive screens the MCP does not reach). A scheduled cloud run has no browser. Do not mark these as failed and do not repeat them in every message.

1. Create or update one Asana task per client titled `Browser queue: [step]`, assigned to Ndiya, with the exact actions, the URL, and the read back that proves it. This is the queue. Asana is the state.
2. A run that has the Claude in Chrome connection (a local Claude Code session on Ndiya's laptop, scheduled the same way) drains the queue: it opens each queued task, does the action, reads it back, comments the evidence and closes the task. It never enters a password or signs in, and stops with a comment if claude.ai or the platform is signed out.
3. A run without the connection leaves the queue untouched. The task being open is the "saved for the next run with a browser".

## Messaging

1. Post in `#spectra-onboardings` only when something changed since the last pass: a step completed, a new blocker appeared, a blocker cleared, or a connector that was working started failing. One consolidated message per client, naming everything completed.
2. A blocker that was already reported and has not changed is not repeated. Keep one standing comment on the parent task and edit nothing else. The Asana due dates and the daily run keep holding it to account.
3. A step that has been blocked on a tooling gap for more than one working day becomes a task on Ndiya naming the gap, once.
4. If a connector call itself errors, comment the exact error on the task and name the connector in that client's message, once per distinct error, not once per pass.
5. Never post a Slack message to a client facing channel. Drafts only, per 16b.
