# Blocking bad actors

In this exercise you will learn how to configure Wazuh to automatically block
malicious IP addresses from accessing resources.
For demonstration purposes, we are going to use the NextCloud instance from
[this guide](../Server/nextcloud.md).

For the attacker, I've set up a small [Kali
Linux](https://en.wikipedia.org/wiki/Kali_Linux) machine.
Details can be found on the week plan.

In this exercise you'll use the Wazuh [CDB
list](https://documentation.wazuh.com/current/user-manual/ruleset/cdb-list.html)
and [Active
Response](https://documentation.wazuh.com/current/getting-started/use-cases/incident-response.html)
capabilities.

It is a demonstration of using Wazuh to detect and respond to threats.

You will use your NextCloud instance as target for a simulated attack and
an automated response.
The attack consists of a known bad actor trying to a web request to the target.
The response will be temporarily blocking the IP of the attacker.

## Finding IP addresses

To test the setup you will be doing in this guide, you need to the IP of
something that simulate a malicious actor (attack-box).
You will also need to know the IP of the target (your NextCloud instance).

> [!tip]
> The command to see IP addresses is:
>
> ```sh
> ip address
> ```

### Attack-box IP

You can use the **attack-box** described on moodle for this purpose.
Logon to it over SSH, and find its IP address.

In the rest of this guide, whenever you see `<ATTACKER_IP>`, you need to replace
it with the IP of the attack-box.

### Target IP

The target will be your NextCloud instance.
Logon to it over SSH, and find its IP address.

In the rest of this guide, whenever you see `<TARGET_IP>`, you need to replace
it with the IP of your NextCloud instance.

## Target configuration

To begin, check that NextCloud is accessible by entering the IP the address bar
of your browser.

```
http://<TARGET_IP>/nextcloud
```

If it works, then you can proceed.

Logon to the target.

```sh
ssh <USER>@<TARGET_IP>
```

You will need to configure the Wazuh agent to monitor the Apache access log.
Try to `cat` the log, to see what it looks like.

To edit the configuration file, run:

```sh
sudoedit /var/ossec/etc/ossec.conf
```

Add the following lines:

```xml
<localfile>
  <log_format>syslog</log_format>
  <location>/var/log/apache2/access.log</location>
</localfile>
```

Save and exit.

For the changes to take effect, you need to restart the agent:

```sh
sudo systemctl restart wazuh-agent
```

## Wazuh server configuration

The following steps are performed on the Wazuh server.
You need to logon to it using SSH.

### Create blacklist

We are going to download a list of IP addresses of known malicious actors (IP
reputation database) from FireHOL.

> [!tip]
> Most of Wazuh is installed in the `/var/ossec` directory.
> It is called ossec because Wazuh is a fork of
> [OSSEC](https://en.wikipedia.org/wiki/OSSEC).

```sh
sudo wget https://iplists.firehol.org/files/alienvault_reputation.ipset -O /var/ossec/etc/lists/alienvault_reputation.ipset
```

*The file is downloaded from
<https://iplists.firehol.org/files/alienvault_reputation.ipset> and outputted
(`-O`) to `/var/ossec/etc/lists/alienvault_reputation.ipset`.*

To be able to test the setup, append the IP address of the attack-box to the IP
reputation database, using the commands below:

```sh
sudo su
echo "<ATTACKER_IP>" >> /var/ossec/etc/lists/alienvault_reputation.ipset
exit
```

The IP reputation database you downloaded is in `.ipset` format, but Wazuh
works with `.cdb` format.
A script is needed to convert the database to the format that Wazuh
understands.
Luckily, Wazuh provides a Python script for just that.
You can download the script to a temporary location (`/tmp`) with:

```sh
sudo wget https://wazuh.com/resources/iplist-to-cdblist.py -O /tmp/iplist-to-cdblist.py
```

You can now use the version of Python that comes bundled with Wazuh
(`/var/ossec/framework/python/bin/python3`) to run the script
(`/tmp/iplist-to-cdblist.py`) to convert the `.ipset` file to a format that can
be understood by Wazuh.
The output will be stored in `/var/ossec/etc/lists/blacklist-alienvault`.

```sh
sudo /var/ossec/framework/python/bin/python3 /tmp/iplist-to-cdblist.py /var/ossec/etc/lists/alienvault_reputation.ipset /var/ossec/etc/lists/blacklist-alienvault
```

Change ownership of the newly created `blacklist-alienvault` file to the `wazuh` user.

```sh
sudo chown wazuh:wazuh /var/ossec/etc/lists/blacklist-alienvault
```

### Configure active response

Configure an [active
response](https://documentation.wazuh.com/current/user-manual/capabilities/active-response/index.html)
in wazuh that blocks IPs based on the blacklist created above.
This can be done by specifying a custom [rule](https://documentation.wazuh.com/current/user-manual/ruleset/rules/index.html).
To add a new rule, run:

```sh
sudoedit /var/ossec/etc/rules/local_rules.xml
```

Then append the following to the file:

```xml
<group name="attack,">
  <rule id="100100" level="10">
    <if_group>web|attack|attacks</if_group>
    <list field="srcip" lookup="address_match_key">etc/lists/blacklist-alienvault</list>
    <description>IP address found in AlienVault reputation database.</description>
  </rule>
</group>
```

Save and exit.

Edit the `/var/ossec/etc/ossec.conf` configuration file.

```sh
sudoedit /var/ossec/etc/ossec.conf
```

Then add the `<list>etc/lists/blacklist-alienvault</list>` between `<ruleset>`
and `</ruleset>`.
Then near the existing `<active-response>` section, add:

```xml
  <active-response>
    <disabled>no</disabled>
    <command>firewall-drop</command>
    <location>local</location>
    <rules_id>100100</rules_id>
    <timeout>60</timeout>
  </active-response>
```

Restart the Wazuh manager to apply the changes.

```sh
sudo systemctl restart wazuh-manager
```

### Simulating an attack

Simulating an attack is actually pretty simple.
You just need to login to the attack-box.

```sh
ssh kali@<ATTACKER_IP>
```

Then from the attack-box, make a request to the target.

```sh
time curl http://<TARGET_IP>
```

*`curl` makes a request and `time` shows how long it took*

If you make a second request, then you will see that it takes over a minute.
This slows down attacks to the point where it becomes impractical.

### Cleanup

Before moving on to the next exercise, you need to do a bit of cleanup.
Because you will be using the attack-box to test other things, then you'll need
to disable the blocking again.

On the Wazuh server, edit ``.
Find the `<active-response` section you just inserted.
Change the line `<disabled>no</disabled>` to `<disabled>yes</disabled>` and
restart the service with:

```sh
sudo systemctl restart wazuh-manager
```

You can test that the response is disabled by making more requests from the
attack box.

### Source

This guide was based on the [Blocking a known malicious
actor](https://documentation.wazuh.com/current/proof-of-concept-guide/block-malicious-actor-ip-reputation.html)
guide from Wazuh documentation.
