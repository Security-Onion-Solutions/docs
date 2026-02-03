# Disabling Accounts

## OS

If you need to disable an OS user account, you can expire the account using `usermod --expiredate 1`.  For example, to disable the account for user `tom`:


    sudo usermod --expiredate 1 tom

For more information, please see passwd manual by typing `man passwd` and the usermod manual by typing `man usermod`.

## SOC

If you need to disable an account in [SOC](security-onion-console.md), you can go to [Administration](administration.md) --> Users, expand the user account, and click the `LOCK USER` button.

![Image](images/82_users_detail.png)

After disabling a user account, it will be shown with a disabled icon in the Status column.

For more information about the Users page, please see the [Administration](administration.md) section.