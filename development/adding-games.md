# Adding Games

To add support for a game, a new package for this game should be created in `src/games` for the game.

This package should declare a struct which needs to implement the Game interface.

Below is a walkthrough of the process of supporting Mordhau. You can use the process outlined to add support for other games.

## Walkthrough: Adding Mordhau Support

First up, we create a package in `src/games` called `mordhau`. Then, we create a file inside this package called `mordhau.go`. This file houses the game struct which must implement the Game interface (found in `src/domain/game.go`).

We create the struct like so:

```go
type mordhau struct {
    config *domain.GameConfig
    platform domain.Platform
    cmdOutputPatterns *domain.CommandOutputPatterns
}
```

The `config` field holds the game's configuration. This will be created in the game's constructor which we will create shortly. The `platform` field holds the platform type for this game. For example, `playfab` or `mojang`.

> Note: We do not currently have a platform definition for Steam. This platform definition needs to be created before any games using SteamIDs can be supported!

`cmdOutputPatterns` is a struct that holds patterns for the expected output of commands which get executed on the server. This will be further explained later.

### Creating the Minecraft Constructor

Next, we will create a constructor function to create a minecraft struct instance. We will do this like so:

```go
func NewMordhauGame(platform domain.Platform) domain.Game {
    return &mordhau{
        platform: platform,
        config: &domain.GameConfig{
            // Config fields here
        },
        cmdOutputPatterns: &domain.CommandOutputPatterns{
            // Output patterns here
        },
    }
}
```

### Creating the Minecraft Game Config

A variety of fields must now be set in the config struct. I will now show the completed Mordhau game config and then explain each field and the justification behind their values.

```go
config: &domain.GameConfig{
    UseRCON:                   true,
    AlivePingInterval:         time.Second * 30,
	EnableBroadcasts:          true,
	BroadcastInitCommands:     []string{"listen login", "listen chat"},
	PlayerListRefreshInterval: time.Minute * 20,
	EnableChat:                true,
	BroadcastPatterns: map[string]*regexp.Regexp{
		broadcast.TypeJoin: regexp.MustCompile("^Login: (?P<Date>[0-9\\.-]+): (?P<Name>.+) \\((?P<PlayerID>[0-9a-fA-F]+)\\) logged in$"),
		broadcast.TypeQuit: regexp.MustCompile("^Login: (?P<Date>[0-9\\.-]+): (?P<Name>.+) \\((?P<PlayerID>[0-9a-fA-F]+)\\) logged out$"),
		broadcast.TypeChat: regexp.MustCompile("^Chat: (?P<PlayerID>[0-9a-fA-F]+), (?P<Name>.+), \\((?P<Channel>.+)\\) (?P<Message>.+)$"),
	},
	IgnoredBroadcastPatterns: []*regexp.Regexp{
		regexp.MustCompile("Keeping client alive for another [0-9]+ seconds"),
	},
}
```

#### UseRCON

This field should always be true, unless you are for some reason supporting a game which doesn't use RCON. Refractor was not designed for this purpose, so it's likely that some other refactoring might be needed.

#### AlivePingInterval

This is the interval at which the game server will be pinged. There are cases where some games might terminate idle RCON sessions. The keepalive ping ensures the connection stays alive by periodically sending some data over the RCON connection.

While this field is likely not required for most games, I would strongly recommend setting it. Even something high like 10 minutes could help ensure the connection stays alive and healthy.

#### EnableBroadcasts

Most game RCON implementations are fairly linear. You send a command, the server responds. Some games may have alternate RCON setups where they actually proactively send data to you without you having to explicitly request it by executing a command. In Refractor, we call these _broadcast messages_ (named off of Mordhau's broadcast system).

Many games will not support broadcasts. Unless you know for sure that your game supports broadcasts, then set this to false.

Mordhau has broadcast support so in this case we set this to true.

#### BroadcastInitCommands

If `EnableBroadcasts` is set to true, then you can choose to list some commands you want run on the broadcast RCON client upon connection to the server.

This is useful for Mordhau because Mordhau has support for various broadcast channels. Refractor only needs to listen for player join/quit messages and chat messages, so we run Mordhau's `listen` command using both login and chat to accomplish this.

Again, if your game does not support broadcasts, there is no need to set this config field.

#### PlayerListPollingInterval

If you game supports broadcasts and you have `EnableBroadcasts` to true, you can skip setting this field.

Otherwise, this is the interval at which the server will check your player list and detect and players who've joined or quit. I would suggest setting this to a low interval (e.g every 5 seconds) to let Refractor react quickly to join/quit events.

#### PlayerListRefreshInterval

This setting is important to set for all games. Even if you detect join/quit events via broadcasts or polling, you should still set this field.

If this field is set, the servers player list will be wiped and manually re-fetched on the interval set. This is important in the case of unexpected disconnects or if the server's process gets killed.

If the server's process gets killed, Refractor's player list will be thrown out of sync. When the server comes back up, the old player list will still be displayed even though those players are no longer actually connected. `PlayerListRefreshInterval` helps reduce the impact of this problem by limiting the duration of the desync.

Unless your server crashes very frequently, it's safe to set this value to something like 20 minutes. If your server crashes frequently, you may opt for a lower interval.

#### EnableChat

If your game supports RCON broadcasts and broadcasts chat messages, you can enable live chat in Refractor by setting this field to true.

Mordhau does support chat broadcasts, so we set this to true.

_Currently, broadcasts are the only method of receiving chat messages supported by Refractor, though other methods can be added in the future. Contributions are welcome!_

#### BroadcastPatterns

If your game supports RCON broadcasts, this is where you can define the regex patterns for the required event types.

You must use named groups in your regex to tell Refractor what each field is. Take for example Mordhau's player join pattern:

```regex
^Login: (?P<Date>[0-9\\.-]+): (?P<Name>.+) \\((?P<PlayerID>[0-9a-fA-F]+)\\) logged in$
```

Groups are denoted by `?P<GROUP_NAME>PATTERN`. For example, `(?P<Name>.+)`.

**The group names must be exactly the same as seen in the above config.**

The following groups are required for each broadcast pattern type:

-   Player join: **Name**, **PlayerID**

-   Player quit: **Name**, **PlayerID**

-   Chat message receive: **PlayerID**, **Message**

The other groups you see in the config are there to describe the remaining content, but are not required.

#### IgnoredBroadcastPatterns

If your game uses RCON broadcasts, there may be some garbage broadcasted that you want to ignore. You can put regex patterns for these broadcasts here.

In the case of Mordhau, the `Alive` command sent due to the `AlivePingInterval` returns data which we don't care about, so we put a pattern for it in `IgnoredBroadcastPatterns`.

### Setting Command Output Patterns

Now that we're done with the game config object, we can move onto the next field of the `mordhau` struct: `cmdOutputPatterns`.

This field is important because it tells Refractor how to interpret the output of various commands.

Currently, the online required command output pattern is for the `PlayerList` command.

For Mordhau, the cmdOutputPatterns setting would look like this:

```go
func NewMordhauGame(platform domain.Platform) domain.Game {
    return &mordhau{
        config: ...,
        platform: ...,
        cmdOutputPatterns: &domain.CommandOutputPatterns{
            PlayerList: regexp.MustCompile("(?P<PlayerID>[0-9A-Z]+),\\s(?P<Name>[\\S ]+),\\s(?P<Ping>\\d{1,4})\\sms,\\steam\\s(?P<Team>[0-9-]+)"),
        },
    }
}
```

Notice that we are using regex groups (denoted by the `?P<GROUP NAME>` syntax you can see above.

The following groups must exist for the PlayerList command:

-   PlayerID
-   Name

The other two fields (Ping and Team) are just there to further describe the rest of the data, but are not required by Refractor to function.

### Game Constructor Finished

We are now done with the Mordhau game constructor!

All we need to do now with the `mordhau` struct is attach the required methods to implement the `Game` interface.

At the time of writing this, this is the `Game` interface:

```go
type Game interface {
    GetName() string
    GetConfig() *domain.GameConfig
    GetPlatform() domain.Platform
    GetPlayerListCommand() string
    GetCommandOutputPatterns() *domain.CommandOutputPatterns
    GetBroadcastCommand() string
    GetDefaultSettings() *domain.GameSettings
}
```

I won't walk through what all of these mean as they should be fairly self explanitory. I will now show the methods definitions for the `mordhau` struct in order to implement the Game interface.

```go
func (g *mordhau) GetName() string {
	return "Mordhau"
}

func (g *mordhau) GetConfig() *domain.GameConfig {
	return g.config
}

func (g *mordhau) GetPlatform() domain.Platform {
	return g.platform
}

func (g *mordhau) GetPlayerListCommand() string {
    // Self explanitory (I hope), the command executed to get the list of online players.
	return "PlayerList"
}

func (g *mordhau) GetCommandOutputPatterns() *domain.CommandOutputPatterns {
	return g.cmdOutputPatterns
}

func (g *mordhau) GetBroadcastCommand() string {
    // Self explanitory (I hope), the command executed to broadcast a message to the server.
	return "Say %s"
}

func (g *mordhau) GetDefaultSettings() *domain.GameSettings {
    // See below
	return &domain.GameSettings{
		BanCommandPattern: "Ban {{PLAYER_ID}} {{DURATION}} {{REASON}}",
	}
}
```

#### GetDefaultSettings()

One thing which is important to touch upon is the GetDefaultSettings() method.

This method returns a struct containing default settings for the game. This struct contains fields which can be configured by server owners to better customize their game's behaviour.

A good example of the utility of this feature is Minecraft. Since Minecraft has so many different plugins which add different versions of the ban command, it's important that we give server owners the flexibility to choose which ban command will be used by Refractor during ban synchronization.

Currently, `BanCommandPattern` is the only required field on this struct but there may be others in the future.

The example pattern above matches Mordhau's built in ban command.

### Registering The Game

We are now finished configuring the `mordhau.go` file! Next, we need to register the game in `src/main.go`.

Open `src/main.go` and find the `registerGames()` function above the main function. Register your game above the `// ADD NEW GAME PACKAGES HERE` comment.

For example, Mordhau's game registration line looks like this:

```go
gameService.AddGame(mordhau.NewMordhauGame(_playfab))
```

### Wrapping Up

That's it! You can now launch Refractor and verify that your game is working as expected. If your game is not behaving correctly, double check that your game's config is correct.

If additional features or refactors are required to support your game, please open an issue on GitHub. If you're comfortable fixing it yourself, please submit a pull request. Contributions are welcome!
