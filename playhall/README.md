# Playhall

**Game rooms for friends: a host opens a room, some people play, and everyone else pulls up a chair and watches.** It runs in the browser, with chess and Four in a Row so far, a chat for the players and a separate one for the people watching, and leaderboards that keep score over weeks and months.

Most online games give you a match against a stranger. Playhall is closer to a games night: a room that stays open, people drifting in, a queue for the next game, and a running argument in the corner about who should have played d4. The host decides who plays, who waits and what the people watching may say, and the scores carry on from one evening to the next.

![A chess room during a game: the host playing White against Alex, three people watching, and the players' chat on the right](images/01-hero.png)

*Sam (White, and the room's host) against Alex in a game with no clock. Three people are watching; Kai's ring in the watchers' strip means he's asked for the next seat. The players' chat says they can't read what the watchers are saying until the game ends.*

---

## At a glance

| | |
|---|---|
| **Rooms with roles** | A host, players in seats, and everyone else watching; a queue for seats; who plays the next game |
| **Two games so far** | Chess with clocks, draw offers and promotion; Four in a Row. Seven more are planned on a shared set of boards, pieces, cards and dice |
| **Two conversations** | The table, which everyone reads, and the gallery, the watchers' own chat that players can't read during a game |
| **A room that gets out of the way** | Every panel around the board expands, shrinks or hides; three layouts; works on a phone |
| **The host's rules** | Who may sit down, what happens after each game, who may write where, private rooms with a password |
| **Points and leaderboards** | 3 for a win, 1 for a draw; weekly, monthly and all-time tables; every player's record and head-to-heads |

---

## Rooms, players and people watching

Anyone with an account can open a room, pick the game and a time control, and decide whether it shows in the lobby or only to people with the invite link. The lobby shows each room's game, who's playing, how many are watching, and whether there's a free seat.

![The lobby: four rooms with their game, players and watchers, and the form for a new room](images/02-lobby.png)

Inside a room there are three roles. The **host** runs it, and can play too. **Players** are whoever is sitting in the game's seats. Everyone else is **watching**. A watcher who wants to play asks for a seat and joins a queue; when a game ends, the host's rule decides who plays next: the same players, the winner stays and the next in the queue takes the other seat, or both rotate out.

You can come in as a guest with just a name, straight from an invite link. Guests can watch, and play if the host allows it; hosting a room needs an account.

---

## The table and the gallery

Watchers talking to players spoils games like chess, so every room has two chats. **The table** is the players' conversation: everyone can read it, and watchers can write there between games. **The gallery** belongs to the people watching: they can say "he's about to blunder the knight" without the player hearing it, because players can't read the gallery until the game is over. The host can relax either rule.

![The same game from Priya's seat in the gallery: the watchers' strip with Kai asking for a seat, and the gallery chat](images/03-watching.png)

*Priya is watching in the Social layout, which gives the watchers more room. Her gallery chat is open; the Table tab shows three messages she hasn't read yet.*

---

## The games

**Chess** is checked move by move on the server, so nobody can play an illegal move or touch the other side's clock. It has clocks with increments (one minute up to fifteen, or none), draw offers, resigning, promotion, and every way a game can end: checkmate, stalemate, running out of time, repetition and the 50-move rule. The board is lichess's open-source chessground, and it flips round for the player with Black.

When a game ends, the room shows what it meant: the points each player won, their new totals and rank, and their record against each other.

![A finished game: Priya has mated Lee in four moves, and the card shows +3 and +0, their totals and ranks, and their head-to-head](images/04-result.png)

*A four-move mate. Priya takes 3 points and moves to 119, second in the all-time table; she leads Lee 9 to 5 over their games together.*

**Four in a Row** is the first game built from Playhall's shared kit of boards, discs and player colours, which the games still to come will use too, so they look like one set. Hovering over a column shows where your disc would land, discs drop with a short animation, and the winning four are ringed.

![Four in a Row, Jordan to play: the frame with amber and teal discs, and a faint teal disc where Jordan's would land in the first column](images/05-four-in-a-row.png)

---

## A room that gets out of the way

The board always gets the space. Everything around it (the top bar, the players, the watchers, the chat) can be expanded, shrunk or hidden on its own, and three layouts set them all at once: **Default**, **Focus** for when you're playing, and **Social** for when the conversation matters more. Your choice is remembered for each game. Clocks and whose turn it is stay visible however small the panels get.

![The same room in the Focus layout: a thin top bar, players as avatars, the chat shrunk to a rail of icons, and the board filling the screen](images/06-focus.png)

On a phone the board takes the full width and everything else stacks beneath it.

<p><img src="images/07-phone.png" width="300" alt="The room on a phone: the full-width board, the players, and the watchers' strip with the button to ask for a seat"></p>

---

## The host's rules

The host sets how the room works and can change most of it at any time: whether anyone may take a free seat or only people the host seats, the after-game rule, the time control (between games), whether watchers may write at the table, whether guests may play, whether the room shows in the lobby, and whether its games count for the leaderboard. The host can also move a player to watching, hand the room to someone else, or close it.

![The Room rules dialog: seats, the after-game rule, the time control, and the switches for who reads and writes what](images/08-room-rules.png)

A room can also have a **password**. The host and anyone already playing go straight in; everyone else is asked for it, and changing it means everyone else needs the new one.

<p><img src="images/09-private.png" width="480" alt="The prompt for a private room: this room is private, enter the room password"></p>

---

## Points, leaderboards and everyone's record

Every finished game is scored: **3 points for a win, 1 for a draw, 0 for a loss** (in games for three or more, second place will earn 1). The leaderboard ranks players for this week, this month and all time, separately for each game and for all games together, once they've played five games in the period. Only games between people with accounts, in rooms set to count, are scored; a game the host stopped doesn't count.

![This month's chess leaderboard: Sam first with 34 points from 16 games, the podium, every player's record and their last five results](images/10-leaderboard.png)

Each player has a page of their own: points, games played, points per game, win rate and their current streak; how they do as White and as Black; points week by week; how their games ended; their recent games; and their record against everyone they've played.

![Sam's stats: 120 points from 73 chess games, results as White and Black, points per week and how games ended, and head-to-head records](images/11-stats.png)

---

## What's next

Phase 1 adds seven more games on the shared kit: checkers, snakes and ladders, Ludo, a fleet-hunting game, blackjack and Texas Hold'em (with play chips only), and a colour-matching card game. After that come **voice and video** in every room, following the same table and gallery split; **OpenBOR rooms**, where the server runs the OpenBOR beat-'em-up engine and streams the game to up to four players with controllers (Playhall supplies neither the engine nor any games: the admin installs the engine and players import their own); and **sharing a game from your own PC**, so friends can play a local multiplayer game remotely with their own controllers.

---

## How it runs

Playhall is a Node.js server with a Postgres database, packaged as two containers for podman or Docker Compose. People need only a browser. It comes with demo data (eight players, five rooms and two months of games), which is what's in these screenshots.

## Status

Early: phase 1 of five is under way, and Playhall has been run on a home network, not yet in public. CI runs on every push: unit tests of the game rules, the room logic and the scoring; an end-to-end test that plays a game over the real WebSocket and checks the results, stats and private rooms; a browser test that plays on the real board on desktop and phone; loading and removing the demo data; and building and starting the container image.

Source: [github.com/chrisnock-personal/playhall](https://github.com/chrisnock-personal/playhall)
