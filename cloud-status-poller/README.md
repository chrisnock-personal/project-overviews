# Cloud Status Poller

**A small service for support and operations teams that watches the public status feeds of AWS, OVHcloud, Microsoft Azure and Google Cloud, raises a Zendesk ticket when an incident touches a region you care about, and moves the ticket to pending when the provider says it's over.**

When a cloud provider has a problem, the first place to say so is usually its status page, and nobody is paid to sit and refresh four of them. Customers notice first, or a support engineer finds out by chance. Each provider also publishes differently: one posts every update as a separate entry, another rewords its entries, another just removes them when the event ends. Cloud Status Poller reads all four on a schedule, works out which incidents matter to you, and turns each one into a single ticket with a record of what happened and when.

![The dashboard during a run: eight open incidents across OVH, Google Cloud and AWS, with the poll log below](images/01-hero.png)

*The dashboard at the end of a 50-minute run on 4 November 2026, with real feeds from all four providers. The AWS rows are real: one long-running event in each of two Middle East regions, with 23 and 13 updates on a single ticket each. Entries starting "Demo:" were scripted for this page, so that tickets could open and close during the run. The two OVH rows at the top are real maintenance notices, dated by when the work is scheduled.*

---

## At a glance

| | |
|---|---|
| **Four providers** | AWS, OVHcloud, Azure and Google Cloud, each read in the way its feed needs; any of them can be switched off |
| **Filtering** | By region, using each provider's own region names, and optionally by keyword |
| **Zendesk tickets** | One ticket per incident with the provider, region, link and timeline; provider updates added as internal notes; moved to pending when the incident ends, for a person to close |
| **Failure handling** | Zendesk failures are retried on the next poll; a cycle that fails is recorded with its error; an unreadable feed closes nothing |
| **Dashboard** | Read-only web view: summary tiles, incidents, search, filters by state, provider and date, and a log of every poll |
| **History** | Every incident and update is kept in PostgreSQL, with the provider's own timestamps, ready for SQL reporting |

---

## From a status feed to a ticket

Every five minutes by default, the poller reads each provider's feed and compares it with what it already knows. A new incident in a watched region gets a Zendesk ticket straight away. An incident that was already over the first time the poller saw it is recorded for history, without raising a ticket nobody needs to act on. When an open incident ends, the ticket gets an internal note and moves to **pending**, and closing it stays a human decision.

![Twelve scripted incidents across all four providers: some open, most resolved, with ticket numbers, update counts and detected and resolved times](images/02-lifecycle.png)

*The twelve scripted incidents at the end of the run. Seven went from open to resolved, four are still open, and one (`[SBG7]`) was already over when first seen, so it has no ticket. The Load Balancer incident named two regions and shows both. Updates counts the later entries attached to one incident.*

A ticket carries what the person picking it up needs. This is one the poller raised during the run, as sent to the Zendesk API:

```
Subject:  [GCP] europe-west2 – Demo: Cloud SQL instances in europe-west2 are slow to restart
Priority: high    Tags: cloud-status, auto-raised

Provider: GCP
Region:   europe-west2
Link:     https://status.cloud.google.com/incidents/demoGcp001

--- Timeline ---
Incident published: 2026-11-04 13:00 UTC
Last updated:       2026-11-04 13:00 UTC
Detected by poller: 2026-11-04 13:00 UTC

--- Detail ---
Cloud SQL: We are investigating slow restarts of Cloud SQL instances in europe-west2.
```

Priority, tags and the Zendesk group that receives the tickets are all settings. Across the run the poller raised 15 tickets, added 6 internal notes and moved 7 tickets to pending.

---

## Four providers, four ways of publishing

The useful part of this project is the difference between the feeds. Treating them all the same produces wrong tickets, so each provider has its own rules, based on what its feed really publishes.

| Provider | Source | How one incident is recognised | How it knows the incident is over |
|---|---|---|---|
| **OVHcloud** | RSS history feed | One ID for the incident's whole life | The status word on the newest update: Resolved, Completed or Postmortem |
| **AWS** | One RSS feed per region | Every update is a separate entry, so entries in a region belong to the open incident until one resolves it | `[RESOLVED]` or "Service is operating normally" in the title |
| **Azure** | RSS feed of events in progress | Titles get reworded, so any change counts as an update to the open incident in that region | A title starting "Mitigated" or "Resolved", or the event leaving the feed |
| **Google Cloud** | Structured `incidents.json` | One stable ID | The incident has an end time |

AWS shows why this matters. Its real feed for the Middle East (UAE) region held 24 entries about one event that began on 1 March 2026, and the poller turned them into one incident and one ticket with 23 updates. Its text also can't be trusted for keywords: a real update says power "has not yet been restored", which is not a resolution, so the poller only looks at the provider's own markers. In the run, a scripted AWS event went through exactly that wording and stayed open until its `[RESOLVED]` message.

Azure's feed only lists events in progress, so an event can end by disappearing. The run covered both endings: a scripted West Europe event was reworded three times and finished with "Mitigated", all on one ticket, and a scripted Azure Front Door event simply left the feed and was closed on the next poll. That check needs a successful read of the feed, so a network failure can't close every open incident at once.

![Google Cloud incidents: two scripted, seven real, from a London region disruption to a three-week network issue, with their real start and end times](images/03-providers.png)

*Google Cloud's real incident history, with the provider filter on. Seven real incidents were already over when first seen, so they are recorded without tickets, with real durations from a two-hour event to one that lasted three weeks. The two rows at the top are scripted.*

---

## Choosing what you see

**Regions** come first, using each provider's own names. AWS uses codes such as `me-central-1`, and Google Cloud location IDs such as `europe-west2` or `global`. OVH codes match as whole words, so `GRA` finds `[GRA]` and `GRA11` but not the "gra" in "upgrade" or "migrated". Azure names match exactly, so `West US` isn't `West US 2`, and `Central US` isn't `South Central US`. Include `Multiple Regions` to catch broad Azure events, or use `*` for all of them.

**Keywords** are an optional second filter. With one AWS region, three OVH regions and two Google Cloud locations watched, the real feeds gave 10 incidents with no keywords set, 4 with `Compute,Storage`, and 6 with `incident`. That last one drops OVH's maintenance notices, but also AWS, whose entries don't use the word, and the filter applies to every provider. Entries that don't pass are never stored or ticketed.

---

## The dashboard

The dashboard is a read-only web page that refreshes every 30 seconds. It runs only `SELECT` queries, so it's safe to leave on a wall screen or give to people who shouldn't be able to change anything. Four tiles show open incidents, the total tracked, tickets raised in the last 24 hours, and how the last poll went.

Search covers titles, summaries, regions, providers and every stored update, not just the newest one. Here, the name of a single AWS Availability Zone finds the two long-running events it was mentioned in:

![A search for the Availability Zone mec1-az2 finding the two AWS incidents that mention it](images/04-search.png)

Incidents can be filtered by state and provider, and by Today, 7, 30 or 90 days, or a custom date range. The date filter applies to the poll log too, which is why the log is empty for this period: this instance didn't exist yet.

![The dashboard filtered to 1 October to 3 November 2026, showing five real incidents from OVH and Google Cloud](images/05-range.png)

The page follows the browser's light or dark setting. On a phone the tiles stack and the tables scroll sideways.

![The dashboard in dark mode](images/07-dark.png)

---

## When things go wrong

An unattended service has to cope with the things it depends on failing, so the run included three deliberate faults. The Zendesk here was a stand-in, set up to refuse requests for a few minutes at a time.

**Zendesk refuses new tickets.** For three minutes the stand-in answered every new ticket with an error. Two incidents were affected, and neither was lost: they stayed unrecorded, the poller tried again each cycle, and both tickets went through as soon as the stand-in recovered. No duplicates were created.

**Zendesk refuses updates.** During a second fault of three and a half minutes, two incidents ended. Their tickets stayed open and the poller kept trying until both moved to pending, three to four minutes after the provider had called them over.

**The poll itself fails.** For one cycle the `incidents` table was renamed, as a bad migration might. The poller recorded the failure with its error, carried on, and the next cycle ran normally. No incidents were lost or duplicated.

![The poll log: tickets created and resolved over twenty minutes, the two Zendesk faults, and one failed cycle in red with its database error](images/06-poll-log.png)

*The middle of the poll log. Reading from the bottom: the first fault ends with 2 tickets created at 13:13, the second with 2 resolved at 13:17, and the failed cycle shows at 13:27 with its error. An open count of -1 marks a cycle that couldn't read the database.*

The poller also treats feed content as untrusted, because it ends up on a web page. A scripted entry with a `javascript:` link and a `<script>` tag in its text was stored with the link replaced by the feed's own address and the tag removed.

For checking a setup, `--dry-run` reads the feeds and logs what it would do without calling Zendesk, and `--once` runs a single poll for use from cron. A missing token, no configured regions or an invalid Zendesk group ID stops it at startup with a clear message.

---

## Reporting

Everything is in PostgreSQL, so reporting is a SQL query. The feed's own timestamps are kept apart from the time the poller stored each row, which separates how long an incident lasted from how long the poller took to notice.

```sql
SELECT provider, AVG(resolved_at - detected_at) AS avg_resolve_time
  FROM incidents
 WHERE state = 'resolved' AND resolved_at > detected_at
 GROUP BY provider;
```

---

## How it runs

Three containers under Docker or Podman Compose: PostgreSQL 16, the Python poller, and the Flask dashboard, which listens on localhost by default. Settings live in one `.env` file, with Zendesk credentials, regions per provider, keywords and the poll interval, and incident history survives restarts in a database volume.

---

## Status

A small working tool, written for one job. It has no automated test suite yet. Its matching rules were checked with 38 one-off test cases, then against the live feeds of all four providers.

The screenshots come from a run on 4 November 2026: 50 polls, one a minute instead of the default five, over about 50 minutes. The AWS, OVH and Google Cloud feeds were real and live, and scripted entries marked "Demo:" were added to them. Tickets went to a stand-in for the Zendesk API that recorded each request, not to a real Zendesk account, and the two ticket faults above happened on that stand-in only.

Some limits to know about:

- **Azure hasn't been tried on a live event.** Its feed was empty throughout, so Azure handling is built from its published incident history and reports of how the feed behaves, then tested with scripted entries in that format. Real entries may differ.
- **Events in the same region can merge.** AWS and Azure give no event ID in their feeds, so two separate events open in one region at the same time share one incident and one ticket.
- **OVH maintenance notices count as incidents.** They raise a ticket like any other entry and are dated by their scheduled time, so they can show a future date. A keyword filter can leave them out.
- **Feed failures are easy to miss.** A feed that can't be reached doesn't count as a failed poll: the dashboard still shows `ok`, with fewer entries seen.
