---
layout: post
title: "MySQL: What the Hell is the --secure-file-priv option?"
date: 2017-02-16
---

### Task: Retrieve Data From a Dump File

A coworker had some [MySQL dump files](https://dev.mysql.com/doc/refman/5.7/en/mysqldump-sql-format.html) but no longer had a MySQL installation. I thought it would be a quick job to convert the dump files into something more portable.

I could do a quick install of MySQL on my Mac, bring in the dump file and export it to a portable format. I'd just use [Homebrew](https://www.postgresql.org/) to install it and I'd be rolling. Wait! Homebrew is hosed on my computer. So first a detour to get Homebrew working.

### Installing MySQL on a Mac with Homebrew

The directory permission were changed when I upgraded to OSX 10.11 (El Capitan) I hadn't had cause to figure out the [fix](https://digitizor.com/fix-homebrew-permissions-osx-el-capitan/). Also I'd subsequently upgraded to macOS 10.12 (Sierra).

When Homebrew is working it's a dream. You install open source software just like you would in Linux with [apt](https://wiki.debian.org/Apt) or [yum](https://en.wikipedia.org/wiki/Yellowdog_Updater,_Modified). After a bit of googling I found [instructions to uninstall Homebrew](http://superuser.com/questions/203707/how-to-uninstall-homebrew-mac-os-x-package-manager).
```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"
```
Next I followed the [one-step instructions at brew.sh](https://brew.sh/)
```bash
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
and I was back in Homebrew action.

A not-to-quick
```
brew install mysql
mysql.server start
```
and I was ready to roll<sup>[1](#myfootnote1)</sup> with MySQL. Or was I?

### What the Hell is the --secure-file-priv option?

With many thanks to [Stack Overflow](http://stackoverflow.com/questions/17666249/how-to-import-an-sql-file-using-the-command-line-in-mysql) I was able to quickly load the dump files into my brand new MySQL server:
In mysql:
```sql
create database sailwx_info_shiptracks;
```
At terminal prompt:
```bash
mysql -u root -h localhost sailwx_info_shiptracks < duwamish1.sql
mysql -u root -h localhost sailwx_info_shiptracks < duwamish2.sql
mysql -u root -h localhost sailwx_info_shiptracks < duwamish3.sql
```

After some poking around I found out about [```SELECT ... INTO OUTFILE```](https://dev.mysql.com/doc/refman/5.7/en/select-into.html) syntax for MySQL. The more portable alternative to the dump files I started out with. I thought my job was complete, but then I see it, an error I don't understand.

```sql
select * from reportSources into outfile '/Users/paulmccombs/reportSources.txt';
ERROR 1290 (HY000): The MySQL server is running with the --secure-file-priv option so it cannot execute this statement
```

### Oh, That's What it Means!

I googled my heart out and read many pieces of advise across several different forum postings. No one had the complete answer for me, and that is what inspired me to share my struggle. The first key to removing my confusion was learning of [a change at MySQL version 5.7.6](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-6.html)

>"*secure_file_priv can be set to NULL to disable all import and export operations.*
>
>*The server checks the value of secure_file_priv at startup and writes a warning to the error log if the value is insecure. A non-NULL value is considered insecure if it is empty, or the value is the data directory or a subdirectory of it, or a directory that is accessible by all users. If secure_file_priv is set to a nonexistent path, the server writes an error message to the error log and exits.*
>
>*Previously, the secure_file_priv system variable was empty by default. Now the default value is platform specific and depends on the value of the INSTALL_LAYOUT CMake option, as shown in the following table.*"

Many of the forum posts I found were written before this change and confused me horribly. The default value of secure_file_priv for the Homebrew install of MySQL 5.7.17 was NULL, preventing me from writing outfile entirely. To change secure_file_priv I first had to find the configuration file. According to my research it was named my.cnf and located in one of dozens of different places depending on who you should believe. The forum posts I found were split between many versions of MySQL and various operating systems.

### The Solution

I found the [secret](http://stackoverflow.com/questions/7973927/for-homebrew-mysql-installs-wheres-my-cnf)! Just ask mysql where it was looking for my.cnf.
```bash
mysql --help | more
```
Which returned in part the following list of locations:
>"*Default options are read from the following files in the given order:
/etc/my.cnf /etc/mysql/my.cnf /usr/local/etc/my.cnf ~/.my.cnf*"

The Homebrew install does not create a my.cnf by default. So I had to make one of my own. I decided to put the configuration file in my user directory and found [this helpful tip](https://github.com/piwik/piwik/issues/9528) on what to put in it.

After creating ~/.my.cnf with the following contents, I was ready to try creating my outfile again.
```
[mysqld]
secure_file_priv               = ''
```
At terminal prompt:
```bash
mysql.server restart
```
In mysql:
```sql
mysql> select * from reportSources into outfile '/Users/paulmccombs/reportSources.txt';
Query OK, 12 rows affected (0.04 sec)
```

Success!!!

<a name="myfootnote1"><sup>1</sup></a> Note here I am starting MySQL manually as I don't intend to have the server running on my computer all of the time. Just for the one project. So there is no need for me to set it up to auto start.