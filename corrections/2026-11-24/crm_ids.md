# Spectra CRM ids and wiring

Non secret CRM identifiers only. First built and verified live on 29 October 2026, when BreakCold was retired; moved onto the Spectra hub on 9 November 2026; checked against live Underfive and Make by the automatic onboarding run on 12, 13, 16 and 17 November 2026, updated on 18 November 2026 with that run's corrected file plus the reply relay, CJ Digital and reminder changes of 16 to 18 November, corrected on 23 November 2026 from that day's automatic run (Zeal and Hubrig Make interested alerts found off, CJ Digital agent found inactive), and corrected again on 24 November 2026 (CJ Digital's first Spectra campaigns exist, Gauge Digital's SDR Queue webhook carries real leads). If this file and any other note disagree, this file wins, and it only holds if it is updated in the same session as any change.

Not in this file, by design:
- Hook URLs, relay addresses and any other secret. A Make hook URL or a hub relay address works like a password: refer to it by name and id, and read it where it lives when you need it. Never paste one here.
- Client records, client Slack channel ids and targets. They live in the hub: `list_clients`, `get_client`.
- People, seats and Slack user ids. They live in the hub: `people_and_roles`.

The record of interested, booked, lost, showed and no show is the Spectra hub (`pipeline`), and its alerts are `get_health`.

## Make
- Team 888326, organization 2248499, zone eu2.make.com.
- Slack connection used by every pipeline scenario: 3550475.

### Data stores, retired
| Store | id | Data structure | Status |
| --- | --- | --- | --- |
| Pipeline ledger | 203107 | 651536 | Retired 9 November 2026. Replaced by the hub (`pipeline`, `get_health`). Never read for a figure, never written |
| Pipeline client alerts sent | 203108 | 651537 | Still used by the client alert scenarios so an alert is not sent twice, until they are switched off at the hub migration's step 15. Never a source for a figure. Never written by hand |

The ledger held one row per lead per client, key `Client Label|lead email lowercase`, with set once dates interested_on, booked_on, lost_on and no_show_on. It is history only.

### Live scenarios
Active in Make, read live on 23 November 2026 at about 09:20 CAT. Changed since 17 November 2026: the Zeal and Hubrig Instantly interested alerts (9875824, 9879995) are now off, in line with the hub being the only sender of interested notices; they are listed under Retired below.
| id | Name | Hook id | Does |
| --- | --- | --- | --- |
| 10045732 | SDR Queue (live name in Make: SDR Queue, Underfive to open lead sheet) | 4465293 | Fed by each workspace's `SDR Queue` webhook. Posts each lead event and booking to the hub's ingest endpoints, keyed by the `client` value on the webhook's query string, which the hub resolves to the client. It also still writes to the SDR open lead sheet, which Ndiya keeps running while the SDR workflow settles; never switch that step off unless she asks |
| 10063115 | SDR Queue, sync health watchdog | none (scheduled) | Watches the SDR open lead sheet sync and posts SYSTEM alerts to C0B7SSURDHN |
| 10113458 | SDR, EOD submitted DM to Ndiya | 4493481 | DMs Ndiya when the SDR's end of day is submitted |
| 10063097 | Pipeline, Underfive events to ledger and booking alerts | 4472879 | Ledger writes retired. Booking alert for Hubrig and Shipwright on first booking |
| 9497536 | Pipeline, Calendly booking alerts | 4246945 | Calendly booking alerts for Regency, Outbound Edge, Red Cell, Sozo, Zeal (Spectra event type only). Its ledger booked on write is retired |
| 9474483 | Pipeline, SmartLead interested alerts | 4236960 | SmartLead interested alerts, deduped, one route per client tag. Its ledger interested on write is retired |
| 9474569 | Pipeline, Instantly interested alert (LDC) | 4236999 | LDC interested alert, Spectra test addresses excluded |
| 9528977 | Outbound Edge Calendly | 4260890 | Internal Outbound Edge booking note, no CRM write |

Since 18 November 2026 the hub posts every interested notice itself, for every active and onboarding client, five minutes after the lead lands, unless a notice naming the lead is already in the client channel; if it cannot post or read the channel it raises a red `interest_notice_missed` alert on Health. No Make interested alert is cloned for a new client any more. Updated 20 November 2026: every Instantly client has TWO webhooks to the same hub relay address from `/admin/relay`, both All Campaigns: Reply Received (named Spectra Reply Sync) and Lead Interested (named Spectra Interested Sync). The second lets the hub announce an interested lead within seconds of Instantly marking it, instead of two to three minutes later when Underfive opens its thread. The hub is the only sender of interested notices: never clone or keep a Make interested alert for a client the hub covers, because two senders name the same lead differently (Make by email, the hub by company) and read as duplicates. Where the notice goes is the client record setting "Interested lead notices go to" (client channel, internal, or off). CJ Digital and Gauge Digital have none and need none. (On 17 November Gauge's first interested lead never reached its channel because the per client clone had not been made; that gap is why the hub now does it.)

Since 17 November 2026 the hub also checks every booking itself, and since 19 November it does so about 45 seconds after the booking lands (a five minute run is the backup): it looks in the client facing channel for a notice naming the lead and posts "Booked by Spectra for [client]" if none is there. The Make booking alerts above still run; the hub only fills gaps.

GHL booking clients (`bookingPath` ghl) send every appointment to the hub from their own GHL through a workflow named "Spectra hub, appointment sync" (Appointment Status on each booking calendar, Webhook POST to the hub's `/api/ingest/ghl-appointment?client=<slug>&token=<INGEST_TOKEN>`), so a booking made there reaches the hub in seconds rather than about 20 minutes later through Underfive. Built for Gauge Digital on 19 November 2026 (sub account Gauge Digital Media, both calendars). The hub's hourly check flags any GHL booking client whose bookings arrive without it. The hub passes a GHL appointment on only when it already has that person for that client (an interested lead or an earlier booking, by email or phone), so the client's own bookings on the same calendar (their funnels, ads, referrals) are never counted or announced as Spectra's (Gauge Digital, 20 November 2026: a booking from Joe's own funnel was posted as "Booked by Spectra" before this rule).

### Retired, off, do not turn back on
Read live 23 November 2026, every one inactive: 9875824 Pipeline, Instantly interested alert (Zeal Media) (hook 4398759, off since about 20 November 2026; the hub now sends Zeal's interested notices; its pre Spectra campaign exclusion list still sits in it and in 10063097), 9879995 Pipeline, Instantly interested alert (Hubrig) (hook 4400477; the hub now sends Hubrig's interested notices), 9652484 Red Cell Calendly, 9528957 Regency Calendly, 9634193 NMRK, 9489573, 9507251, 10103844 SDR Queue brief writer (hook 4489427, wrote to the retired sheet).

The Google Calendar booking path, scenario 9923022 with hook 4417003, is off with the rest of the BreakCold set. Hubrig and Shipwright booking alerts now run from 10063097.

History: BreakCold was cancelled on 27 October 2026; every BreakCold scenario (9923022, 9507708, 9484929, 9486028, 9486744, 9486752, 9485016) and its Make keychain are off and stay off.

## Hub reply relay
Since 17 November 2026 each Instantly client's workspace has one webhook named `Spectra Reply Sync`, on All Campaigns, event Reply Received, pointing at that client's hub relay address (shown on the hub's `/admin/relay` page; never written anywhere else). The hub forwards a reply to the client's Underfive agent only when Spectra created the campaign on or after the client's `campaignsFrom` day, retries every five minutes and raises `reply_relay_stuck` after fifteen. Never a webhook per campaign, never routing by campaign name, never an Instantly reply webhook straight to Underfive. Live for CJ Digital, Zeal Media and Hubrig Crew Marketing on 17 November 2026, and Gauge Digital the same day. Start days: Zeal Media 28 September 2026, CJ Digital 15 November 2026.

## Underfive
Workspaces, every workspace webhook and every agent's status read live 17 November 2026 at 07:20 CAT, updated 18 November 2026. Every workspace webhook read live again 23 and 24 November 2026 and matches this table; CJ Digital's agent status corrected on 23 November and still inactive on 24 November 2026. The `SDR Queue` webhook is the one that feeds the hub; its `client` query value is what the hub resolves, and it is recorded here exactly as it is live.
| Workspace (client label) | Workspace id | Agent | SDR Queue webhook id, client value | Pipeline Ledger webhook id (retired, still enabled) |
| --- | --- | --- | --- | --- |
| LDC Strategies | 6a8bf89cf5678264c4287a9a | LDC Email 6a8bf94cf5678264c4287b4b, inactive. Client paused in the hub 17 November 2026 | 6adf67b7f47c0265bc4661d2, `LDC` | 6ae2af68a67c83630b3fcdda |
| Zeal Media | 6ab579e5640a1f27d4badef0 | Zeal Media Solutions 6ab9152536e7fd637cc3baef, active, human approval on every reply. Four GoHighLevel trade calendars of Reece's attached | 6adf67b1f47c0265bc466140, `Zeal Media` | 6ae2af6ba67c83630b3fcdeb |
| Shipwright Consulting Group | 6ab9104136e7fd637cc394a1 | Shipwright Consulting Group Email 6ab9154236e7fd637cc3bc35, active, human approval. Meeting reminders: workflow 6afbf5592a00d9ce62f054cb, live from 18 November 2026 | 6adf67bdf47c0265bc4661fc, `Shipwright` | 6ae2af6ea67c83630b3fcdfb |
| Hubrig Crew Marketing | 6ab9104236e7fd637cc394b1 | Hubrig Crew Marketing Email 6ab9155c36e7fd637cc3bd77, active, human approval. Meeting reminders: workflow 6afbf5582a00d9ce62f054b2, live from 18 November 2026 | 6adf67c4f47c0265bc46623b, `Hubrig` | 6ae2af71a67c83630b3fce0b |
| Spectra Acquisition (Outbound Edge) | 6aaf2fe99f2dd69ea9f78b5b | Spectra Acquisition Email 6a8ba036f5678264c427756c, active. Spectra LinkedIn 6a8f0096d2c24a3e7cf29d40 also sits here, inactive | Named `Spectra hub`, not `SDR Queue`: 6aefd0af837da83785bc3d36, `Spectra Acquisition`, with `forward=hub` | 6ae2af74a67c83630b3fce19 |
| CJ Digital | 6af300ba94d3c6ec646f09ca | CJ Digital Email 6afa998b51d9e2e7ee754196, created 16 November 2026, switched on 17 November 2026 on Ndiya's yes, read inactive on 23 and 24 November 2026 (last changed 21 November 2026 08:10 UTC; hub path_broken alert open until it is switched back on before CJ's first Spectra campaign sends), every reply and every meeting on human approval, follow ups configured and off. iClosed calendar connection 6af33a6694d3c6ec6474630f attached, single calendar mode. Slack `#inbox-cj-digital` linked (connection 6afce93d7d43569880805dc1). Reminders left to iClosed | 6af54ceaf150f73dd062da5d, `CJ Digital`, created 12 November 2026 | none, never add one |
| Gauge Digital | 6afb2dd82a00d9ce62d42f2b, created 16 November 2026 | Gauge Digital Email 6afb678e2a00d9ce62e303e6, switched on by Zane 16 November 2026, every reply and every meeting on human approval, follow ups configured and off, Slack connected. GoHighLevel calendar connection 6afb5d1a2a00d9ce62dfb378 (Lead Flow Discovery Session, plumbing and HVAC) attached, single calendar mode; a second connection, 6afb5d7d2a00d9ce62dfd433 (water treatment), is connected and deliberately not attached. No Underfive meeting reminders: the client's own GoHighLevel workflows send them | 6afbe3b42a00d9ce62ee8e4e, `Gauge Digital`, created 17 November 2026 by the automatic onboarding run | none, never add one |
| Sozo Labs | 6aaf33db9f2dd69ea9f7a96b, not returned by `underfive_list_workspaces` since 16 November 2026. Inactive client | Sozo Labs Email 6ab9150436e7fd637cc3b70f, inactive, filed under no workspace | none, inactive client | none, inactive client |

The agents RegencyFN, Smartlead (6aa8ba351dde28fff71f4526, inactive) and Sozo Labs Email (6ab9150436e7fd637cc3b70f, inactive) belong to no workspace. Underfive lists seven workspaces on 17 November 2026: Spectra Acquisition, LDC Strategies, Zeal Media, Shipwright Consulting Group, Hubrig Crew Marketing, CJ Digital and Gauge Digital. Gauge Digital has sent since 17 November 2026 and its `SDR Queue` webhook has carried real leads (7 interested and 3 booked in the hub from 17 to 24 November 2026). CJ Digital's first two Spectra campaigns were created in its Instantly workspace on 23 November 2026, both still draft on 24 November (C1 London Premium Independents e61979d3, C2 Big City Independents 45e06fc7), so CJ's `SDR Queue` webhook has not yet carried a real lead.

Every active workspace carries a webhook to the SDR Queue scenario with the three events `lead.category_set`, `lead.category_changed` and `meeting.scheduled`. The `Pipeline Ledger` webhooks above are retired: never add one to a new workspace. The old `Spectra booking intake` webhooks were deleted from Hubrig and Shipwright on 29 October 2026.

The hub client record holds each client's Underfive workspace name and id (`underfiveWorkspace`, `underfiveWorkspaceId`). Where the hub and this table disagree, check Underfive live (`underfive_list_workspaces`) and correct whichever is wrong.

## Client labels and tags
The client label is the Underfive workspace name. The platform tag is the route key in the alert scenarios and the hub record's `platformTag`.
| Platform tag | Client label |
| --- | --- |
| regency | Regency |
| redcell | Red Cell Therapy |
| oe, outboundedge | Spectra Acquisition |
| sozo, sozo_labs | Sozo Labs |
| anvil, anvil_partners | Anvil Partners |
| shipwright | Shipwright Consulting Group |
| zeal, zeal_media | Zeal Media |
| hubrig | Hubrig Crew Marketing |
| ldc | LDC Strategies |
| nmrk | NMRK (Newmark), archived |
| not set yet, set at onboarding step 73 | CJ Digital |
| not set yet, set at onboarding step 73 | Gauge Digital |

Zeal's Spectra Calendly event type: `ee833f53-8a71-4294-aa64-4890e3eab88a`. Zeal campaigns created before its start day are excluded; the list lives in scenarios 9875824 and 10063097, and the hub applies the same start day through `campaignsFrom`.

## Pipeline infrastructure Slack channels
Context only. The record of alerts is `get_health`.
- SYSTEM alert posts: C0B7SSURDHN (renamed `#spectra-internal` on 17 November 2026; it was `#outbound-edge`).
- Booking notes and Slack failures on booking alerts: C0BEC4A0WEQ.

Client alert channels are on each client's hub record (`get_client`).
