# Minecraft Server Setup

While Minecraft has support for RCON, some features which Refractor requires are missing by default. To add these features, you must install the Refractor server plugin. Due to this, **Refractor does not work with vanilla Minecraft servers**.

Download the latest version of the RefractorMC plugin from [https://github.com/RefractorGSCM/RefractorMC/releases/latest](https://github.com/RefractorGSCM/RefractorMC/releases/latest) and drop it into your plugins folder. Restart your server and your Minecraft server is good to go.

## Command Configuration

As Refractor has ban synchronization, it must be able to execute ban commands on your server. Refractor bans have a duration and by default, Minecraft does not have a ban command which bans for a specified duration.

Because of this, you must have a plugin which adds support for temporary bans. For example, [EssentialsX](https://www.spigotmc.org/resources/essentialsx.9089).

Once you have a tempban command ready for use, you must configure Refractor to use it. You can do this by navigating to `Admin Settings > Games > Minecraft` on the Refractor dashboard. From here, you can configure the ban commands to be run on any of the infraction events (create, update, delete and repeal).

For example, if you're using EssentialsX you would want to set the ban creation command to the following:

```
tempban {{PLAYER_NAME}} {{DURATION}}m {{REASON}}
```

Note the `m` after `{{DURATION}}`. This is because Refractor records durations in minutes. The `m` denotes minutes in the EssentialsX ban command.

![Minecraft settings page](/images/minecraft_settings.png)

## Setup Complete

Your Minecraft server should now be completely ready to use with Refractor. Enjoy!
