+++
title = "Use Queries to Replace Nested Loops"
weight = 1350
+++
_Originally by Bogdan Sturzoiu, Microsoft Development Center Copenhagen_

## **Abstract**

This pattern shows how the new query object type introduced in NAV 2013 allows you to replace costly loops when inspecting data from two or more tables.

### **Description**

One of the core operations in a relational database is joining two or more tables. For example, you might need to extract all sales lines in the database together with information regarding the related sales header. This requires joining the Sales Header and Sales Line tables using Sales Header No. as the connecting field.;

The join operation has traditionally been done in C/AL by record looping. When NAV 2013 introduced the query object, it allowed us to produce a data set that is the result of a join operation between two or more tables. This simplifies the problem of finding related records in two tables linked through a foreign key. 

#### **Pattern Elements**

1. Two or more tables that contain records linked through a foreign key: Table 1, Table 2, Table n.
2. A query object Query X, that joins Table 1, Table 2, etc. based on the connecting key.
3. A processing codeunit that loops through the query records (or any other code-bearing object).

#### **Pattern Steps**

1. Run the query on the connected tables.
2. Loop through the records returned by the query.
3. Process the records. 

[![ ][image0]][anchor0]

_Figure 1\. The pattern elements_

### **Usage**

#### **Sample Problem**

The Bank Acc. Reconciliation Line table (274) and the Bank Account Ledger Entry table (271) are connected through the Bank Account No. field. Identify the matching pairs of records based on having the same remaining amount and transaction date.

#### **Solution Using Nested Loops**

The classic C/AL approach is to:

1. Set the necessary filters on the left table, i.e. table 274\.
2. Loop through the filtered records.
3. For each record in the filter, find the related records in the right table (table 271) and set the required filters on it.
4. For each pair of records from the left and right table, decide if they are a solution and if so, apply them to each other.

    PROCEDURE MatchSingle@5(BankAccReconciliation@1003 : Record 273);
    VAR
    BankAccRecLine@1005 : Record 274;
    BankAccLedgerEntry@1006 : Record 271;
    BankAccEntrySetReconNo@1007 : Codeunit 375; 
    BEGIN
    BankAccRecLine.SETRANGE("Bank Account No.",BankAccReconciliation."Bank Account No.");
    BankAccRecLine.SETRANGE("Statement No.",BankAccReconciliation."Statement No.");
    BankAccRecLine.SETFILTER(Difference,'<\>%1',0);
    BankAccRecLine.SETRANGE(Type,BankAccRecLine.Type::"Bank Account Ledger Entry");
    IF BankAccRecLine.FINDSET THEN
    REPEAT
    BankAccLedgerEntry.SETRANGE("Bank Account No.",BankAccRecLine."Bank Account No.");
    BankAccLedgerEntry.SETRANGE(Open,TRUE);
    BankAccLedgerEntry.SETRANGE("Statement Status",BankAccLedgerEntry."Statement Status"::Open);
    BankAccLedgerEntry.SETFILTER("Remaining Amount",'<\>%1',0);
    IF BankAccLedgerEntry.FINDSET THEN
    REPEAT
    IF (BankAccRecLine.Difference = BankAccLedgerEntry."Remaining Amount") AND 
    (BankAccRecLine."Transaction Date" = BankAccLedgerEntry."Posting Date") THEN 
    BankAccEntrySetReconNo.ApplyEntries(
    BankAccRecLine,BankAccLedgerEntry, Relation::"One-to-One");
    UNTIL BankAccLedgerEntry.NEXT = 0;
    UNTIL BankAccRecLine.NEXT = 0;
    END;

  
**Solution Using Query**

The new query-based approach involves:

1. Define a query that returns the full filtered join of tables 271 and 274\.
2. Loop through the records returned by the query.
3. For each query record, decide if it represents a solution and then connect the two table records that formed it through an application.

    PROCEDURE MatchSingle@5(BankAccReconciliation@1003 : Record 273);
    VAR
    BankRecMatchCandidates@1001 : Query 1252;
    BankAccEntrySetReconNo@1007 : Codeunit 375;
    BEGIN
    BankRecMatchCandidates.SETRANGE(Rec\_Line\_Bank\_Account\_No,
    BankAccReconciliation."Bank Account No.");
    BankRecMatchCandidates.SETRANGE(Rec\_Line\_Statement\_No,
    BankAccReconciliation."Statement No.");
    IF NOT BankRecMatchCandidates.OPEN THEN
    EXIT;
    WHILE BankRecMatchCandidates.READ DO BEGIN 
    BankAccLedgerEntry.GET(BankRecMatchCandidates.Entry\_No); 
    BankAccRecLine.GET(BankAccRecLine."Statement Type"::"Bank Reconciliation",
    BankRecMatchCandidates.Rec\_Line\_Bank\_Account\_No,
    BankRecMatchCandidates.Rec\_Line\_Statement\_No,
    BankRecMatchCandidates.Rec\_Line\_Statement\_Line\_No);
    BankAccEntrySetReconNo.ApplyEntries(BankAccRecLine,BankAccLedgerEntry, 
    Relation::"One-to-One"); 
    END;
    END;
    

where the query 1252 is defined as:

    OBJECT Query 1252 Bank Rec. Match Candidates
    {
    OBJECT-PROPERTIES
    {
    Date=;
    Time=;
    Version List=;
    }
    PROPERTIES
    {
    }
    ELEMENTS
    {
    { 1 ; ;DataItem; ;
    DataItemTable=Table274;
    DataItemTableFilter=Difference=FILTER(<\>0),
    Type=FILTER(=Bank Account Ledger Entry) }
    { 2 ;1 ;Column ;Rec\_Line\_Bank\_Account\_No;
    DataSource=Bank Account No. }
    { 3 ;1 ;Column ;Rec\_Line\_Statement\_No;
    DataSource=Statement No. }
    { 4 ;1 ;Column ;Rec\_Line\_Statement\_Line\_No;
    DataSource=Statement Line No. }
    { 5 ;1 ;Column ;Rec\_Line\_Transaction\_Date;
    DataSource=Transaction Date } 
    { 6 ;1 ;Column ;Rec\_Line\_Difference ;
    DataSource=Difference } 
    { 7 ;1 ;DataItem; ;
    DataItemTable=Table271;
    DataItemTableFilter=Remaining Amount=FILTER(<\>0),
    Open=CONST(Yes),
    Statement Status=FILTER(Open);
    DataItemLink=Bank Account No.=Bank\_Acc\_Reconciliation\_Line."Bank Account o.",
    Remaining Amount=Bank\_Acc\_Reconciliation\_Line.Difference,
    Posting Date=Bank\_Acc\_Reconciliation\_Line."Transaction Date" }
    { 8 ;2 ;Column ; ;
    DataSource=Entry No. }
    { 9 ;2 ;Column ;Bank\_Account\_No ;
    DataSource=Bank Account No. }
    { 10 ;2 ;Column ; ;
    DataSource=Posting Date } 
    { 11 ;2 ;Column ; ;
    DataSource=Remaining Amount }
    { 12 ;2 ;Column ;Bank\_Ledger\_Entry\_Open;
    DataSource=Open }
    { 13 ;2 ;Column ; ;
    DataSource=Statement Status } 
    }
    CODE
    {
    BEGIN
    END.
    }

####   
When comparing the two implementations, we notice the following advantages of using a query instead of two loops:Comparison

1. A query produces the Cartesian product of tables 1 and 2 faster than by looping through both of them. The advantage grows as there are more tables linked.

The query object leverages the power of SQL Server (as it basically executes a SQL query directly against the database server), and as such it only requires one trip to the database to fetch the data, instead of getting the related records on the right side of the join one by one.

1. A query is scalable in the sense that it allows reusing its definition by applying filters when looping through its dataset. So a generic query can be reused for a variety of purposes just by adapting the filters to the programming need. Duplicating traditional record loops in different functions, on the other side, requires extensive code copy-pasting, which can introduce code defects.
2. Using a query only requires one loop, whereas joining two or more tables requires multiple code loops that quickly become difficult to read and follow.
3. Queries are easy to create and maintain and generally provide a cleaner design.

### **NAV Specific Examples**

In Microsoft Dynamics NAV 2013 R2, we can see the query object used in the bank account reconciliation matching algorithm. The object is query Bank Rec. Match Candidates query (1252), and it is called by the matching engine in the Match Bank Rec. Lines codeunit (1252).

### **Ideas for Improvement**

The query object type could be improved to allow the passing of parameters at runtime, or, in general, being built dynamically at runtime. This will remove the need for multiple static definitions of the same base query used in slightly different contexts.



[anchor0]: 5040.clip_5F00_image002.png


[image0]: 5040.clip_5F00_image002.png
