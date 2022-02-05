+++
title = "Masked Text"
weight = 180
+++
_By Bogdana Botez at Microsoft Development Center Copenhagen_

[![ ][image0]][anchor0]  

**Aliases:** Masking out

**Context**: In the user interface (UI) of a software system, the user enters protected information such as a password, an access key, a credit card number etc.

**Problem**: The entered information is visible during data entry and whenever any user (the one who entered the data, or a foreign user) opens the UI.

**Forces:**

* **Information disclosure:** sensitive data is visible in the UI.

**Solution:** Use the "Masked" field property to display dots instead of characters on the sensitive text field in the UI.

**Usage:** Figure 1 shows how an unmasked and a masked field look in Dynamics NAV. On page Microsoft Dynamics CRM Connection Setup, the first two fields (Dynamics CRM URL and User Name) are not masked. The next field (Password) is masked. As the user types text into the Password field, the characters are one by one replaced with dots. When the user had finished typing and had left the Password field (moved focus to another page element), then a pre-defined number of dots is showed in the field, no matter what the real length of the password is. This is done so that the length of the text is not disclosed. Hence, no matter if the text had 5, 10 or 20 characters, as soon as the user leaves the masked field, 10 dots will be visible.

[![ ][image1]][anchor1]

_Figure 1 - The field "Password" is masked._

To apply this pattern in Dynamics NAV, the developer has two choices:

1. **Mask everywhere**

Mask the field in all pages which expose it or will expose it in the future. In this case, masking needs to be set at the table level, by opening the table in design mode and setting the field's property ExtendedDatatype=Masked.

2. **Mask only in selected pages**

Mask the field in only a subset of pages. Open the pages where the field should be masked in design mode, open the property page for the field in question, and set ExtendedDatatype=Masked. This option can be used for example when a field should be hidden from most users (in most usual pages), but still visible to administrators in specific pages.

**Benefits:**

* **Information protection**: sensitive data is stored and can be used by the system, but once entered it is not visible in the UI anymore.

**Drawbacks:**

* **"Forgotten password":** the text in a masked field is hidden from all users, including users who theoretically have the right to see it. In this case, if the developer wishes to disclose the text only to certain users, they have to write extra code (like a lookup trigger) which will verify the user's permissions and if permitted, show or send the clear-text value to the user who requested it.

**References**

[1] "Wikipedia," [Online]. Available: https://en.wikipedia.org/wiki/Separation_of_concerns.

[2] "GUID Structure," [Online]. Available: https://msdn.microsoft.com/en-us/library/aa373931(VS.85).aspx.

[3] "Wikipedia," [Online]. Available: https://en.wikipedia.org/wiki/Binary_large_object.

[4] waldo, "How Do I: Manage Companies in Microsoft Dynamics NAV 2013 R2".

[5] Microsoft, "Multitenant Deployment Architecture," Microsoft, [Online]. Available: https://msdn.microsoft.com/en-us/library/dn271675(v=nav.90).aspx.

[6] B. Botez, "Setup Table design pattern," Microsoft, 2013\. [Online]. Available: https://community.dynamics.com/nav/w/designpatterns/76.setup-table. [Accessed 31 07 2016].

[7] "Shotgun Surgery," [Online]. Available: https://en.wikipedia.org/wiki/Shotgun_surgery. [Accessed 31 07 2016].

[8] M. Fowler, Refactoring: Improving the design of existing code, Addison Wesley, 1999\.

[9] "Masking out," [Online]. Available: https://en.wikipedia.org/wiki/Data_masking\#Masking_out. [Accessed 29 7 2016].

[10] "Key Vault," Microsoft, [Online]. Available: https://azure.microsoft.com/en-us/services/key-vault/.

[11] "How to: Configure SSL to Secure the Connection to Microsoft Dynamics NAV Web Client," Microsoft, [Online]. Available: https://msdn.microsoft.com/en-us/library/hh167264(v=nav.90).aspx. [Accessed 2 8 2016].

[12] "sniffer," [Online]. Available: http://compnetworking.about.com/od/networksecurityprivacy/g/bldef_sniffer.htm. [Accessed 02 08 2016].



[anchor0]: Logo-_2D00_-Masked-Text.png
[anchor1]: Masking-_2D00_-CRM-Connection-Setup-page.PNG


[image0]: Logo-_2D00_-Masked-Text.png
[image1]: Masking-_2D00_-CRM-Connection-Setup-page.PNG
