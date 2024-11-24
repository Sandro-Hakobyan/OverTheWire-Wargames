## Login

SSH: `ssh leviathan0@leviathan.labs.overthewire.org -p 2223`

Password: `leviathan0`

## A little bit of Theory

`Backups` are an important part of data security. You create a copy of your important files in case of data loss. However, if not correctly secured, backups might be a way for attackers to do **privilege escalation**. Privilege escalation is a way for an attacker to get more or different access to your computer. In the case of this wargame, we will get access rights of other users.

**Revision:** `ls -la` explained in [Bandit Level 1](), [Bandit Level 20]() `grep` explained in [Bandit Level 6]()

## Solution

After logging into the machine through ssh, we can get an overview of the home directory of the current user:

```bash
leviathan0@leviathan:~$ ls -la
total 24
drwxr-xr-x  3 root       root       4096 Aug 26  2019 .
drwxr-xr-x 10 root       root       4096 Aug 26  2019 ..
drwxr-x---  2 leviathan1 leviathan0 4096 Aug 26  2019 .backup
-rw-r--r--  1 root       root        220 May 15  2017 .bash_logout
-rw-r--r--  1 root       root       3526 May 15  2017 .bashrc
-rw-r--r--  1 root       root        675 May 15  2017 .profile
```

Looking at the permissions for the different users and groups, we can see that we - as user ’leviathan0’ - have read (and execute) permissions for the `.backup` folder. So we can check out its content:

```bash
leviathan0@leviathan:~$ cd .backup/
leviathan0@leviathan:~/.backup$ ls -la
total 140
drwxr-x--- 2 leviathan1 leviathan0   4096 Aug 26  2019 .
drwxr-xr-x 3 root       root         4096 Aug 26  2019 ..
-rw-r----- 1 leviathan1 leviathan0 133259 Aug 26  2019 bookmarks.html
```

So the folder contains one file called `bookmarks.html`. However, we can also see that it is a very large file. So manually looking through the content is not a good idea. Still, let’s first get an idea of the structure of the content:

```bash
leviathan0@leviathan:~/.backup$ head bookmarks.html 
<!DOCTYPE NETSCAPE-Bookmark-file-1>
<!-- This is an automatically generated file.
     It will be read and overwritten.
     DO NOT EDIT! -->
<META HTTP-EQUIV="Content-Type" CONTENT="text/html; charset=UTF-8">
<TITLE>Bookmarks</TITLE>
<H1 LAST_MODIFIED="1160271046">Bookmarks</H1>

<DL><p>
    <DT><H3 LAST_MODIFIED="1160249304" PERSONAL_TOOLBAR_FOLDER="true" ID="rdf:#$FvPhC3">Bookmarks Toolbar Folder</H3>
```

Based on the file name, it is an Html file that contains bookmarks. Looking at the first few lines of the file seems to confirm that. Now, there are different ways to look through the file, as well as different things to look for. We could search for a password, but since it contains bookmarks, my idea was to search for ’leviathan’ with the `grep` command:

```bash
leviathan0@leviathan:~/.backup$ cat bookmarks.html | grep leviathan
<DT><A HREF="http://leviathan.labs.overthewire.org/passwordus.html | This will be fixed later, the password for leviathan1 is 3QJ3TgzHDq" ADD_DATE="1155384634" LAST_CHARSET="ISO-8859-1" ID="rdf:#$2wIU71">password to leviathan1</A>
```


---
### Official Level Link

[Leviathan Level 1](https://overthewire.org/wargames/leviathan/leviathan1.html)
