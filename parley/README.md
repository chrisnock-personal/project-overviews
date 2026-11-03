# Parley

**An IP intercom for crews who talk while they work: talk groups and direct calls in a web browser, with live transcripts, group chat and call recording.** It runs as a handful of containers on one server, and the people using it need nothing but a browser.

Live productions, events and operations rooms run on talkback. The director talks to the cameras, the floor manager to the producer, everyone to everyone when it matters. Dedicated intercom hardware does this well and costs accordingly; a conference bridge is cheap but has no idea who should hear whom. Parley sits between the two. Each person logs in from any browser (a desk PC, a laptop, an iPad), sees the groups they belong to and the people they can call, and joins as many groups at once as their job needs. What was said in a group is written down as it's said, typed messages sit beside it, and calls can be recorded to a compliance recorder.

![Parley in use: the Floor Manager is in the Production group while the Director talks. Its conversation mixes what was said with what was typed, and the Studio Floor transcript can be read without joining](images/01-hero.png)

*The Floor Manager (extension 2007) during a countdown. Production is joined and recorded, the Director is talking (the ring round the Director's avatar), and the conversation mixes what people said with what they typed. Studio Floor and All Call are favourites, so they stay open even though they aren't joined.*

---

## At a glance

| | |
|---|---|
| **Talk groups** | Open-mic or push-to-talk groups, as many joined at once as you like, each with its own talk, listen and volume controls; who's in each group and who's talking, before you join |
| **Direct intercom** | Press a key and the other person's intercom answers by itself; hold and resume; call anyone in the directory |
| **Conversation** | Live per-speaker transcripts and typed group chat, in one timeline, readable without joining; typed messages read aloud if you want them |
| **Recording** | SIP-REC (RFC 7866) to any compliance recorder, per person, with participant metadata; a REC tag wherever it applies |
| **Admin console** | Live registrations and calls, groups, people and their keys, recording and retention settings, health, audit log, database backups |
| **Network** | Its own SIP core in TypeScript, media anchored for routed networks, WebRTC bridged to plain RTP, HTTPS and SIP digest authentication |

---

## Talk groups

A group is a party line: everyone in it hears everyone else who's talking. Groups are open mic (talk freely, mute when you need to) or push to talk (hold the big button). You can be in several at once, the way a floor manager listens to Production and Studio Floor together, and each group has its own talk button, listen mute and volume, so one noisy group can be turned down without leaving it.

Each group shows who's in it right now and who's talking, whether or not you've joined, so you can see where the conversation is before you walk into it. Star a group to keep it open as a full card; the rest fold into small tiles until you need them. Click anyone's avatar to see who they are, whether they're online or talking, which of your groups they're in, and to call them.

![A person card opened from the Production group: the Director, online and talking, in Production, with a button to call them](images/02-person-card.png)

Muting and push-to-talk happen in your own browser, so pressing the button takes effect at once, with no signalling round trip and nothing for the server to get wrong.

---

## Direct intercom

The intercom panel holds your keys: one per person you call often. Press a key and the other person's intercom answers by itself, so you're talking straight away, the way a studio intercom works. It's a real call: either side can put it on hold and resume it. Anyone not on your keys can be called from the directory.

![A direct call from the Director, answered automatically: connected for 7 seconds, recorded, with mic, hold and hang-up controls](images/03-direct-call.png)

---

## Read what was said

Switch on transcription for a group and everything said in it is written down as it's said, attributed to the person who said it. Switch on chat and members can type to the group as well: for the things you'd rather not say over the air, or that need to stay on screen ("running order change", "second guest is in reception"). With both on, spoken and typed lines share one timeline, each marked *said* or *typed*, and a filter shows just one kind.

Both are readable by anyone with the group, joined or not, so someone walking back to their desk can catch up on what they missed without listening to it again. A *new* marker shows where there's something unread.

For people whose eyes are busy, a group's typed messages can be **read aloud**. Each person switches it on per group, and it follows them to whichever device they log in on. It only reads groups you've joined, never your own messages and never what was said before you joined. Messages are queued so they don't talk over each other, the group's audio dips to 30% while one is read, and reading waits while you're holding Push To Talk. It uses the browser's own voice; voice, speed and volume are set per device.

![Read aloud on in Production: the Producer's message is being read, one more waits, with Skip and Stop; beside it, the read-aloud settings in the profile menu](images/04b-read-aloud.png)

*The Producer's typed message being read (outlined), the Director's waiting, and the group's read-aloud settings in the profile menu: voice, speed, volume, and whether to say who sent it, turn the group down, and wait while you talk.*

![The Production conversation with spoken and typed lines, and Studio Floor's transcript read without joining](images/04-conversation.png)

Transcription runs on your own server with whisper.cpp, sharing one GPU (or CPU) speech model. It listens to each member's own voice, so lines are attributed correctly even when two people talk over each other, and it never sends silence or a muted microphone. A pitch check keeps music, tones and other held notes out of both the transcript and the "who's talking" ring. In these runs each member's audio reached the transcriber within about 60 ms of them joining. It isn't perfect: the first word of an utterance is sometimes lost ("Standby everyone" came out as "and by everyone"), as the screenshots show.

---

## Recording

Turn on recording for a person and every call they make, direct or group, is recorded by any SIP-REC compliance recorder (RFC 7865/7866). Parley takes a copy of the media from its media relay, so nothing is added to the call's audio path, and the recorder gets one labelled stream per party plus metadata saying who they are. Recording fails open: if the recorder can't be reached, the call goes ahead unrecorded and the failure is logged.

Everyone can see it happening. A **REC** tag shows in the top bar when your calls are recorded, on a direct call while it's being recorded, and on a group while anyone in it is.

![A Parley group call as received by CallNest, a SIP-REC recorder: the Director's leg into Production, 46 seconds, complete with no packet loss](images/05-recording.png)

*The Director's leg into Production, recorded live by CallNest, another of my projects. The participants come from Parley's metadata, and each party is on its own channel.*

---

## Desk, laptop, iPad, phone

The client is a web page: nothing to install, and a person's groups and keys follow them to whichever device they log in on. It has two looks, a graphite **Panel** theme and a warmer, rounder **Warm** one.

![The same screen in the Warm theme](images/06-warm-theme.png)

On a phone the cards stack into one column, and a group's conversation opens full screen.

![On a phone: keys and groups, a joined group with push to talk, and the Production conversation full screen](images/10-phone.png)

*Keys and groups, a joined group with push to talk, and the Production conversation full screen, at 390 px wide.*

---

## The admin console

Administrators run Parley from a separate console with a sidebar: **Live**, **People** and **Groups**, and for admins **Settings**, **Health**, **Audit** and **Backups**. **Live** shows what's happening right now, straight from the SIP core's own state: who's online, each group in use with who's in it and who's talking, direct calls, and what's being recorded.

![The console's Live page: 5 of 10 people online, Production in use with the Director talking, a direct call between the two cameras, one call being recorded](images/07-console-live.png)

**People** is everyone with a login, in a list you can search, showing their groups, intercom keys, whether they're online and whether they're recorded. Pick someone to change their name or extension, whether they can log in and whether their calls are recorded, which groups they have and which people they can call with one press, or to reset their password.

![The People page with the Floor Manager selected: can log in and record calls switched on, three groups and three intercom keys](images/08-console-people.png)

**Groups** works the same way: each group with how people talk in it (open mic, push to talk), whether it's transcribed or has chat, how many people have it and how many are in it now. The selected group shows who has it, so a group can be given to several people in one place, and who's in it at the moment.

![The Groups page with Production selected: open mic, transcribed and with chat, five people who have it, two in it now](images/09-console-group.png)

**Settings** holds the recording server and how long transcripts and chat are kept. **Audit** is a searchable, plain-language history ("Floor Manager: record calls turned on", "Joined Production") filtered by time, person, kind of event and subject, and exportable as CSV. **Health** shows the server's load, the database, whether the SIP core is up, and each container's logs. **Backups** downloads and restores database backups, runs maintenance, and purges or archives old rows. Console users are viewers, editors or admins, and actions they can't take are greyed out with the reason.

![The Health page: host load, database size, SIP core up, table sizes and the SIP core's log](images/09b-console-health.png)

---

## Built for real networks

Parley's SIP core is its own code, written in TypeScript on the drachtio SIP engine, not a general-purpose PBX configured into shape. It handles registration, direct calls and group calls, and authenticates every registration and every call with SIP digest authentication, checking the caller is who they say they are.

Media goes through rtpengine, so calls work across routed networks and subnets where devices can't reach each other directly, and it bridges the browsers' encrypted WebRTC audio to plain RTP for the group mixer (FreeSWITCH's conference module, used purely as a mixer). The browser side runs over HTTPS with Parley's own local certificate authority, which a device trusts once, iPads included.

---

## How it runs

Seven containers under Podman (or Docker) Compose on one Linux host: the SIP core, rtpengine, FreeSWITCH, the transcriber, the API with PostgreSQL, and nginx serving the console and the client over HTTP and HTTPS. Transcription uses a whisper.cpp server, which can be shared with other services on the same machine.

---

## Status

Parley is a working prototype in active development. Every feature shown here was exercised for this page with real calls: a browser client logged in as the Floor Manager, four scripted crew members speaking into the groups, and recordings going to a real SIP-REC recorder. Features are tested by hand and with scripted SIP calls as they are built; there is no automated test suite yet. Next on the list are keyboard hotkeys, audio device selection, saved presets and an admin remote mute.
