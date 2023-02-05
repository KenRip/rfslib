# RFSLIB CICS/REXX Application for z/VSE (and VSEn)

## Table of Contents

- [Introduction](#introduction)
  - [Installation Prerequisite](installation-prerequisite)
- [Functionality](#functionality)
  - [TOP AREA](#top-area)
- [RFSLIB Commands](#rfslib-commands)
- [CICS/REXX Full Screen Editor](#cicsrexx-full-screen-editor)
  - [Overview](#overview)
  - [Screen Format](#screen-format)
  - [Prefix Commands](#prefix-commands)
  - [Individual Line Commands](#individual-line-commands)
  - [Consecutive Block Commands](#consecutive-block-commands)
  - [Command Line Commands](#command-line-commands)
- [Installation](#installation)
  - [Installation Steps](#installation-steps)
  - [Tailoring JCL Skeletons](#tailoring-jcl-skeletons)
  - [Tailoring existing CICS/REXX PROCs to enhance the functionality of the RFSLIB application](#tailoring-existing-cicsrexx-procs-to-enhance-the-functionality-of-the-rfslib-application)
  - [Cross reference of VSE Library members provided with the RFSLIB application](#cross-reference-of-vse-library-members-provided-with-the-rfslib-application)

## Introduction

The RFSLIB application is an extension of the CICS/REXX support already provided with VSE of charge with the IBM CICS Transaction Server for VSE/ESA V1.1.1 product (it is also include in the CICS Transaction Server for z/VSE 2.2 product).  It provides an Intercative Interface look and feel to managing and editing the CICS/REXX File System.  It also provides several enhancements to the CICS/REXX functionality provided with VSE.  These enhancements include; SUBMIT and PRINT functionality, interfaces with an External Security Manager to provide a mechanism for securing the features and functions of the RFSLIB application.

### Installation Prerequisite
You must complete the instructions found in Appendix K: Post-Installation Configuration in the CICS Transaction Server for VSE/ESA REXX Guide.  This is necessary because the RFSLIB application relies on the existence of at least one REXX/CICS File System “Pool” being defined.

Please note that the steps decribed in the section titled "Rename supplied Procedures" of Appendix K in the CICS Transaction Server for VSE/ESA REXX Guide have been made obsolete.  These *.Z members are no longer provided in PRD1.BASE.  Instead they are shipped already renamed to *.PROC in PRD1.BASE which makes them avaialble in the CICSICCF region shipped with z/VSE.

Please also note that the REXX DB2 Interface support described in the section titled "" of Appendix K in the CICS Transaction Server for VSE/ESA REXX Guide is NO longer available in z/VSE 6.1 and above.  CICS REXX support has been added to the z/VSE database connector DBCLI (Database Call Level Interface) as of z/VSE 6.2 (via PTF).

## Functionality

The RFSLIB main screen is a member listing and selection screen similar to that provided by IBM in the z/VSE Interactive Interface for accessing an ICCF Library.  This main screen provides a directory listing of the contents of the current CICS/REXX File System (or RFS) directory.  From this directory listing the user can select members for viewing and editing as well as several other functions.  In addition a Command Line area is available for accessing specific commands which have been provided to add functions and features to the CICS/REXX environment.

The screen is broken into several areas.  These are...

### TOP AREA

The top of the screen contains a list of selections available which can be placed in the prefix area in front of a file or directory to perfrom a specific operation.  These include...

1=EDIT
Open the selected file in the CICS/REXX full screen editor.  This editor is very close in function and features to the VM/CMS XEDIT editor. More information on the Editor can be obtained by entering "HELP EDITOR" in the Command Line area. The editor can also be tailored to an individual user's preferences.  The user can create a file in their "home" directory called EDITPROF.EXEC which can contain CICS/REXX commands that can be used to customize the editor.

For example...

    ADDRESS EDITSVR
    'SET CASE MIXED RESPECT'
    'SYNONYM BOT BOTTOM'

Please see your System Administrator for more information.

2=CHANGETO
This option is only available against a directory and allows for changing the current directory to the the selected directory.  Once selected the display will refresh to now provide a directory listing of the selected directory.

3=PRINT
This option is only available against files (not directories) and spools a copy of the selected file to the VSE POWER List Queue for printing.  The attributes used to spool the data to VSE POWER can be controlled by the user.  The default values are specified by the System Administrator in the RFSLIB.CONFIG member. The user can override there own default values by creating a file in the "home" directory called PRINT.PROFILE. The PRINT.PROFILE file can contain any of the following statements...

    NAME = nnnnnnnn
    CLASS = c
    DISP = d
    PRI = p
    DEST = dddddddd
    FORM = ffff

4=COPY/MOVE
This option provides the user the ability to perform a COPY or MOVE operation against a file.  A separate panel screen is displayed providing the user with the option to select the target filename and folder of the COPY or MOVE operation.

5=DELETE
This option allows for the deletion of the selected file or directory.  Please note that a delete can not be performed on a directory that contains any files.

6=RENAME
This option allows the user to rename the selected file or directory.  The NEWNAME (and optionally the NEWTYPE) field is required.

7=SUBMIT
This option allows the user to submit the selected file to the VSE POWER Reader Queue for execution. This option can only be performed against files with the FILETYPE of "JCL".  In addition this option requires special security permissions to be authorized to submit jobs to the VSE POWER Reader Queue.  Additional security permissions are needed if the JECL JOBCARD contains a SEC, FROM or LDEST parameter or if the VSE JCL contains a "// ID" statement.

8=COMPILE
This option provides the user the ability to perform one of the available programming language functions. A separate panel screen is displayed presenting the user with the options available.  These options are installation specific.  This option requires special security permissions as well because it involves generating and submitting JCL to VSE POWER for execution. 

9=DISPLAY
This option allows the user to select the member for display.  The dislay function provides all the functions of the CICS/REXX Full Screen Editor but does not allow the user to save the current file.

**COMMANE LINE AREA**

The Command Line area allows the user to enter available commands for execution.  More information on the commands available can be obtained by entering "HELP COMMANDS" in the Command Line area.

**MAIN DIRECTORY LIST**

The main directory list area shows the list of files and directories found in the current RFS directory.  Each line in this area contains the FILENAME and optional FILETYPE of each file or directory along with the DATE and TIME the file or directory was last updated and the SIZE of the file in records.

Please note that the directory listing can be sorted in several different ways.  The current sort option and order is displayed below the column heading area. More information on how to control the sort order can be found below in the PF Key descriptions.

**PF KEY LABEL AREA**

The PF Key label area displays the available PF Keys to the user.  These include the following.

**PF1**=HELP
Provides access to the available help panels.

**PF2**=REFRESH
Refreshes the current directory listing displayed.

**PF3**=END
Exits the RFSLIB product and returns to either CICS or the z/VSE Interactive Interface.

**PF4**=UP ONE DIR
This allows the user to move up one directory in the CICS/REXX File System.  The RFS is a hierarchical file system similar to the file system found on a Windows or Linux based PC.

**PF6**=NEW MEM/DIR
This allows the user to create a new file (or member) or a new directory.  A separate panel screen is displayed.

**PF7**=BACKWORD **PF8**=FORWARD
Allows the user to page backward and forward through the current RFS directory listing.  These PF keys are only available when the directory listing contains more data than fits on a single screen.

**PF9**=SORT.DATE
Sorts the directory listing in DATE/TIME order. When this key is pressed it will re-sort the display in DATE/TIME order.  Initially this will be in descending order showing the most recently accessed files and directories at the top of the list.  If this PF Key is pressed again the sort order will be reversed to ascending order showing the oldest files and directories at the top of the list.

**PF10**=SORT.NAME
Sorts the directory listing in FILENAME order. When this key is pressed it will re-sort the display in FILENAME order.  Initially this will be in ascending order.  If this PF Key is pressed again the sort order will be reversed to descending order.

**PF11**=SORT.SIZE
Sorts the directory listing base on file SIZE. When this key is pressed it will re-sort the display in SIZE order showing the smallest files at the top of the list.  Pressing this PF Key again will reverse this sorting the listing showing the largest files at the top of the list.

Please also note that the PF5 key is available and will retrieve the last command entered in the Command Line area at the top of the screen.  The last 25 commands entered are retained.  Hitting the PF5 key multiple times will retreive the next oldest command entered from the command history.

**FILENAME PREFIX AREA**

The FILENAME PREFIX selection can be used to limit the displayed directory listing to only the files and directories which start with the specified PREFIX. Enter the desired PREFIX and hit PF2 to REFRESH the display.

## RFSLIB Commands

The RFSLIB Command Line provides access to several important commands.  Please note that all commands require special security permissions in order to be able to execute them.  

The supported commands include the following...

**CD (Change Directory)**

The CD command can be used to change the current RFS directory directly to the specified directory. The format is as follows...

    CD dddddddd

Where "dddddddd" can be either a fully qualified directory name (for example "POOL1:\USERS\SYSA") or a subdirectory of the current directory (without the  qualifying higher directory names and pool name).  In addition the RFS directory can be specified using a shortcut to represent moving up a directory level (for example ".\OPER" would move up one directory level and then CD to the "OPER" under the higher level directory). 

**ICCFP**

The ICCFP command can be used to PUNCH ICCF library members from ICCF to the CICS/REXX File System. The format of the command is as follows...

    ICCFP iccflib iccfmember rfsfileid <option>

Where "iccflib" is the ICCF library number, "iccfmember" is the ICCF membername in that library and "rfsfileid" is the target RFS filename. "option" can be specified as "REPLACE" if the target RFS filename already exists and you'd like to replace it.

**LIBRP**

The LIBRP command can be used to PUNCH VSE sublibrary members from a VSE sublibrary to the CICS/REXX File System. The format of the command is as follows...

    LIBRP l.s m.t rfsfileid <option>

Where "l.s" is the VSE Library.Sublibrary (for example PRD2.CONFIG), "m.t" is the membername.membertype and "rfsfileid" is the target RFS filename. "option" can be specified as "REPLACE" if the target RFS filename already exists and you'd like to replace it.

Please note that the user must have "READ" access to the specified VSE sublibrary member.

**LIBRC**

The LIBRC command can be used to CATALOG an RFS file into a VSE sublibrary member. The format of the command is as follows...

    LIBRC l.s m.t rfsfileid

Where "l.s" is the VSE Library.Sublibrary (for example PRD2.CONFIG), "m.t" is the membername.membertype and "rfsfileid" is the source RFS file to be CATALOGed into the specified VSE sublibrary member.

Please note that the user must have "UPDATE" access to the specified VSE sublibrary member.

**EXEC**

The EXEC command can be used to execute a CICS/REXX EXEC or PROCedure. The format of the command is...

    EXEC xxxxxxxx

Where "xxxxxxxx" is the name of the CICS/REXX EXEC or PROCedure.  The EXEC or PROCedure must be in the current CICS/REXX search chains.  For PROCedures this must also be a VSE sublibrary specified in the LIBDEF PROC SEARCH list.

**RESET**

The RFSLIB product adds support for RFS filename level enqueue / dequeue support.  This prevents users from editing the same member at the same time.  Under certain circumstances (i.e. disconnecting your terminal while editing an RFS file) the enqueue lock on an RFS file can be left in the "locked" state.  The RESET command can be used to release the RFS file enqueue lock.  The format of the command is as follows...

    RESET rfsfileid

Please note that this command is typically restricted to the System Administrator or Security Administrator.

## CICS/REXX Full Screen Editor

### Overview

The CICS/REXX Full Screen Editor is patterned after VM/CMS XEDIT. The editor is provided so data can be created, updated and viewed from within the CICS environment.

The CICS/REXX editor includes several prefix commands (for example: C, CC, M, MM, B, A, F, P) in common with the XEDIT editor.

While in an editor session, commands entered on the command line can be chained by placing a ";" between each command. Support is also provided for macros written in REXX. This lets you customize editor settings with a profile exec or add new commandes to the editor.

### Screen Format

The CICS/REXX Editor screen is laid out as follows...

The first line is reserved for the TITLE LINE and contains the following:
- Full qualified RFS File ID
- Column numbers
- Displayed messages from the Editor

There are informational lines that let you know you have reached "*** TOP OF DATA ***" or "*** BOTTOM OF DATA ***".

The line which starts with "COMMAND ===>" is the Command Line where Editor commands can be entered or Editor macros can be invoked. The line immediately below the Command Line is concidered the "current line". This "current line" is highlighted and is significant for certain Editor commands.

The Data Lines area of the screen is used to display and edit your data. Each line in the Data Area contains a The Data Lines area of the screen is used to display and edit your data. Each line in the Data Area contains a "prefix area" which typically displays the line number of each data line. Prefix Commands may be entered in this "prefix area".

### Prefix Commands

Several prefix commands are provided by the Editor. These commands are entered in the prefix area and give you the ability to copy, move, delete and replicate lines on either an individual or a consecutive block basis.

### Individual Line Commands

The folloing commands work with individual lines and consist of one character...

    '/' - Specify current line in file
    'I' - Insert a line
    'D' - Delete a line
    'C' - Copy a line
    'M' - Move a line
    'R' - Replicate a line
    '"' - Synonym for replicate

When you enter one of these commands in the prefix area of a line the command performs its respective function on that line. You can also append a number to the end of the prefix command. This acts as a replication factor. If number "5" is appended to the "I" command five lines are inserted into the file.

### Consecutive Block Commands

The following commands work with consecutive blocks of lines and consist of two characters...

    'DD' - Delete a block of lines
    'CC' - Copy a block of lines
    'MM' - Move a block of lines
    'RR' - Replicate a block of lines
    '""' - Synonym for block replicate
    'RR' - Replicate a block of lines
    '""' - Synonym for block replicate

Block commands are processed in pairs. You place one command in the prefix area of the first line of the block and place the same command in the prefix area of the last line of the block.  For example to delete a block of lines place a "DD" in the prefix area of the first line of the block and a "DD" in the prefix area of the last line in the block. If this was done on lines 00001 and 00005 it would delete line 00001 through 00005 inclusive.  The only block prefix command that
allows a replication factor is the replicate command ('RR' or '""').  The replication factor must be specified with the first 'RR' replication command.

### Destination Commands

The following commands are called destination commands...

    'A' - After
    'B' - Before
    'F' - Following
    'P' - Preceding

These commands are used in conjunction with the copy and move commands (both individual and consecutive block) to specify the placement of the resulting copy or move function.

### Command Line Commands

The syntax and description for each of the Command Line commands follows...

**BACKWARD**

    |             +-1---+
    |>>--BAckward-+-----+----------------------------------------<<
    |             +-n---+
    |             +-*---+

BACKWARD scrolls backward toward the beginning of a file for a specified number of screen displays.

The default is 1 screen.
'n' specifies the number of screen displays.
'*' If you specify an asterick (*) the screen scrolls back to the top of the file.

**BOTTOM**

    |>>--BOTTOM--------------------------------------------------<<

BOTTOM scrolls to the bottom of the file.

**CANCEL**

    |>>--CANCEL--------------------------------------------------<<

CANCEL ends the current edit session without saving the changes.

**CASE**

    |         +--(1)--------------+
    |>>--CASE-+-------------------+------------------------------<<
    |         +-MIXED-+-RESPECT-+-+
    |         |       +-IGNORE--+ |
    |         |                   |
    |         +-UPPER-+-RESPECT-+-+
    |                 +-IGNORE--+

CASE sets the case translation and interpretation preferences.

UPPER - translates lowercase character to uppercase when entered.
MIXED - performs no case translation and allows mixed case input.
RESPECT - respects the case of each character while doing a search.
IGNORE - ignores the case of each character while doing a search.
IGNORE - ignores the case of each character while doing a search.

(1) the default is specified in the editor profile.

**CHANGE**

    |>>--CHANGE--/string1/string2/--+-----+----------------------<<
    |                               +-ALL-+

CHANGE changes a sting in the file.

string1 - specifies the string being replaced.
string2 - specifies the string that replaces string1.
ALL - indicates that all occurrance on all lines of string1 should be replaced by string2.

**CMDLINE**

    |>>--CMDLINE--+-TOP----+-------------------------------------<<
    |             +-BOTTOM-+

CMDLINE sets the Command Line display preference.

TOP - displays the Command Line on the second line of the screen.
BOTTOM - displays the Command Line on the bottom of the screen.

**CURLINE**

    |>>--CURLINE--n----------------------------------------------<<

CURLINE sets the current line to the number specified (n).

**DOWN**

    |          +-1--+
    |>>--DOWN--+----+--------------------------------------------<<
    |          +-n--+

DOWN scrolls forward in the file.

The default is 1 line.
n specifies the number of lines.

**EDIT**

    |          +-NONAME---+
    |>>--EDIT--+----------+--+--------------------+--------------<<
    |          +-fileid---+  +-(-MACRO-macroname--+

EDIT opens a new edit session from within the existing edit session.

If NONAME is specified (this is the default) a new file is created and opened in an edit session.

fileid - specifies the RFS file ID of the file to b edited.

MACRO macroname - specifies that the file to be opened or created will have the specified MACRO executed against it.

**EXEC**

    |>>--EXEC--REXXprogramname--+------------+-------------------<<
    |                           +-arguments--+

EXEC executes a REXX program within an editor session.

**FILE**

    |>>--FILE--+---------+---------------------------------------<<
    |          +-fileid--+

FILE saves the current file being edited.

The default is to save the current file as the defauls file ID.
fileid - specifies the RFS file ID to save the current file to.

**FIND**

    |          +-previous_searchstring--+
    |>>--FIND--+------------------------+------------------------<<
    |          +-search_string----------+

FIND locates a string of text in the current editor file.

NOTE: The CASE command RESPECT option can effect the function of the FIND command.  For more information see the CASE command section above.

**FORWARD**

    |            +-1---+
    |>>--FORward-+-----+----------------------------------------<<
    |            +-n---+
    |            +-*---+

FORWARD scrolls forward toward the end of a file for a specified number of screen displays.

The default is 1 screen.
'n' specifies the number of screen displays.
'*' If you specify an asterick (*) the screen scrolls forward to the end of the file.

**GET**

    |>>--GET--fileid--------------------------------------------<<

GET imports an RFS file into the current edit session. The file is inserted after the current line.

fileid - specifies the RFS file ID of the file to import.

**GETLIB**

    |>>--GETLIB--lib.sublib(mem.type)---------------------------<<

GETLIB imports a member from a VSE sublibrary into the current edit session. The member is inserted after the current line.

lib.sublib(mem.type) specifies the VSE sublibrary and member to be imported.

**JOIN**

    |>>--JOIN---------------------------------------------------<<

JOIN joins two lines into one. It will join the line the cursor is on with the line immediately following it.

**LEFT**

    |          +-1--+
    |>>--LEFT--+----+-------------------------------------------<<
    |          +-n--+

LEFT scrolls left in the file.

The default is to scroll 1 character left.
n specifies the number of characters to scroll.
If you specify 0 for n you will scroll to the far left side.

**MACRO**

    |>>--MACRO--fileid------------------------------------------<<

MACRO calls a macro.

fileid specifies the RFS file ID of the macro you want to run.

**NULLS**

    |>>--NULLS--+-ON--+-----------------------------------------<<
    |           +-OFF-+

NULLS controls whether the fields on the screen will be written with training blanks or trainling nulls.

ON specifies that fields on the screen are written with trailing nulls.
OFF specifies that fields on the screen are written with trailing blanks.

**NUMBERS**

    |>>--NUMbers--+-ON--+---------------------------------------<<
    |             +-OFF-+

NUMBERS sets the prefix area display preference.

ON specifies that sequential line numbers should be displayed in the prefix area.
OFF specifies that equal signs should be displayed in the prefix area.

**PFKEY**

    |           +-(1)--+
    |>>--PFkey--+------+----------------------------------------<<
    |           +-text-+

PFKEY sets or processes a PF key.
(1) if text is not specified the PF key is processed.
text specifies the text that the PF key is set to.

**PFKLINE**

    |>>--PFKLINE--+-TOP----+------------------------------------<<
    |             +-BOTTOM-+
    |             +-n------+
    |             +-OFF----+

PFKLINE sets the PF key line display preferences.

TOP displays the PF key line at the top of the screen.
BOTTOM displays the PF key line at the bottom of the screen.
n specifies the line number on the screen where to display the PF key line.
OFF removes the PF key line from the screen.

**QQUIT**

    |>>--QQUIT--------------------------------------------------<<

QQUIT ends the current edit session without saving changes.

**QUIT**

    |>>--QUIT---------------------------------------------------<<

QUIT ends the current edit session.  When the current file has been changed the editor does not let you exit until either a save is done or you enter the QQUIT command.

**RESET**

|>>--RESET--------------------------------------------------<<

RESET terminates any pending prefix commands.

**RIGHT**

    |           +-1--+
    |>>--RIght--+----+------------------------------------------<<
    |           +-n--+

RIGHT scrolls right in the file.

The default is to scroll 1 character right.
n specifies the number of characters to scroll.

**SAVE**

    |>>--SAVE--+---------+---------------------------------------<<
    |          +-fileid--+

SAVE saves the current file being edited.

The default is to save the current file as the defauls file ID.
fileid - specifies the RFS file ID to save the current file to.

**SORT**

    |                  +-A--+  +-1-------+
    |>>--SORT--+-*--+--+----+--+---------+-----------------------<<
    |          +-n--+  +-D--+  +-fromcol-+
    |                          +-tocol---+

SORT sorts the lines from the current line down.

* specifies that all the lines from the current line to the end of the file are sorted.
n specifies that n lines are sort from the current line down.
A specifies that the lines are sorted in ascending order.  This is the default.
D specifies that the lines are sorted in descending order.
fromcol specifies that the lines are sorted based on the data beginning at this column. If you do not specify fromcol and
tocol sorting begins at column 1.
tocol specifies that the lines are sorted based on the data ending in this column.

**SPLIT**

    |>>--SPLIT---------------------------------------------------<<

SPLIT splits a line into two lines at the current cursor position.

**STRIP**

    |>>--STRIP---------------------------------------------------<<

STRIP strips the training blanks off all lines in the current file.

**TOP**

    |>>--TOP-----------------------------------------------------<<

TOP scrolls to the top of the file.

**TRUNC**

    |>>--TRUNC--column-------------------------------------------<<

TRUNC truncates each line of the current file to a given length.

column specifies the last column you want to keep.

**UP**

    |        +-1--+
    |>>--UP--+----+----------------------------------------------<<
    |        +-n--+

UP scrolls backward in the file.

The default is 1 line.
n specifies the number of lines.

NOTE:
More information on the CICS/REXX Full screen Editor can be found in Chapter 18 of the following IBM manual...

IBM CICS Transaction Server for VSE/ESA REXX Guide.
IBM Publication Number SC34-5764-01.

## Installation

### Installation Steps
<to be create>

### Tailoring JCL Skeletons
	
The RFSLIB Application requires tailoring of JCL skeletions for use with the RFSLIB "Compile" feature.  One option is using the IBM provided Skeletons in ICCF Library 2 as the basis of the RFSLIB skeletons.  To do so you can do the following...
	
1) Catalog the appropriate ICCF skeleton members from ICCF library 2 into PRD2.CONFIG (or you desginated RFS Skeleton sublibrary in RFS.CONFIG).
2) Make the following replacements in the skeletons...
a) Locate all occurrances of the "* $$ SLI" card included in the skeleton.  It normally looks like the following...
    * $$ SLI ICCF=(&PROGNAME,&PASSWORD),LIB=(&LIBNO)
Replace all occurrances of this entire card with the following...
    /INCLUDE &&PARAM2
b) Replace all occurrances of &JOBNAME with &&PARAM1
c) Replace all occurrances of &PROGNAME with &&PARAM1
d) Replace all occurrances of &CATALOG and &HTML (present in MAP related skeletons) to an approriate value of 1 or 2. These values are not provided by the RFSLIB "Compile" feature ao they most be hardcoded into your skeletons as required.  In most cases you would set this to 1 for Catalog and 2 for HTML unless you also required generation of the HTML templates. 
3) Copy RFSCOMP.Z to PRD2.CONFIG as RFSCOMP.CONFIG and tailor this member as needed for the skeleton member names you created above.
Note: RFSCOMP.Z ships pre-configured for supporting the VSE provided Assembler and COBOL for z/VSE skeleton names.

### Tailoring existing CICS/REXX PROCs to enhance the functionality of the RFSLIB application

With a few minor changes to several of the CICS/REXX PROCs shipped with VSE you can provide some needed enhancements to how the RFSLIB application functions.

These members include the following...

	CICESVR.PROC 
	CICFPROF.PROC
	CICSPROF.PROC
	CICSTART.PROC
	
### Cross reference of VSE Library members provided with the RFSLIB application

Members in PRD2.RFSLIB for the main "RFSLIB" application...

    RFSAUTH.PROC      - RFSLIB interface to an External Security Manager to provide security around the RFSLIB product's features and functions.
    RFSCOMP.PROC      - RFSLIB "Compile" feature.
    RFSCOMPD.PANSRC   - RFSLIB Panel Source for the RFSLIB "Compile" feature.
    RFSCOMPD.PROC     - RFSLIB "Compile" feature additional functions.   
    RFSHELP.PANSRC    - RFSLIB Panel Source for the RFSLIB "Help" feature (Panel for Model2 3270 terminal).
    RFSHELP.PROC      - RFSLIB PROC which provides the "Help" feature.
    RFSHELP3.PANSRC   - RFSLIB Panel Source for the RFSLIB "Help" feature (Panel for Model3 3270 terminal).
    RFSHELP4.PANSRC   - RFSLIB Panel Source for the RFSLIB "Help" feature (Panel for Model4 3270 terminal).
    RFSICCFP.PROC     - RFSLIB PROC which provides the "ICCFP" feature.  
    RFSLIB.PANSRC     - RFSLIB Panel Source for main RFSLIB screen (Panel for Model2 3270 terminal).
    RFSLIB.PROC       - RFSLIB PROC provides the main RFSLIB functionality.
    RFSLIBA.PANSRC    - RFSLIB Panel Source for "Create New RFS File or Directory" function.
    RFSLIBC.PANSRC    - RFSLIB Panel Source for "Compile Job Generation" function.
    RFSLIBF.PANSRC    - RFSLIB Panel Source for "RFS File Copy/Move" function.
    RFSLIBRC.PROC     - RFSLIB PROC which provides the "LIBRC" feature. 
    RFSLIBRP.PROC     - RFSLIB PROC which provides the "LIBRP" feature.
    RFSLIB3.PANSRC    - RFSLIB Panel Source for main RFSLIB screen (Panel for Model3 3270 terminal).
    RFSLIB4.PANSRC    - RFSLIB Panel Source for main RFSLIB screen (Panel for Model4 3270 terminal).
    RFSPRINT.PROC     - RFSLIB PROC which provides the "PRINT" feature.
    RFSSKEL.PROC      - RFSLIB PROC which provides the "JCL Skeleton Substitution and JCL Generation" feature.
    RFSSUBMT.PROC     - RFSLIB PROC which provides the "SUBMIT" feature.
    RFSTOOL.PROC      - RFSLIB Batch Utility 

Additional members in PRD2.RFSLIB which add features and functions to the REXX and FLST environments...
    
    AUTHFUNC.PROC     - RFSLIB ships with a "dummy" version of AUTHFUNC.PROC which does not include any interfacing to an External Security Manager.
    AUTHFUNC.Z        - Replace AUTHFUNC.PROC with this member if you want to activate the interface to an External Security Manager to control access to the SUBMIT, PRINT, LIBRP and LIBRC PROCs.
    DELEXEC.PROC      - Tool for managing EXECs in POOL2:\EXECLIB.  
    DELPANEL.PROC     - Tool for managing PANELs in POOL2:\PANELS.
    DELSHR.PROC       - Tool for managing items in POOL2:\SHARED.
    EDITPROF.PROC     - CICS/REXX Editor Profile for use with RFSLIB product.  
    GENJCL.PROC       - Tool to test JCL Skeleton substitutions and review generated JCL.
    GETEXEC.PROC      - Tool for managing EXECs in POOL2:\EXECLIB.  
    GETPANEL.PROC     - Tool for managing PANELs in POOL2:\PANELS.  
    GETSHR.PROC       - Tool for managing items in POOL2:\SHARED.  
    LIBRC.PROC        - PROC included which provides ICCF LIBRC like command functionality from the CICS/REXX RFS File System.
    LIBRP.PROC        - PROC included which provides ICCF LIBRP like command functionality into the CICS/REXX RFS File System.
    LSTEXEC.PROC      - Tool for managing EXECs in POOL2:\EXECLIB.  
    LSTPANEL.PROC     - Tool for managing PANELs in POOL2:\PANELS.  
    LSTSHR.PROC       - Tool for managing items in POOL2:\SHARED.  
    MD.PROC           - PROC included which provides a simplified command for defining CICS/REXX RFS File System directories.
    MOVE.PROC         - PROC included which provides a simplified MOVE command for members in the CICS/REXX RFS File System.
    PRINT.PROC        - PROC included which provides a method to send CICS/REXX RFS File System members to the POWER LST queue for printing.
    PUTEXEC.PROC      - Tool for managing EXECs in POOL2:\EXECLIB.   
    PUTPANEL.PROC     - Tool for managing PANELs in POOL2:\PANELS.   
    PUTSHR.PROC       - Tool for managing items in POOL2:\SHARED.    
    RD.PROC           - PROC included which provides a simplified command for deleting/removing CICS/REXX RFS File System directories.  
    RFSSETUP.PROC     - PROC included to perform required SETUP steps (i.e. move panels, etc.) to complete the installation of the RFSLIB application.
    RULER.PROC        - CICS/REXX Editor Macro for adding ruler lines to the display.  
    SETUSER.PROC      - PROC included which copies the default user EDITPROC.EXEC, FLSTPROF.EXEC and PRINT.PROFILE (located in POOL2:\PUBLIC) to a USERS folder.  
    SKELETON.PROC     - PROC included which provides the "JCL Skeleton Substitution and JCL Generation" feature.
    SUBMIT.PROC       - PROC which provides the "SUBMIT" command functionality from the REXX and FLST transactions.
    VIEW.PROC         - PROC which provides the "VIEW" command functionality from the REXX and FLST transactions.

These members in PRD2.RFSLIB will be copied to PRD2.CONFIG and control the configuration of the RFSLIB application...

    RFSCOMP.Z         - Will be copied to PRD2.CONFIG as RFSCOMP.CONFIG 
    RFSLIB.Z          - Will be copied to PRD2.CONFIG as RFSLIB.CONFIG

For CICS/REXX Security Features (security over the RFS File System and access to VSE Library members) you will need to CICS Translate, Assemble and Link tailored versions of the CICSECX1 and CICSECX2 Security Exits.  Sample sourcecode for these is provided in PRD2.RFSLIB in the following members...

    CICSECX1.A
    CICSECX2.A
