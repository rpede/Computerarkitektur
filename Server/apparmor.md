# AppArmor

> [!caution]
> This guide is still very much work-in-progress.

AppArmor is a solution to further restrict capabilities and permissions of a
program than what is offered by the traditional UNIX security model.

## Traditional UNIX security

In the traditional UNIX security model we have users and groups.
A user can belong to one or more groups.
There is one special user known as `root` (also sometimes referred to as
super-user) has full access to the system.
Some system calls (syscalls) are restricted to only the root user.

Each file system entry (files, directories &
[symlinks](https://www.wikihow.com/Linux-How-to-Symlink)) are owned by a user
and group.
Permission metadata on the file specifies what the owning user can do, what the
owning group can do and what everyone else can do to the entry.
The following permissions can be set user, group and others:

- **r** permission to read the content.
- **w** permission to write to it.
- **x** permission to execute (as a program or script).

You can more about Linux permissions
[here](https://www.redhat.com/en/blog/linux-file-permissions-explained).

When a program is started the process has same rights as the user who started
it.
They run as that user.
It can therefore only access the same files as that user.

## Strengthen security with AppArmor

AppArmor adds to the traditional security model with program specific policies.
A security policy in AppArmor is called a profile.
A policy allows specifying what parts of the file system a process can access.
It also allows fine-grained control over what [Linux
capabilities](https://www.man7.org/linux/man-pages/man7/capabilities.7.html)
are permitted.

*Remember: a process is created when executing a program.*

## AppArmor profiles

An AppArmor profile is a security policy for an individual program.

Ubuntu comes with AppArmor pre-installed.
You can see if it is running with:

```sh
sudo aa-status
```

Extra profiles can be installed with:

```sh
sudo apt install apparmor-profiles apparmor-profiles-extra
```

If you run `sudo aa-status` after installing the above packages, then you will
see a lot more profiles.
There are even profiles for things like Discord, steam, obsidian and various
web-browser.
However, those profiles aren't very useful on a server.
But, mind you that Ubuntu can also be used as an everyday desktop OS.

For some complex programs, such as Apache with all its modules, it can be
useful to have different sub-profiles for different parts of the program.
In AppArmor terminology this is called changing hats.

You can read more about AppArmor hats
[here](https://documentation.suse.com/sles/15-SP6/html/SLES-all/cha-apparmor-hat.html).

```sh
sudo apt install libapache2-mod-apparmor
sudo a2enmod apparmor
sudo systemctl restart apache2
```
