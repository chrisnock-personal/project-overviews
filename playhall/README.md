# Playhall

**Game rooms for friends: a host opens a room, some people play, and everyone else pulls up a chair and watches.** It runs in the browser, with thirteen games (chess, checkers, Four in a Row, Snakes and Ladders, Ludo, Sea Battle, Blackjack, Texas Hold'em, a colour-matching card game, a crossword tile game, a creature card duel, a country-house murder mystery and a WW2 hidden-role game), a camera table for any real board game, voice and video, a chat for the players and a separate one for the people watching, and leaderboards that keep score over weeks and months.

Most online games give you a match against a stranger. Playhall is closer to a games night: a room that stays open, people drifting in, a queue for the next game, and a running argument in the corner about who should have played d4. The host decides who plays, who waits and what the people watching may say, and the scores carry on from one evening to the next.

![A chess room during a game: the host playing White against Alex, three people watching, and the players' chat on the right](images/01-hero.png)

*Sam (White, and the room's host) against Alex in a game with no clock. Three people are watching; Kai's ring in the watchers' strip means he's asked for the next seat. The players' chat says they can't read what the watchers are saying until the game ends.*

---

## At a glance

| | |
|---|---|
| **Rooms with roles** | A host, players in seats, and everyone else watching; a queue for seats; who plays the next game |
| **Thirteen games, and any board game** | Chess, checkers, Four in a Row, Snakes and Ladders, Ludo and Sea Battle; Blackjack, Texas Hold'em and Colour Clash with play chips and cards; Letterworks, a crossword tile game; Realm Duel, a creature card duel; Manor Mystery, a murder mystery for up to six; Ship2Shore, a hidden-role game for five to eleven. One to eleven players, all drawn from one set of boards, pieces, cards, chips and dice. The camera table plays any real board game over a camera |
| **Two conversations** | The table, which everyone reads, and the gallery, the watchers' own chat that players can't read during a game |
| **A room that gets out of the way** | Every panel around the board expands, shrinks or hides; three layouts; works on a phone |
| **The host's rules** | Who may sit down, what happens after each game, a move timer, an optional delay for watchers, who may write where, private rooms with a password, removing people |
| **Points and leaderboards** | 3 for a win, 1 for a draw or second place; weekly, monthly and all-time tables; every player's record, head-to-heads and replays |

---

## Rooms, players and people watching

Anyone with an account can open a room, pick the game and a time control, and decide whether it shows in the lobby or only to people with the invite link. The lobby shows each room's game, who's playing, how many are watching, and whether there's a free seat.

![The lobby: rooms playing chess, checkers, Snakes and Ladders, Ludo and Sea Battle, and the form for a new room listing every game](images/02-lobby.png)

Inside a room there are three roles. The **host** runs it, and can play too. **Players** are whoever is sitting in the game's seats. Everyone else is **watching**. A watcher who wants to play asks for a seat and joins a queue; when a game ends, the host's rule decides who plays next: the same players, the winner stays and the next in the queue takes the other seat, or both rotate out.

You can come in as a guest with just a name, straight from an invite link. Guests can watch, and play if the host allows it; hosting a room needs an account.

---

## The table and the gallery

Watchers talking to players spoils games like chess, so every room has two chats. **The table** is the players' conversation: everyone can read it, and watchers can write there between games. **The gallery** belongs to the people watching: they can say "he's about to blunder the knight" without the player hearing it, because players can't read the gallery until the game is over. The host can relax either rule.

![The same game from Priya's seat in the gallery: the watchers' strip with Kai asking for a seat, and the gallery chat](images/03-watching.png)

*Priya is watching in the Social layout, which gives the watchers more room. Her gallery chat is open; the Table tab shows three messages she hasn't read yet.*

**Voice and video follow the same split.** Players talk at the table; watchers talk among themselves in the gallery and can listen in at the table. Everyone joins muted, with open mic or push to talk, a ring around whoever's talking, and a volume (or "silence for me") for each person. Players' cameras appear on their seat cards; watchers' cameras appear in the gallery strip if the host allows it. When a game starts or ends, who may talk and show their camera changes at once. If the host has set a delay for watchers, they don't get the players' voice or video during play, since live media can't be held back. All of it runs on a self-hosted media server (LiveKit) behind Playhall, so no call goes through anyone else's service.

**Transcripts**, if the host turns them on, write what people say in voice into the chat as "said" lines, so the chat keeps a record and anyone who can't hear can follow. Everyone in voice sees a clear notice while it's on. Speech goes to a speech-to-text server on the same machine, and only the text is kept, never the audio.

---

## The games

**Chess** is checked move by move on the server, so nobody can play an illegal move or touch the other side's clock. It has clocks with increments (one minute up to fifteen, or none), draw offers, resigning, promotion, and every way a game can end: checkmate, stalemate, running out of time, repetition and the 50-move rule. The board is lichess's open-source chessground, and it flips round for the player with Black.

When a game ends, the room shows what it meant: the points each player won, their new totals and rank, and their record against each other.

![A finished game: Priya has mated Lee in four moves, and the card shows +3 and +0, their totals and ranks, and their head-to-head](images/04-result.png)

*A four-move mate. Priya takes 3 points and moves to 119, second in the all-time table; she leads Lee 9 to 5 over their games together.*

**Four in a Row** is the first game built from Playhall's shared kit of boards, discs and player colours, which the games still to come will use too, so they look like one set. Hovering over a column shows where your disc would land, discs drop with a short animation, and the winning four are ringed.

![Four in a Row, Jordan to play: the frame with amber and teal discs, and a faint teal disc where Jordan's would land in the first column](images/05-four-in-a-row.png)

**Checkers** is English draughts. Captures are compulsory, so the board only offers a capture when there is one, and a piece that can keep jumping must: you click each landing square in turn. A man that reaches the far row is crowned and then moves backwards too.

![Checkers, Kai (White) to play: Kai has picked a piece that must capture, and the one square it can land on is marked](images/13-checkers.png)

**Snakes and Ladders** and **Ludo** are for two to four players and bring in the dice: always rolled on the server, never in anyone's browser, so nobody can load them. Snakes and Ladders has its own drawn snakes and ladders and needs an exact roll to finish on 100. Ludo follows the usual rules: a 6 to bring a token out and another roll after a 6, rivals sent back to their yard (except on the starred squares), and an exact roll to get home. In both, everyone gets a finishing place, and second place earns a point.

<p>
<img src="images/14-snakes.png" width="49%" alt="Snakes and Ladders with three players: Jordan on 57 after rolling a 1, Lee on 19, Priya to roll">
<img src="images/15-ludo.png" width="49%" alt="Ludo with four players, a few tokens out on the track and the rest still in their yards">
</p>

**Sea Battle** is the first game with hidden information. Each player's fleet is laid out at random (shuffle until you like it, then press Ready); you only ever see your own ships and the results of the shots, red for a hit and white for a miss, and people watching see the shots alone until the game ends and both fleets are shown.

![Sea Battle from Jordan's side: Jordan's fleet with Lee's shots in it on the left, and the enemy waters with Jordan's shots on the right](images/16-sea-battle.png)

The five ships are drawn for Playhall: a carrier with its flight deck, a battleship with three turrets, a cruiser, a submarine and a destroyer. You can place them yourself: click a ship to pick it up, turn it, and a shadow shows where it would go, red if it won't fit. Or press Shuffle for a random layout.

![Placing a fleet: the Battleship picked up, its red shadow showing it would overlap the Carrier, and the buttons to choose a ship, rotate, shuffle or say you're ready](images/17-sea-battle-placing.png)

**The card games** share one deck design, a card back with the Playhall mark, and chips that always show their amount, and they use play chips only: nothing to buy and nothing to cash out. **Blackjack** is one to four players against the house over a session of 5, 10 or 20 hands, most chips at the end winning (a game on your own is practice and doesn't count). **Texas Hold'em** is a sit-and-go: everyone starts with the same chips, the blinds double every few hands, side pots look after short stacks, and the last player with chips wins. **Colour Clash** is our own colour-matching card game in the four player colours. In all three, your cards are hidden from everyone else, people watching included.

<p>
<img src="images/18-blackjack.png" width="49%" alt="Blackjack, hand 2 of 10: the dealer's 2 showing and a face-down card, Sam on 7 deciding whether to hit, Kai on 18 and Priya with a blackjack">
<img src="images/20-holdem.png" width="49%" alt="Texas Hold'em after the turn: four shared cards, Morgan's own two cards face up and the others face down, and the buttons to fold, check, bet or go all in">
</p>

![Colour Clash with four players: the others' card counts across the top, the draw pile and a rose 1 in play, and Priya's hand of five cards](images/19-colour-clash.png)

**Letterworks** is a crossword tile game for two to four players, on our own board layout and tile set rather than the commercial game's. You build words from a private rack of seven tiles: tap a tile then a square (or drag it), and Playhall shows the words you're making and what they'd score before you commit. Every word is checked against ENABLE, a public-domain list of about 173,000 English words, and one that isn't allowed is turned down before anything is placed, so there are no challenges and no penalties. The Moves tab keeps the words played, and a panel lists all 96 two-letter words.

![Letterworks with three players: HALL across the centre, PAWN down through its A and QUEEN ending on PAWN's N, Priya's rack of seven tiles under the board, and the Moves tab listing the three words and their scores](images/23-letterworks.png)

**Realm Duel** is a two-player creature card duel. Each player holds three creatures, each with its own strengths, an element and an ability. Every round both players secretly pick a creature and how it attacks (melee, ranged or magic), then both are revealed together: the quicker creature strikes first, elements beat one another in a cycle, and poison, stuns and shields carry over. The last player with creatures standing wins. The creature stats were balanced by simulating 20,000 games.

![Realm Duel: Kai's three violet card backs and "Locked in" at the top, and Priya's Duskmaw, Stonehorn and Tidecaller with their stats and health, the Melee, Ranged and Magic buttons, and Lock in](images/25-realm-duel.png)

**Manor Mystery** is a murder at Blackthorn Hall for three to six players, with its own cast, objects and rooms. One suspect, object and room are sealed in an envelope. You move through the hall, suggest who did it, with what, in the room you're in, and the players after you must secretly show you one card that proves you wrong. Your notebook fills in what you've learned for certain and keeps your own marks. Accuse when you're sure: right wins, wrong puts you out, and if nobody solves it, nobody scores.

![Manor Mystery: the plan of Blackthorn Hall with three tokens in the Chapel and the hidden passages marked, the move buttons, Priya's six cards, the envelope, and her notebook with what she knows already filled in](images/26-manor-mystery.png)

**Ship2Shore** is our own hidden-role game for five to eleven players, set aboard the merchant ship SS Marigold in 1943. Everyone secretly belongs to the Royal Navy, the enemy spies or the black market, and each side is steering for a different port: Plymouth, a U-boat rendezvous, or Lisbon. Each round a captain picks a watch, course orders are passed along in secret, and the ship moves on the chart; the crew can mutiny, inspect each other, or throw a suspect to the sharks, which is exactly how the black market's fixer hopes to win. Roles stay hidden from everyone, watchers included, until the end.

![Ship2Shore with five players: your secret role card (Royal Navy), the captain picking the watch, the chart of the Bay of Biscay with the ship near the start, and the ship's log](images/28-ship2shore.png)

Every game can be replayed move by move once it's over, with cards kept hidden that weren't shown at the time, and the players can ask for a rematch that starts when they all agree.

Every game explains itself. The **Rules** tab in any room, and **How to play** when you pick a game in the lobby, give the goal in a sentence and then the essentials: taking turns, how the pieces move, how a game ends, and a few tips. Each new game has to come with its own.

![The Rules tab in a chess room: the goal, then taking turns, how the pieces move, special moves and how a game ends](images/12-rules.png)

---

## Any board game: the camera table

For everything nobody has written for Playhall, there's the camera table. The host types the game's name (Catan, say), points a camera at the real board on their own table and moves the pieces for everyone. A phone on a stand will do: the host shows a QR code, the phone opens it without signing in, and its rear camera fills everyone's stage, never cropped or mirrored. The link can only show a picture on that room's stage: no seat, no sound, no view of the room, and the host can cut it off at any time.

Playhall keeps the rest: the seats and whose turn it is, a dice roller (any mix of d4 to d20, rolled on the server and posted in the chat, so nobody argues about a roll), a score pad everyone can see, the move timer, voice and the chat. At the end the host enters the places, every player confirms, and only then does it count, on a leaderboard of its own for each game name.

![The camera table: whose turn it is with a Next turn button, a board on a wooden table filling the camera picture, the dice with Kai's roll of 12, the score pad, and the rolls in the table chat](images/27-camera-table.png)

---

## A room that gets out of the way

The board always gets the space. Everything around it (the top bar, the players, the watchers, the chat) can be expanded, shrunk or hidden on its own, and three layouts set them all at once: **Default**, **Focus** for when you're playing, and **Social** for when the conversation matters more. Your choice is remembered for each game. Clocks and whose turn it is stay visible however small the panels get.

![The same room in the Focus layout: a thin top bar, players as avatars, the chat shrunk to a rail of icons, and the board filling the screen](images/06-focus.png)

On a phone the board takes the full width and everything else stacks beneath it. There's a light theme too, which follows your system unless you pick one.

Everyone has a **Settings** page. Ahead of voice and video, it's where you choose your microphone, speakers and camera if you have more than one of any, and check them before a game: a level meter that moves as you speak, a test sound through the speakers you picked, and a preview of your camera. Each computer or phone remembers its own choice. You can change your password there too.

Whoever runs Playhall gets a separate **platform settings** page, for things that apply to everyone: whether people can sign up or join as guests, whether voice, video and transcripts are allowed, the defaults for new rooms, the HTTPS certificate, other admins, and closing any room. A fresh install comes with an admin account whose password has to be changed at its first sign-in, before anything else opens.

<img src="images/24-settings.png" width="62%" alt="The Settings page: a microphone chosen from a list with its level meter moving, speakers with a Play a test sound button, a camera preview, and the theme">

<p>
<img src="images/07-phone.png" width="300" alt="The room on a phone: the full-width board, the players, and the watchers' strip with the button to ask for a seat">
<img src="images/22-light-lobby.png" width="62%" alt="The lobby in the light theme, with rooms for Hold'em, Colour Clash, Blackjack, Sea Battle and chess">
</p>

---

## The host's rules

The host sets how the room works and can change most of it at any time: whether anyone may take a free seat or only people the host seats, the after-game rule, the time control (between games), a **move timer** for the games with turns (15 seconds to 5 minutes a move; run out and you lose, or in a game for more players your roll is made for you), a **delay for watchers** (off unless the host wants it: watchers then see the board and the players' chat up to two minutes late, so nobody can feed a player moves), whether watchers may write at the table, whether guests may play, whether the room shows in the lobby, and whether its games count for the leaderboard. The host can also move a player to watching, remove someone from the room for good, hand the room to someone else, or close it. If a player's connection drops mid-game, they have a minute to come back (everyone sees the countdown); after that, a two-player game goes to their opponent, and in bigger games play carries on without them.

![The Room rules dialog: seats, the after-game rule, the delay for watchers, the move timer, and the switches for who reads and writes what](images/08-room-rules.png)

A room can also have a **password**. The host and anyone already playing go straight in; everyone else is asked for it, and changing it means everyone else needs the new one.

<p><img src="images/09-private.png" width="480" alt="The prompt for a private room: this room is private, enter the room password"></p>

---

## Points, leaderboards and everyone's record

Every finished game is scored: **3 points for a win, 1 for a draw, 0 for a loss** (in games for three or more, second place will earn 1). The leaderboard ranks players for this week, this month and all time, separately for each game and for all games together, once they've played five games in the period. Only games between people with accounts, in rooms set to count, are scored; a game the host stopped doesn't count.

![This month's chess leaderboard: Sam first with 34 points from 16 games, the podium, every player's record and their last five results](images/10-leaderboard.png)

Each player has a page of their own: points, games played, points per game, win rate and their current streak; how they do as White and as Black; points week by week; how their games ended; their recent games; and their record against everyone they've played.

![Sam's stats: 120 points from 73 chess games, results as White and Black, points per week and how games ended, and head-to-head records](images/11-stats.png)

Finished games of chess, checkers and Four in a Row can be **replayed** move by move, from a player's recent games or from the card at the end of the game.

![A chess replay: the board five moves in, with buttons to go to the first move, back, next or last, and a slider](images/21-replay.png)

---

## What's next

Playhall is planned in five phases, with a backlog behind them.

1. **Phase 1, done: the games.** Nine games on one shared kit, and since then Letterworks.
2. **Phase 2, done: voice and video, and any board game at all.** Voice, video and transcripts are in every room, split the same way as the chat, and the **camera table** below.
3. **Phase 3: OpenBOR rooms.** The server runs the open-source OpenBOR engine for beat-'em-ups and streams the game into the room, and up to four players control it from their browsers with a gamepad or keyboard. Playhall supplies neither the engine nor any games: an admin installs the engine from its official releases, and players import their own games.
4. **Phase 4: share a game from your own PC.** A host runs any local-multiplayer game on their own computer; Playhall streams it to the room and friends play it remotely with their own controllers, which appear on the host's PC as ordinary game controllers.
5. **Phase 5: games from anyone.** A plug-in kit so other people's web games can run in a room, plus a faster, smoother version of sharing a game from your PC.

**In the backlog:** Ship2Shore, a hidden-role game for 5-11 players set aboard a 1943 merchant ship where the Royal Navy, enemy spies and a black-market fixer each steer for a different port and the crew throw suspects to the sharks; tournaments (knockout, round robin and Swiss, with Playhall pairing the players and opening the rooms each round); more games; animations and sounds for dice, cards and pieces; and computer opponents.

---

## How it runs

Playhall is a Node.js server with a Postgres database, packaged as two containers for podman or Docker Compose, plus an optional third, the self-hosted LiveKit media server, for voice and video. People need only a browser. It serves HTTPS from the start: on first run it makes its own certificate (each device accepts it once), and a real certificate can be swapped in without a restart. It comes with demo data (eight players, six rooms and two months of games), which is what's in these screenshots.

## Status

Early: phases 1 and 2 of five are done, and Playhall has been run on a home network, not yet in public. CI runs on every push: over 200 unit tests of the game rules, the room logic and the scoring, including hundreds of random games (Hold'em checking no chips are ever created or lost, Realm Duel and Manor Mystery checking nobody sees hidden cards); an end-to-end test that plays games of chess and Letterworks over the real WebSocket and checks the results, stats and private rooms; a browser test in Chromium, Firefox and WebKit that plays on the real board on desktop and phone and checks the device settings with a fake microphone and camera; loading and removing the demo data; and building and starting the container image, then running the end-to-end test against it over HTTPS and two browsers talking to each other through the media server.

Source: [github.com/chrisnock-personal/playhall](https://github.com/chrisnock-personal/playhall)
