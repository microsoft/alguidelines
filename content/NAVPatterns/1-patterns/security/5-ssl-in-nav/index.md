+++
title = "SSL in NAV"
weight = 190
+++
_By Bogdana Botez at Microsoft Development Center Copenhagen_

[![ ][image0]][anchor0]   

**Context**: The security of data transmission is just as important as the security of data storage. When data is transmitted over the web, Secure Sockets Layer (SSL) is available to be used with the web client in Dynamics NAV. Microsoft's NAV cloud solution has SSL enabled by default. However, if a partner company chooses to deploy their own NAV, then they need to handle SSL explicitly.

**Problem**: Although data is stored securely, before it even gets to be stored, it needs to travel the web on a client-server connection, where it is vulnerable.

**Forces:**

* **Insecure communication:** When the user enters a password, if unprotected, a network sniffer [12] could catch and read it. A sniffer is either a software program or hardware device which examine network traffic. Years ago, sniffers were tools used exclusively by professional network engineers, but nowadays, they are also popular with Internet hackers and people just curious about networking. A public Wi-Fi network could easily be eavesdropped by an unwanted actor.

By using data storage patterns like **Sensitive Data Encapsulation**, **Encryption, Single Point of Access** or **Azure Key Vault**, the sensitive data is preserved securely in the implementation of Dynamics NAV. But before it gets into a secure store, this data needs to be transmitted from the user, through a user interface, on a client-server connection and all the way to the database. Is the data safe while being transmitted?

**Solution:** To protect the data before it reaches the server, remember to configure SSL (Secure Sockets Layer) in Dynamics NAV.

_SSL_ is a web protocol that encrypts data that is transmitted over a network to make the data and the network more secure and reliable. A website that is enabled with SSL uses Hypertext Transfer Protocol Secure (HTTPS) instead of Hypertext Transfer Protocol (HTTP) as a communication protocol.

Figure 1 shows data communication between the client (where the user enters data) and the server (which connects further to the database). Without encryption (left side), data is available in clear text over the wire. A person equipped with a network sniffer can easily intercept and read it. On the right side of the picture, SSL is used to encrypt the user's data. Sniffing the traffic would capture the encrypted stream, but access to the real content would be impeded by encryption.

[![ ][image1]][anchor1]

_Figure 1 - Data transmission before (http://...) and after SSL encryption (https://...)._

**Usage**: the latest information about how to configure SSL for the web client in Dynamics NAV is found online at on MSDN at [https://msdn.microsoft.com/en-us/library/hh167264(v=nav.90).aspx][anchor2].

**Benefits:**

* **Secure communication**: encryption protects the data transmitted over a network, so the data is safe all the way on its journey from the user to the database.

**Consequences:**

* **Awareness:** a NAV system administrator might not be aware that they need to enable SSL when using the web client.
* **Extra work:** there is extra effort to enable SSL on a self-administered NAV system. The good news is that Microsoft's cloud NAV solution has SSL by default and no extra work is required from the developer on that aspect.

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



[anchor0]: Logo-_2D00_-SSL.JPG
[anchor1]: SSL-_2D00_-before-and-after.PNG
[anchor2]: https://msdn.microsoft.com/en-us/library/hh167264(v=nav.90).aspx


[image0]: Logo-_2D00_-SSL.JPG
[image1]: SSL-_2D00_-before-and-after.PNG
