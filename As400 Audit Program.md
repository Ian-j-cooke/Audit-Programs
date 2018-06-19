# AS/400 (also known as IBM i, Power System and iSeries) Audit Program #
### **Objectives: To review the security settings on the AS/400 system to ensure that the confidentiality, integrity and availability of the application(s) it supports are maintained.** ###

**Author: 	Ian Cooke
Twitter:	@COOKEI
Date:		18/06/18**

_____________________________________________

**Risk 1: The system is not configured securely or according to best practice.**

**Control 1: Ensure that the system values are configured security or according to best practice.**

**Test 1**

<pre>

Output the system values using the following commands:
Security settings - DSPSYSVAL *SEC OUTPUT(*PRINT)
Password settings - DSPSYSVAL SYSVAL(QPWD*)
Auditing settings - DSPSYSVAL SYSVAL(QAUD*) OUTPUT(*PRINT)

For each of the above copy spool file to an output file (CPYSPLF command)
Compare the output values to the values as recommend in the “Notes to Auditor” section.

Also run
PRTSYSSECA
The Print System Security Attributes (PRTSYSSECA) command prints a report of security related system values and network attributes to a spooled file. The report includes the system value or network attribute name, the current value, and the recommended value.

Notes to Auditor: System Value / Recommended Value / Explanation

QSECURITY / 40 or 50 / Controls the level of operating system integrity. Forty is the absolute minimum acceptable level.
Thirty has numerous well-known exploits. Twenty and 10 indicate that every user has root-level privileges.

QALWOBJRST / *NONE / Controls the kinds of programs that can be restored to the system.  While the *ALWPGMADP and
ALWPTF values may be acceptable from time to time, depending on specific circumstances, the default value should be the more stringent *NONE.

QALWUSRDMN / Shall not contain the values *ALL or *DIR / Certain sensitive objects can facilitate breaches if they are allowed in all libraries on a system. If these objects are required on a system, the applications and libraries that require these objects should be known and documented here.

QAUTOCFG / 0 / Controls the automatic configuration of new physical devices as soon as they are connected to the system.  This value should be turned off (0) until there is a specific need to use it, and turned off after use.

QAUTORMT / 0 / Controls the automatic configuration of remote device controllers as soon as they are connected to the system.  This value should be turned off (0) until there is a specific need to use it, and turned off after use.

QAUTOVRT / 0 / Controls the automatic configuration of new virtual devices as soon as they are connected to the system.  This value should be turned off (0) until there is a specific need to use it, and turned off after use.

QCRTAUT / *EXCLUDE / Controls what access the general public (*PUBLIC) should automatically receive to newly created objects (files and programs); ships as *CHANGE

QCRTOBJAUD / *ALL / Controls default auditing levels for all users and objects; should be set to the widest setting possible

QDSCJOBITV / No more than 240 /After an inactive Telnet session times out, determines how long (in minutes) to wait before the job is ended. A longer time frame is tolerable if the QINACTITV and QINACTIVMSGQ values are set properly.

QDSPSGNINF / 1 / Requests that information about the last successful and unsuccessful sign-on attempts be displayed to the user as he/she signs on

QFRCCVNRST / Set to 3-7 / Forces program conversion on restore. Set to at least three.

QINACTITV / No more than 30 / The number of minutes before an inactive Telnet session times out

QINACTMSGQ / *DSCJOB / After a Telnet session has timed out, identifies what action should be taken. This setting instructs the system to disconnect the job but leave it active in suspended animation for the amount of time described in system value QDSCJOBITV. If the same user signs onto the same device within the QDSCJOBITV value, the job resumes where it left off.

QMAXSGNACN / 2 / After (QMAXSIGN) number of invalid sign-on attempts, identifies what action should be taken. Two indicates that the user ID should be disabled. A setting of three (disable user and device) is both counterproductive and ineffective in a TCP/IP environment.

QMAXSIGN / No more than 5 /Maximum number of invalid sign-on attempts before a user is subjected to the action described in system value QMAXSGNACN

QRMTIPL / 0 / Level one allows the system to be IPL'd (booted) remotely via a modem. Should be set to zero unless a specific contrary reason exists.

QRMTSIGN / *VERIFY / When a known user attempts to log in from a remote computer, allows login after verification has occurred

QUSEADPAUT / A named authority list / Names an authority list that names the system users who are allowed to create a program that adopts another user's authority. This list of users should be small and well managed.

QVFYOBJRST / 3 or 5 /Verifies object signatures when objects are restored to the system

QAUDCTL / *AUDLVL, *OBJAUD, *NOQTEMP / Specifies what type of auditing is allowed on the system. Value *NOQTEMP is permitted but not required.

QAUDENDACN / *NOTIFY  Specifies the action to take if journal entries cannot be recorded.  Allowable values are "Notify" and "Power Down System Immediately," which may be too harsh for most environments.

QAUDFRCLVL / *SYS / Controls the buffering ration of records written to the security auditing journal. *SYS (system regulated buffering) is sufficient.

QAUDLVL / *AUTFAIL, *DELETE, *OBJMGT, *SYSMGT, *SAVRST, *SECURITY, *SERVICE, *PGMFAIL / Specifies what types of security events should be audited. This recommended group represents a minimum standard for best practices. More settings will require more data storage, but will also provide a fuller picture of system activity.

QAUDLVL2 / Use QAUDLVL value to set system auditing / An extension of the QAUDLVL system value; could contain some additional values

QPWDEXPITV / 90 / Number of days before a password expires

QPWDLMTAJC / 1 / Limits adjacent digits in password. Level 1 limits to a single digit.

QPWDLMTCHR / *NONE / Prevents the listed characters in a password. Characters listed here would not be valid for use in a password.

QPWDLMTREP / 2 /  Limits repeating characters in a password. Level 2 allows repeated characters, but they cannot be consecutive.

QPWDLVL / 3 / Supports the more complex 128-byte upper-/lowercase pass phrases rather than the shorter, 10-character, uppercase passwords

QPWDMAXLEN / 128 /  Maximum length of password

QPWDMINLEN / 6 / Minimum length of password

QPWDPOSDIF / 0 / Limits password character positions. If "1," the same character cannot be in the relative position in a new password.

QPWDRQDDGT / 1 / Requires a digit in the password

QPWDRQDDIF / A number less than or equal to 5 Number of new passwords that must be used before a previous password can be recycled. /
The non-intuitive values are:
0=Any password can be used
1=Cannot be the same as last 32
2=Cannot be the same as last 24
3=Cannot be the same as last 18
4=Cannot be the same as last 12
5=Cannot be the same as last 10
6=Cannot be the same as last 8
7=Cannot be the same as last 6
8=Cannot be the same as last 4

QPWDVLDPGM /  *NONE, *REGFAC or a program name / A system exit program that sees and controls password changes. A program may be registered that prevents the creation of weak passwords or dictionary words. Any program registered here should be treated as very sensitive due to its ability to see and disclose
 passwords.

</pre>
_____________________________________________

**Risk 2: Operating systems users have access to data because they have access to tools such as File Transfer Protocol (FTP) and Open Database Connectivity (ODBC) via client access.**

**Control 1: Access to such tools such be restricted and granted on an as needed basis only.  Exit points are created to audit the such of such tools.**

**Test 1**

<pre>
Question if any of the tools in the “notes to auditor” are in use. See if exit points are defined for these

WRKREGINF *ALL *PRINT
Copy spoof file to an output file.

The Work with Registration Information Command (WRKREGINF) shows information about exit points and exit programs. Information about a single exit point, multiple exit points and the exit programs associated with the exit points are displayed.
Answer List: Unsatisfactory (0); Satisfactory except for (4); Satisfactory (7); Good (10) 


Notes to Auditor:
Exit Point	Server Description
*DDM		Alternate ODBC server
*DQSRV		Client data queue server
*FILESRV	Remote file server-used when drives are mapped to integrated file system
*FTPCLIENT	FTP client on the iSeries-used for requests originating from the System i server
*FTPSERVER	FTP server on the iSeries
*NDB		ODBC and JDBC native database
*RMTSRV		Remote command server
*RTVOBJINF	ODBC and JDBC retrieve object info
*SQL		ODBC and JDBC sign-on (logon)
*SQLSRV 1	ODBC and JDBC server
*SQLSRV 2	ODBC and JDBC server
*TELNET		TCP/IP terminal emulation
*DATAQSRV	Remote data queue server
*FTPREXEC	Remote command through FTP
*REXEC_SO	Remote command sign-on (logon)
*TFRFCL		Client file transfer server
<pre>
_____________________________________________

**Risk 3: The authority on systems programs and files within the operating system are not securely configured.**

**Control 1: Authority to programs and files should be to authorised users only and on an as needed basis**

**Test 1:**

For the application(s) under review, request an appropriately authorised user, to:
 
DSPLIBL *PRINT 
Copy spoof file to an output file (CPYSPLF command) 

For the each of the libraries from above 
DSPOBJAUT OBJ(library name) OBJTYPE(*LIB) OUTPUT(*OUTFILE) OUTFILE(outfile name) 

For the main program library from above
DSPOBJD OBJ(library name/*ALL)  OBJTYPE(*PGM) OUTPUT(*OUTFILE) OUTFILE(outfile name) 

For the main file (data) library from above 
DSPOBJD OBJ(library name/*ALL)  OBJTYPE(*FILE) OUTPUT(*OUTFILE) OUTFILE(outfile name)

For 5 main or “important” programs (e.g. Maintenance, transactions etc.) from DSPOBJD above 
DSPOBJAUT OBJ(library name/program name) OBJTYPE(*PGM) OUTPUT(*OUTFILE) OUTFILE(outfile name n) Where n is a number from 1 to 5

For 5 main or “important” files (e.g. Master, transactions etc) from above
DSPOBJAUT OBJ(library name/program name) OBJTYPE(*FILE) OUTPUT(*OUTFILE) OUTFILE(outfile name n) Where n is a number from 1 to 5  

Review the selected objects to ensure that the authority is appropriate.  The authority should be reviewed in conjunction with the user authority (see the risk below)
Answer List: Unsatisfactory (0); Satisfactory except for (4); Satisfactory (7); Good (10) 

_____________________________________________


Risk 4: User authority is not correctly set allowing user access to programs or files to which they should not be entitled.

Control 1: User authority should be set under the principle of least privilege

Test 1:

Output all users to an output file 
DSPUSRPRF(*ALL)  TYPE(*ALL)  OUTPUT(*OUTFILE)  OUTFILE(outfile) 
Check the authority in conjunction with the object authority in risk 3 above
Check for special authorities (see notes to auditor).  If special authority has been granted what is the purpose?  Has it been authorised?

Ensure all shipped (default) passwords have been changed
ANZDFTPWD ACTION(*NONE)
Copy spoof file to an output file (CPYSPLF command).  Also copy file QASECPWD in library QUSRSYS

Display all authorised groups
DSPAUTUSR SEQ(*GRPPRF) OUTPUT(*PRINT)
Copy spoof file to an output file (CPYSPLF command). 
Review the most recent password change date

Display all authorised users
DSPAUTUSR SEQ(*USRPRF) OUTPUT(*PRINT)
Copy spoof file to an output file (CPYSPLF command).
Review the most recent password change date

Notes to Auditor:
Special Authority Name	Special Authority Description
*ALLOBJ			Root- or administrator-level access (very powerful)
*SECADM			Security administrator (can create new user profiles)
*IOSYSCFG		Network services configuration
*AUDIT			Configuration of audit and logging settings
*SPLCTL			Full access to reports and printer spool files
*SERVICE		Hardware administration
*JOBCTL			System operator controls
*SAVSYS			Backup and restore operations 

