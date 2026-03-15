# Unattended upgrade

Since new vulnerabilities are continuously being discovered.
It is important to keep systems up to date, with new security patches.

Luckily, Ubuntu got a built-in feature that can take care of this.
It is called "Unattended upgrades".
Updates on Linux can generally be installed while the system is running.
Occasionally the component being updated needs to be restarted for the update
to take effect.
It is relatively rare that the system needs a full reboot.
While updates are being installed, the system (for the most part) can continue
to function as normal.
Updates on Linux generally have much lower risk than updates on Windows.

To check that "Unattended upgrades" are installed on your Ubuntu Server, run
the following command:

```sh
apt search unattended-upgrade
```

It should say "installed" somewhere in the output.

"Unattended Updates" will periodically run the equivalent of `sudo apt update`
and `sudo apt upgrade`.

You can see how often update are being run with the following command:

```sh
cat /etc/apt/apt.conf.d/20auto-upgrades
```

The number at the end of each line is the number of days.
A value of "0" will disable it.
A value of "1" means each day.
"2" means every other day.
And so on.

You can see when it will run again, by checking the status of the trigger with
this command:

```sh
systemctl status apt-daily-upgrade.timer
```

Here is what the output looks like:

```
● apt-daily-upgrade.timer - Daily apt upgrade and clean activities
     Loaded: loaded (/usr/lib/systemd/system/apt-daily-upgrade.timer; enabled;>
     Active: active (waiting) since Tue 2026-03-10 08:49:39 UTC; 3h 54min ago
    Trigger: Wed 2026-03-11 06:38:56 UTC; 17h left
   Triggers: ● apt-daily-upgrade.service
```

You can see that there are 17 hours left until next time it will trigger.

You can see the update log with:

```sh
cat /var/log/unattended-upgrades/unattended-upgrades-dpkg.log
```

There are many configuration options that can be set for "Unattended Upgrades".
Among them are, skipping updates for some packages, postponing updates, or
sending summary by email.

You can read more about "Unattended Upgrades" in [Ubuntu Server - Automatic
updates](https://ubuntu.com/server/docs/how-to/software/automatic-updates/#where-to-pick-updates-from)
documentation.
