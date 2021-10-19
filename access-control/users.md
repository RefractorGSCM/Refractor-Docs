# User Management

![Users page](/images/users_page.png)

You can get to the user management page by navigating to `Admin Settings > Users`.

## Inviting Users

You can invite users to join Refractor using the Invite User button on the bottom left. A modal will appear asking you to provide the new user's email and username. Users are able to change their email and username later on.

The account setup process is as follows:

1. An invitation email is dispatched to the user's email

2. The user clicks in the link in the email. They are Redirected to the account settings page where they are instructed to set a password.

3. The user sets a password and is redirected to the email verification page where a verification email is dispatched to their email.

4. The user opens the verification email, clicks the link and is redirected to Refractor.

5. The user's account is now set up and activated. They can begin using Refractor.

## Managing User Groups

Select a user on the left sidebar and a management screen for the selected user will open on the right side of the screen.

From this screen, you can check any groups you want to assign them and uncheck any you want to remove. These changes take effect instantly. The user must reload the application for any visual access changes to take place.

## Deactiving User Accounts

To maintain integrity of infractions and other data records, **you cannot delete user accounts**.

Instead, you can opt to deactivate an account from the user management screen by clicking on the "Deactivate Account" button.

Deactivated users can still log in, but will be unable to access any protected features of Refractor or any of the application and will be presented with an unauthorized screen upon visiting Refractor.

## Linking Players

To support future features, we have support for linking players to user accounts. To do this, click the "Link Player" button while managing a user account.

![Link player section](/images/linking_players.png)

Upon clicking "Link Player", you will be presented with the player search screen. Search for the player you wish to link and when you find them, select them and click "Select Player". If this player is not already linked to another user, the linking will be successful.

Just a reminder: there are currently no features which use linked players. That said, it is still highly recommended that you link users to their player records so that when features are added that **do** rely on linked players, you will have less setup work to do.

## Development Notes

1. Email verification could technically be completed once they accept the invitation without the second verification email. Due to our current user management setup with ORY Kratos, this is not feasible or worthwhile to implement at this time and could raise some security concerns. Please bear with this slightly verbose user signup process for now.

2. The current email templates are just markup and contain no styling. As such, they are not very pleasing to look at. If anyone would like to
   improve the aesthetic of these templates, contributions are welcome!

3. Deactivated users can likely be deleted from ORY Kratos without damaging the integrity of records. The decision to disallow account deletion was made before we had the UserMeta database table. This means that technically user identities _could_ be deleted in ORY Kratos since we now reference the UserMeta table for records tied to a user identity (e.g Infractions). This is not a pressing change as it doesn't affect the authorized end user's experience at all, only the experience of deactivated users.
