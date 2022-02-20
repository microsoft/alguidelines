+++
title = "Nav Upgrade"
weight = 840
+++
## Anti-Patterns in NAV Upgrade

_By Carlos Raul Garcia and Bogdana Botez at Microsoft Development Center Copenhagen_

[![ ][image0]][anchor0]

**Context**: when NAV is upgraded, whether on-premises or in the cloud, developers have the chance to write upgrade code to move data across changing data structures. Writing good quality code will help successful upgrades.

### General on upgrade

**Problem:** assuming that the upgrade table contains data.

If the table is empty, it means that either the upgrade has run, or there was no data in the original tenant; in both cases, the upgrade code should exit immediately.

**Solution:** if using an upgrade table, always validate that the table contains data before doing anything.

### Upgrade code, can it be rerun safely?

**Problem**: if the upgrade code is not written in a way that makes it runnable twice ([idempotent][anchor1]), then several failures can happen, including something as critical as data corruption.

In on premise NAV installations, if something fails at upgrade, there is no way to run only the "remaining" tasks. You will need to run the whole upgrade again, and might end-up with data that you cannot trust.

What about the cloud? In Platform As A Service (PaaS), in some situations, the upgrade code needs to be run twice (for example, when moving tenants from a broken/frozen VM to a healthy one).

**Solution:** Make sure each of your upgrade procedures only kicks in if it didn't run before.

**Examples**

The examples below have happened in real live NAV PaaS upgrade:

**Table data overwrite**

* **Problem**: at upgrade, a new column has bee­n added to a table and initialized with default values. In the meanwhile, during production, some of the default values are changed to production real life values. The second time the upgrade runs, those values will be overwritten with defaults, any personalization lost.
* **Solution:** before initializing with default values, check if non-default values exist.

**Crash on math operations**

* **Problem:** one tenant upgrade managed to divide by zero, by assuming a non-zero value.
* **Solution:** don't assume values can never be zero, always check before using them in divisions.

**Use of external components**

* **Problem:** a one-time registration through web services to an external service failed when attempting to register a second time.
* **Solution:** check if already registered, before attempting again.

### Parallelism

**Problem**: Upgrade procedures can be run in parallel, causing issues when different procedures attempt to modify the same table at the same time.

When modifications to the same table are being made from two or more different procedures, there is no guarantee on sequential run, or on a certain order they will be run in.

**Solution**: if sequential or ordered execution is needed, make the affected upgrade procedures local and call them all, in the desired sequence, from a public upgrade procedure.

### Access to cloud machines

**Problem:** dependencies on manual installation steps do not fit in the cloud.

If Dynamics NAV is installed on-premises, then any additional setup (like dependencies of external dlls, manual configuration steps etc.) can be done manually or semi-manually by the IT admin, at first setup and upgrade.

In the cloud, NAV partners don't have access to the machines -- hence they cannot deploy and configure those external dependencies as they did in the old on-premises installations.

**Solution:** Don't assume you will have access to PaaS or SaaS machines. Build your solution in such a way that it doesn't depend on executing manual configurations on the host machine.



[anchor0]: upgrade.png
[anchor1]: http://stackoverflow.com/questions/1077412/what-is-an-idempotent-operation#1077421


[image0]: upgrade.png
