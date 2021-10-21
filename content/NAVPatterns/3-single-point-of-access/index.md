+++
title = "3-single-point-of-access.md"
weight = 160
+++
## Single Point of Access

_By Bogdana Botez at Microsoft Development Center Copenhagen_

_[![ ][image0]][anchor0]   
_

__

**Context**: Protected data needs to be used. There are many types of entities which might attempt to use the data.

**Problem**: If no standard way of accessing data exists, then each entity might attempt to build its own system for handing the sensitive data. The data access layer might be implemented over and over again by each entity, without reuse of known best practices and with a lot of code duplication.

****

**Forces:**

* **Code duplication: **if each entity attempts to write its own routines for data access, invariably this will bring duplication.
* **No knowledge reuse: **if a bug is found and fixed in one of the implementations, there is no guarantee that all the other implementations will be updated. For example
  * **Double-Encryption**: lack of care or knowledge could lead a NAV developer to attempt to double-encrypt strings, which would render them unusable.
  * **Multi-company configuration \[4\]**: in NAV, it is possible to store multiple companies on the same tenant \[5\] database. A developer who has not investigated such a configuration and its consequences on encryption, can attempt to encrypt data in (for example) a Setup Table \[6\] of only one company, which would make this table unusable from any unencrypted company (since the server will observe that encryption is enabled, and try to retrieve it as it were enabled for all companies and fails).
  * **"Shotgun surgery" \[7\]:** one change in the data access technique (like a new requirement to validate the user's identity before viewing any protected data), calls for updates in every single implementation of data access, if multiple implementations exist. Hence, one change of requirement triggers multiple efforts to update the product.

**Solution:** In Dynamics NAV the same library which offers [**Encryption**][anchor1], is also intended to be used as a **Single Point of Access**. Write code to create, read and remove sensitive data only through codeunit 1266 Encryption Management, and never directly.

In Figure 9 (figure numbering is continued from pattern **[Encryption][anchor2]**), the panels 1, 2 and 3 have applied the [**Sensitive Data Encapsulation**][anchor3] and [**Encryption** ][anchor1]patterns. Once data is encapsulated, no matter if it has been encrypted or not, the next pattern, **Single Point of Access**, becomes available as seen in the panels 2 and 3 of Figure 1\.

You can observe that, in panel 3, each usage needs to access separately the encrypted data. This means that each usage needs to implement again the encryption capabilities. This is resolved in panel 4, where all users access one common API which encrypts, hence the workload of encryption is moved from the usages, to the API, and needs to be implemented only once.

[![ ][image1]][anchor4]

_Figure 1- **Single Point of Access **pattern applied._

This shows how pattern application is an iterative process, where one step follows another. Refactoring \[8\] the code to apply one pattern, cleans and clarifies the code and in some cases, makes clear the possibility of further refactoring.

**Usage**: call the procedures available in codeunit 1266 Encryption Management to store and access sensitive data.

**Benefits:**

* **Simplicity**: only one implementation exists.
* **Knowledge reuse**: if a bug is found and fixed, there is just one place which needs to be repaired. Therefore, there is no risk that some of the usages would still be flawed by the same bug. For example:
  * **Double encryption**: The**Single Point of Access** API already implements knowledge to avoid encryption of already encrypted strings.
  * **Multi-company**: The**Single Point of Access** API already implements knowledge for handling encryption in a multi-company setup.
  * **Easy maintenance**:****a change in specification needs only one code update.

**Consequences:**

* **Single point of failure:** A defect in the access library will affect all usages until it is found and fixed.

**References**

\[1\]

"Wikipedia," \[Online\]. Available: https://en.wikipedia.org/wiki/Separation\_of\_concerns.

\[2\]

"GUID Structure," \[Online\]. Available: https://msdn.microsoft.com/en-us/library/aa373931(VS.85).aspx.

\[3\]

"Wikipedia," \[Online\]. Available: https://en.wikipedia.org/wiki/Binary\_large\_object.

\[4\]

E. Wauters, "How Do I: Manage Companies in Microsoft Dynamics NAV 2013 R2".

\[5\]

Microsoft, "Multitenant Deployment Architecture," Microsoft, \[Online\]. Available: https://msdn.microsoft.com/en-us/library/dn271675(v=nav.90).aspx.

\[6\]

B. Botez, "Setup Table design pattern," Microsoft, 2013\. \[Online\]. Available: https://community.dynamics.com/nav/w/designpatterns/76.setup-table. \[Accessed 31 07 2016\].

\[7\]

"Shotgun Surgery," \[Online\]. Available: https://en.wikipedia.org/wiki/Shotgun\_surgery. \[Accessed 31 07 2016\].

\[8\]

M. Fowler, Refactoring: Improving the design of existing code, Addison Wesley, 1999\.

\[9\]

"Masking out," \[Online\]. Available: https://en.wikipedia.org/wiki/Data\_masking\#Masking\_out. \[Accessed 29 7 2016\].

\[10\]

"Key Vault," Microsoft, \[Online\]. Available: https://azure.microsoft.com/en-us/services/key-vault/.

\[11\]

"How to: Configure SSL to Secure the Connection to Microsoft Dynamics NAV Web Client," Microsoft, \[Online\]. Available: https://msdn.microsoft.com/en-us/library/hh167264(v=nav.90).aspx. \[Accessed 2 8 2016\].

\[12\]

"sniffer," \[Online\]. Available: http://compnetworking.about.com/od/networksecurityprivacy/g/bldef\_sniffer.htm. \[Accessed 02 08 2016\].



[anchor0]: Logo-_2D00_-Single-Point-of-Access.png
[anchor1]: /nav/w/designpatterns/276.2-data-encryption
[anchor2]: /nav/w/designpatterns/276.encryption
[anchor3]: /nav/w/designpatterns/275.1-sensitive-data-encapsulation
[anchor4]: Multi-_2D00_-1-2-3-4.JPG


[image0]: Logo-_2D00_-Single-Point-of-Access.png
[image1]: Multi-_2D00_-1-2-3-4.JPG
