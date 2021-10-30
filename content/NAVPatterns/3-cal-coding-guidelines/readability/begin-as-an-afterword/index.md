+++
title = "Begin as an 'After Word'"
weight = 230
+++
When BEGIN follows THEN, ELSE, DO, it should be on the same line, preceded by one space character. Bad code

    IF ICPartnerRefType = ICPartnerRefType::"Common Item No." THEN
    BEGIN
    ...
    END;

Good code

    IF ICPartnerRefType = ICPartnerRefType::"Common Item No." THEN BEGIN
    ...
    END;
