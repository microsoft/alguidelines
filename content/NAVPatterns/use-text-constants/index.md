+++
title = "use-text-constants.md"
weight = 1360
+++
Pass user messages using Text Constants. It makes translation easy.

Bad code

    ImportAttachmentQst@1021 : TextConst 'ENU="Import attachment "';
    ...
    IF CONFIRM(ImportAttachmentQst + Caption +'?',TRUE) THEN BEGIN

Good code

    ImportAttachmentQst@1021 : TextConst 'ENU="Import attachment %1?"';
    ...
    IF CONFIRM(STRSUBSTNO(ImportAttachmentQst, Caption),TRUE) THEN BEGIN

Bad code

    ...
    IF NOT
    CONFIRM(
    STRSUBSTNO(
    'Difference on Periodic entries: %1 on %2' +
    'Do you want to continue?',Balance,Date),
    TRUE)
    THEN
    ERROR('Program terminated by the user');

Good code

    DiffOnPeriodEntiesQst@100 : TextConst 'ENU="Difference on Periodic entries: %1 on %2\\ Do you want to continue?"';
    ProgramTerminatedErr@200 : TextConst 'ENU="Program terminated by the user"';
    ...
    IF NOT CONFIRM(STRSUBSTNO(DiffOnPeriodEntiesQst,Balance,Date),TRUE) THEN
    ERROR(ProgramTerminatedErr);
