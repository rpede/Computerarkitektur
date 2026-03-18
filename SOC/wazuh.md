# Wazuh

## What is Wazuh

Wazuh is an open source SIEM and XDR.
It can be used as a single pane of glass (SPOG) into your organizations
IT infrastructure.

- It gives high visibility to everything that going on at the endpoints.
- It can detect threats and provide security alerts.
- It can be configured to automatically respond to threats.
- Integrate with external tools to detect, provide context and respond to threats.

A SOC (Security Operations Center) analyst will spend a good portion of their
day looking for and analyzing suspicious activity with a tool like Wazuh.

Wazuh consists of the following components:

- **Indexer** scalable, full-text search and analytics engine.
- **Server** process and analyze data.
- **Dashboard** web interface for data visualization, analysis, and management.
- **Agent** threat prevention, detection and response capabilities on endpoints.

For a Wazuh setup, you need a central installation of **indexer, server and dashboard**.
For brevity, we will think of these components as a single logical unit and
call it Wazuh server.
Then we have the **agent**.
This is what gets installed on the systems what you want to monitor with Wazuh.
That is all your end user devices (Windows, macOS, Linux) and your servers
(Linux, Solaris, AIX etc.).

The **agent** is installed on the system you want to manage with Wazuh.

## Getting started

You need to make a new VM for Wazuh server.
We will go with Ubuntu Server configured 4 cores, 8GiB memory and 50GB storage
according to [Wazuh
Requirements](https://documentation.wazuh.com/current/quickstart.html#requirements).

1. Follow [create VM guide](../Virtualization/proxmox-create-vm.md).
    - Remember: 50GiB disk, 4 cores and 8192 MiB memory.
2. [Install Wazuh server](https://documentation.wazuh.com/current/quickstart.html#installing-wazuh) on your new VM.
    - Make sure you follow instructions for "APT (Debian/Ubuntu)".
    - Remember to take note of the password.
3. [Install Wazuh
   agent](https://documentation.wazuh.com/current/installation-guide/wazuh-agent/wazuh-agent-package-linux.html)
on your NextCloud VM.
    - Make sure you follow instructions for "APT".

## Exercises

Following are some lab exercises designed to provide initial exposure to Wazuh.
They are based on [Proof of Concepts guide](https://documentation.wazuh.com/current/proof-of-concept-guide/index.html) from the Wazuh documentation.
Through these exercises, you won't get exposed to the full capabilities Wazuh as they are selected to be on the lower end of the complexity scale.
Also, these exercises have been customized for this [NextCloud
setup](/Server/nextcloud.md).

## Limitations

**Configuration**

Wazuh probably requires some more configuration to function as a full XDR than
some solutions from big-buck vendors.

**Mobile**

In many organizations Mobile devices have become as important as desktops.
The Wazuh agent doesn't run on iOS and Android.

**Artificial intelligence**

Solutions from big-buck vendors have adopted AI to help derive sense of the
large quantities of data being collection.
Though Wazuh can integrate with AI tools, it doesn't come with it out of the
box.
