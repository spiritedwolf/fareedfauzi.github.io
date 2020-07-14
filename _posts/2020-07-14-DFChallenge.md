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

**Answer:**

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
 

