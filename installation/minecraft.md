# Minecraft Server Setup

While Minecraft has support for RCON, some features which Refractor requires are missing by default. To add these features, you must install the Refractor server plugin. Due to this, **Refractor does not work with vanilla Minecraft servers**.

Download the latest version of the RefractorMC plugin from [https://github.com/RefractorGSCM/RefractorMC/releases/latest](https://github.com/RefractorGSCM/RefractorMC/releases/latest) and drop it into your plugins folder. Restart your server and your Minecraft server is good to go.

## Command Configuration

As Refractor has ban synchronization, it must be able to execute ban commands on your server. Refractor bans have a duration and by default, Minecraft does not have a ban command which bans for a specified duration.

Because of this, you must have a plugin which adds support for temporary bans. For example, [EssentialsX](https://www.spigotmc.org/resources/essentialsx.9089).

Once you have a tempban command ready for use, you must configure Refractor to use it. You can do this by navigating to `Admin Settings > Games > Minecraft` on the Refractor dashboard. You should see a text field labeled Ban Command pattern.

You must now configure this to match the correct usage of your ban plugin's ban command. For example, if you are using EssentialsX your ban command pattern should be as follows:

```
tempban {{PLAYER_NAME}} {{DURATION}}m {{REASON}}
```

Note the `m` after `{{DURATION}}`. This is because Refractor records durations in minutes. The `m` denotes minutes.

Once you have this setup, you can test it out by logging a ban for yourself on Refractor and then attempting to join the server. After a few seconds, it should ban you for the correct period of time.

![Minecraft settings page](/images/minecraft_settings.png)

## Setup Complete

Your Minecraft server should now be completely ready to use with Refractor. Enjoy!
