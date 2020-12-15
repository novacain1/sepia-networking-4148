# sepia-networking-4148
This playbook is responsible for the full configuration aspects of the Dell PowerSwitch S4148F-ON switch pair that powers the Ceph.com community cage private network.  The switches are configured in a vLT (Dell's Multichassis Link aggregation feature) with full HA down to all of the Dell PowerEdge R730xd, R730, R220 systems in the rack itself.

Note that the switches *must* be reachable via their IPv4 management interface (typically mgmt 1/1/1).  It is assumed that the switch pair is reachable and the admin username and credentials are known.  This repo takes care of every other configuration task (system, stp, vlt, lag, interfaces, vlans, ntp, etc).

## Instructions & Prerequisites

1. `git clone https://github.com/novacain1/sepia-networking-4048pair`
2. Edit `hosts` file and set ansible_password to the proper value
3. Locally download the Galaxy Dell Networking roles that are written specifically to modify Dell switches - `ansible-galaxy install -r ansible-roles/dellos10_roles.yml`
4. Change any desired configuration parameters in the `host_vars` directory for either sw01 or sw02
5. Run the playbook with `ansible-playbook dell-switches-sepialab.yml.``

## Tested on

* Dell PowerSwitch S4148 (OS10 Network System v.10.5+).
* Ansible 2.9.13 with Python 3.8.5.

## Other notes

1. If you get a failure of execution due to not being able to iterate over a dictionary in python, more than likely the Dell OS10 modules need patching.
    +  Look under the directory templates/dellos10_system.js
    + I ran into issues with both the system role and the lldp role.
2. I found the DNS module on galaxy to not even support OS10.  Look under Github and download straight from there.
3. I found the system module did not support setting a management route on OS10.  Use the command `management route 0.0.0.0/0 172.21.47.254` if using static IPs.
4. If you have already run this playbook once, running it in again may cause errors as the vLT is already configured.
    + The hammer method is to delete the vLT configuration and port-channels, which will allow you to launch this playbook again.  This will be disruptive to network traffic, so be careful if choosing this option.

## Other helpful Links
* https://ansible-dellos-docs.readthedocs.io/en/latest/intro.html
* https://github.com/Dell-Networking/
