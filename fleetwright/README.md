# FleetWright

**A self-hosted tool for keeping a fleet of Ubuntu and Fedora servers up to date: it reaches each host through a small agent or over SSH, shows what's pending across the fleet, and installs updates by hand, in booked maintenance windows, or automatically by kind of update, with a record of every package it changes.**

Keeping servers patched is mostly bookkeeping: which hosts have security updates waiting, which can take them tonight, which must wait for a window, which need a reboot afterwards, and what actually changed. A shell loop or a cron job of `apt upgrade` does the installing, but answers none of those questions, and tools that do tend to be large or tied to one distribution. FleetWright is one small service that works the same way for apt and dnf, whether a host runs its agent or is reached over SSH.

Source code: [github.com/chrisnock-personal/fleetwright](https://github.com/chrisnock-personal/fleetwright)

![The FleetWright overview in its default dark theme: summary tiles, a card per group, recent runs, hosts updated per day, and the fleet by operating system](images/01-hero.png)

*The overview after a test run on 9 November 2026 against two real virtual machines, one Ubuntu 24.04 host reached over SSH and one Fedora 44 host running the agent. Every number on these pages comes from those hosts.*

---

## At a glance

| | |
|---|---|
| **Two ways to reach a host** | An agent (a Python service that connects out, so the host needs no open port, and updates itself when told to), or agentless over SSH with a stored key. Both run the same update code |
| **Ubuntu and Fedora** | apt and dnf, with security updates told apart from bug fixes and enhancements, and Fedora's advisory severities |
| **Fleet view** | Totals across the fleet, groups and tags, a filterable host list built for thousands of hosts, and each host's pending updates and history |
| **Installs** | Pick updates by hand, by kind, or all; roll them out in batches with a canary host first; stop after a set number of failures; reboot never, if needed, or always |
| **Auto-install policies** | Install chosen kinds of update (security above a severity, kernel, bug fixes, enhancements) on a daily, weekly or monthly window, each with its own on/off switch |
| **Booked installs** | One-off installs for a set window, optionally needing an admin's approval |
| **Notifications** | Email, webhook or ntfy when a run fails, a host stays unreachable, a booking needs approval or a critical update appears, batched so an outage is one message |
| **History** | Every run, every host's output, and every package version change, kept in PostgreSQL |
| **Secure by default** | HTTPS from the first start, agents that check the server's certificate, pinned SSH host keys, encrypted keys, no default password, three roles and an audit log |
| **API and CLI** | Everything the pages can do, as JSON with the same roles, and a command-line client over it, for scripts, CI pipelines and AI agents; per-person tokens that can be limited to read-only |
| **Backups** | A daily encrypted backup of the database and certificate that the server itself can't read, a restore script, and a way to change the encryption key |

---

## The fleet at a glance

The overview answers the first question of a patching day: what's waiting, and where. It counts hosts with updates, hosts with security updates, hosts waiting for a reboot and hosts that can't be reached, then shows a card per group. Each card has a bar split into up to date, updates pending and security updates pending, so a group that needs attention stands out without opening it. Below are the latest runs, how many hosts were updated each day for two weeks, the mix of operating systems, and a list of hosts that need attention: unreachable, failed in the last day, waiting on a critical update, or waiting for a reboot.

The host list is built for size rather than for two hosts. It filters by status (needs updates, security, reboot, unreachable, not scanned, installing updates by itself, agent out of date), group, operating system, connection and tag, searches host names, addresses, package names and advisory IDs, and pages through the results. Tick any number of hosts to scan them, install security updates or everything, schedule an install, reboot, tag them, move them to another group, update their agents, or stop their own automatic updates. In a load test with 5,800 hosts and 377,000 pending updates, the host list, a host's page and a package search each took under 30 ms to load.

![The host list: status filters with counts, group, OS, connection and tag filters, bulk actions, and the two test hosts with their pending and security counts](images/02-hosts.png)

## Every host's updates and history

Each host has a page with what it is (OS, kernel, address, how FleetWright reaches it), its pending updates, and what has been done to it. Pending updates show the installed and available versions and what kind of update each one is. On Fedora that comes from the distribution's advisories, so FleetWright shows the advisory ID and its severity: of the 84 security updates the Fedora test host started with, the list put the critical `ca-certificates` update first, followed by the important ones.

![The Fedora host's pending security updates, each with its installed and available version, its severity and its Fedora advisory ID](images/03-host-fedora.png)

Updates can be ticked and installed straight away, or ticked and scheduled for later, in which case FleetWright installs exactly those versions: if a newer release lands before the window, it's skipped and shown in the run rather than slipped in. Each host also has a "never install" list. On the Ubuntu test host, `git*` was held, so after everything else was installed the two `git` packages were still pending, marked as held, and every run skipped them. Below that is every package version FleetWright has changed on the host, including the ones the package manager pulled in as dependencies, such as new kernel packages.

![The Ubuntu host after its updates: two held git packages still pending, the list of package changes, the policies that apply to it, and its history](images/04-host-ubuntu.png)

FleetWright also checks whether something else on the host installs updates by itself. Ubuntu runs `unattended-upgrades` out of the box, and Fedora hosts may run `dnf-automatic`. Either one can install packages in the middle of a FleetWright run, and those changes would then be recorded as FleetWright's. Each scan reports what it finds, the host page says so with a *Turn off* button, and such hosts appear under "Needs attention" on the overview. Turning it off is reversible: on Ubuntu FleetWright adds one small apt setting file, and on Fedora it disables the timer.

![An Ubuntu host whose stock unattended-upgrades installs security updates daily: the warning above its pending updates, with a Turn off button, and the same fact in the host details](images/05-auto-updates.png)

*This screenshot, and those under Notifications and Agents below, come from a second test on 23 November 2026, with the agent installed in Ubuntu 24.04 and Fedora 44 containers.*

## Runs: batches, canaries and live output

Every install, reboot or scan that touches hosts is a run, split into one task per host. A run can go in batches: a fixed number or a percentage of hosts at a time, with one host on its own first as a canary. FleetWright starts the next batch only when the current one has finished, stops the run when too many hosts have failed, and starts no new host after a maintenance window closes. A run can be paused after the current batch, aborted, or re-run for just the hosts that failed.

Output streams into the browser as it happens, whether the host is reached over SSH or through its agent. This run applied the "Security nightly" policy to both test hosts, Fedora first as the canary:

![A run in progress: the canary batch running on the Fedora host, batch 1 waiting, and dnf's live output as it downloads 43 packages](images/06-run-live.png)

Every install first refreshes the package lists and rescans, so it installs what is pending now rather than what was pending at the last scan. It then installs, works out which package versions really changed, and checks whether the host needs a reboot. With "reboot if needed", the Fedora host rebooted after its 43 updates. FleetWright waited for the agent to reconnect from the new boot before marking the host done and moving on to the Ubuntu host, which took 18. A second run installed everything else on the Ubuntu host and rebooted it over SSH, then waited for the host to come back and rescanned it.

![The finished run: canary and batch 1 done, 61 packages changed in total, and the Fedora host's plan of 40 updates with their installed and new versions](images/07-run-done.png)

## Auto-install policies and booked installs

The Schedules page puts everything that will happen by itself in one place: a calendar of the next seven days, the auto-install policies, and one-off installs booked for a window.

![The Schedules page: the next seven days with policy runs and booked installs, three policies with their on/off switches, and the booked installs below](images/08-schedules.png)

An **auto-install policy** says which kinds of update to install, on which hosts, and when. The kinds are security updates (optionally only those at or above a severity), kernel updates, bug fixes and enhancements. Package patterns can be added to always include or never install. A policy applies to groups, tags or every host, minus hosts with an excluded tag. It runs daily, weekly on chosen days or monthly, in a window of a set length, and sets the hosts per batch, the canary, when to stop and when to reboot. Each policy has an on/off switch, and while you edit it, it shows what it would install if it ran now. For the "Security nightly" policy on the test hosts, that was 58 updates on 2 hosts.

![The policy editor: security updates of important severity and above plus openssl, on the build and web groups except hosts tagged no-auto, daily at 02:00 for two hours, 10% of hosts at a time with a canary, rebooting if needed](images/09-policy.png)

A **booked install** runs once, in the window you choose: hosts ticked on the Hosts page or updates ticked on a host, plus whole groups or tags. It installs everything, chosen kinds, or the exact updates you picked. You also choose what happens if the window closes mid-run, how many hosts at a time, and whether to reboot. A booking can require an admin's approval. If nobody approves it before its window opens, it's marked missed rather than run late. Two were booked for the test: three named updates for the Ubuntu host on Thursday evening, and an approval-required "everything, then reboot" for the build group on Saturday night.

![The booking form with both test hosts picked, security updates chosen, a two-hour window, one host at a time and reboot if needed](images/10-book.png)

## Notifications

Without notifications, a policy run that fails at 2 a.m. goes unnoticed until someone opens the dashboard. FleetWright can send a message by email, to a webhook or to [ntfy](https://ntfy.sh) (push notifications on a phone) when:

- a run fails, stops early or misses its window, with the hosts that failed and why
- a host has been unreachable for a while (30 minutes by default), and again when it's back
- a booked install needs an admin's approval
- a new critical security update appears on a host
- optionally, a run finishes without problems

Each channel picks which of these it wants and has a *Send test* button. Messages that pile up for a channel within a few seconds go out as one, so an outage across hundreds of hosts is one message, not hundreds. If a channel can't be reached, FleetWright retries for about four hours and shows the error on the channel. Webhooks get JSON that Slack and Mattermost accept as it is, and can be signed with HMAC-SHA256 so the receiver can check they came from FleetWright. SMTP passwords, signing secrets and ntfy tokens are stored encrypted.

![The Notifications settings page with three channels, an ntfy topic for on-call phones, an ops email address and a team chat webhook, each with the events it receives and when it last sent](images/11-notifications.png)

In the test, an SSH host was added at an address that doesn't answer, and a security-update run was started for it and an Ubuntu host. The Ubuntu host updated, the other failed, and a minute later (the test's grace period) the host was reported unreachable. Both messages reached the ntfy topic as high-priority alerts, each linking to the page concerned:

![Two ntfy messages: db1 is unreachable, and Run #6 Security updates (retry): 1 host failed, both naming the connection timeout and linking to FleetWright](images/12-ntfy.png)

## Agents and agentless hosts

The **agent** is a small Python service that runs on the host as root under systemd. It connects out to FleetWright over a WebSocket, so the host needs no open port and can sit behind NAT. It scans for updates on its own timer, staggered across the fleet, and runs only FleetWright's update operations, never arbitrary commands. To add hosts, an admin creates an enrolment token that puts new hosts in a group with tags, optionally limited in uses and lifetime, and runs the one-line install command it shows on each host. If a machine is enrolled again, for example after reinstalling the agent, FleetWright recognises it by its machine ID, hostname and current boot, and keeps its history. A cloned machine that still has its template's machine ID gets a host of its own instead, and both host pages point out the shared ID.

Agents also **update themselves**. When the server has a newer agent, each host page says so, and Settings → Agents has an *Update N agents* button. An update runs like any other run: one host first, then a tenth of them at a time, stopping at the first failure. Each agent downloads its two files over its own verified connection, checks each one against the SHA-256 hash the server sent, and refuses an agent file that won't even compile. It then swaps the files in and restarts. The update counts as done only when the agent reconnects running the new version. In the test, an updated agent was back in 11 seconds, and a deliberately broken one stopped the run at the first host.

![The Agents settings page: the agent version this server hands out, matched by every enrolled agent, an enrolment token that puts new hosts in the web group with the prod tag, and the form for creating another](images/13-agents.png)

**Agentless** hosts need only SSH and passwordless `sudo` for `python3`. FleetWright connects with a stored key, sends its update script on standard input and streams the output back, running up to 50 hosts at once per worker. Hosts are added in bulk, one `name address[:port]` per line. Both kinds of host appear side by side everywhere else.

## Secure by default

FleetWright serves **HTTPS from the first start**, with a self-signed certificate naming its public address. The HTTPS settings page shows the certificate's names, expiry and SHA-256 fingerprint (to check against the browser's warning). You can upload your own certificate and key, which FleetWright checks before using, keeping the previous pair in case the new one won't load, or generate a fresh self-signed certificate. Session cookies are HTTPS-only.

![The HTTPS settings page: the current self-signed certificate with its names, expiry, fingerprint and public-key pin, and the forms to upload a certificate or generate a new one](images/14-https.png)

**Agents check the server before trusting it.** The install command pins the server's public key, so a host can't be served a different install script, and the agent trusts exactly the certificate it enrolled with. Because it checks during the TLS handshake, its token is never sent to anything else. When the server's certificate is replaced, FleetWright first sends the new one to every connected agent over its existing connection. In the test, the agent picked up the new certificate, the server restarted with it, and the agent reconnected by itself. Agents refuse plain HTTP unless they are explicitly enrolled with `--insecure`.

**SSH host keys are pinned.** The first connection records the key a host offers; after that, only that key is accepted during the SSH handshake. A host that offers a different key is refused before FleetWright logs in or sends anything, and its page says so:

![The Ubuntu host's page after FleetWright refused a connection because the host offered a different SSH host key from the pinned one](images/15-hostkey.png)

**Backups** are encrypted with [age](https://age-encryption.org) to a public key, so the server can make them but can't read them; the private key stays on another machine. Each daily backup holds the database and the HTTPS certificate and key, so agents keep trusting a server restored from it. In the test, both volumes were wiped and an empty FleetWright started: its agent refused the new certificate. After `restore.sh`, the hosts, history and users were back, and the agent reconnected by itself. The key that encrypts stored SSH keys and notification secrets can be changed: FleetWright re-encrypts everything under the new key at its next start, and warns admins if the key changed without the old one being given.

There's no default password: the first admin needs a setup code printed in the server's log, and everyone else gets a single-use setup link. There are three roles: admins change everything, operators run updates and edit schedules, and viewers look. SSH private keys are stored encrypted; agent tokens, enrolment tokens and setup links are stored only as hashes. Every change goes into the audit log.

![The Users settings page: an admin, an operator and a viewer, each with their role and last sign-in](images/16-users.png)

## An API for scripts and AI agents

Everything the web pages can do is also available as a JSON API, with exactly the same roles. Under the hood, every action a person can take, from scanning a host to approving a booking, is declared once with the lowest role allowed to do it, and both the pages and the API go through it. Tests check that no page checks a role on its own and that every action has an API endpoint, so the two can't drift apart.

Access is by API token, made on each person's Account page. A token acts as that person, with their role or a lower one. A dashboard or an AI assistant can get a viewer token that can only read, and a CI pipeline an operator token that can start runs but not manage users. Tokens are shown once and stored as hashes. They can expire, can be revoked, and show when they were last used. Everything a token changes appears in the audit log under the person's name and the token's.

![The Account page with four API tokens: a viewer token for an AI assistant that expires in a month and hasn't been used, an operator token for a CI pipeline, a viewer token for a Grafana dashboard, and a revoked script token](images/17-account.png)

Starting a run returns its number at once, and a stream of server-sent events follows its hosts and logs live, as the run page does. A pipeline that retries a request after a timeout sends an idempotency key, so the retry doesn't start a second run. Lists page through results with a cursor and take the same filters as the hosts page, and each token is rate-limited. FleetWright serves its own API reference, where each endpoint is described by the action behind it and says which role it needs.

![The API reference FleetWright serves, open at the runs section: start a run, list runs, a run with its batches, one host's task, pause, resume, abort, retry, and following a run as server-sent events](images/18-api-docs.png)

For the terminal there's **`fw`**, a command-line client over the same API, with the same limits as its token. It knows hosts, groups and keys by name, and asks before changing hosts unless told `--yes`. `--dry-run` shows what a run would install without starting it, and `--wait` blocks until the run ends and fails if any host did, which suits a CI pipeline. Every command can answer in JSON, and the exit codes say whether it worked, wasn't allowed or found nothing, so scripts and AI agents can rely on them. `fw login` shows the server's certificate fingerprint to compare with the HTTPS settings page. From then on it accepts only that certificate, and checks it before sending the token. In the test run, installing security updates on one group, then a scan that included a host that couldn't be reached:

```
$ fw run apply --group load-dublin --kinds security --yes --wait
Started run #2.
Run #2 done (1 done, 0 failed, 1 packages changed)
$ fw run scan ghost load-ssh-0000 --yes --wait
Started run #3.
Run #3 done: 1 host failed (1 done, 1 failed, 0 packages changed)
  ghost: Can't connect to 10.255.255.1:22: timed out
$ echo $?
1
```

## Light theme and small screens

The interface is dark by default and has a light theme, remembered per browser. It shares its look with [Skyfarer](../skyfarer/README.md).

![The overview in the light theme](images/19-light.png)

It works at phone width, with the tiles, cards and tables stacking into one column.

<img src="images/20-phone.png" alt="The overview at phone width, with tiles and cards stacked" width="300">

On first start, before any accounts exist, FleetWright asks for the setup code from its log:

<img src="images/21-first-run.png" alt="The first-run page asking for the setup code, a name, an email and a password" width="640">

---

## How it runs

Three containers with Docker Compose or Podman: PostgreSQL, the web app (dashboard and agent gateway, in Python with FastAPI) and one or more workers. Postgres is also the job queue, so more workers can be added without any other moving parts. The agent needs `python3` and `python3-websockets`, both packaged on Ubuntu and Fedora.

## Status

A first version, built and tested on 9 November 2026 against Ubuntu 24.04 and Fedora 44 virtual machines, one reached over SSH and one through the agent. Tested end to end: scans, picked, policy and booked installs, a canary rollout with a reboot through the agent, a reboot over SSH, held packages, a refused SSH host key, pinned agent enrolment and certificate replacement with an agent connected.

Added and tested in November 2026, with real agents in Ubuntu and Fedora containers:

- detecting and turning off `unattended-upgrades` and `dnf-automatic`
- agent self-update, including a broken update stopped at the first host
- keeping cloned machines apart at enrolment
- notifications, delivered to a real SMTP server, ntfy server and webhook receiver, including a retry after the receiver had been down
- encrypted backups, and a full restore onto wiped volumes with an agent reconnecting afterwards
- changing the secret key, and what happens when it's changed without the old one
- the command-line client against a real server: certificate pinning (including refusing a changed certificate), dry runs, waiting on runs, exit codes and role limits
- the API: 66 end-to-end checks against SSH hosts, covering every role limit, the error responses, paging, idempotency, the live run stream and the rate limit, plus every web form submitted again to confirm nothing changed for people using the pages

A load test ran 500 simulated agents and 300 SSH hosts against one web process and one worker. All the agents connected and scanned within a minute. Installing everything on all 800 hosts, 100 at a time, took 96 seconds with no failures. With 5,800 hosts and 377,000 pending updates, every page loaded in under 90 ms. Getting there took four fixes the test turned up; the slowest page had taken 1.4 seconds.

Unit tests cover the package-list parsing, update selection, schedules and batching, agent updates, telling clones from reinstalls, composing notifications, changing the secret key, and the rule that pages and API share one permission check. What could come next is on the [backlog](https://github.com/chrisnock-personal/fleetwright/blob/main/BACKLOG.md).
