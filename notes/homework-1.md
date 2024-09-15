# Homework 1 Notes

## User account management in Unix/Linux

### Creating an account
Use `useradd <username>` to add a new user.

However, note that a user will not be able to log in until they have a password. To set a password for a user, use `passwd <username>`.

### Adding users to groups
Use `usermod -aG <group_id | group_name>`. Note that you can specify either the group ID (gid) or the name of the group.

The `-G` flag indicates the "supplementary groups" we want to add the user to. The `-a` flag, when used in conjunction with `-G`, specifies that we should ***a**ppend* the group(s) to the user's list of groups.

Example usage: `usermod -aG wheel karinen`\
This adds `karinen` to the `wheel` group.
- The `wheel` group usually gives you `sudo` access, meaning you can execute commands with superuser (root) permissions.

### Removing users from groups
Use `gpasswd -d <username> <group_name>` To remove the user from the group.
- Note that with this command you will need to specify the group name and not the group ID.

### View all groups and which users belong to them
Generally, Unix/Linux follows the philosophy of storing every configuration option as text in a file located somewhere on the system.

To see all groups, visit the text file located at `/etc/groups`.

### View all users
To see all users, go to `/etc/passwd`.

## In sum
Just like any other system, you can create users and allow them to login with passwords. Users can belong to one or many groups. Groups are generally used to control permissions for multiple users.
