+++
title = "By Reference Parameters"
weight = 280
+++
Do not declare parameters by reference if their values are not intended to be changed.

Unintentional value changes might propagate. Also, it might lead people to believe that value changes are intended. 

Bad code

    LOCAL PROCEDURE ShowMessage@15(VAR Text@1000 : Text\[250\]);
    BEGIN
    Text := GetMessageText;
    IF (Text <\> '') AND GenJnlLineInserted THEN
    MESSAGE(Text);
    END;

Good code

    LOCAL PROCEDURE ShowMessage@15(Text@1000 : Text\[250\]);
    BEGIN
    Text := GetMessageText;
    IF (Text <\> '') AND GenJnlLineInserted THEN
    MESSAGE(Text);
    END;
