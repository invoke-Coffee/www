---
layout: post
title:  "Running PowerShell Scripts"
date:   2015-05-22 00:00:00 +0000
categories: Powershell
---

Running PowerShell scripts is significantly different from batch files and normal exe files.
## Starting PowerShell
Search for PowerShell in the start menu, then right click and run as admin.
Enter your username and password (or user your Piv Certificate) like normal.

You will then have PowerShell running as admin.
## Basic navigation

You can navigate around the file system and find the script you want to run with 2 commands

### cd
```cd c:\temp```
would change the current directory to c:\temp

UNC paths are also supported
``` cd ‘\\MyServer\MyShare’ ```
will also work.

One thing to note is that if the path has spaces or special characters PowerShell may try to interpret the path instead or just using it. You can enclose the path in single quotes ( ‘ )  to ensure that PowerShell only uses it as a path.

### ls
ls will list the files and folders in the current working directory
``` Powershell
PS C:\temp> ls
    Directory: C:\temp
Mode                LastWriteTime     Length Name                                               
—-                ————-     —— —-                                               
-a—         1/21/2015  12:35 PM     317314 53440-042-0024.pdf                                 
-a—         1/21/2015  12:34 PM     519521 53440-112-0045.pdf                                 
-a—         1/21/2015  12:57 PM      12375 CertReport.csv                                     
-a—         1/21/2015   2:37 PM      49965 OpenPowershell.jpg                                 
-a—         1/21/2015  12:41 PM      61343 test.sv                                            
-a—         1/21/2015  10:29 AM        929 test.txt                                           
```



## Setting your execution policy

By default  PowerShell will not allow you to run scripts. so you need to set your execution policy to allow for this.
``` Powershell
Run “Set-ExecutionPolicy unrestricted ” then Y to accept the change.
PS C:\> Set-ExecutionPolicy unrestricted
Execution Policy Change
The execution policy helps protect you from scripts that you do not trust. Changing the execution policy might expose
you to the security risks described in the about_Execution_Policies help topic. Do you want to change the execution
policy?
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is “Y”): y
PS C:\>
```




### Running a PowerShell script

once you have to location of your script and have changed your working directory (cd) to the location you can run it by.
``` PS C:\temp> .\Myscript.ps1 ```
And any augments or switches you may want to use
``` PS C:\temp> .\Myscript.ps1 -Myswitch -MyAugument “yeah” ```


## Further information

Microsoft has a lot of great documentation for PowerShell

Specificly the [Scripting guy! blog](http://blogs.technet.com/b/heyscriptingguy/archive/tags/scripting+guy_2100_/default.aspx)

if you want a general into to PowerShell I would recommend this tutorial

[PowerShell Pro Tutorials](http://www.powershellpro.com/powershell-tutorial-introduction/)
