---
title: From LastPass to a free password manager
date: 2021-02-21
summary: >
  LastPass is charging for multi-device support. Learn how to do this for
  free.
---

# tl;dr

- Export your LastPass passwords as CSV.
- Import CSV into KeePass as a new database.
  - Ensure the columns are mapped properly.
- Sync database between devices.

If you are an exclusive Apple user, use the Apple's built-in password manager.

# Motivation

Recently [LastPass announced that it would start charging for allowing users to
access their passwords using a desktop client and a mobile
client](https://blog.lastpass.com/2021/02/changes-to-lastpass-free/). There was
a time that the mobile app for LastPass was pay to use. I started looking into
LastPass alternatives and found that there are many password managers that are
much more secure. That said, it's worth noting that LastPass _is_ secure. 

I was looking for a password manager that was easy to use across devices
(mobile support is important to me), that allows me to export my passwords as a
file, is open sourced and actively developed. A password manager that is open
sourced and actively developed means that any security vulnerabilities could be
quickly addressed and any privacy concerns could be audited by folks who can
follow through the source code. This means that the interests of the users are
above any kind of interest when it comes to making money (since it's truly free).

# Password manager of choice

I ended up choosing [KeePassXC](https://keypassxc.org) as my password manager of
choice. The reason I chose [KeePassXC]() over KeePass (or other forks) is
because [KeePassXC](https://keypassxc.org) is actively developed and it's
written in C++ (as opposed to C#, which means that it can natively compile
across different operating systems).

I downloaded the latest version of [KeePassXC](https://keepasdsxc.org/download/)
on my desktop, where I would initially migrate my passwords.

# Exporting passwords from LastPass

In order to get [KeePassXC](https://keypassxc.org) up and running, I wanted to
migrate my passwords from LastPass.

[LastPass offers the ability to export your
Vault](https://support.logmeininc.com/lastpass/help/how-do-i-nbsp-export-stored-data-from-lastpass-using-a-generic-csv-file).
Download the CSV into an easy to remember location, because this will be used to
import your passwords.

# Importing into KeePassXC

When you create a database in KeePass, you have to specify where you want to
store the database. The database is an encrypted file that is stored in your
operating system. This will be helpful if you want to sync it to a cloud
provider.

In KeePassXC, go to:

```
Database -> Import -> Import CSV File
```

Select the CSV generated above **but do not import yet! The format of LastPass's
CSV does not conform to the default mapping KeePassXC specifies.** Make sure
that you confirm what the import looks like in the preview window. At the time
of writing, I used the following mapping:

| column name   | LastPass column |
| ---           | ---             |
| Group         | column 6        |
| Title         | column 5        |
| Username      | column 2        |
| Password      | column 3        |
| Url           | column 1        |
| Notes         | column 4        |
| Last Modified |                 |
| Created       |                 |

Once the mapping looks okay, import the passwords.

# Cloud Sync

I personally used Google Drive to backup my KeePass database. I installed Google
Drive, created a folder in Google Drive, selectively chose only that folder for
sync (I don't want to sync all my Drive to my computers), and dropped the
KeePass database in there. This functionality is cross platform compatible, and
totally free (granted, I'm using Google for cloud storage). This step is
critical for mobile support.

# Mobile Support

I'm an iOS user that has a Windows desktop. This means that I don't really get
the tight mobile/desktop integration that Apple provides. That said, there are
only two things to worry about to make sure mobile support is provided:

- Being able to download the KeePass database from the cloud
- A program to load the KeePass database

I found [Strongbox](https://strongboxsafe.com/) to be the best iOS app to use.
It allows for both a local KeePass database in iOS as well as support for
multiple cloud providers (including iCloud).

I connected the application to my Google Drive, and loaded up the keypass
database. This allowed me to view all my passwords in Strongbox, which meant
that the mobile integration was basically done.

The last step to complete the mobile integration was enabling the iOS feature to
have Strongbox fill in my passwords. To do so, I did the following:

```
Settings -> Passwords -> AutoFill Passwords -> Strongbox
```

Once enabled, any password prompt will source passwords from the KeePass database.

# Closing thoughts

I like that this is all free, and is using open source tools. The only
questionable bit of this is the cloud sync, which could be safer by using a
privacy focused cloud provider such as [NextCloud](https://nextcloud.com/).

That said, this requires a bit of configuration to get going. I think if I had
an Apple-based desktop/laptop, I would just use the native Apple password
manager, as it's built into all their products and syncs across your iCloud.
