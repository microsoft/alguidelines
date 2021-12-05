+++
title = "Data Encryption"
weight = 140
+++
_By Bogdana Botez at Microsoft Development Center Copenhagen_

__

_[![ ][image0]][anchor0]  
_

****

**Context**: After applying [**Sensitive Data Encapsulation**][anchor1], all sensitive data is gathered in a known place in the database. This makes it possible to apply further protection best practices.

**Problem**: If any non-authorized actor manages to get access to a copy of the database, the sensitive data is immediately available in clear-text.

**  
**

**Forces:**

* **Accessibility:** anyone who managed to steal a copy of the database can at once read the sensitive information.

**Solution:** Encrypt sensitive data. Dynamics NAV offers a simple mechanism for data encryption, to be used by NAV developers.

Figure 1 adds one more step (in continuation of the figure in pattern **[Sensitive Data Encapsulation][anchor2]**), in which the first two panels describe how sensitive data has all been gathered in one known place. This makes the current pattern, **Encryption**, much easier -- since now data needs to be encrypted in only one place. The last panel of Figure 1 shows the system after the next step, **Encryption**, has been implemented. This shows the iterative process of applying patterns and making the code better step by step.

[![ ][image1]][anchor3]

_Figure 1- **Encryption** becomes easier after first applying [**Sensitive Data Encapsulation**][anchor1]._

****

**Usage**: in Dynamics NAV, codeunit 1266 Encryption Management offers an API for encryption of data. The available functionality is described in Table 1\.

Table 1- Encryption functionality in Dynamics NAV (found in codeunit 1266 Encryption Management)

**Procedure**

**Description**

**EnableEncryption**

Confirms with the user before enabling encryption in all companies of the current database.

**EnableEncryptionSilently**

Enables encryption without UI interaction (to be used for web services).

**DisableEncryption**

Disables encryption. Has a boolean "Silent" parameter which enables/disables UI interaction.

**Encrypt**

Checks that encryption is possible before encrypting the given text.

**Decrypt**

Checks that encryption is possible before decrypting the given text.

**ExportKey**

If encryption is possible, it shows a confirmation dialog to confirm the export of the encryption key, and proceeds to saving it to a location chosen by the user.

**ImportKey**

Imports the encryption key from a user chosen location.

**ChangeKey**

Changes the encryption key.

**IsEncryptionEnabled**

Returns TRUE is encryption is enabled.

**IsEncryptionPossible**

Check is the correct key is present, which only works if encryption is enabled.

**DeleteEncryptedDataInAllCompanies**

Confirms through UI and if the user agrees, deletes all data stored in the Service Password table for all companies in the current database. At the end, it deletes the encryption key.

**User confirmation of encryption**

Ideally, encryption should be enabled by default.

However, in some versions of Dynamics NAV, there are places where any user who happens to hit a sensitive field (like a password) is asked if they want to encrypt or not. If user confirmation is needed, then let only the security administrators decide (not any random user).

To ask the security administrator if they want to enable encryption, plug the code below into the OnValidate trigger of the data field which is to be encrypted. This trigger will then get executed each time there's a change on the password field. For example, if the user is entering a password, then add a call to this procedure to Password -- OnValidate() trigger:

EncryptionIsNotActivatedQst@1001 : TextConst 'ENU=Data encryption is not activated. It is recommended that you encrypt data. \\Do you want to open the Data Encryption Management window?';

LOCAL PROCEDURE CheckEncryption@6();

BEGIN

IF NOT ENCRYPTIONENABLED THEN

IF CONFIRM(EncryptionIsNotActivatedQst) THEN

PAGE.RUN(PAGE::"Data Encryption Management");

END;

The code above will guide the user on encrypting, storing the encryption key and choosing a safe password, as follows. Consider for example that NAV is used by a small business, where Stan is the business owner. Hence, Stan has access to all NAV setup and security decisions. Stan wants to enable a connection between NAV and CRM. In NAV, he opens page CRM Connection Setup and enters the URL, user name and password for CRM (Figure 10). When Stan leaves the password field, if data encryption is not enabled, then he sees the confirmation message in Figure 2\.

[![ ][image2]][anchor4]

_Figure 2- The administrator is advised to enable encryption._

Now Stan becomes aware that there is no encryption and has the opportunity to enable it. If he clicks on "Yes", Stan will be taken to the Data Encryption Management page (Figure 3).

[![ ][image3]][anchor5]

_Figure 3- Data Encryption Management page_

Stan can now choose "Enable Encryption" from the ribbon which informs him that an encryption key will be created (Figure 4).

[![ ][image4]][anchor6]

_Figure 4- The administrator is encouraged to save a copy of the encryption key._

Stan has the option to save the encryption key in a safe location. He invokes the action Enable Encryption and is guided forward (Figure 5).

[![ ][image5]][anchor7]

_Figure 5- The encryption key needs to be protected by a password._

Stan chooses a password, which in this implementation requires minimum 8 characters with at least one uppercase character, one lowercase character and one digit. If the chosen password is too weak, Stan is given information on the expected complexity (Figure 6).

[![ ][image6]][anchor8]

_Figure 6- The administrator chooses a strong password._

In the end, Stan chooses a location for the encryption key on disk, in a standard file save dialog. The layout of the file save dialog depends on the display target used (web browser, rich client etc.).

**NAV Usages.** Encryption examples can be found in NAV in the following places:

* Page 1260 Bank Data Conv. Service Setup
* Page 1270 OCR Service Setup
* Page 5330 CRM Connection Setup
* Table 1275 Doc. Exch. Service Setup

**Benefits:**

* **Protection**: even if the database has been compromised, the sensitive data is encrypted hence not immediately accessible. Combined with best user practices like setting strong passwords, encryption can prove to be a very strong protection mechanism.

**Anti-patterns:**

* **Performance:** Do not encrypt everything, because it will have a performance impact on the system. Only important information should be encrypted.
* **System calls and "Do It Yourself" solutions**: Although system calls for encryptions are available in NAV (ENCRYPT, DECRYPT and <action\>ENCRYPTIONKEY), unless there is no other way, refrain from using them directly. Use instead he API in codeunit 1266 Encryption Management, which is safer to use, because it protects against common mistakes (like attempting to encrypt an already encrypted string, enabling encryption in only one company which makes data not usable in another on the same tenant etc.). Use the [**Single Point of Access**][anchor9] pattern to handle sensitive data which needs to be encrypted.
* **System-level encryption.** The similar codeunit API 1803 Encrypted Key/Value Management is not intended to be reused by partner NAV developers. Normal NAV users do not have permission to access this resource. This stores sensitive data to be accessed by system NAV functionality.

**References**

\[1\]

"Wikipedia," \[Online\]. Available: https://en.wikipedia.org/wiki/Separation\_of\_concerns.

\[2\]

"GUID Structure," \[Online\]. Available: https://msdn.microsoft.com/en-us/library/aa373931(VS.85).aspx.

\[3\]

"Wikipedia," \[Online\]. Available: https://en.wikipedia.org/wiki/Binary\_large\_object.

\[4\]

waldo, "How Do I: Manage Companies in Microsoft Dynamics NAV 2013 R2".

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



[anchor0]: Logo-_2D00_-Encryption.png
[anchor1]: /nav/w/designpatterns/275.1-sensitive-data-encapsulation
[anchor2]: /nav/w/designpatterns/275.sensitive-data-encapsulation
[anchor3]: Multi-_2D00_-1-2-3.JPG
[anchor4]: Encryption-_2D00_-1.JPG
[anchor5]: Encryption-_2D00_-2.png
[anchor6]: Encryption-_2D00_-3.png
[anchor7]: Encryption-_2D00_-4.png
[anchor8]: Encryption-_2D00_-5.png
[anchor9]: /nav/w/designpatterns/277.3-single-point-of-acces


[image0]: Logo-_2D00_-Encryption.png
[image1]: Multi-_2D00_-1-2-3.JPG
[image2]: Encryption-_2D00_-1.JPG
[image3]: Encryption-_2D00_-2.png
[image4]: Encryption-_2D00_-3.png
[image5]: Encryption-_2D00_-4.png
[image6]: Encryption-_2D00_-5.png
