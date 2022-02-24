+++
title = "Product Name"
weight = 950
tags = ["C/AL"]
categories = ["Pattern"]
+++
#### **Abstract**

On many occasions, it's needed to refer to the product name in messages or errors. It's not a good practice to hardcode the brand name like Dynamics NAV 2017, and change this value with each rebranding. You can use client **ProductName** System Object instead to refer to the product name. 

[![ ][image0]][anchor0]

#### **Problem**

When you want to refer to product name in informational messages or errors, it's not a good practice to hardcode the brand name, as this requires changing this text when a rebranding happens. 

#### **Solution**

Instead of hardcoding the product name like "Dynamics NAV 2017", it's recommended to use **ProductName** System Object that platform provides.

You can use **ProductName** to refer to the product name, this you can use in text messages. **ProductName** has 3 values **ProductName.Short**, **ProductName.Full**, and **ProductName.Marketing**, each one should be used according to the context of your message. 

It's recommended to use **ProductName.Short** for most in-product texts , **ProductName.Full** when other dynamics apps are present in the message context (like CRM), and **ProductName.Marketing** only when absolutely necessary.

**Usage**: You should make a placeholder in your text constant and substitute this placeholder with **ProductName** as shown below. 

[![ ][image1]][anchor1]

**Output**

[![ ][image2]][anchor2]

#### **Benefits** 

* This decouples your messages from the application name 
* This removes the effort of maintaining these messages and keeping them up to date with the brand name.   

#### **Limitations**

* **ProductName** can't be used for tooltips and captions, it can only be used for text constants (Labels).
* **ProductName** System object doesn't exist in NAV 2016 and earlier version.



[anchor0]: ProductName-Logo.png
[anchor1]: ProductName-Sample.PNG
[anchor2]: ProductName-output.png


[image0]: ProductName-Logo.png
[image1]: ProductName-Sample.PNG
[image2]: ProductName-output.png
