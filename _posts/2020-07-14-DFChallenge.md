---
title: "Korea Digital Forensics Challenge 2020"
date: "2020-05-12"
layout: single
tags:
- CTF
- DFIR
categories:
- Write-Up
---

Last month, I've participate in Digital Forensics Challenge hosted by Korea Institute of Information Security & Cryptology (KIISC) aims to expand knowledge of digital forensics and contribute to the field.

The challenge were quiet good and realistic as participator need to submit the report in Write-up report rather than submit in flag box.

Sample of my report:

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/dfchallenge-korea/2020-07-14-09-57-17.png)

I play this individually to test my own skill in blue team security.

I'll update this write-up from time to time as I really busy with my work at the office. Also, my report is not 100% true as the organization give the mark based on report. Feel free to correct my techniques and methodology in order to solve the challenge perfectly.

If you want the dump challenge, feel free to reach me at my email.

So let's start.

# 101 - Same or different

**Description:** 

You are reverse engineering analyst. You need installation of specific version of python to execute a given pyc file. There are two arguments for executing the pyc file as seen in a below table. The pyc file tests whether the two files meet the specific condition.

| Info | Detail |
| --- | --- |
| Filename: | checker.pyc |
| SHA256:| a0a1ecc08bb6470f4253286e50a4a40f1335916f3b1fd1aff14971a21ffc8be1 |
| Usage: | `python3 checker.pyc [file1] [file2]` |
Tip: If the following error appears, find appropriate version of python; “RuntimeError: Bad magic number”

**Questions:**

Find each SHA3-512 hash values of two files that meet the specific condition through analyzing given pyc file. But bypass method by patching binary or source code is not allowed. Report the source when using open data.
Teams must:
- Describe step-by-step processes for generating your solution.
- Specify any tools used for this problem.

## Answer

### Tool used:
1. Uncompyle6
 
### Run the program with random files as parameter
I first run the pyc file with random random files in our virtual machine. As an output, the program returns a text said “Unfortunately, it is not the correct answer.”

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/dfchallenge-korea/2020-07-14-10-08-34.png)

### Decompile the python compiled program
Then, I used a tool call Uncompyle6 by Python to decompile the program. This will ease us to understand the program in detail and clearer.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/dfchallenge-korea/2020-07-14-10-08-51.png) 

As I read and understand the code, the program will always return the answer “Unfortunately, it is not the correct answer” for a normal file that being issued as the parameter.
To bypass the answer to get “Great! I hope this problem will increase awareness and convince the industry to quickly move to safer algorithm” message, we need to find files or craft our own files to make it a hash collision.
This is common technique use by attacker to avoid hash check mostly from anti-virus blacklist hash databases.

### Research on Google about Hash Collison
I then go to Google to research about this hash collision technique and found a GitHub repository that explain and make a proof of concept on how this hash collision works.

Source: https://github.com/cr-marcstevens/sha1collisiondetection

By using the files (pdf file) provided by the author of the project, I managed to get “Great! I hope this problem will increase awareness and convince the industry to quickly move to safer algorithm” message on my screen.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/dfchallenge-korea/2020-07-14-10-09-09.png)
 

# 102 – What is windows prefetch?

**Description:** 

Answer the question by analyzing given prefetch files.

**Questions:**

1.	What is the name of the most executed program and how many times it was run? (10 points)
2.	What is the full path of the last program executed? (10 points)
3.	List all PDF files opened with Acrobat Reader. (10 points)
4.	List all files that were extracted with 7zip. (10 points)
5.	Among the files that has been opened with Powerpoint, which files are downloaded from the Internet? (20 points)
6.	Identify all volumes (Name and Serial Number) by analyzing the prefetch files. (20 points)
7.	Find the following information of the programs executed on NTFS ADS. (20 points)
- Executable name
- File Size
- Run count and run times

## Answer

### Tool used:

1. FTK Imager
2. PECMD
3. Timeline Explorer

### Open the .ad1 file with FTK Imager

We first open the ad1 file with FTK Imager. As we navigate through the files, there we see a lot of prefetch files artifacts.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/dfchallenge-korea/2020-07-14-10-19-04.png)
 
Then, we export all the prefetch files to our machine for future analysis.
 
### Parse all the prefetch files

We use a tool called PECmd by Eric Zimmerman to parse all the prefetch files to .csv files. 

```
Cmd> PECmd.exe -d C:\Work\dfchall-korea\102\Prefetch --csv "C:\Work\dfchall-korea\102"
Cmd> PECmd.exe -d C:\Work\dfchall-korea\102\Prefetch --csv "C:\Work\dfchall-korea\102" > output.txt
```

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/dfchallenge-korea/2020-07-14-10-19-34.png)
 
Now we have output of PECmd result in txt file and parsed data of prefetches in CSV format.


**What is the name of the most executed program and how many times it was run? (10 points)**

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/dfchallenge-korea/2020-07-14-10-20-50.png)

Most executed program : Chrome .exe
Execute count: 75

**What is the full path of the last program executed? (10 points)**

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/dfchallenge-korea/2020-07-14-10-26-09.png)

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/dfchallenge-korea/2020-07-14-10-26-19.png)
 
Last program executed: 
`\VOLUME{01d4de07837f46e5-86839005}\WINDOWS\SYSTEM32\WBEM\WMIADAP.EXE`


**List all PDF files opened with Acrobat Reader. (10 points)**
```
1.	\VOLUME{0000000000000000-6887004a}\MAGNET\IMAGES\LGM-V300L_ANDROIDLIVE_20200305\LGM-V300L_ANDROIDLIVE_20200305_LOGICAL_FILELIST.PDF
2.	\VOLUME{0000000000000000-6887004a}\MAGNET\IMAGES\LGM-V300L_ANDROIDLIVE_20200305\LGM-V300L_ANDROIDLIVE_20200305_LOGICAL_REPORT.PDF
3.	\VOLUME{01d5d83c2b55bd0d-222b643b}\MAGNET\SM-G960N\LGM-V300L_ANDROIDLIVE_20200305\LGM-V300L_ANDROIDLIVE_20200305_LOGICAL_FILELIST.PDF
4.	\VOLUME{0000000000000000-50c44ca2}\SCAN_00\PC CHECKLIST.PDF
5.	\VOLUME{01d4de07837f46e5-86839005}\USERS\PERSONA\DOWNLOADS\CLOUDSNOOPER_REPORT.PDF
6.	\VOLUME{01d4de07837f46e5-86839005}\USERS\PERSONA\DOWNLOADS\[F-INSIGHT]ACTIVE DIRECTORY AND SECURITY_FIOS.PDF
7.	\VOLUME{01d4de07837f46e5-86839005}\USERS\PERSONA\DOWNLOADS\MF_APFS_WP_V2.PDF
8.	\VOLUME{01d4de07837f46e5-86839005}\USERS\PERSONA\DOWNLOADS\THREAT-ANALYSIS-REPORT-CORONA-VIRUS-AS-A-MALWARE.PDF
9.	\VOLUME{0000000000000000-66373530}\PDF\FLAP - AN END-TO-END EVENT LOG ANALYSIS PLATFORM FOR SYSTEM MANAGEMENT.PDF
```
 
**List all files that were extracted with 7zip. (10 points)**
```
1.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.7-ZIPHISTORY.DLL
2.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.APPCOMPATCACHE.DLL
3.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.APPCOMPATFLAGS.DLL
4.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.ARES.DLL
5.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.BAMDAM.DLL
6.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.BLUETOOTHSERVICESBTHPORT.DLL
7.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.CIDSIZEMRU.DLL
8.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.DHCPNETWORKHINT.DLL
9.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.FILEEXTS.DLL
10.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.FIRSTFOLDER.DLL
11.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.KNOWNNETWORKS.DLL
12.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.LASTVISITEDMRU.DLL
13.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.LASTVISITEDPIDLMRU.DLL
14.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.OFFICEMRU.DLL
15.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.OPENSAVEMRU.DLL
16.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.OPENSAVEPIDLMRU.DLL
17.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.RECENTAPPS.DLL
18.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.RECENTDOCS.DLL
19.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.RUNMRU.DLL
20.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.SAM.DLL
21.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.SERVICES.DLL
22.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.SYSCACHEOBJECTTABLE.DLL
23.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.TASKFLOWSHELLACTIVITIES.DLL
24.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.TERMINALSERVERCLIENT.DLL
25.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.TIMEZONEINFORMATION.DLL
26.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.TYPEDURLS.DLL
27.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.USERASSIST.DLL
28.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\PLUGINS\REGISTRYPLUGIN.WORDWHEELQUERY.DLL
29.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\RECMD.EXE
30.	VOLUME{01d5d83c2b55bd0d-222b643b}\#TOOLS\REGISTRYEXPLORER_RECMD\REGISTRYEXPLORER\REGISTRYEXPLORER.EXE
31.	\VOLUME{0000000000000000-6887004a}\MAGNET\IMAGES\LGM-V300L_ANDROIDLIVE_20200305\LGM-V300L_ANDROIDLIVE_20200305.MDF
32.	 \VOLUME{0000000000000000-6887004a}\MAGNET\IMAGES\LGM-V300L_ANDROIDLIVE_20200305\LGM-V300L_ANDROIDLIVE_20200305.MDF.INFO
33.	 \VOLUME{0000000000000000-6887004a}\MAGNET\IMAGES\LGM-V300L_ANDROIDLIVE_20200305\LGM-V300L_ANDROIDLIVE_20200305_LOGICAL_APPLIST.XLSX
34.	 \VOLUME{0000000000000000-6887004a}\MAGNET\IMAGES\LGM-V300L_ANDROIDLIVE_20200305\LGM-V300L_ANDROIDLIVE_20200305_LOGICAL_FILELIST.PDF
35.	 \VOLUME{0000000000000000-6887004a}\MAGNET\IMAGES\LGM-V300L_ANDROIDLIVE_20200305\LGM-V300L_ANDROIDLIVE_20200305_LOGICAL_FILELIST.XLSX
36.	 \VOLUME{0000000000000000-6887004a}\MAGNET\IMAGES\LGM-V300L_ANDROIDLIVE_20200305\LGM-V300L_ANDROIDLIVE_20200305_LOGICAL_LOG.TXT
37.	 \VOLUME{0000000000000000-6887004a}\MAGNET\IMAGES\LGM-V300L_ANDROIDLIVE_20200305\LGM-V300L_ANDROIDLIVE_20200305_LOGICAL_REPORT.PDF
38.	 \VOLUME{0000000000000000-6887004a}\MAGNET\IMAGES\LGM-V300L_ANDROIDLIVE_20200305\_LOG.TXT
```
 

**Among the files that has been opened with Powerpoint, which files are downloaded from the Internet? (20 points)**
```
1.	6957D8EA.PPTX
2.	88BD3FE3.PPTX
3.	963BB088.PPTX
4.	A6A626E9.PPTX
5.	657830C5.PPTX
6.	CDC5F802.PPTX
7.	9531AC2B.PPTX
8.	3BD4B255.PPTX
9.	3327EC52.PPTX
10.	[FIOS] FILELESS MALWARE FORENSICS.PPTX
11.	AD121B08.PPTX
```


**Identify all volumes (Name and Serial Number) by analyzing the prefetch files. (20 points)**
```
1.	VOLUME{0000000000000000-6887004a} Serial: 6887004A 
2.	\VOLUME{01d4de07837f46e5-86839005} Serial: 86839005 
3.	\VOLUME{01d5d83c2b55bd0d-222b643b} Serial: 222B643B 
4.	\VOLUME{0000000000000000-50c44ca2} Serial: 50C44CA2
5.	\VOLUME{0000000000000000-66373530} Serial: 66373530
6.	\VOLUME{0000000000000000-12c92d0e} Serial: 12C92D0E
7.	\VOLUME{0000000000000000-62b2eb50} Serial: 62B2EB50
8.	\VOLUME{01d51863771c177a-40776820} Serial: 40776820
9.	\VOLUME{0000000000000000-7c4fa43b} Serial: 7C4FA43B	
10.	\VOLUME{0000000000000000-12c92d0e} Serial: 12C92D0E
```
 
**Find the following information of the programs executed on NTFS ADS. (20 points)**
- Executable name: `System32:svchost.exe`
- File Size (bytes): `39,538`
- Run count and run times: `3`
 
![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/dfchallenge-korea/2020-07-14-10-28-18.png) 

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/dfchallenge-korea/2020-07-14-10-28-27.png)

# 104 – Event Log analysis

**Description:** Analyze the given Event Logs and answer the question.

**Questions:**
1.	Why did the user fail to Windows backup? (15 points)
2.	Find all connected wireless AP SSIDs. (15 points)
3.	Find the manufacturer, model and serial number of the external storage device(s) that the user connected. (20 points)
4.	Write the list of ZIP files detected by AV on the user’s PC. (20 points)
5.	When did the user perform the VPN connection and disconnection? (yyyy-mm-dd hh:mm UTC + 0) (30 points)

## Answer

### Tool used:
1. Windows event viewer
2. ExtxCmd

We first parse out all the event log with ExtxCmd using a bash script

```
#!/bin/bash

for f in Logs/*.evtx
do
	./EvtxECmd.exe -f $f --csv out/
done
```

During parsing the events, EvtxCmd output on the terminal were quiet useful and readable than it's csv output. So, I redirect every output of the screen to a text file so that I can review the output efficiently without scrolling up and down in my terminal

```
script.sh > evtxecmd-output.txt
```

**Why did the user fail to Windows backup? (15 points)**

Open Application event log, we will see error indicating of Windows backup.

Dive in into that specific event present us with a Korean text with its Windows error code. By using Google, we search about the error code “0x800700E1” and translate the Korean text to English.

We can know that the Windows fail to backup due to the machine contains virus or other unwanted software

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/dfchallenge-korea/2020-07-14-10-37-30.png)

**Find all connected wireless AP SSIDs. (15 points)**

Connected wireless AP SSIDs can be found in `Microsoft-Windows-WLAN-AutoConfig%4Operational.evtx`.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/dfchallenge-korea/2020-07-14-10-38-19.png)

SSID:
```
1.	BBBB4444
2.	Alpinelab 
3.	DIRECT-w3D9-HPuIYI
4.	KT_GiGA_2G_Wave2_1A36
```


**Find the manufacturer, model and serial number of the external storage device(s) that the user connected. (20 points)**

By analyze and look up in `Microsoft-Windows-Partition%4Diagnostic`, we can find a few external storages that has been connected to the user.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/dfchallenge-korea/2020-07-14-10-39-02.png)

| Manufacturer |	Model |	Serial Number |
| --- | --- | --- |
| Generic	| STORAGE DEVICE | NULL | 
| SanDisk	Cruzer|  Glide | 4C530103461121103482 | 
| Seagate	Backup+ |  BK | NA5BF9H5 | 
| JMICRON	| JMS583 | 	DD564198838D7 | 
| Tammuz S | SD 1TB R062 | DD564198838D7 | 
| JetFlash| Transcend 16GB | AA00000000000489 | 
| NULL| SAMSUNG MZVLW1T0HMLH-000H1 | 0025_38BC_61B2_F28C | 
 
**Write the list of ZIP files detected by AV on the user’s PC. (20 points)**

AV logs can be found in `Microsoft-Windows-Windows Defender%4Operational` where we first parse it is using Eric Zimmerman tool called “EvtxECmd”.

`EvtxECmd.exe -f "C:\Work\dfchall-korea\104\Logs\Microsoft-Windows-Windows Defender%4Operational.evtx" --csv windefenderoutput`

We then open the csv file using excel and start search for “.zip”	 keyword.

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/dfchallenge-korea/2020-07-14-10-46-30.png)

```
1.	Brutus.zip
2.	Havij 1.17 Pro + Crack.zip
3.	php-webshells-master.zip
```

**When did the user perform the VPN connection and disconnection? (yyyy-mm-dd hh:mm UTC + 0) (30 points)**

User did the VPN Connection on `2020-04-25 15:50:24.686`

![](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/dfchallenge-korea/2020-07-14-10-47-20.png)


# Todo

1. 103
2. 201
3. 202
4. 203
5. 204
6. 301
7. 302
8. 303
9. 304
10. 401
11. 402
12. 403
13. 404
14. 501