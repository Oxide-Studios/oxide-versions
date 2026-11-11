# Changelog

## [1.0.0] - 2026-11-11

### Added
- First release of `oxide-dice`.
- Runs on QBCore, Qbox and ESX through `o-link` 1.11.0 or newer. ESX needs `ox_inventory`.
- Street craps, player against player: the shooter sets a bet and the other players fade it, in full or in part. Whatever nobody covered goes back to the shooter before the roll.
- The come-out roll: 7 or 11 wins for the shooter, 2, 3 or 12 loses, and any other total becomes the point. With a point set, the shooter rolls until they hit the point (win) or roll a 7 (seven-out).
- A winning shooter keeps the dice and can press the bet or keep it. After craps or a seven-out the dice pass clockwise.
- Side bets between players while a point is set: point before 7, 7 before point, and number bets at odds the players agree on. Each player can hold up to 4.
- No house, no rake: every dollar won comes from another player.
- Bets use the framework's cash in hand. Every stake leaves the player's cash as soon as it's accepted and is held until the roll decides it.
- A server restart in the middle of a game gives every stake back to the player who put it up.
- Leaving a game settles by the rules: a shooter who walks away with a point set loses to the faders, and money already in play is paid out even after a player has gone.
- Street games started with the `dice` item at the player's feet, for 2 to 8 players sitting round it on upturned milk crates.
- Two kinds of dice table, each placed with its own item: a long casino table (`dice_table`) and a folding card table with four chairs (`dice_folding_table`). Both have three placement modes (admins only, players inside allowed areas, players anywhere outside blocked areas), plus fixed tables at spots you choose.
- A real throw: aim with the camera, hold the mouse button to shake, let go to throw. Everyone nearby sees the dice tumble and land on the same faces.
- A throw camera for the players in the game. It keeps the dice in view from the moment they leave the hand, looking past the shooter, then looks closely at the result. On the street it stays low beside the shooter's shoulder, steps aside when the shooter would block the view, rises only when nothing beside them is clear, and narrows its view so the dice stay easy to see. Every player in the game gets the same shot.
- Its own die, street crate and dice table models. The table is long, with green felt, a cash ledge for the pot and a back wall the dice bounce off. Up to 8 players stand round it, and 5 of the spots take the dice.
- Dice animations for the street, the casino table and the folding table's chairs, with matching versions for characters in high heels, dice sounds, and cash that grows with the pot.
- A tag over each game showing the pot, the shooter, the point and the last roll. It keeps one width through a game, ends a long shooter name in an ellipsis, and steps back while you have the amount prompt or the side bets card open.
- A pop-up over the game for each roll's result, shown once the tag has the roll and held long enough to read.
- A side bets card that lists the open offers, says which side each one gives you, and marks the bets you hold.
- A controls card that says what's happening, what to press, what you have at stake and how long you have, with tips for a player's first three games, and a rules panel on H. Both can be moved in Oxide HUD's `/hud` editor.
- A dice seller NPC selling dice and dice tables for cash.
- Optional police alerts when a street game starts, off by default (Alert chance from 0 to 1 in the settings). Dice tables never alert.
- Single `/dice` command: `/dice help` and `/dice leave` for players, and `/dice settings`, `/dice give`, `/dice table place`, `/dice end`, `/dice games` and `/dice demo` for admins.
- Live settings editor (`/dice settings`): change settings in game with no restart, and draw table placement areas in the world.
- Model replacements for older game builds (`Config.ModelSwaps`).
- Three inventory items with icons, and a ready-to-paste `items/ox_inventory_items.lua`.
- Database tables are created automatically when the resource starts; `sql/install.sql` ships as a reference copy.
- English locale (`locales/en.json`) for the notifications, prompts, cards and menus players see.
