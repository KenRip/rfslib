# RFSLIB CICS/REXX Application for z/VSE (and VSEn)

A CICS/REXX application that provides advanced navigation plus additional features and functions over and above those provided in the base CICS/REXX provided with the VSE Operating System.  It provides seemless integration into the VSE Interactive User Interface (IUI) provided with VSE and promotes VSE users to use the powerful full-screen editor environment included with the CICS/REXX support.

![Screenshot of RFSLIB Mainscreen](https://github.com/KenRip/rfslib/raw/master/images/Screenshot%20RFSLIB%20Mainscreen%20M2.png)

![Screenshot of RFSLIB Mainscreen](https://github.com/KenRip/rfslib/raw/master/images/Screenshot%20RFSLIB%20CICS-REXX%20Editor%20M4.png)

## Table of Contents

- [Introduction](#introduction)
  - [Installation Prerequisite](#installation-prerequisite)
- [Installation](#installation)
  - [Installation Steps](#installation-steps)
   - [Restore RFSLIB VSE Library to your system](#restore-rfslib-vse-library-to-your-system)
	- [Tailoring existing CICS/REXX PROCs to enhance the functionality of the RFSLIB application](#tailoring-existing-cicsrexx-procs-to-enhance-the-functionality-of-the-rfslib-application)
	  - [Tailoring the CICSTART.PROC](#tailoring-the-cicstartproc)
	  - [Tailoring CICESVR.PROC](#tailoring-cicesvrproc)
	  - [Tailoring CICFPROF.PROC](#tailoring-cicfprofproc)
	  - [Tailoring CICSPROF.PROC](#tailoring-cicsprofproc)
    - [Tailoring JCL Skeletons](#tailoring-jcl-skeletons)
	- [Installing RFSLIB Panels](#installing-rfslib-panels)
	- [Tailoring the RFSLIB.CONFIG member](#tailoring-the-rfslibconfig-member)
	- [Tailoring the CICS/REXX Security Exits for interfacing with an External Security Manager](#tailoring-the-cicsrexx-security-exits-for-interfacing-with-an-external-security-manager)
	  - [Concerns with using the VSE supplied versions of these exits](#concerns-with-using-the-vse-supplied-versions-of-these-exits)
	  - [Assembling the RFSLIB provided version of the security exits](#assembling-the-rfslib-provided-version-of-the-security-exits)
	- [Tailoring your External Security Manager to support the RFSLIB application](#tailoring-your-external-security-manager-to-support-the-rfslib-application)
	  - [Additional ESM Tailoring for the CICSECX1 and CICSECX2 security exits](#additional-esm-tailoring-for-the-cicsecx1-and-cicsecx2-security-exits)
- [Functionality](#functionality)
  - [TOP AREA](#top-area)
  - [COMMANE LINE AREA](#command-line-area)
  - [MAIN DIRECTORY LIST](#main-directory-list)
  - [PF KEY LABEL AREA](#pf-key-label-area)
  - [FILENAME PREFIX AREA](#filename-prefix-area)
- [RFSLIB Commands](#rfslib-commands)
- [CICS/REXX Full Screen Editor](#cicsrexx-full-screen-editor)
  - [Overview](#overview)
  - [Screen Format](#screen-format)
  - [Prefix Commands](#prefix-commands)
  - [Individual Line Commands](#individual-line-commands)
  - [Consecutive Block Commands](#consecutive-block-commands)
  - [Command Line Commands](#command-line-commands)
- [APPENDIX](#appendix)
  - [Adding the RFSLIB Application to the Interactive User Interface](#adding-the-rfslib-application-to-the-interactive-user-interface)
  - [Cross reference of VSE Library members provided with the RFSLIB application](#cross-reference-of-vse-library-members-provided-with-the-rfslib-application)
  - [The RFSTOOL.PROC REXX/VSE Batch Utility](#the-rfstoolproc-rexxvse-batch-utility)
    - [RFS Backups](#rfs-backups)
    - [RFSTOOL.PROC Special Considerations](#rfstoolproc-special-considerations)
    - [RFSTOOL.PROC JCL and Input Paramters](#rfstoolproc-jcl-and-input-paramters)
      - [RFSTOOL.PROC Input Parameter Control Cards](#rfstoolproc-input-parameter-control-cards)
      - [RFSTOOL.PROC Sample JCL](#rfstoolproc-sample-jcl)

## Introduction

The RFSLIB application is an extension of the CICS/REXX support already provided with VSE of charge with the IBM CICS Transaction Server for VSE/ESA V1.1.1 product (it is also include in the CICS Transaction Server for z/VSE 2.2 product).  It provides an Intercative Interface look and feel to managing and editing the CICS/REXX File System.  It also provides several enhancements to the CICS/REXX functionality provided with VSE.  These enhancements include; SUBMIT and PRINT functionality, interfaces with an External Security Manager to provide a mechanism for securing the features and functions of the RFSLIB application.

[Return to TOC](#table-of-contents)

### Installation Prerequisite
You must complete the instructions found in Appendix K: Post-Installation Configuration in the CICS Transaction Server for VSE/ESA REXX Guide.  This is necessary because the RFSLIB application relies on the existence of at least one REXX/CICS File System ???Pool??? being defined.

Please note that the steps decribed in the section titled "Rename supplied Procedures" of Appendix K in the CICS Transaction Server for VSE/ESA REXX Guide have been made obsolete.  These *.Z members are no longer provided in PRD1.BASE.  Instead they are shipped already renamed to *.PROC in PRD1.BASE which makes them avaialble in the CICSICCF region shipped with z/VSE.

Please also note that the REXX DB2 Interface support described in the section titled "Configure the REXX DB2 Interface" of Appendix K in the CICS Transaction Server for VSE/ESA REXX Guide is NO longer available in z/VSE 6.1 and above.  CICS REXX support has been added to the z/VSE database connector DBCLI (Database Call Level Interface) as of z/VSE 6.2 (via PTF).

[Return to TOC](#table-of-contents)

## Installation

The first part of installing the RFSLIB application is ensuring you have met the prerequisite requirements discussed in the "Introduction" section of this document.  By default the configuration of the RFSLIB application is configured to use both an RFS "POOL1:" and RFS "POOL2:" file systems.  As part of the default CICS/REXX setup VSE ships with the necessary VSAM files defined to support both file systems.  As part of the process you will format both these file systems.  You will also tailor the CICSTART.PROC in PRD2.CONFIG (orignally shipped in PRD1.BASE).  Tailoring should include updating the "AUTHUSER RCUSER" statement to add any additional userids that would be "authorized" users in the CICS/REXX environment.  You will also perform steps to generate the supplied CICS/REXX online help files (VSE default location is POOL2:\BOOK).  Finally you will run the installation verification PROC called CICIVP1 to verify your CICS/REXX environment is working properly.

[Return to TOC](#table-of-contents)

### Installation Steps

#### Restore RFSLIB VSE Library to your system

For the actual installation of the RFSLIB application there is a provided VSE Librarian Backup file in the format of an AWS Virtual Tape.  This VSE library backup can be restored using JCL simulare to the following...

    * $$ JOB JNM=LIBRREST,CLASS=0,DISP=D                       
    * $$ LST CLASS=A,DISP=D                                    
    // JOB LIBRREST 
    // ON $CANCEL OR $ABEND GOTO VTAPSTOP
    VTAPE START,UNIT=cua,LOC=<hostname/ipaddr of VTAPE server>,FILE='<path to AWS file>'
    // ASSGN SYS005,cua                                        
    // MTC REW,SYS005                                          
    // EXEC LIBR                                   
      RESTORE PRD2.RFSLIB.*.*:PRD2.RFSLIB T=SYS005 REP=YES   
    /*          
    /. VTAPESTOP
    VTAPE STOP,UNIT=cua
    /*
    /&                                                         
    * $$ EOJ                                                   

Note: This JCL may require tailoring for your environment and at a minumum the "cua" of the VSE VTAPE device must be specified.  The AWS Virtual Tape file is provided in the 'install' folder.

[Return to TOC](#table-of-contents)

#### Tailoring existing CICS/REXX PROCs to enhance the functionality of the RFSLIB application

Once the VSE library is installed there are a number of steps to be completed to tailor the RFSLIB application for you environment.  With a few minor changes to several of the CICS/REXX PROCs shipped with VSE you can provide some needed enhancements to how the RFSLIB application functions.

These members include the following...

	CICSTART.PROC
	CICESVR.PROC 
	CICFPROF.PROC
	CICSPROF.PROC

[Return to TOC](#table-of-contents)

##### Tailoring the CICSTART.PROC

By default CICS/REXX will automatically "authorize" the VSE sublibrary where the CICSTART.PROC is loaded from which is usually PRD2.CONFIG.  Because the RFSLIB application is loaded into another VSE library called PRD2.RFSLIB the CICSSTART.PROC must be updated to "authorize" this additional library.

This involves editing the CICSTART.PROC member in PRD2.CONFIG and locating the following lines...

    HELPPATH = 'POOL2:\BOOK'               /* SET HELP PATH       OW06629*/
    'RLS VARPUT HELPPATH \SYSTEM\DEFAULTS' /* SAVE IT             OW06629*/
    IF RC ^= 0 THEN EXIT RC                /* EXIT IF ERROR       OW06629*/

Insert the following lines after the lines above...

    'SETSYS AUTHCLIB PRD2.CONFIG' 
    'SETSYS AUTHCLIB PRD2.RFSLIB' 
    'SETSYS AUTHELIB PRD2.CONFIG' 
    'SETSYS AUTHELIB PRD2.RFSLIB' 
    
Note: It is recommended to include PRD2.CONFIG statements even though PRD2.CONFIG would normally be automatically "authorized" at startup of CICS/REXX.  This will avoid any issues if you change your CICS partitions LIBDEF SEARCH order.

It is also recommended that you uncomment the following lines if you haven't done so during your initial tailoring of the CICS/REXX environemnt...

    /* 'EXECLOAD PROCLIB CICEDIT' */       /* $V1C    OW20371,    OW06629*/
    /* 'EXECLOAD PROCLIB CICESVR' */       /* $V1C    OW20371,    OW06629*/
    /* 'EXECLOAD PROCLIB CICEPROF'*/       /* $V1C                OW20371*/
    
These would now look something like this...

    'EXECLOAD PROCLIB CICEDIT'             /* $V1C    OW20371,    OW06629*/
    'EXECLOAD PROCLIB CICESVR'             /* $V1C    OW20371,    OW06629*/
    'EXECLOAD PROCLIB CICEPROF'            /* $V1C                OW20371*/

[Return to TOC](#table-of-contents)

##### Tailoring CICESVR.PROC

To eliminate an annoying "flash" of an unformatted 3270 screen when exiting the CICS/REXX Editor and returning to the RFSLIB application make the following change to the CICESVR.PROC.  It is recommended that you first copy CICESVR.PROC from PRD1.BASE to PRD2.CONFIG and update the version in PRD2.CONFIG.

Locate the following lines in the CICESVR.PROC member (they will be at the very bottom of the member)...

    ESVR.EXITMSG     = 'Edit session complete;'
    ESVR.MSG         = 'REXX/CICS Editor'      

Change them to the following...

    ESVR.EXITMSG     = ''
    ESVR.MSG         = ''      

Note: this will have no adverse effect of the functionality of CICS/REXX outside the RFSLIB application.

[Return to TOC](#table-of-contents)

##### Tailoring CICFPROF.PROC

To add some enhancements to the CICS/REXX FLST transaction you can perform the following update to the CICFPROF.PROC.  It is recommended that you first copy CICFPROF.PROC from PRD1.BASE to PRD2.CONFIG and update the version in PRD2.CONFIG.

Add the following lines anywhere between the "ADDRESS FLSTSVR" and "ADDRESS REXXCICS" statements...

    'SET PFKEY 4 SUB' 
    'SET PFKEY 6 VIEW'   
    'SYNONYM DEL RFS DELETE'   
    'SYNONYM PRT PRINT'        
    'SYNONYM RFRSH REFRESH'    
    'SYNONYM SUB SUBMIT'       

Note: This will add these PFKEY and Command Synonyms to the CICS/REXX FLST Transaction.  This will have no adverse effect of the functionality of CICS/REXX outside the RFSLIB application.

[Return to TOC](#table-of-contents)

##### Tailoring CICSPROF.PROC 

**Note: This step is optional.**  The changes made here are only necessary if you want to update the CICS/REXX EXEC PATH or if you intend to run the CICS/REXX environment and the RFSLIB application with integration to an External Security Manager (ESM) like the VSE Basic Security Manager or a 3rd Party ESM like Top Secret.

To update the CICS/REXX EXEC PATH you can perform the following update to the CICSPROF.PROC.  It is recommended that you first copy CICSPROF.PROC from PRD1.BASE to PRD2.CONFIG and update the version in PRD2.CONFIG.

Locate the following statement in CICSPROF.PROC...

    EXECEXIT: ROUT = 'EXIT'

Add the following line after the above line...

    ADDRESS REXXCICS 'PATH POOL2:\EXECLIB'  /* Set default PATH       */

Note: The above value "POOL2:\EXECLIB" is simply an example.  You can add any RFLS folders to this if needed.  Please refer to the IBM CICS Transaction Server for VSE/ESA REXX Guide for more information on the "PATH" command.

To add support for automatically defaulting the RFS "MKDIR" command to use the "SECURED" option perform the following update to the CICSPROF.PROC. It is recommended that you first copy CICSPROF.PROC from PRD1.BASE to PRD2.CONFIG and update the version in PRD2.CONFIG.

Locate the following statement in CICSPROF.PROC...

    ADDRESS RFS 'MKDIR' RESULT       /* Ensure default dir exists     */ 
    
Add the following lines after the above line...

    /* AUTH section added to ensure External Security is used */            
    AUTH: ROUT = 'AUTH'                                                     
    ADDRESS REXXCICS 'CD'            /* Do not remove this command    */ 
    ADDRESS RFS 'AUTH' RESULT 'SECURED'   /* Ensure dir XSEC      */     

Note: This will add the External Security (XSEC) flag to the RFS folders that are created with the RFS "MKDIR" command.  You will also need to tailor the RFSLIB.CONFIG member to set "USEESM = 'YES'" and Assemble and LinkEdit versions of the CICSECX2 Security Exit.  This is described later in this document.

[Return to TOC](#table-of-contents)

#### Tailoring JCL Skeletons
	
The RFSLIB Application requires tailoring of JCL skeletions for use with the RFSLIB "Compile" feature.  One option is using the IBM provided Skeletons in ICCF Library 2 as the basis of the RFSLIB skeletons.  To do so you can do the following...
	
Catalog the appropriate ICCF skeleton members from ICCF library 2 into PRD2.CONFIG (or you desginated RFS Skeleton sublibrary in RFS.CONFIG).

Locate all occurrances of the "* $$ SLI" card included in the skeleton.  It normally looks like the following...
	
    * $$ SLI ICCF=(&PROGNAME,&PASSWORD),LIB=(&LIBNO)
	
Replace all occurrances of this entire card with the following...
	
    /INCLUDE &&PARAM2
	
Replace all occurrances of &JOBNAME with &&PARAM1

Replace all occurrances of &PROGNAME with &&PARAM1

Replace all occurrances of &CATALOG and &HTML (present in MAP related skeletons) to an approriate value of 1 or 2. These values are not provided by the RFSLIB "Compile" feature ao they most be hardcoded into your skeletons as required.  In most cases you would set this to 1 for Catalog and 2 for HTML unless you also required generation of the HTML templates. 

Copy RFSCOMP.Z to PRD2.CONFIG as RFSCOMP.CONFIG and tailor this member as needed for the skeleton member names you created above.

Note: RFSCOMP.Z ships pre-configured for supporting the VSE provided Assembler and COBOL for z/VSE skeleton names.

[Return to TOC](#table-of-contents)

#### Installing RFSLIB Panels

The CICS/REXX Panel Facility relies on the panels being installed in an RFS File System directory.  By default the RFSLIB application uses the POOL2:\PANELS directory for this (although this could be modified in the RFSLIB.CONFIG member described in the next section).

The RFSLIB "PANSRC" members are included in the PRD2.RFSLIB sublibrary but need to be copied to the POOL2:\PANELS directory.  This can be performed using the following command...

    CALL RFSPANLS.PROC
	
If the location is another folder other than POOL2:\PANELS you can override the default location by executing the command with the input paramter of the alternate location like this...

    CALL RFSPANLS.PROC 'POOL1:\RFSLPANS'

[Return to TOC](#table-of-contents)

#### Tailoring the RFSLIB.CONFIG member

The default RFSLIB.CONFIG member is included in the PRD2.RFSLIB sublibrary as member RFSLIB.Z.  Copy this memebr to PRD2.CONFIG as RFSLIB.CONFIG and tailor it to meet you needs.

Here's the default values provided...

    RFSPRODUCTLIB = 'PRD2.RFSLIB'      
    USEESM = 'NO'                      
    VSEIUISTARTED = 'NO'               
    DTSFILEVOLUME = 'SYSWK1'           
    DEFAULTPRINTCLASS = 'A'            
    DEFAULTPRINTDISP = 'H'             
    DEFAULTPRINTFORM = ''              
    DEFAULTTOUSERDIR = 'YES'           
    RFSUSERDIR = 'POOL1:\USERS'        
    RFSTOPDIR = 'POOL1:\USERS'         
    RFSPANELPATH = 'POOL2:\PANELS'     
    DEFAULTSORT = 'DATE'               
    DEFAULTSORTORDER = 'D'             
    SKELETONLIB = 'PRD2.CONFIG'        
    SKELETONMEMTYPE = 'SKELETON'       
    RFSHELPLIB = 'PRD2.RFSLIB'         

These values will work for most installations.  Here's a description of each setting and it's valid values...

**RFSPRODUCTLIB**

Set this value to the VSE sublibrary where the RFSLIB application was installed.  The value must be contained in single quotes as the default value above shows.

**USEESM**

This value is used to control if the RFSLIB application uses an interface to an External Security Manager (ESM) like the VSE Basic Security Manager or a 3rd party product like Top Secret.

Valid values are 'YES' or 'NO'.  The value must be contained in single quotes as the default value above shows.

**VSEIUISTARTED**

This value controls the return behavior when exiting the RFSLIB application.  If you have added the start of the RFSLIB application to a VSE Internative User Interface (IUI) Selection Panel then you should set this value to 'YES'.  Otherwise set this value to 'NO'.  The value must be contained in single quotes as the default value above shows.

**DTSFILEVOLUME**

Specify the DASD volume where your ICCF DTSFILE file is located.  The value must be contained in single quotes as the default value above shows.

**DEFAULTPRINTCLASS**

Specify the POWER LST CLASS that the RFSLIB PRINT function will defaults to.  The value must be contained in single quotes as the default value above shows.

**DEFAULTPRINTDISP**

Specify the POOWER LST DISP that the RFSLIB PRINT function will defaults to.  The value must be contained in single quotes as the default value above shows.

**DEFAULTPRINTFORM**

Specify the POWER LST FNO (FORM) value the RFSLIB PRINT function defaults to.  This can be blank (a value of '').  When specified the value must be contained in single quotes as the default value above shows.

**DEFAULTTOUSERDIR**

This value controls whether the default directory set when a user first enters the RFSLIB application will be their default USERS directory.

Valid values are 'YES' or 'NO'.  The value must be contained in single quotes as the default value above shows.

**RFSUSERDIR**

This sets the default USERS folder location in the RFS File System.  The default value is 'POOL1:\USERS'.  The value must be contained in single quotes as the default value above shows.

**RFSTOPDIR**

This value limits the TOP MOST directory a user can navigate to using the normal RFSLIB navigation of Option 2 and PF4.  The default value is 'POOL1:\USERS'.  The value must be contained in single quotes as the default value above shows.

Note: an authorized user can use the Command Line 'CD' command to navigate outside the limits set my this option.

**RFSPANELPATH**

This sets the default PANELS folder location for the RFSLIB PANELS.  The default value is 'POOL2:\PANELS'.  The value must be contained in single quotes as the default value above shows.

**DEFAULTSORT**

This value controls the default SORT option initially displayed to a user in the RFSLIB application.

Valid values are 'DATE', 'NAME' or 'SIZE'.  The value must be contained in single quotes as the default value above shows.

**DEFAULTSORTORDER**

This value controls the default SORT order initially displayed to a user in the RFSLIB application.

Valid values are 'D' (for descending) and 'A' (for ascending).  The value must be contained in single quotes as the default value above shows.

**SKELETONLIB**

This sets the default Skeleton VSE sublibrary location for the RFSLIB SKELETON members.  The default value is 'PRD2.CONFIG'.  The value must be contained in single quotes as the default value above shows.

**SKELETONMEMTYPE**

This sets the default Skeleton VSE sublibrary member type for the RFSLIB SKELETON members.  The default value is 'SKELETON'.  The value must be contained in single quotes as the default value above shows.
 
**RFSHELPLIB**

This sets the default VSE sublibrary where the RFSLIB Help File is located.  The default value is 'PRD2.RFSLIB'.  The value must be contained in single quotes as the default value above shows.

[Return to TOC](#table-of-contents)

#### Tailoring the CICS/REXX Security Exits for interfacing with an External Security Manager

There are two security exits provided with the CICS/REXX feature of VSE.  These are CICSECX1 and CICSECX2.

**CICSSECX1 - CICS/REXX Library Member Access Authorization**

This security exit provides access authorization for VSE Library Members by CICS/REXX.  CICS/RXX supports access to VSE Library Members using the RFS IMPORT and RFS EXPORT commands as well as the EDIT command with the "LIB" option.  This exit is ALWAYS invoked for these access attempts.

**CICSSECX2 - CICS/REXX Rexx File System Access Authorization**

This security exit provides access authorization for REXX File System (RFS) directories.  Only RFS directories can be secured, files withing directories cannot.  This exit is ONLY invoked for RFS directories that have been defined as "SECURED" using the RFS AUTH command.

[Return to TOC](#table-of-contents)

##### Concerns with using the VSE supplied versions of these exits

The CICSECX1 and CICSECX2 security exits that ship with VSE are provided in PRD1.BASE in source (*.A members), object **.OBJ members) and executable (*.PHASE members).  The CICSSECX1 code that is provided is void of any External Security Manager (ESM) code (it simply set Register 15 to 0 and returns which would "allow" all VSE sublibrary access without any ESM checks.  Unfortunately that is NOT true of the CICSECX2 security exit.  Instead the CICSECX2 code provided does a query to to the ESM for a RESCLASS(REXRFS) which is NOT supported by the VSE Basic Security Manager.  Because of this if you were to use the CICS/REXX RFS AUTH command to change a CICS/REXX File System directory to "SECURED" (which enables external security and calls to the CICSECX2 security exit) ALL access to that folder would fail because the exit would ALWAYS return a non-zero value in Register 15.

[Return to TOC](#table-of-contents)

##### Assembling the RFSLIB provided version of the security exits

Members CICSECX1.A and CICSECX2.A are provided in the PRD2.RFSLIB with the RFSLIB application.  If you would like to utilize these exits to interface to an External Security Manager (like the VSE Basic Security Manager) these exits will need to be CICS Translated, Assembled and LinkEdited.  These exits are standard CICS Command Level Assembler programs and can be assembled using the Interact Interface Dialogs and the C$$ASONL skeleton provided in ICCF library 2.  The target "Catalog" sublibrary should be either PRD2.CONFIG or the PRD2.RFSLIB as long as these libraries are in the LIBDEF search chain for your CICS partition ahead of the PRD1.BASE sublibrary. 

[Return to TOC](#table-of-contents)

#### Tailoring your External Security Manager to support the RFSLIB application

If you intend to enable the RFSLIB application's External Security Manager (ESM) interface (by setting USEESM = 'YES' in the RFSLIB.CONFIG member) you will need to first add the following RESCLASS(FACILITY) entries in your ESM...

    RFSLIB.CD             - Controls the use of the CD command      
    RFSLIB.COMPILE        - Controls access to the "Compile" function 
    RFSLIB.CONFIG         - Controls the use of the CONFIG command
    RFSLIB.EXEC           - Controls the use of the EXEC command    
    RFSLIB.ICCFP          - Controls the use of the ICCFP command   
    RFSLIB.LIBRC          - Controls the use of the ICCFC command   
    RFSLIB.PRINT          - Controls the use of the PRINT command
    RFSLIB.RESET          - Controls the use of the RESET command
    RFSLIB.SUBMIT         - Controls the use of the SUBMIT command
    RFSLIB.SUBMIT.JCLID   - Provides addition control over including the "// ID" within the submitted JCL.
    RFSLIB.SUBMIT.JOBCARD - Provides additional control over the use of a custom JECL JOBCARD within the submitted JCL.

Optionally you can also add the following RESCLASS(FACILITY) entries to your ESM if you would like to enable the ESM interfaces for the the additional features and functions include with the RFSLIB application for use outside the application...

    CICSREXX.LIBRC          - Controls the use of the ICCFP command              
    CICSREXX.LIBRP          - Controls the use of the ICCFC command   
    CICSREXX.PRINT          - Controls the use of the PRINT command   
    CICSREXX.SUBMIT         - Controls the use of the SUBMIT command   
    CICSREXX.SUBMIT.JCLID   - Provides addition control over including the "// ID" within the submitted JCL.   
    CICSREXX.SUBMIT.JOBCARD - Provides additional control over the use of a custom JECL JOBCARD within the submitted JCL.   

**Note:** To enable these optional ESM security checks replace the AUTHFUNC.PROC in PRD2.RFSLIB with the AUTHFUNC.Z member (i.e. rename AUTHFUNC.PROC to AUTHFUNC.PROCO and rename AUTHFUNC.Z to AUTHFUNC.PROC).  This will activate the interface to an External Security Manager to control access to the SUBMIT, PRINT, LIBRP and LIBRC PROCs.

[Return to TOC](#table-of-contents)

##### Additional ESM Tailoring for the CICSECX1 and CICSECX2 security exits

If you intend to utilize the custom CICSECX1 and CICSECX2 security exits provided with the RFSLIB application you will also need to define you REXX File System (RFS) security to your External Security Manager (ESM) as RESCLASS(FACILITY) entires.

Create your RESCLASS(FACILITY) entries as follows...

    Prefix "RFS-"
    RFS POOLID
    Replace ":" with "-"
    Replace all "\" with ">"

The "-" and ">" are used in place of the ":" and "\" characters because the VSE Basic Security Manager does not support the ":" and "\" characters.

Here's and example...

    POOL1:\USERS\KRIP
	
The above would need a RECLASS(FACILITY) profile as follows...

    RFS-POOL1->USERS>KRIP

These are limited to 39 characters so only high level directories can be secured using the External Security Exit CICSECX2.

Here's some more examples...

    RFS-POOL1->USERS>KRIP>SOURCE
    RFS-POOL1->USERS>KRIP
    RFS-POOL1->USERS>KRIP.SOURCE           <----- Example of directory name with dot in it.

**NOTE:** External security only checks for directory access, not member level access.

[Return to TOC](#table-of-contents)

## Functionality

To start the RFSLIB application you can execute the CICS transaction REXX with the inpu parmameter of 'RFSLIB'.  This can be done from a blank screen in CICS.  If accessing from the VSE Interactive User Interface (IUI) you will need to use the PF9 'Escape(m)' option to escape from the IUI to a CICS screen in mixed case mode.  If you'd like to add the RFSLIB application to your IUI selection panels please see the Appendix section titled [Adding the RFSLIB Application to the Interactive User Interface](#adding-the-rfslib-application-to-the-interactive-user-interface).

The RFSLIB main screen is a member listing and selection screen similar to that provided by IBM in the z/VSE Interactive Interface for accessing an ICCF Library.  This main screen provides a directory listing of the contents of the current CICS/REXX File System (or RFS) directory.  From this directory listing the user can select members for viewing and editing as well as several other functions.  In addition a Command Line area is available for accessing specific commands which have been provided to add functions and features to the CICS/REXX environment.

[Return to TOC](#table-of-contents)

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

[Return to TOC](#table-of-contents)

### COMMANE LINE AREA

The Command Line area allows the user to enter available commands for execution.  More information on the commands available can be obtained by entering "HELP COMMANDS" in the Command Line area.

[Return to TOC](#table-of-contents)

### MAIN DIRECTORY LIST

The main directory list area shows the list of files and directories found in the current RFS directory.  Each line in this area contains the FILENAME and optional FILETYPE of each file or directory along with the DATE and TIME the file or directory was last updated and the SIZE of the file in records.

Please note that the directory listing can be sorted in several different ways.  The current sort option and order is displayed below the column heading area. More information on how to control the sort order can be found below in the PF Key descriptions.

[Return to TOC](#table-of-contents)

### PF KEY LABEL AREA

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

[Return to TOC](#table-of-contents)

### FILENAME PREFIX AREA

The FILENAME PREFIX selection can be used to limit the displayed directory listing to only the files and directories which start with the specified PREFIX. Enter the desired PREFIX and hit PF2 to REFRESH the display.

[Return to TOC](#table-of-contents)

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

**CONFIG**

The CONFIG command can be used to directly edit the PRD2.CONFIG(RFSLIB.CONFIG) member in the CICS/REXX Full Screen Editor.  The format of the command is as follows...

    CONFIG

Please note that this command is typically restricted to the System Administrator or Security Administrator.

[Return to TOC](#table-of-contents)

## CICS/REXX Full Screen Editor

### Overview

The CICS/REXX Full Screen Editor is patterned after VM/CMS XEDIT. The editor is provided so data can be created, updated and viewed from within the CICS environment.

The CICS/REXX editor includes several prefix commands (for example: C, CC, M, MM, B, A, F, P) in common with the XEDIT editor.

While in an editor session, commands entered on the command line can be chained by placing a ";" between each command. Support is also provided for macros written in REXX. This lets you customize editor settings with a profile exec or add new commandes to the editor.

[Return to TOC](#table-of-contents)

### Screen Format

The CICS/REXX Editor screen is laid out as follows...

The first line is reserved for the TITLE LINE and contains the following:
- Full qualified RFS File ID
- Column numbers
- Displayed messages from the Editor

There are informational lines that let you know you have reached "TOP OF DATA" or "BOTTOM OF DATA".

The line which starts with "COMMAND ===>" is the Command Line where Editor commands can be entered or Editor macros can be invoked. The line immediately below the Command Line is concidered the "current line". This "current line" is highlighted and is significant for certain Editor commands.

The Data Lines area of the screen is used to display and edit your data. Each line in the Data Area contains a The Data Lines area of the screen is used to display and edit your data. Each line in the Data Area contains a "prefix area" which typically displays the line number of each data line. Prefix Commands may be entered in this "prefix area".

[Return to TOC](#table-of-contents)

### Prefix Commands

Several prefix commands are provided by the Editor. These commands are entered in the prefix area and give you the ability to copy, move, delete and replicate lines on either an individual or a consecutive block basis.

[Return to TOC](#table-of-contents)

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

[Return to TOC](#table-of-contents)

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

[Return to TOC](#table-of-contents)

### Destination Commands

The following commands are called destination commands...

    'A' - After
    'B' - Before
    'F' - Following
    'P' - Preceding

These commands are used in conjunction with the copy and move commands (both individual and consecutive block) to specify the placement of the resulting copy or move function.

[Return to TOC](#table-of-contents)

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

[Return to TOC](#table-of-contents)

## APPENDIX

### Adding the RFSLIB Application to the Interactive User Interface

It is fairly easy to integrate the RFSLIB application into the VSE Interactive User Interface (IUI).  First you will need to define an 'Application Profile' to the IUI.  This can be done by navigating the the IUI menus as follows...

    Resource Definition > User Interface Tailoring > Maintain Application Profiles
	
Note: If you've tailored your IUI selection panels these options may vary.  The specific Application Profile you would like to navigate to is named 'IESADMUIFA' (titled 'MAINTAIN APPLICATION PROFILES').

Press ENTER from the IESADMUIFA screen to view a list of all the existing Application Profiles.  Move the cursur to the first entry on the list enter a '1' (for ADD) and hit ENTER.

Enter the information seen on the screen shot below to define the RFSLIB Application Profile.

![Screenshot of RFSLIB Application Profile for IUI](https://github.com/KenRip/rfslib/raw/master/images/Screenshot%20Define%20RFSLIB%20Application%20to%20IUI.png)

Once the Application Profile is defined you can add it to a Selection Panel assigned to the users you want to have access to the RFSLIB Application.

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
	RFSPANLS.PROC     - RFSLIB PROC provided to import the PANSRC members provided in the default VSE sublibrary to an RFS directory.
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

    CICSECX1.A        - Taliored version of the CICSECX2 exit which interfaces with the ESM using the RESCLASS(FACILITY) profiles.
    CICSECX1.PHASE    - Dummy version of the CICSECX1 exit which simply set Reg15 to 0 and returns which 'allows' all requests.
	CICSECX2.A        - Taliored version of the CICSECX2 exit which interfaces with the ESM using the RESCLASS(FACILITY) profiles.
    CICSECX2.PHASE    - Dummy version of the CICSECX2 exit which simply set Reg15 to 0 and returns which 'allows' all requests.

[Return to TOC](#table-of-contents)

### The RFSTOOL.PROC REXX/VSE Batch Utility

The RFSTOOL.PROC is included with the RFSLIB application in the PRD2.RFSLIB sublibrary.  This REXX procedure is used to process CICS/Rexx RFS files and directories from a batch job.  The tool can perform the following functions from a batch job...

    DIRECTORY   - Produces a directory listing of an RFS File System directory
    RESTOREFILE - Restores a file from a backup (backup must be in a specific format in a SAM disk file - See details below)
    RESTOREDIR  - Restores a directory and all files within from a backup (backup must be in a specific format in a SAM disk file - See details below)
    MAKEDIR     - Creates a new directory in the RFS File System
    DELETEDIR   - Deletes a directory from the RFS File System
    DELETEFILE  - Deletes a file from the RFS File System

[Return to TOC](#table-of-contents)

#### RFS Backups

The backup files need to be created using the IDCAMS REPRO method.  Sample JCL for this can be seen below but would most likely need to be taiored to your specific VSE environment (i.e. for specific EXTENT information or for a 3rd party disk management solution).

    // DLBL RFSDIR1,'',,VSAM
	// DLBL DISKOUT,'RFSDIR1.DISK.BACKUP' 
    // EXTENT SYS001                      
    // EXEC   IDCAMS,SIZE=AUTO            
      REPRO  INFILE (RFSDIR1)  -      
            OUTFILE (DISKOUT   -      
            ENVIRONMENT        -      
               (RECFM(VB)      -      
                BLKSZ(25000)))      
    /*				
	// DLBL RFSPOL1,'',,VSAM
    // DLBL DISKOUT,'RFSPOL1.FILE01.DISK.BACKUP' 
    // EXTENT SYS001                      
    // EXEC   IDCAMS,SIZE=AUTO            
      REPRO  INFILE (RFSPOL1)  -      
            OUTFILE (DISKOUT   -      
            ENVIRONMENT        -      
               (RECFM(VB)      -      
                BLKSZ(25000)))      

Please note that each REXX File System (RFS) Pool is made up of a pool directory file (RFSDIR1 above) and at least one pool data file (RFSPOL1 above).  To insure that your backups have data integrity you should backup the VSAM clusters that make up the Pool together and if possible when no update activity is taking place.

Please also note that the RECFM(VB) and BLKSIZE(25000) are required for the RFSTOOL.PROC to be able to process the backup files.

If you would like to send your backup to tape you will need to restore the tape backup to SAM disk files before processing and restores with the RFSTOOL.PROC.  The RFSTOOL.PROC does not support reading the backups from tape.

[Return to TOC](#table-of-contents)

#### RFSTOOL.PROC Special Considerations

The RFSTOOL.PROC supports restoring REXX File System directories and files while the CICS region owning these files it up and the RFS files are OPEN to CICS.  It does so by utilzing the VSAM-via-CICS Service (documented in the e-Business Connectors, User Guide).  In order to enable this RFSTOOL.PROC functionality the target CICS region must have the VSAM-via-CICS Service running.  This IS enabled by default in the VSE supplied CICS ICCF region but if you plan on hosting the RFSLIB application in another CICS region you would need to enable this support in that region.

The following CICS Resource definitions are required (supplied in the VSESPG RDO Group) to do so...
                                                                 
      NAME     TYPE                                              
      ====     ====                                              
      IESXACT1 PROFILE                                           
      IESCVMIR PROGRAM                                           
      IESCVSRV PROGRAM                                           
      IESCVSTA PROGRAM                                           
      IESCVSTI PROGRAM                                           
      IESCVSTP PROGRAM                                           
      ICVA     TRANSACTION                                       
      ICVM     TRANSACTION                                       
      ICVP     TRANSACTION                                       
      ICVS     TRANSACTION                                       

You would also need to include the ICVA transaction in your CICS region startup.  Please see the e-Business Connectors, User Guide for more details.

[Return to TOC](#table-of-contents)

#### RFSTOOL.PROC JCL and Input Paramters

The JCL and Input Cards for the RFSTOOL PROC are as follows...

    // DLBL DIRIN,'RFSDIR1.REPRO.BACKUP'           
    // EXTENT ,VOL001,,,10000,250                  
    // ASSGN SYS004,xxx                            
    // DLBL POLIN1,'RFSPOL1.FILE01.REPRO.BACKUP'   
    // EXTENT ,VOL001,,,10000,250                  
    // ASSGN SYS005,xxx                            
    // DLBL POLIN2,'RFSPOL1.FILE02.REPRO.BACKUP'   
    // EXTENT ,VOL001,,,10000,250                  
    // ASSGN SYS006,xxx                            
    // DLBL POLIN3,'RFSPOL1.FILE03.REPRO.BACKUP'   
    // EXTENT ,VOL001,,,10000,250                  
    // ASSGN SYS007,xxx                            
    // DLBL POLIN4,'RFSPOL1.FILE04.REPRO.BACKUP'   
    // EXTENT ,VOL001,,,10000,250                  
    // ASSGN SYS008,xxx                            
    // EXEC REXX=RFSTOOL                           
    <INPUT CONTROL CARDS GO HERE>                  
    /*    
	
Again the specifc DLBL dataset names and EXTENT information will need to be tailored to you specific requirements.

The RFSTOOL.PROC can support up to four (4) Pool data files as part of a single RFS File System.  As the CICS/REXX support ships with VSE the two pools (POOL2 and POOL2) only include a single pool data file each.  In this case you wouldn't need the POLIN2 - POLIN4 related DLBL, EXTENT and ASSGN statements.

[Return to TOC](#table-of-contents)

##### RFSTOOL.PROC Input Parameter Control Cards

The supported input paramter control cards are as follows...

**CICSREGION**

This specifies the target CICS Region APPLID where the target RFS files are located (used during RESTORE processing).  The format of the control card is...

    CICSREGION aaaaaaaa

Where "aaaaaaaa" is the APPLID of the target CICS region.

**POOLIN**

This specifies the input SAM backup files to be used in the RFSTOOL.PROC processing.  The format of the control card is...

    POOLIN dirid poolfile1 poolfile2 poolfile3 poolfile4

"dirid" is the RFSDIR name.
"poolfile1" is the RFS Pool data file 1 file name.
"poolfile2" is the RFS Pool data file 2 file name.
"poolfile3" is the RFS Pool data file 3 file name.
"poolfile4" is the RFS Pool data file 4 file name.

Note: The name values need to be the original CICS FILE (FCT) names that the RFS files are defined to the CICS Region as.  The REXX File System utilizes these names inside the records of the VSAM clusters as the part of the linking records between the RFSDIR records and the Pool data file records.  It is important that the names here match the CICS FILE (FCT) names in the CICS region or the RFSTOOL.PROC processing would fail. 

**POOLOUT**

This specifies the output RFS File System files to be used in the RFSTOOL.PROC processing.  The format of the control card is...

    POOLOUT dirid poolfile1 poolfile2 poolfile3 poolfile4

"dirid" is the RFSDIR name.
"poolfile1" is the RFS Pool data file 1 file name.
"poolfile2" is the RFS Pool data file 2 file name.
"poolfile3" is the RFS Pool data file 3 file name.
"poolfile4" is the RFS Pool data file 4 file name.

Note: The name values need to be the CICS FILE (FCT) names that the RFS files are defined to the CICS Region as.  The REXX File System utilizes these names inside the records of the VSAM clusters as the part of the linking records between the RFSDIR records and the Pool data file records.  It is important that the names here match the CICS FILE (FCT) names in the CICS region or the RFSTOOL.PROC processing would fail. 

**REPLACE**

This specifies if the target RFS directory of file is to be replaced during RESTOREDIR or RESTOREFILE processing.  The format of the control card is...

    REPLACE YES|NO


**DIRECTORY** 

This command produces a directory listing of the specified RFS File System directory.  It can produce directory listing from the POOLIN or POOLOUT files.  The format of the control card is...

    DIRECTORY rfsdirid **SHORT**|FULL **POOLIN**|POOLOUT

'rfsdirid" is the RFS File System directory within the specified pool.  It should not include the "POOLx:" value.  It should be in the following format...

    - rfsdirid should be in the following format...               
      \dirlevel1\dirlevel2 etc.                                   
      \xxxxx\yyy*                                                 
    - If "*" is specified as a wildcard it can only be used at the end of the parameter.  i.e. \USERS\A*                       

'SHORT|FULL' produces either a breif listing (SHORT) of the directory or a detailed listing (FULL).  The default is 'SHORT'
'POOLIN|POOLOUT' indicates whether the directory listing is produced from the POOLIN SAM backup files or from the target RFS file System specified on POOLOUT.

**RESTOREFILE**

The RESTOREFILE function is used to restore files from the POOLIN SAM backup files to the target RFS File System specified on POOLOUT above.  The format of the control card is...

    RESTOREFILE rfsfileid (-)
             TO rfsfileid    

Note that the 'TO' option is not required.  If omitted the file is restored to it's original location (from the SAM backup file).  If specified it can override the target location and filename.

**RESTOREDIR** 

The RESTOREDIR function is used to restore directories and all the files contained in the directory from the POOLIN SAM backup files to the target RFS File System specified on POOLOUT above.  The format of the control card is...

    RESTOREDIR rfsdirid (-)
             TO rfsdirid    

Note that the 'TO' option is not required.  If omitted the file is restored to it's original location (from the SAM backup file).  If specified it can override the target directory location.

**MAKEDIR**    

The MAKEDIR command is used to create new directories in the RFS File System.  It only applies to the POOLOUT target RFS File System.  The format of the control card is...

    MAKEDIR rfsdirid rfsdirtype
	
'rfsdirid' should be in the following format...

    \dirlevel1\dirlevel2 etc.                    
	
'rfsdirtype' can be one of the following...    

    PRIVATE, SECURED, PUBLICR, or PUBLICW        

The RFSTOOL.PROC defaults to 'PRIVATE'.  If you've enabled RFS File System security using the CICSECX2 security exit you may need to specify 'SECURED' to create the new directory with the XSEC setting to invoke External Security Manager (ESM) checks using the CICSECX2 security exit.
 
**DELETEDIR**  

This command deletes a directory from the RFS File System.  It only applies to the POOLOUT target RFS File System.  The format of the control card is...

    DELETEDIR rfsdirid
	
'rfsdirid' should be in the following format...

    \dirlevel1\dirlevel2 etc.

**DELETEFILE** 

This command deletes a file from the RFS File System.  It only applies to the POOLOUT target RFS File System.  The format of the control card is...

    DELETEFILE rfsfileid
	
'rfsfileid' should be in the following format...

    \dirlevel1\dirlevel2\..\filename

[Return to TOC](#table-of-contents)

##### RFSTOOL.PROC Sample JCL

Here are some examples of RFSTOOL.PROC usage scenarios.

**Using RFSTOOL.PROC to list the directory from a backup file set**

The jobstep JCL would be something like the following...

    // DLBL DIRIN,'RFSDIR1.REPRO.BACKUP'           
    // EXTENT ,VOL001,,,10000,250                  
    // ASSGN SYS004,xxx                            
    // DLBL POLIN1,'RFSPOL1.FILE01.REPRO.BACKUP'   
    // EXTENT ,VOL001,,,10000,250                  
    // ASSGN SYS005,xxx                            
    // EXEC REXX=RFSTOOL                           
     POOLIN RFSDIR1 RFSPOL1
     DIRECTORY * FULL POOLIN	 
    /* 

This could be used to list the contents of a backup file set and might be something you include in your backup job after the REPRO backup step so your backup job includes a listing of the files and directories included in the backup.

**Using RFSTOOL.PROC to restore a specific file to a new filename**

The jobstep JCL would be something like the following...

    // DLBL DIRIN,'RFSDIR1.REPRO.BACKUP'           
    // EXTENT ,VOL001,,,10000,250                  
    // ASSGN SYS004,xxx                            
    // DLBL POLIN1,'RFSPOL1.FILE01.REPRO.BACKUP'   
    // EXTENT ,VOL001,,,10000,250                  
    // ASSGN SYS005,xxx                            
    // EXEC REXX=RFSTOOL  
     CICSREGION DBDCCICS	
     POOLIN RFSDIR1 RFSPOL1
	 POOLOUT RFSDIR1 RFSPOL1
     REPLACE YES
     RESTOREFILE \USERS\SYSA\TEST.EXEC -
              TO \USERS\OPER\NEW.EXEC	 
    /* 

In the above example the RFSTOOL.PROC will utilize the VSAM-via-CICS support running in CICSREGION DBDCCICS and restore file '\USERS\SYSA\TEST.EXEC' from the input backup file to the active RFS File System in the CICS region in the CICS Files RFSDIR1 and RFSPOL1 (specified on the POOLOUT statement) as filename '\USERS\OPER\NEW.EXEC'.  Note that this is a completely different location from the original backup and that because 'REPLACE YES' was specified the target filename would be replaced if it existed.

**Using RFSTOOL.PROC to create a new directory and then restore all files from a directory on the backup to the new directory**

The jobstep JCL would be something like the following...

    // DLBL DIRIN,'RFSDIR1.REPRO.BACKUP'           
    // EXTENT ,VOL001,,,10000,250                  
    // ASSGN SYS004,xxx                            
    // DLBL POLIN1,'RFSPOL1.FILE01.REPRO.BACKUP'   
    // EXTENT ,VOL001,,,10000,250                  
    // ASSGN SYS005,xxx                            
    // EXEC REXX=RFSTOOL  
     CICSREGION DBDCCICS	
     POOLIN RFSDIR1 RFSPOL1
	 POOLOUT RFSDIR2 RFSPOL2
     REPLACE YES
	 MAKEDIR \PUBLIC\SYSA PUBLICW
     RESTOREDIR \USERS\SYSA -
              TO \PUBLIC\SYSA	 
    /* 

In the above example the RFSTOOL.PROC will utilize the VSAM-via-CICS support running in CICSREGION DBDCCICS and restore all files in directory '\USERS\SYSA'1 from the input backup file to the active RFS File System in the CICS region in the CICS Files RFSDIR2 and RFSPOL2 as filename '\PUBLIC\SYSA'.  Before doign so the MAKEDIR command would have defined a new directory in the target RFS File System called '\PUBLIC\SYSA' with the PUBLICW attribute meaning every user would have read/write access to the directory.

**Using RFSTOOL.PROC to delete files and directories from the online RFS File System**

The jobstep JCL would be something like the following...

    // DLBL DIRIN,'RFSDIR1.REPRO.BACKUP'           
    // EXTENT ,VOL001,,,10000,250                  
    // ASSGN SYS004,xxx                            
    // DLBL POLIN1,'RFSPOL1.FILE01.REPRO.BACKUP'   
    // EXTENT ,VOL001,,,10000,250                  
    // ASSGN SYS005,xxx 
    // EXEC REXX=RFSTOOL  
     CICSREGION DBDCCICS	
	 POOLIN RFSDIR1 RFSPOL1
	 POOLOUT RFSDIR1 RFSPOL1
     DELETEFILE \USERS\SYSA\TEST.EXEC
	 DELETEDIR \USERS\SYSA\TESTDIR
    /* 

In the above example the RFSTOOL.PROC will utilize the VSAM-via-CICS support running in CICSREGION DBDCCICS and delete the file named '\USERS\SYSA\TEST.EXEC' and the directory named '\USERS\SYSA\TESTDIR' from the online RFS File System specified on the POOLOUT statement.  Note that the POOLIN statement is required by the current version of the RFSTOOL.PROC but in this case (where the RESTOREFILE, RESTOREDIR or DIRECTORY with POOLIN are not specified) is NOT used.  Unfortunately you must have a backup file set specified in the JCL at this time (this will be corrected in a future release of the RFSTOOL.PROC).

[Return to TOC](#table-of-contents)
