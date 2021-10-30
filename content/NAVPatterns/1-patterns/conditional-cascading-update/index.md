+++
title = "Conditional Cascading Update"
weight = 370
+++
_Originally by Jan Hoek at IDYN _

## **Abstract**

The Conditional Cascading Update pattern is used to intelligently populate fields whose values depend on other field values. In this pattern description, the field triggering the update will be called "source field", and the depending field will be called "target field". 

## **Description**

The value of one table field sometimes depends on the value of another field, typically following an application-defined transformation (note that we're talking about transformations of field values here. This has nothing to do with e.g. form transformation), such as conversion to uppercase, removal of certain characters etc.

If the target field is non-editable, said transformation is usually the only way for the target field to receive new values, so no irreproducible information can be lost. However, if the target field is editable, the user may have cared enough to override the default (transformed) value, in which case revalidating the source field should not blindly replace the target field's value. 

## **Usage**

In the OnValidate trigger of the source field, test if the target field value is either blank, or equal to the transformed value of the source field's previous contents. If it is, populate the target field's value with the transformed source field value. If it is not, do nothing (effectively preserving the value set by the user).

## **NAV Specific Example**

In the base application, this pattern can be found in Search Name/Search Description fields, which are updated with the uppercase value from the corresponding Name/Description field when the latter is validated, only if the Search Name/Description in question is currently blank, or equal to the (uppercase equivalent) of the previous contents of the Name/Description field.

[![ ][image0]][anchor0]

In this particular case, the transformation between source and target fields is implicit and due to the different data types of the fields (text vs. code). Note how the field triggers of the Search Name field itself do not contain any logic linked to this pattern. 

## **Consequences**

There is a case when this pattern should not be used. If the target field is non-editable, this pattern will not add any value, since there won't be any user-overridden values to protect. 



[anchor0]: 3124.T18_5F00_Name_5F00_OnValidate.png


[image0]: 3124.T18_5F00_Name_5F00_OnValidate.png
