# Permissions Overview

Refractor has a comprehensive permissions system built with default permissions, user groups and server-specific permission overrides. This allows you to finely tune what your users have access to.

### Available Permissions

| **Permission**          | **Description**                                                                                       | **Default State** |
| ----------------------- | ----------------------------------------------------------------------------------------------------- | ----------------- |
| Administrator           | Grants full administrator access to Refractor. Some things are still restricted to super-admins only. | off               |
| View Servers            | Allows users to view servers.                                                                         | on                |
| View Player Records     | Allows users to view player records, including player search and player summaries.                    | on                |
| View Infraction Records | Allows users to view and search infractions.                                                          | on                |
| View Chat Records       | Allows users to view chat records. (does not grant direct access to live chat)                        | on                |
| Log Player Warnings     | Users with this permission can log warnings for players.                                              | on                |
| Log Player Mutes        | Users with this permission can log mutes for players.                                                 | on                |
| Log Player Kicks        | Users with this permission can log kicks for players.                                                 | on                |
| Log Player Bans         | Users with this permission can log bans for players.                                                  | on                |
| Edit Own Infractions    | Allows users to edit their own infraction records (warns, mutes, etc).                                | on                |
| Edit Any Infractions    | Allows users to edit any infraction record, including those which they did not create.                | off               |
| Delete Own Infractions  | Allows users to delete their own infraction records (warns, mutes, etc).                              | off               |
| Delete Any Infractions  | Allows users to delete any infraction record, including those which they did not create.              | off               |
| Read Live Chat          | Grants access to read live server chat.                                                               | on                |
| Send Live Chat          | Grants access to send live messages from Refractor.                                                   | on                |

### Permission Overrides

You can apply permission overrides for groups on servers. This could be useful in many cases. For example, if you had different moderation teams for different games you could give the Mordhau admins access to Mordhau servers and deny them access to Minecraft servers all using overrides.

There are some situations where **allow** overrides may not work how you expect. For example, if you have a group called Test which does not have permission to View Chat Records, they will not be able to view chat records even if you grant them an allow override on a server because they will not be able to access the chat records page in the first place. There are very few situations where this would be a problem, but it's worth keeping in mind.
