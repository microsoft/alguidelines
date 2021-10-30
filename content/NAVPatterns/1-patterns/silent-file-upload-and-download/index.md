+++
title = "Silent File Upload and Download"
weight = 1080
+++
_By Bogdana Botez, at Microsoft Development Center Copenhagen_

## Abstract 

This pattern is about silently handing file transfers between NAV Service Tier and the NAV client. By "silently" we mean: without showing a dialog box at upload or download time.

## Description

As a terminology clarification \[1\], note that both "upload" and "download" are named as seen from the client's point of view:

* Download" defines transferring a file from the server to the client.
* "Upload" transfers the file from the client to the server.

[![ ][image0]][anchor0]

Sometimes, files must be transferred to or from known locations without triggering file-save or file-load dialogs.

In the following, both the historical and the recommended ways of silently transferring files are described. Since we keep both implementations possible for the sake of backward compatibility, we strongly recommend that you use the file-transfer API provided that is provided with the File Management codeunit (419).

The legacy API for file transfers \[2\]:

\[Ok :=\] UPLOAD(DialogTitle, FromFolder, FromFilter, FromFile, ToFile) 

\[Ok :=\] DOWNLOAD(FromFile, DialogTitle, ToFolder, ToFilter, ToFile)

As you can see, this historical API leaves no place for turning off the functionality for showing a dialog. Historically, NAV offered a remedy to this, namely by using the "Magicpath" string, which is the constant '<TEMP\>'. Under this condition, the way to invoke silent file upload or download becomes:

\[Ok :=\] UPLOAD(DialogTitle, Magicpath, FromFilter, FromFile, ToFile) 

\[Ok :=\] DOWNLOAD(FromFile, DialogTitle, Magicpath, ToFilter, ToFile)

This remedy introduced an issue: If we use "Magicpath" instead of **FromFolder** and **ToFolder** specifications, then where do we upload from and where do we download to? The answer is that they are uploaded to and downloaded from the NAV server's temporary folder. The path to the temporary file can be obtained when this file is created, by using the following function in **File Management: <tempFileName\> := ServerTempFileName(<fileExtension\>)**.

The new API for file transfers in the **File Management **codeunit:

\[Text :=\] UploadFileSilent(ClientFilePath)

\[Text :=\] DownloadTempFile(ServerFileName)

Using the API in the **File Management** codeunit instead of the historical API is recommended for all file transferring and file management in NAV implementations.

## Usage

The following describes a scenario for the silent file upload/download pattern, both from the user's point of view and from the NAV developer's point of view.

### The NAV User

The production manager at CRONUS needs an XML file in a specific format containing his latest product list with description, prices, and quantities. He wants to import the list into his web shop to keep product information updated with data in NAV.

[![ ][image1]][anchor1]

The production manager wants to have the file in a predefined location on his hard drive. The location has been defined in a NAV setup table.

### The NAV Developer

The NAV developer has written a module to query the CRONUS database and to export the product list in the pre-described XML format required by the web shop. He saves the data in a temporary server file created with this code:

ServerFileName := FileManagement.ServerTempFileName('xml');

When the file has been populated with the latest product data, the NAV developer uses the following call to download the file from the temporary location on the server to the predefined location on the client:

FileManagement.DownloadToFile(ServerFileName,ClientFileName);

The call to **DownloadToFile** is part of the **File Management **codeunit, and it embeds the silent download offered by **DownloadTempFile**:

PROCEDURE DownloadToFile@13(ServerFileName@1002 : Text;ClientFileName@1000 : Text);

VAR TempClientFileName@1001 : Text;

BEGIN ValidateFileNames(ServerFileName,ClientFileName); TempClientFileName := DownloadTempFile(ServerFileName); MoveFile(TempClientFileName,ClientFileName);

END

## Consequences

1. Even today, there is no fully silent up or download. The user will get a warning stating the system is trying to run a client side .NET component. However when dismissing this warning, the user can show to not see it again.
2. Due to client side .NET interop and of security concerns, silently downloading/uploading files on the web is disabled. Therefore, this pattern is recommended for implementation with Windows clients only.



[anchor0]: 8688.Silent-file-upload-and-download-NAV-design-pattern.png
[anchor1]: 6428.Silent-File-download-design-pattern.png


[image0]: 8688.Silent-file-upload-and-download-NAV-design-pattern.png
[image1]: 6428.Silent-File-download-design-pattern.png
