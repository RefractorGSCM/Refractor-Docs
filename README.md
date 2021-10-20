# Refractor

Refractor is a game server community management panel written in Go and Svelte. Refractor makes moderating game servers easier by improving the speed, consistency, and coordination of game server moderation with the following features:

-   **Support for multiple servers and multiple users**: Get started quickly! Simple add your servers and create accounts for your staff team.
-   **Live player lists**: Allows users to quickly see who's online on a server.
-   **Player infraction logging**: Supports logging of warnings, mutes, kicks and bans. These infractions can then be viewed individually, on a player's summary page, or through the infraction lookup feature.
-   **Player summary lookup**: Allows users to lookup players to see their history.
-   **Infraction lookup**: Quickly search for logged player infractions matching your specified criteria.
-   **Real time chat**: Allows users to check in on servers without having to log in.
-   **Comprehensive permissions management**: Create permission groups, assign users to them and finely tune what they have access to. Permissions can also be overridden on individual servers to give you even more control.
-   **Chat message flagging**: Admins can set a list of flagged words which when detected in any message will cause the message to be held in the flagged message queue for review.

## Supported Games

Currently, Mordhau and Minecraft are supported. Other games can be supported. See [supporting other games](/#supporting-other-games).

<div class="game-images">
    <img src="https://www.minecraft.net/etc.clientlibs/minecraft/clientlibs/main/resources/img/header/Minecraft_Core-Logo.png" alt="Minecraft logo" />
    <img src="https://mordhau.com/static/img/mordhau.59ce5187cf5a.png" alt="Mordhau logo" />
</div>

<style>
    .game-images {
        display: grid;
        grid-template-columns: 1fr 1fr;
        place-items: center;
    }

    .game-images img {
        max-height: 100px;
        width: auto;
    }
</style>

## Per-Game Features

Because every game is designed differently, with varying levels of support for things like RCON, not all features are available across all games. Refer to the table below to see what's supported and what's not.

| Game      | Live player list      | Infraction logging    | Live two-way chat     | Ban Sync              |
| --------- | --------------------- | --------------------- | --------------------- | --------------------- |
| Mordhau   | <span class="check"/> | <span class="check"/> | <span class="check"/> | <span class="check"/> |
| Minecraft | <span class="check"/> | <span class="check"/> | <span class="x"/>     | <span class="check"/> |

<style>
    span.check::after {
        display: inline-block;
        width: 100%;
        text-align: center;
        content: "✓";
        color: #74ff29;
    }

    span.x::after {
        display: inline-block;
        width: 100%;
        text-align: center;
        content: "✕";
        color: #ff3e29;
    }
</style>

## Supporting Other Games

Because of the varying implementations of game server protocols, it is impossible for us to fully support every game. However, many games implement a protocol called RCON (Remote Console). Any game with RCON support can be at least partially supported by Refractor!

Refractor is modular by design, meaning that adding support for new games can be done quickly and easily. There will be a guide on this process coming soon.
