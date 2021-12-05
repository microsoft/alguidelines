+++
title = "Setup Specificity Fallback"
weight = 1060
+++
_By Jan Hoek at IDYN_

## **Abstract**

The Setup Specificity Fallback pattern allows users to efficiently define a potentially complex setup in terms of rules and exceptions to these rules, exceptions to the exceptions, etc.

## **Description**

The pattern involves a setup table with a compound (i.e. consisting of more than one field) primary key, where each record in the table maps a combination of primary key values to a particular setup value. However, setting up and maintaining each and every combination could prove to be rather labour-intensive. 

With the Setup Specificity Pattern in place, primary key fields in the setup table can have a special value (typically: zero or an empty string) that acts as a wildcard, meaning that the setup record in question applies to a combination of primary key fields with any value in the field containing that wildcard. According to the pattern, when querying the setup table, the application attempts to find the appropriate setup record by searching less and less specific, starting with only specified primary key values, and possibly ending with only wildcard values, replacing specific values with wildcards in a predefined order until a setup record is found, e.g.:

Step 1\.

Try to find record with:  
Field A = value "a"  
Field B = value "b"
If found, return record; if not, go to step 2\.
Step 2\.

Try to find record with:  
Field A = value "a"  
Field B = wildcard value
If found, return record; if not, go to step 3\.
Step 3\.
Try to find record with:  
Field A = wildcard value  
Field B = value "b"
If found, return record; if not, go to step 4\.
Step 4\.
Try to find record with:  
Field A = wildcard value  
Field B = wildcard value  
If found, return record; if not, optionally return hard-coded value

The least specific record can be thought of as the general rule, and each of the subsequent records is an exception to that rule. By defining only exceptions to the rule, instead of each and every combination, the burden of setting up and maintaining the setup records is strongly reduced, and the overall setup is much easier to read and understand.

## **Usage**

1. Create your setup table. Remember that this pattern applies only to setup tables with a compound primary key;
2. Create a function that will return the required value from the setup;
3. For each level of specificity, the function should test if the corresponding record exists. If it does, the setup value should be returned. If it doesn't, the logic should fall-back to a lower level of specificity.
4. (Optional) If even the least specific setup record can not be found, the function may return a blank value or hard-coded default value.

## **NAV Usages**

The Printer Selection table in NAV is an example of the Setup Specificity Fallback pattern. Its primary key consists of two fields. A blank value in either field means that the record applies to any user or report respectively.

[![ ][image0]][anchor0]

The FindPrinter function in codeunit 1 searches for the printer to use, with a decreasing level of specificity. 

[![ ][image1]][anchor1]



[anchor0]: 0724.Printer-Selection.png
[anchor1]: 2335.FindPrinter.png


[image0]: 0724.Printer-Selection.png
[image1]: 2335.FindPrinter.png
