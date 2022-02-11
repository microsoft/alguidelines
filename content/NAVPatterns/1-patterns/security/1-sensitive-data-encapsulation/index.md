+++
title = "Sensitive Data Encapsulation"
weight = 120
+++
_By Bogdana Botez at Microsoft Development Center Copenhagen_

[![ ][image0]][anchor0]

**Aliases:** Encapsulation, Separation of Concerns [1]


**Context**: You want to store and protect sensitive data which already exists in a system, but it is not clear which data needs protection and how to store it.

**Problem**: Sensitive data is scattered and mixed with other data in various parts of the system (passwords residing in the same table with non-sensitive data, part of the private information might be stored in files, hardcoded text constants, hardcoded info as part of the code etc.).

**Forces:**

* **Obscurity:** sensitive data mixed with other data and code makes it hard to have an overview of what needs to be protected.
* **Effort:** the protection mechanisms needs to be applied multiple times, once for each location of the sensitive data.
* **Diversity:** different data storage mediums can require different protection solutions.
* **Cohesiveness:** code, low importance data, highly sensitive data are all monolithically mixed which might make it hard to select and protect only what is important.
* **Low** **Performance**: protecting all data can slow down the system.

**Solution:** Extract and separate sensitive data into a single known repository.

To apply this pattern in Dynamics NAV, a table structure similar to Table 1261 Service Password can be used. The Service Password table also contains additional functionality which will help to further apply related patterns like [**Access Control**][anchor1] and [**Encryption**][anchor2].

Figure 1 describes the definition of a table which is already available in Dynamics NAV. This table can be used for storing sensitive data. As a minimum, the table only needs two fields:

1. The key is of type GUID (Globally Unique Identifier), which is a 128-bit value consisting of multiple groups of hexadecimal digits [2]. Each key needs to be unique and will be used for storing and retrieving the protected information.
2. The value (the actual data to be encapsulated) is of type BLOB (Binary Large OBject) [3], which contains the encrypted or un-encrypted data (for encryption, see the related [**Encryption**][anchor2] pattern).

[![Figure 1- Example definition, table used for Data Encapsulation][image1]][anchor3]

_Figure 1- Example definition, table used for **Sensitive** **Data** **Encapsulation**_

Let's use a software system (which can be a Dynamics NAV extension or customization). Chances are that the system will look similar to the one described in part 1 of Figure 2: there is data handled in various places of the system, on various storage solutions. Intertwined with normal data, there is sensitive data. For example: Dynamics CRM (Customer Relationship Management) connection information could be all stored in one table, and consists of the connection URI (normal data), enabled/disabled status (normal data), and connection password (sensitive data).

Figure 2 illustrates the system before and after applying the **Sensitive Data Encapsulation** pattern.

* Before: picture part 1 shows the initial system, where most likely the data is scattered across the system. Furthermore, it is stored in different mediums, like
  * In a table (possible together with low-importance data)
  * Hardcoded (in Dynamics NAV, it can be a text constant, or just plainly typed in the code)
  * In files

Furthermore, actors which have to interact with the data, need to remember where to find each piece of information and how to retrieve it.

* After: the part 2 of Figure 2 shows the same system after application of **Sensitive Data Encapsulation**. Now, all data is stored in one place, and all actors which interact with it can read it from the unique location.

[![ ][image2]][anchor4]

_Figure 2 - Data access before and after **Sensitive** **Data** **Encapsulation.**_

**Benefits:**

* **Clarity:** when all sensitive data is encapsulated in one place, it is clear which is the information that needs to be protected.
* **Simplicity:** easier to protect just a limited number of known resources when they are grouped.
* **Homogeneity:** the same protection can be applied to all data, since it is stored in the same place.
* **Separation of concerns:** treat each section of the computer program differently, by separating it and clearly addressing its own requirements and limitations. [1]
* **Performance**: data protection techniques like **Access Control** and **Encryption** can now be applied only to the sensitive data (not to everything), which improves the performance of the system.

**Drawbacks:**

* **Single point of failure:** a maliciously intended actor has no longer a need to reverse engineer the places where important data is stored. In the unwanted situation when this actor would have already obtained access to the system, they can more easily locate the sensitive information. This is a step towards information disclosure, but mechanisms like [**Encryption**][anchor2] and logging can provide further protection.
* **Limited Text Length**: there is a limit on how long the encrypted text can be. This limit is imposed by the OS encryption service and it depends on the composition of the text as well as on the system specifics. In NAV, we had implemented the Encrypted Text for text values of max 250 chars, which is enough to cover passwords, person ID numbers, credit card info, but it might turn insufficient in other future scenarios.
* **Nomenclature**: the name of the table **Service Password** is too specific, since it started by being used for passwords, but it has the capability and it now contains other sensitive data like API Keys, credit card numbers etc.

**References**

[1] "Wikipedia," [Online]. Available: https://en.wikipedia.org/wiki/Separation_of_concerns.

[2] "GUID Structure," [Online]. Available: https://msdn.microsoft.com/en-us/library/aa373931(VS.85).aspx.

[3] "Wikipedia," [Online]. Available: https://en.wikipedia.org/wiki/Binary_large_object.

[4] waldo, "How Do I: Manage Companies in Microsoft Dynamics NAV 2013 R2".

[5] Microsoft, "Multitenant Deployment Architecture," Microsoft, [Online]. Available: https://msdn.microsoft.com/en-us/library/dn271675(v=nav.90).aspx.

[6] B. Botez, "Setup Table design pattern," Microsoft, 2013\. [Online]. Available: https://community.dynamics.com/nav/w/designpatterns/76.setup-table. [Accessed 31 07 2016].

[7] "Shotgun Surgery," [Online]. Available: https://en.wikipedia.org/wiki/Shotgun_surgery. [Accessed 31 07 2016].

[8] M. Fowler, Refactoring: Improving the design of existing code, Addison Wesley, 1999\.

[9] "Masking out," [Online]. Available: https://en.wikipedia.org/wiki/Data_masking#Masking_out. [Accessed 29 7 2016].

[10] "Key Vault," Microsoft, [Online]. Available: https://azure.microsoft.com/en-us/services/key-vault/.

[11] "How to: Configure SSL to Secure the Connection to Microsoft Dynamics NAV Web Client," Microsoft, [Online]. Available: https://msdn.microsoft.com/en-us/library/hh167264(v=nav.90).aspx. [Accessed 2 8 2016].

[12] "sniffer," [Online]. Available: http://compnetworking.about.com/od/networksecurityprivacy/g/bldef_sniffer.htm. [Accessed 02 08 2016].



[anchor0]: Logo-_2D00_-Protected-Data-Encapsulation.png
[anchor1]: /navpatterns/1-patterns/security/3-single-point-of-access/
[anchor2]: /navpatterns/1-patterns/security/2-data-encryption/
[anchor3]: Data-Encapsulation-_2D00_-figure-1.png
[anchor4]: Multi-_2D00_-1-2.JPG


[image0]: Logo-_2D00_-Protected-Data-Encapsulation.png
[image1]: Data-Encapsulation-_2D00_-figure-1.png
[image2]: Multi-_2D00_-1-2.JPG
