+++
title = "Multi-file Download"
weight = 800
+++
_By Martin Dam at Microsoft Development Center Copenhagen_

## Abstract

The goal of this pattern is to enable the users to download multiple files as a zip file instead of downloading one by one. On the Web Client this is preferred way of delivering multiple files since it is one of the web patterns and we cannot use File Management code unit to place files silently on the machine.

## Description

When generating reports that consists of multiple, and usually an unknown number of files, the developer will have to handle the download which also depends on the client the user is on. The problem is that the WinClient has access to the user's file system, whereas the web client does not. Following web guidelines, and the fact that client side .NET is not available in Web client, you can't initiate multiple downloads which requires the developer respond to the type of client. In some browsers it is possible to download files one-by-one in the Web client by using a confirm dialog, however this is a hack and should not be used.

To solve this problem, a generic download mechanism is used that is client dependent event when multiple files need to be downloaded. For Web client the files are compressed using ZIP and for WinClient the files are downloaded directly to the file system.

The pattern is usable for all objects that output multiple files and is available in both WinClient and Web client.

## Usage

The pattern consists of two steps: 1) Gathering the files and 2) downloading the file(s)

For first step consists of a loop that goes through the files that needs to be downloaded. If it is on the Web client, the files are added to a ZIP archive server-side using a naming convention defined by the integration function GetSeriesFileName. This function takes a filename and number, and transforms it to unique names following a meaningful deterministic pattern e.g. prepend an integer before the file extension. The same function is used when the temporary files are created server side, so the files can be found deterministically later. This removes the need for storing filenames and consequently allows an arbitrary number of files. The second integration function: GetTotalNumberOfFiles, returns the total number of files generated during the data processing and makes the pattern able to handle an arbitrary number of files.

The second step is the actually download of file(s). For the Web client this consists of closing the ZIP achieve and downloading via the standard download handler that works in the Web client. For the Win client, the files are saved directly to the client during the first step.

Code 1: File loop shows an example implementation of this pattern. ServerFileName is generated at the beginning of the report/codeunit, and is the base for GetSeriesFilename. The file that is actually written to during data processing is stored in another variable which holds the output from GetSeriesFilename on the current file number. Note; the example code will only create a ZIP file if there in fact are multiple files to be downloaded. 

    IF FileName = '' THEN
    ERROR(SupplyFileNameErr);
    IF ZipMultipleFiles AND (GetTotalNumberOfFiles \> 1) AND FileManagement.IsWebClient
    THEN BEGIN
    Basename := FileManagement.GetFileName(FileName);
    ZipFileName := FileManagement.CreateZipArchiveObject;
    FOR FileNo := 1 TO GetTotalNumberOfFiles DO
    FileManagement.AddFileToZipArchive(
    GetSeriesFilename(ServerFileName,FileNo),GetSeriesFilename(Basename,FileNo));
    FileManagement.DownloadHandler(ZipFileName,'','','',
    STRSUBSTNO('%1.zip',FileManagement.GetFileNameWithoutExtension(FileName)))
    END ELSE BEGIN
    IF FileManagement.IsWebClient THEN BEGIN
    IF GetTotalNumberOfFile \> 1 THEN
    ERROR(MultipleFilesWebClientErr);
    FileManagementDownloadHandler(GetSeriesFilename(ServerFileName,1),'','','',
    FileManagement.GetFileName(FileName));
    END ELSE
    FOR FileNo := 1 TO GetTotalNumberOfFiles DO
    FileManagement.DownloadToFile(GetSeriesFilieName(ServerFileName,FileNo),
    GetSeriesFilename(FileName,FileNo));
    END; 

_Code 1: File loop_

__

The following code is an example implementation of the GetSeriesFilename function. It needs to support the case where no directory is given, only a filename, in order to add files to the root of the ZIP archive. The example will add a file number right before the extension, e.g. C:\\directory\\file.txt will become C:\\directory\\file1.txt etc. 

    LOCAL GetSeriesFilename(FileName : Text;FileNo : Integer) : Text
    IF STRPOS(FileName,'\\') <\> 0 THEN
    Directory := FileMgt.GetDirectoryName(FileName) + '\\';
    EXIT(Directory + FileMgt.GetFileNameWithoutExtension(FileName) + FORMAT(FileNo) + '.' +   
    FileMgt.GetExtension(FileName));

#### _Code 2: GetSeriesFilename_

The pattern depends on .NET library System.IO.Compression.ZipFile, which from NAV 2015 is implemented in Codeunit 419 (File management). It consists of three functions:

* CreateZipArchiveObject: Which creates a System.IO.Compression.ZipArchive on a server side file
* AddFileToZipArchive: Which adds a server side file to the archive using System.IO.Compression.ZipFileExtensions.CreateEntryFromFile(). This function allows to use arbitrary naming and does not require to create a server directory before creating the ZIP achieve.
* CloseZipArchive: Which simply closes the ZIP file and saves it to disk.

The pattern is integrated into the report/codeunit in question by providing a filename field on the request page if it is on WinClient but hidden if it is in Web client. On the Web client, a meaningful default filename is used for the file to download, e.g. in Code 1, Filename is set by the user on the request page if it is WinClient, and set to a default value in Web client. 

## NAV Usages

This pattern is used by VAT Report and Blacklist communication in the Italian localization in NAV 2015\. The ZIP archive functions are available on all localization from NAV 2015\.

## Consequences

This pattern can be used whenever you need to ZIP one or more files. The above example downloads the file, but it could have been transmitted, saved to a persistent storage etc. It could also be used to improve download speed between server and client where the WinClient would unzip the files locally and save individually to disk. This follows the normal procedure in NAV in the WinClient, which is to download files directly to disk, so a ZIP file should only be created when the user specifically needs it or the Web client is invoking the report/codeunit.

## Related Topics

File Management 

## References

[System.IO.Compression.ZipFile][anchor0]



[anchor0]: http://msdn.microsoft.com/en-us/library/vstudio/system.io.compression.zipfile
