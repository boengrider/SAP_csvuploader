# SAP_projects (csvuploader)
#############################
######## Description ########
#############################

Regional branches require automated upload of .csv files to SAP
File name follows a naming convention so that uploading script is 
able to parse the name and process the file based on rules defined
in the configuration file

Example file name:     SK01_A1_AP_SK01-A-xxxxx_ThisIsATestFile_2D_YYYYMMDDHHMM.csv
Delimiting character:  _
Individual file parts:
[0] Company code
[1] Document type
[2] SAP posting submodule
[3] SAP batch session identifier
[4] Header text
[5] Decimal places
[6] DateTime stamp

[0] Company code of the specific branch. It is also used to search within configuration file
[1] Document type
[2] SAP posting submodule
[3] SAP batch session identifier. First 4 characters must match company code. Session identifier is 12 characters long
    Charactes -A- define the project name, so that we know which project/script/program generated the particular file
    "SK01" + "-A-" = SK01-A-   <- This is the first part of the session identifier. It comes from the source. Uploading
    script then adds another "randomly" generated five character (numbers) to make the session unique and therefore
    easily identifiable for the SM35 transaction. We upload 1 file per session and therefore each session identifies
    the specific file. Full session identifier for example is: SK01-A-02576
    Session identifier changes during runtime, so that we can reprocess files if needed
[4] Header text/File name
[5] Decimal places radio button
[6] Datetime stamp of the file


Script requests list of all 'Queued' files from the sharepoint library and then processes each file one at a time.
If file fulfills criteria e.g File is not empty, company code matches session, doctype is allowed for the company code
configuration entry for the company code found or no duplicate configuration entries for the company code defined THEN
this file is downloaded from the sharepoint library and uploaded to SAP via "ZTC_ZCVDOC47" transaction.

If errors occur during this transaction, such file is not marked as processed and various columns in the sharepoint library are
updated in order to reflect this error. This information is also included in the final report sent via mail to administrators

If the file is uploaded w/o errors, sharepoint library item is updated and marked as processed
