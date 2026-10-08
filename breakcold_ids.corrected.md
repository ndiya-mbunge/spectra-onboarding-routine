# Spectra BreakCold ids and wiring

Canonical, machine verified source for every BreakCold identifier the Spectra pipeline uses. Every id below was pulled live from the workspace on 26 July 2026 through the BreakCold and Make connectors, not transcribed from memory. When any skill, SOP, Make scenario, or sweep needs a BreakCold id, read it here. If this file and any other note disagree on a BreakCold id, this file wins.

Revised 8 August 2026. That pass added the NMRK client option, added the dedicated NMRK Instantly scenario, corrected the claim that 9474569 serves both LDC and NMRK, and standardised the interested alert copy. Every id added on that date was read back from the live workspace or the live scenario blueprint after the write, not assumed from the write request.

Revised 25 September 2026. Caught by a client onboarding automation run, which correctly refused to trust this file's client list without checking it live first, exactly the discipline this file exists to enforce on everyone else. Added five client options missing from the list: Anvil Partners and Sozo Labs, both live and working for some time before this pass with no record here, confirmed by pulling real interested and booked leads off each one's own board rather than assuming the option existing meant the pipeline worked; and Hubrig Crew Marketing, Shipwright Consulting Group, Zeal Media Solutions, all option only, created 24 September 2026 during onboarding, with no scenario, alert branch or webhook for any of the three. This file's own rule at the top, that this is the canonical source and other notes lose to it if they disagree, only holds if it is actually kept current, an id sitting live in the CRM for weeks with nothing here is exactly the drift this file exists to prevent, not a small thing to shrug off.

Revised 1 October 2026. Caught by a client onboarding automation run that compared the live Client options (ten, all ids and values matching this file) and then checked the Zeal Media Solutions wiring against the live Make scenario. The options were right, the wiring notes were stale: Zeal now has its own dedicated Instantly scenario, 9875824, active, hook 4398759, built 27 September 2026, so the Zeal option line, the client tag map and the automations list below are updated. Hubrig Crew Marketing and Shipwright Consulting Group are still option only.

Revised 4 October 2026. Caught by the same automation run. The ten live Client options still match this file on id, label and value. The 1 October revision left Hubrig described as option only, which is now wrong: Hubrig has its own dedicated Instantly scenario, 9879995, active, hook 4400477, built 28 September 2026, read from the live blueprint. Shipwright Consulting Group is still option only.
Revised 8 October 2026. Caught by the same automation run, which compared the ten live Client options (all ids, labels and values match this file) and then checked every Breakcold scenario in Make team 888326 against the notes below. Two corrections. First, 9634193, the NMRK Instantly scenario, was documented as Active and is live inactive: the switch is off and Make reports it invalid and unlinked, last edited 8 August 2026, so no Newmark interested lead has been written by it since it was switched off, confirm with Ndiya whether that is intended. Second, 9923022, Breakcold Underfive Google Calendar bookings (Hubrig, Shipwright), created by Zane on 6 October 2026, was not documented at all, it is added under the active automations. It is an alert only path today, it does not stamp booked_on, booked_for or Stage.

What this file deliberately does not hold, so the same id never lives in two places:
- Client Slack channel ids and Asana project GIDs live in the spectrasweep skill, in clients_config.md. This file points at that file, it does not copy those ids. The only Slack channels named here are the two the BreakCold automations post to that are pipeline infrastructure, not client channels.

## Workspace and objects
- workspace_id: px77vhrnwzk7wsg5sezf2h7gvn8970pf
- person_object_type_id: nn71nerby1gqhv16y1y63qe4f9897095
- company_object_type_id: nn7342q70a6kr7kn2j0s4qrpdx896tm9
- rest_base: https://http.us.breakcold.com/api/v1/records
- make_breakcold_connection: keychain id 206581, the BreakCold auth the Make scenarios use

## Fields on the person object
The slug is the stable identifier, it never changes, use it in filters and API bodies. The fieldDefinitionId is what view configs need, both the columns array and groupBy. Select fields carry their options in the next section.

| slug | display name | fieldDefinitionId | type | use |
| --- | --- | --- | --- | --- |
| name | Name | jh70hz3pbj30z25278gm5zb4m5897ye3 | text | Primary. Cannot be hidden, always the kanban card title. |
| job_title | Job Title | jh7ddc4atbj39400cngb920ppx8967b0 | text | Role. |
| company | Company | jh76kwbz4q360gyb2xx5wfb3en897yyj | relation | Links to the company object. |
| email | Email | jh79qgnf6yhr1924ghaqntytj5897f15 | email | Identifier, multi value. A duplicate email is what triggers a 409 on create, see the SKILL. |
| linkedin | LinkedIn | jh7dgzpj809xdrstgzjv32cf3589709t | linkedin | Identifier. |
| phone | Phone | jh78pd0ej2mp1fmzj6ypa01nf5897tfr | phone | Multi value. |
| owner | Owner | jh7frfc3c2g1db5bzymkrptcz9896wsq | member | The Spectra user who owns the lead. |
| notes | Quick Notes | jh70kg8t9q10tfbmz46a96v3d98971yz | textarea | Free text. |
| stage_3 | Stage | jh74pfgap3zxapfwq9yh9ch3m9897wx7 | select | The pipeline stage. Groups every client board. |
| client | Client | jh7bf7djsv6yqv4ay30yxdp82h89r0dk | select | Which Spectra client the lead belongs to. |
| channel | Channel | jh76wv36f2pv9qhzn621de5ax189s4ed | select | Which platform the lead came in through. |
| booked_for | Booked For | jh790wmx7p4gtjsq381n3d193189r9a2 | date | The call time. Not the same as booked_on. |
| next_follow_up | `Next Follow-Up` | jh79g2x9k8509thk9ctjxbwk7d89r2q1 | date | Set plus 2 working days each follow up. Drives the Follow Ups Due queue. |
| icp | ICP | jh78ytwm7wj01w4qbf26f1a9c989sz1j | select | Fit check. Defaults to To Review on create. |
| interested_on | Interested On | jh76n9646qxq9ahjsxk58p75tn8a1fx6 | datetime | System set by the interested automation. The bonus denominator date. |
| booked_on | Booked On | jh7br41pdf47gq2rmaexagm9x58a1yx3 | datetime | System set at booking. The bonus numerator date. Never the same field as booked_for. |
| held_on | Held On | jh72ng4926nc925ejzfe7eaa5s8a10te | date | Placeholder, not in use yet. Leave empty. |
| booking_value | Booking Value | jh79sy034z4p7kqdhvexkprgkx8a0ha6 | number | Placeholder, not in use yet. Leave empty. |
| no_show_on | No Show On | jh75422mky7w905z9p9cf5tn518a8vy3 | date | System set by the no show sweep. Do not enter by hand. |

Stock BreakCold fields that exist on the object but are not central to the Spectra pipeline: deals, bio, location, last_interaction and its per channel variants, created_at, updated_at, telegram, whatsapp. Do not build pipeline logic on these without a reason.

## Select field options
Format is label, then the option id, then the stored value.

Stage, slug stage_3, in pipeline order:
- Interested, jn74x8xaj8a8vkzd7v1e5gv3558970e8, value interested
- Qualified, jn72by3pbh0gec6nq8rmjn4635896abd, value qualified
- Meeting Booked, jn78706sqbx98m3kve44jwvyhd897n9r, value meeting_scheduled
- Proposal Sent, jn73ka3gjggyp61n5fmjajfbdx897r2m, value proposal_sent
- Closed Won, jn7fcc3vpg89ghrwshnbsk3tex897nfa, value closed_won
- Closed Lost, jn7a5n339yt0znghc760gh2sfh897spp, value closed_lost
- Not a fit, jn7ejgxn5zr8bvvxmtx5st2q8n89dykq, value not_a_fit
- No Show, jn7bqdxbnc03s6tzkss0asnwhd8a95rj, value no_show

Client, slug client. Ten options as of 25 September 2026. NMRK is in the CRM but stays outside the bonus pool, see the note under the option list. Anvil Partners and Sozo Labs existed live before this date but were never added to this file, caught during a routine reconciliation pass, confirmed still live by pulling real records off each client's own board, Anvil has interested leads with interested_on dates, Sozo has a booked meeting with booked_on and booked_for both set, so both are working pipelines, this was a documentation gap only, not a live wiring gap. Shipwright Consulting Group is option only, created 24 September 2026 during onboarding, no interested scenario, alert branch or webhook exists for it yet, do not read the option existing as the wiring being done. Hubrig Crew Marketing and Zeal Media Solutions were created the same day and have since been wired, see their lines below, scenarios 9879995 and 9875824.
- Regency, jn77fqsx94dj5qwtt37njv6pqn89rp1m, value regency
- Red Cell Therapy, jn743dq1dbcyte9rne4zbx47nd89s5wh, value red_cell_therapy
- Outbound Edge, jn7afdhp2ha13ez1878110aayd89rnkp, value outbound_edge
- LDC Strategies, jn73dqmc0kj87a5abz8nr1svvx89spc0, value ldc_strategies
- NMRK, jn77f7dxvd0853n29er0ej1s0s8c26x5, value nmrk, added 8 August 2026
- Anvil Partners, jn70vjd0n48trpgz7grbx0jppx8d85by, value anvil_partners, live before this file was updated, exact date not recorded, added to this file 25 September 2026
- Sozo Labs, jn7eb6aa3tm0hzy58ywbxb3m398crm1p, value sozo_labs, live before this file was updated, exact date not recorded, added to this file 25 September 2026
- Hubrig Crew Marketing, jn7a46cge7sqwpabenc0vfy8zd8f0m41, value hubrig_crew_marketing, created 24 September 2026, scenario built 28 September 2026 as its own dedicated Instantly scenario 9879995, hook 4400477, alert to the client facing channel. Whether the Instantly side webhook is attached is not confirmed, it waits on Zane confirming Instantly's client record equivalent
- Shipwright Consulting Group, jn7fmqwbhjjab10pkd20z38k0x8f02r4, value shipwright_consulting, option only, created 24 September 2026, no scenario or webhook wiring yet
- Zeal Media Solutions, jn7a1v9yv7mqs7jekbvh4hdfa58f005k, value zeal_media, created 24 September 2026, wired 27 September 2026 through its own dedicated Instantly scenario 9875824, webhook tag zeal

NMRK and the bonus pool. Adding the option does not put Newmark in the bonus. Both scoreboards filter client with is_any_of against the four pool option ids listed above, so a fifth option cannot leak in. Verified live on both views on 8 August 2026. Spectra does not handle Newmark bookings, Zach Caine does, so a Newmark interested lead would inflate the denominator with no reachable numerator. If anyone ever widens either scoreboard filter to is_not_empty on client, Chi's ratio silently drops. Treat that filter as load bearing.

Channel, slug channel:
- Email SmartLead, jn7bnbnbh17ec1vrp1rfxey7z989sdsn, value email_smartlead
- Email Instantly, jn732m6mwt1y436hbx2s5hev4s89rj4k, value email_instantly
- LinkedIn Aimfox, jn7as2hyym9fzdha8ptp1zf8fx89sstv, value linkedin_aimfox

ICP, slug icp:
- To Review, jn77bbqa7e9a4aepajnh0wy6eh89r1kz, value to_review, this is the default on create
- Fit, jn727p3en834ze65hzqchme8cx89sya0, value fit
- Not a Fit, jn7dqw6y32fc8v674nkpwjz5ts89r995, value not_a_fit

## Client tag map
The Make automations receive the client as a short webhook tag on the query string, then set the client select option. The webhook tag and the stored option value are not the same string, keep both straight.

| webhook tag | client option id | stored value | label |
| --- | --- | --- | --- |
| regency | jn77fqsx94dj5qwtt37njv6pqn89rp1m | regency | Regency |
| redcell | jn743dq1dbcyte9rne4zbx47nd89s5wh | red_cell_therapy | Red Cell Therapy |
| oe | jn7afdhp2ha13ez1878110aayd89rnkp | outbound_edge | Outbound Edge |
| ldc | jn73dqmc0kj87a5abz8nr1svvx89spc0 | ldc_strategies | LDC Strategies |
| nmrk | jn77f7dxvd0853n29er0ej1s0s8c26x5 | nmrk | NMRK, in the CRM since 8 August 2026, outside the bonus pool |
| zeal | jn7a1v9yv7mqs7jekbvh4hdfa58f005k | zeal_media | Zeal Media Solutions, wired 27 September 2026, own dedicated scenario 9875824, not the shared switch |

Anvil Partners and Sozo Labs are both live and correctly tagged on real leads, confirmed 25 September 2026, but their webhook tag and which scenario carries them are not independently confirmed here, records for both show interested via SmartLead so they very likely route through 9474483 alongside Regency, Red Cell and Outbound Edge, worth confirming directly against the live scenario blueprint rather than assumed from this note. Shipwright Consulting Group has no row here at all, since it has no webhook tag or scenario assignment yet. Zeal has a row, added 1 October 2026. Hubrig has its own dedicated scenario 9879995 on hook 4400477, its webhook tag is not read from the blueprint and is not recorded here until confirmed against the Instantly side webhook URL.

Type oe, never outboundedge. The booking scenario still tolerates outboundedge for old links, but oe is the only value to use going forward.

## Views
Boards are kanban, grouped by stage_3, showNoStageColumn true, filtered to one client. Cards show only Name, Job Title, Company. Queues and scoreboards are tables.

Client boards:
- Regency Board, pn755j1fahpybbwwksnh8pteed89rrp4
- Outbound Edge Board, pn71naj4wgqncrqqpc7re2txt589rexr
- Red Cell Therapy Board, pn7fvwgaq3dtq47c0cytypt1ts89r0xs
- LDC Strategies Board, pn741eatxrxr69gx15bvv0e7ms89rf4d

Working queues:
- Bookings, pn77n5bstayr1nmznj29t2rv4589r3y6, filter stage is Meeting Booked
- Follow Ups Due, pn76g9459trbjnpcbtqykxrsdx89rkka, filter stage is Interested or Qualified, sorted by next_follow_up ascending
- Needs ICP Check, pn70f53x7aa0eb6h8kysgfhtvs89r259, filter icp is To Review

Bonus scoreboards, read only, the pool is Regency, Red Cell, Outbound Edge, LDC:
- Pooled Interested (Bonus Denominator), pn7f5xmk2wrq80cytp63yksnq18a39j5, filter interested_on not empty and client in the four
- Pooled Bookings (Bonus Numerator), pn725xvj4xyg1fk78zkz1rary58a3451, filter booked_on not empty and interested_on not empty and client in the four

Stock BreakCold views, not part of the Spectra pipeline, leave them alone: All People pn78saeb1m153mkc8v4p4979mh8979jq, Last Interaction 7+ Days pn77k8cvakdwq1cs1bcr035k6189619z, 0 conversations pn70fnh088pv0e5nmyhv1tdpx9896wjr.

## Automations that touch the CRM
Confirmed live from Make team 888326 on 26 July 2026. State is the switch state on that pull.

Active:
- 9474483, Breakcold, SmartLead interested to CRM (denominator). Writes the interested record for SmartLead clients and posts the client alert. Resolves the client from the webhook tag through a switch, so one webhook serves Regency, Red Cell and Outbound Edge. Hook 4236960.
- 9474569, Breakcold, Instantly interested to CRM. LDC only, despite the generic name. Its clientOptionId is a hardcoded constant, not a switch, so this scenario cannot serve a second client. Every record it has written carries an LDC campaign name, checked lead by lead on 8 August 2026. It has no Slack alert module and no create failure alert. Hook 4236999. Do not convert it to the tag and switch pattern without updating the webhook URL inside LDC's Instantly account in the same change, or LDC interested leads stop writing silently.
- 9634193, Breakcold, Instantly interested to CRM (NMRK). Inactive as of 8 October 2026 (switch off, Make reports it invalid and unlinked, last edited 8 August 2026), created 8 August 2026, documented as Active before this revision which was wrong. Newmark only, own webhook 4305358 at [webhook url, see live scenario], deliberately separate from 9474569 so LDC is never at risk. Dedupes on email, fires only on event_type lead_interested, writes the record tagged NMRK with channel Email Instantly and stage Interested, posts the client alert to the Newmark channel C0B4XMVSPH7, and posts a red SYSTEM alert to C0B7SSURDHN on a create failure. The alert carries the lead name only, because the Instantly payload as wired carries first_name, last_name, lead_email, campaign_name, reply_text_snippet and event_type, with no job title field. Read a real execution payload before adding a role line.
- 9875824, Breakcold, Instantly interested to CRM (Zeal Media). Active, created 27 September 2026, hook 4398759, cloned from the single client shape of 9634193 with its own hardcoded clientOptionId for Zeal. Dedupes on email, fires only on event_type lead_interested, and only for campaigns created after 27 September 2026, enforced by a filter that excludes the 39 campaign ids that existed that day. Writes the record with channel Email Instantly and stage Interested, posts the client alert to the Zeal client facing channel C0C48KBEH0A, and posts a red SYSTEM alert to C0B7SSURDHN on a create failure. Instantly webhook is workspace level, interested category only, so it is complete for every post 27 September campaign without per campaign attachment.
- 9879995, Breakcold, Instantly interested to CRM (Hubrig). Active, created 28 September 2026, hook 4400477, own hardcoded clientOptionId for Hubrig. Dedupes on email, fires only on event_type lead_interested, writes the record with channel Email Instantly and stage Interested, posts the client alert to the Hubrig client facing channel C0C2680DYA1, and posts a red SYSTEM alert to C0B7SSURDHN on a create failure. Unlike the Zeal scenario it has no campaign exclusion filter. The Instantly side webhook attach is not confirmed.
- 9923022, Breakcold, Underfive Google Calendar bookings (Hubrig, Shipwright). Active, created 6 October 2026, hook 4417003, for clients who book on Google Calendar and so have no Calendly subscription. Receives the Underfive workspace webhook on meeting.scheduled and posts the raw payload to the Chi inbox management channel C0BEC4A0WEQ with an instruction to stamp booked_on, booked_for and Stage Meeting Booked in BreakCold by hand. It does not stamp automatically and it posts no client booking alert. The full mapping is still to be built from a real payload.
- 9497536, Breakcold, Booking intake (shared, all clients). Stamps booked_on, booked_for, and Stage Meeting Booked on a single guest name match. Zero or multiple matches raise a reconcile alert.
- 9507708, Breakcold, No show and rebook sweep. Drives no_show_on.
- 9528977, Outbound Edge Calendly. Active. See the open item below, its relationship to the shared booking scenario needs confirming.

Off:
- 9528957, Regency Calendly.
- 9484929 and the per client Aimfox interested scenarios, Regency 9486028, Red Cell 9486744, LDC 9486752, plus 9485016 Aimfox sync Regency. All off. While these are off, LinkedIn Aimfox interested leads do not get an interested_on date, so they are invisible to the bonus denominator.
- 9489573, Breakcold, Booking to CRM. Legacy, superseded by the shared 9497536.
- 9507251, Breakcold, Bonus monthly freeze.

Open items on the wiring, confirm before relying on any of these:
- Instantly webhooks are configured per campaign, not per workspace. Scenario 9634193 only receives a Newmark lead from a campaign that has the webhook attached, so Newmark interested counts are incomplete until every live Newmark campaign carries it. Verify campaign by campaign against the campaign tracker before quoting a Newmark interested number.
- There is no NMRK kanban board view. The four client boards listed above cover the pool clients only, so Newmark records are visible in All People and nowhere purpose built. Create one if Newmark reporting ever moves in house.
- The interested alert copy is standardised across 9474483 and 9634193 as of 8 August 2026 and reads: New interested lead, the bold lead name, then the line Our team is following this up to book the call. Nothing needed from you. The earlier wording invited the client to claim the lead and Regency read it as ambiguous, so do not reintroduce a claim or opt out sentence without asking Ndiya. On 9474483 the job title renders in brackets after the name through the roleSuffix variable in module 2, which collapses to nothing when SmartLead has no title, so the alert never shows an empty line.
- The Calendly scenario ids that earlier notes carried, 9515392, 9515394, 9515395, 9515396, do not exist in the live list. The real Calendly scenarios are 9528977 Outbound Edge, active, and 9528957 Regency, off. No Red Cell or LDC Calendly scenario is visible. Bookings are carried by the shared 9497536 meanwhile.
- Because 9528977 Outbound Edge Calendly is active at the same time as the shared 9497536, confirm they cannot both stamp the same Outbound Edge booking, or you get double stamping. Check what 9528977 actually writes before trusting Outbound Edge booking counts.

## People, BreakCold member ids
- Ndiya, ph74n4rej2etphmkkj69h4fp8989p4nn, confirmed live as the creator of the client boards.
- Zane, ph76zmjxg8segd9ze3yvb6nqv1895qcy, info@spectraacquisition.com, confirmed live as record owner and view creator.
- Chi, ph78a0jxmtzf6ytbdcm7a0bmhx89qykx, from setup notes, confirm before reassigning ownership in bulk.

## Pipeline infrastructure Slack channels
Only the two channels the BreakCold automations post to that are not client channels. Client alert channels live in clients_config.md in the spectrasweep skill.
- SYSTEM alerts, an interested create failure, land in the Outbound Edge internal channel, C0B7SSURDHN.
- Booking reconcile alerts, zero or multiple name matches, land in the Chi inbox management channel, C0BEC4A0WEQ.
