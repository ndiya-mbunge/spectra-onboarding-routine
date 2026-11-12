# Spectra CRM ids and wiring

Non secret CRM identifiers only. First built and verified live on 29 October 2026, when BreakCold was retired; moved onto the Spectra hub on 9 November 2026; checked against live Underfive and Make by the automatic onboarding run on 12 November 2026. If this file and any other note disagree, this file wins, and it only holds if it is updated in the same session as any change.

Not in this file, by design:
- Hook URLs and any other secret. A Make hook URL works like a password: refer to a scenario by name and id, and read the URL in Make when you need it. Never paste one here.
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
Active in Make on 12 November 2026, read live.
| id | Name | Hook id | Does |
| --- | --- | --- | --- |
| 10045732 | SDR Queue (live name in Make: SDR Queue, Underfive to open lead sheet) | 4465293 | Fed by each workspace's `SDR Queue` webhook. Posts each lead event and booking to the hub's ingest endpoints, keyed by the `client` value on the webhook's query string, which the hub resolves to the client. The open lead sheet it once fed is retired |
| 10063115 | SDR Queue, sync health watchdog | none (scheduled) | Watches the SDR Queue sync. Added to this file 12 November 2026 |
| 10113458 | SDR, EOD submitted DM to Ndiya | 4493481 | DMs Ndiya when the SDR's end of day is submitted. Added to this file 12 November 2026 |
| 10063097 | Pipeline, Underfive events to ledger and booking alerts | 4472879 | Ledger writes retired. Booking alert for Hubrig and Shipwright on first booking |
| 9497536 | Pipeline, Calendly booking alerts | 4246945 | Calendly booking alerts for Regency, Outbound Edge, Red Cell, Sozo, Zeal (Spectra event type only). Its ledger booked on write is retired |
| 9474483 | Pipeline, SmartLead interested alerts | 4236960 | SmartLead interested alerts, deduped, one route per client tag. Its ledger interested on write is retired |
| 9875824 | Pipeline, Instantly interested alert (Zeal Media) | 4398759 | Zeal interested alert, campaigns before Spectra excluded |
| 9879995 | Pipeline, Instantly interested alert (Hubrig) | 4400477 | Hubrig interested alert |
| 9474569 | Pipeline, Instantly interested alert (LDC) | 4236999 | LDC interested alert, Spectra test addresses excluded |
| 9528977 | Outbound Edge Calendly | 4260890 | Internal Outbound Edge booking note, no CRM write |

No interested alert scenario exists yet for CJ Digital or Gauge Digital (both Instantly, own account). Each needs one cloned at onboarding step 73 once the hub holds the client's Instantly key.

### Retired, off, do not turn back on
Read live 12 November 2026, every one inactive: 9652484 Red Cell Calendly, 9528957 Regency Calendly, 9634193 NMRK, 9489573, 9507251, 10103844 SDR Queue brief writer (hook 4489427, wrote to the retired sheet).

The Google Calendar booking path, scenario 9923022 with hook 4417003, is off with the rest of the BreakCold set. Hubrig and Shipwright booking alerts now run from 10063097. `identifiers.md` in the client onboarding skill still lists hook 4417003 as a live path and needs the same correction.

History: BreakCold was cancelled on 27 October 2026; every BreakCold scenario (9923022, 9507708, 9484929, 9486028, 9486744, 9486752, 9485016) and its Make keychain are off and stay off.

## Underfive
Read live 12 November 2026. The `SDR Queue` webhook is the one that feeds the hub; its `client` query value is what the hub resolves, and it is recorded here exactly as it is live.
| Workspace (client label) | Workspace id | Agent | SDR Queue webhook id, client value | Pipeline Ledger webhook id (retired, still enabled) |
| --- | --- | --- | --- | --- |
| LDC Strategies | 6a8bf89cf5678264c4287a9a | LDC Email 6a8bf94cf5678264c4287b4b | 6adf67b7f47c0265bc4661d2, `LDC` | 6ae2af68a67c83630b3fcdda |
| Zeal Media | 6ab579e5640a1f27d4badef0 | Zeal Media Solutions Email 6ab9152536e7fd637cc3baef | 6adf67b1f47c0265bc466140, `Zeal Media` | 6ae2af6ba67c83630b3fcdeb |
| Shipwright Consulting Group | 6ab9104136e7fd637cc394a1 | 6ab9154236e7fd637cc3bc35 | 6adf67bdf47c0265bc4661fc, `Shipwright` | 6ae2af6ea67c83630b3fcdfb |
| Hubrig Crew Marketing | 6ab9104236e7fd637cc394b1 | 6ab9155c36e7fd637cc3bd77 | 6adf67c4f47c0265bc46623b, `Hubrig` | 6ae2af71a67c83630b3fce0b |
| Spectra Acquisition (Outbound Edge) | 6aaf2fe99f2dd69ea9f78b5b | Spectra Acquisition Email 6a8ba036f5678264c427756c, not in this workspace as of 29 Oct 2026, move it | Named `Spectra hub`, not `SDR Queue`: 6aefd0af837da83785bc3d36, `Spectra Acquisition`, with `forward=hub` | 6ae2af74a67c83630b3fce19 |
| CJ Digital | 6af300ba94d3c6ec646f09ca | none yet, built at onboarding step 72a | 6af54ceaf150f73dd062da5d, `CJ Digital`, created 12 November 2026 | none, never add one |
| Sozo Labs | 6aaf33db9f2dd69ea9f7a96b | Sozo Labs Email, paused | none, inactive client | none, inactive client |

Gauge Digital (onboarding) has no Underfive workspace yet; it is created at onboarding step 72a and its `SDR Queue` webhook at 72b.

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
| not set, CONFIRM WITH NDIYA | CJ Digital |
| not set, CONFIRM WITH NDIYA | Gauge Digital |

Zeal's Spectra Calendly event type: `ee833f53-8a71-4294-aa64-4890e3eab88a`. Zeal campaigns created on or before 16 September 2026 are excluded; the list lives in scenarios 9875824 and 10063097.

## Pipeline infrastructure Slack channels
Context only. The record of alerts is `get_health`.
- SYSTEM alert posts: C0B7SSURDHN.
- Booking notes and Slack failures on booking alerts: C0BEC4A0WEQ.

Client alert channels are on each client's hub record (`get_client`).
