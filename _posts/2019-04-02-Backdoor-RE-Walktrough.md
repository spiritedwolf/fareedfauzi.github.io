---
title: "Backdoor CTF Practice Arena - RE Challenges Walktrough"
date: "2019-04-02"
layout: single
tags:
- CTF
- RE
categories:
- Write-Up
---


Below are the few reverse engineering category's challenges that I've been solved. It was a pretty easy challenge to solve.

# Challenges

 1. [revfun](#revfun)
 2. [debug](#debug)
 3. [bin-easy](#bin-easy)
 4. [bin-medium](#bin-medium)
 5. [hiddenflag–easy](#hiddenflag–easy)
 6. [2013-bin-500](#2013-bin-500)
 8. [2013-bin-200](#2013-bin-200)
 9. [2013-bin-100](#2013-bin-100)
 10. [2013-bin-50](#2013-bin-50)

---
&nbsp;
&nbsp;

# revfun
![enter image description here](https://lh3.googleusercontent.com/IP_vy612A3wQTzD7_Bl4PESAsD4WqOQomCOrHKCz9S8T-PRC2plYuP4s5IdutHddHqtugxxlrZnK){: .align-center}

**Step 1**:  
Run `file` command on the binary. Now we know that it’s a ELF 64 bit file.

![enter image description here](https://lh3.googleusercontent.com/esx0fJrHcJOtzAI-35R068MJv96oxOh97C1gWF9DiG3Ny_h5FMT_nIPkFkRvLC7VDdRRoI5ejgPB){: .align-center}

**Step 2**:  
Use `strings` command to display all the strings on the binary and we can see there is a suspicious string `“dlr0w_s1h7_s1_yz4rC”`. If we reverse the char string, it said `“crazy_is_this_world”`.

![enter image description here](https://lh3.googleusercontent.com/jbgh59-OXvTZ01hWVXHiwfP4S-MkOgmnygJH5xxQh3o_kLuUtK3XcA-__Zy2-2M-d3BzDnjRffBq){: .align-center}

**Step 3**:  
Try the password on the binary.

![enter image description here](https://lh3.googleusercontent.com/XZrLWvU0nKoFRc5EIg5_sroLNcq6GLE1YcbjnStAtVOttgep8RriH2_kRi8r7UF-vXsd0-MgNXRb){: .align-center}

**Step 4**:  
Try on `netcat`, and it will print our flag.

![enter image description here](https://lh3.googleusercontent.com/OJ7pyS5QCZZRoNU2A24J9oo_2t9t7zJ8PeT6KUwRMrOyHunTU68-bFqFXdGDHyiCimh63lUZuLbt){: .align-center}

---
&nbsp;
&nbsp;

# debug
![enter image description here](https://lh3.googleusercontent.com/feGh6uDvVORIj0orILBmKlIUuiog3F96XVmkjifgNwjOoszspVu91aQ7TodUSfKe46988BCX4laM){: .align-center}

**Step 1:** 
Run `file` command on the binary. Now we know that it’s a ELF 32 bit file.

![enter image description here](https://lh3.googleusercontent.com/rBHcc_uuJOK0Hxps5oTW9_lX4T8c2kEiHD2RiYCy914XhRKlTnu87ompqanW37VwjEBt2H8hICF6){: .align-center}

**Step 2:**
Try to run the program, and it’s return nothing.

![enter image description here](https://lh3.googleusercontent.com/J97ngUZh5BV4Y3NDNb0xpAm47EFyxt1EUDoXLd15c9RqL6FUEsvXRHx38QGiji7c-xY7jnFDYFXE){: .align-center}

**Step 3:**
Open the program using IDA Pro. We can see on the left side of the IDA program, there is a bunch of function names. After little bit of exploration on the functions, the functions `sub_804849B` look interesting to me.

![enter image description here](https://lh3.googleusercontent.com/PFWRKbDj2YFgJN1PzsaA5jQAG43sQ24yx-H-lYyAO44ck7svXZ5R8oNVuUJ6pe0i3SYJ8otiq83I){: .align-center}

It push a strings `Printing flag`. So, I think this must be a function that print the flag. So, I rename the function `sub_804849B` to `PrintFlag` for easy for me to remember which function does the print flag.

![enter image description here](https://lh3.googleusercontent.com/IU-hMztC6mdKuJnYQjbnjwdkXP1XE_-VbESw0M9cy0Wwy2RBEPBkLJOkr8LqGHsQufQnl5prF00i){: .align-center}

**Step 4**:  
Okay all we need now it’s to patch the program that will jump to the `PrintFlag` function. First thing we must know, every program will start executing something at `_start` function. So, go to the `_start` and we can see it will call `_libc_start_main`.

![enter image description here](https://lh3.googleusercontent.com/SqFUPWhjkthP_aftA_qmeZ6ddL7-IcmgZgzycQq2Nul0v1ZmW2vGWCb6uWYWQGKllStjSAaUfH1C){: .align-center}

Let’s patch the program by change the `_libc_start_main` function to `PrintFlag` function.

Click on `libc_start_main` function => Go to `edit` => `Patch program` => `Assemble`

![enter image description here](https://lh3.googleusercontent.com/jH4J1GtQV82WpSgZRLRXsT75HYT4gXLBkJRgowOgtkTCAgXRGJPhB9btEFywxLI2EFCaxiCRt48-){: .align-center}

Change the `libc_start_main` function to `_PrintFlag` function. Then click OK. Then close.

![enter image description here](https://lh3.googleusercontent.com/-vRkwCP1ui5YfeOKblJPG02nxorJZn4X7NC6_kHwhNTnjiCRqtiZo65iZhENbbCxuNIq4jb-DC0X){: .align-center}

**Step 5:**

![enter image description here](https://lh3.googleusercontent.com/FocbBXcgR9ToVA01wFegz6Rv3M6TNafKcjmPD1QxGBifPLwWbqSM2Z3N650qwv63kMlFk3UjR7sQ){: .align-center}

Save the patch by go to _edit_ => _Patch program_ => _Apply patches to input file._

**Step 6:**
After patched the program, just run the binary and it will print the flag.

![enter image description here](https://lh3.googleusercontent.com/xqx3qu2AgSS_xB1TbLwbT6MUkjOCsEkTdU2FQrl-WN2gmh2soAXO3p2_OGThk07XFfj9Byq9S8AK){: .align-center}

---
&nbsp;
&nbsp;

# bin-easy
![enter image description here](https://lh3.googleusercontent.com/-1B0MKDWpEm7dAscH1Bi6pTdrFzGoMTKPE4eIpN2HnAr81QXr9XcBqPEJXY62h16QAGasYiEKs6x){: .align-center}

**Step 1:**
Run `file` command on the binary.

![enter image description here](https://lh3.googleusercontent.com/wTufu72ZiPcAHiy_tCxa4JjtxYLX0Qc8ZrX0dvC6xJPIq4dmdE-Xox6gjdT6jwEUpz_u695--LOL){: .align-center}

**Step 2:**
Run `strings` command and we got the sha256 of the flag.

![enter image description here](https://lh3.googleusercontent.com/jOtZ8eQ4uFflYM2_qgvZaNxx8AAurvujE8r5MYDy2KE3Eprgb--vdMtoYFffICI-1QhtB73vceQn){: .align-center}

---
&nbsp;
&nbsp;

# bin-medium
![enter image description here](https://lh3.googleusercontent.com/fOsVDOwYfotgf2FH64eWG-r3TGd3zTmBvGkUwnrAUwzaIKKCOR0ax7qermCQxrbFvhwaWSlRAySD){: .align-center}

**Step 1**:  
Run `file` command on the binary.

![enter image description here](https://lh3.googleusercontent.com/Z5tA1TCdkgdBQ2LQyvWtdAAWqEctfoqMxV2NnCfD6xE22Fqh27a1a_P0ZdNYn_-q_5U99M6C9Yb_){: .align-center}

**Step 2:**
Try execute the program, and input random passphrase and it return a string.

![enter image description here](https://lh3.googleusercontent.com/a9Us5ngpX8i8hKCz1l2GdPH4m8bwNFpmI-LMqtkA5AiUVZcJv_7ae9DuisleSdT5HOi57W34nz-G){: .align-center}

**Step 3:**
By analyzed the binary on IDA, if the input are wrong it will jump to a function that will print the string `Thank you, but you aint getting the flag`. I renamed the function to `WrongPassword` for easy for me to remember the function name.

So, all we need now it’s patch the conditional jump `jnz WrongPassword` to `jz WrongPassword`.

![enter image description here](https://lh3.googleusercontent.com/TSHOJppnf5aXPsLOpnPSRw-F-GSSX7NX_I3XqvZlsI3r3AKqsBENxIpiG8_m_M5u59_ZWwQYqV2U){: .align-center}

Save the patched program.

**Step 4:**
Run the program and enter a random passphrase. Now, it will print the sha256 of the flag.

![enter image description here](https://lh3.googleusercontent.com/15cSfC4KcZ2lGhcBrYPtooYapeEdJBV2jX_MpogmJGiD_WvsUXl4PpNCLwHsQCYY76s5PUdNM-iO){: .align-center}

---
&nbsp;
&nbsp;

# hiddenflag–easy
![enter image description here](https://lh3.googleusercontent.com/s16oGazLsT83e7344J25VJtiiXhBXp3rFVYI7s74TwQ0GMYAhQdlJ3fKvCwCcgTVX6zqg7AsWVTF){: .align-center}

**Step 1**:  
Run `file` command on the binary first.

![enter image description here](https://lh3.googleusercontent.com/k6ugC_n92j30CiHi5wyBdvVtz4bOAjR7zL2Fxk5UZS96WAxXkAep6-RU7kYUU1sPxwIh2WAvG_7t){: .align-center}

**Step 2**:  
Execute the binary and it’s return a string.

![enter image description here](https://lh3.googleusercontent.com/nkWoDO5OG6H5XbeguSg0KKzUXway7byax9AUccaNHdOW2Z5qGG0I3zRRHsFas04Riq_xKobSFGOw){: .align-center}

**Step 3**:  
Issuing `strings` command and from there we got the sha256 of the flag.

![enter image description here](https://lh3.googleusercontent.com/VUc6Rrlq2qgMGa1tYm04eJaijsXPaOxXkCq7o6jzGOrxwKc8v_CPRD-y-uRt-S7ayq9dVW-9-9MW){: .align-center}

---
&nbsp;
&nbsp;

# hiddenflag-medium
![enter image description here](https://lh3.googleusercontent.com/_T3WkN15o5cXjkRp54xOOgaQADOuTM1mstW8yzrgbu4BmfVvrREntieRv6zzWfmD0Jeuf9bnWl2A){: .align-center}

**Step 1**:  
Issue `file` command on the binary.

![enter image description here](https://lh3.googleusercontent.com/yWqbbfbLwRzgU4288t_o_TOzVrALQw7OGw6rXhnrKutlRQCrtBiwh_mGk4IkTdoBDI27Y6lQ1KaR){: .align-center}

**Step 2**:  
Reverse engineering it using _gdb debugger_ by run `_gdb ./<filename>`.  After that, display all the functions of the binary by supply “_info functions”_ in gdb debugger. It will list all the functions.

![enter image description here](https://lh3.googleusercontent.com/W7nqoFnB2YaMsOO0dg4oVUwjaq6MpGYUun9B6Ka0f9pPErFs0S69E-ALYokV4ym3EMkI5wmJuZZF){: .align-center}

From the list of functions gdb echoed, we can see there is a function name `_print flag` . That is interesting.

**Step 3**:  
Now all we need is jump to the function `_print flag` and the program will print the flag.

So, breakpoint to the entry point function which is `_start` function. Use b_ for breakpoint and address of the function that we want to break. 
Here the command: 
`b *0x080483a0`

![enter image description here](https://lh3.googleusercontent.com/kozPUlbhFvjVfMMnVoYHEOY7guznlQsrQ2Ikbhzrx_tDWkOxbOJ1MegALGvn7Ouz5LuQlJOHxS3d){: .align-center}

**Step 4**:  
After put a breakpoint, execute the program by type `run` and enter.

![enter image description here](https://lh3.googleusercontent.com/LLHNTZMLAOeDieH5rr58OSjhwt358GPAgluYGWYgGdcayeT4lyiAu_AFMTqtpLzoW0woKRArXciY){: .align-center}

**Step 5**:  
Now it will break to the address that we’ve been break which is `0x080483a0`.Now all we need is jump to the function “print flag” and the program will print the flag. Type `jump <printflag address>` and the program will print the sha256 of the flag.

![enter image description here](https://lh3.googleusercontent.com/7HtqqjHBvuyYquwRefiVL9nKINytKaZPDiuzMQOiZVosy8O2H6cQ1g8sWcCnuQUZeZMzTyZZHps-){: .align-center}

---
&nbsp;
&nbsp;

# 2013-bin-500
![enter image description here](https://lh3.googleusercontent.com/goERDpO522MtCBbucyihmdtdZweKZ8QJl6JHG5taDcjFFd6tOCIoIdi1ghnHjQs_GJyk4N7Uq6j7){: .align-center}

**Step 1**:  
Run `file` command on the binary. The file is PE32 file.

![enter image description here](https://lh3.googleusercontent.com/O7pq6HqPcNzGKiSdo1AZxJNSH9d4s3CWE0RhLcjez8Em48qfeoGNpaxlebPfEB-URGCEyOvfnIwZ){: .align-center}

**Step 2**:  
Execute the program and it will ask for the registration key. Try some random key and it will print `Invalid key`.

![enter image description here](https://lh3.googleusercontent.com/hE1MXTrTB6JZhlH-AnWtpA8Zr44dldLvlyNb1Sz2Z33qQ5nyKZbIr1hT5zCSWJt-R0ufqlEIokkr){: .align-center}

**Step 3**:  
I try open it using IDA, but the binary seems like have been obfuscated. To detect what packer have been use, open the file using _PEiD_ software.

![enter image description here](https://lh3.googleusercontent.com/aqr9ye3cYXNio4iV9swvYb-sqc2i0Yf0zYc1VZaZ0bJC7rboZvCEqam6lTPmX3JzsqO92RREEr5d){: .align-center}

It display “Microsoft Visual Basic 5.0”.

Googling some Microsoft Visual Basic 5.0 unpacker. It said

“Your program is not packed, but rather compiled as [Visual Basic P-code](http://www.woodmann.com/crackz/Tutorials/Vbpcode.htm) or Visual Basic native code. If it’s VB native code, you can use your favorite debugger (OllyDbg, IDA, etc.) to debug it, and IDA to disassemble it. If it’s VB P-code, you can use [VB Decompiler Pro](http://www.vb-decompiler.org/) to disassemble/decompile it.”

So compiled as [Visual Basic P-code.](http://www.woodmann.com/crackz/Tutorials/Vbpcode.htm) To decompile we must use [VB Decompiler Pro](http://www.vb-decompiler.org/).

**Step 4**:  
Decompile it using _VB Decompiler Pro_ and read the codes. On line `loc_404D2E`, we can see if text = `&H289BC` it will print “_Thank you… bla bla.. “._

![enter image description here](https://lh3.googleusercontent.com/3DG5JEa5Pdp1h-KE9798WJxOrQxJXlOoj4p6eXBvwwYV_6XBLA7Px4Mc0yoSfsEgJ8F3hqblf1T9){: .align-center}

**Step 5**:  
Copy the string, paste it into the text box and click “_Submit_” and we got the flag.

![enter image description here](https://lh3.googleusercontent.com/1-khMJ2C7YxjpcaVYlxd3ZIxPTfS7_9z-jmNFK5XLUsuBozfoCgxjsSF5xtO6u6SKjGtAFpa3AqK){: .align-center}

---
&nbsp;
&nbsp;

# 2013-Bin-200

![enter image description here](https://lh3.googleusercontent.com/mrGPhmQjcg4o2dTiYMCSJoGS2sRP9XnHm78OLUZ813ywx5Q_WtiINJKaqgUivy36_gAF5_G0Xlfz){: .align-center}

**Step 1**:  
Execute the program and it will ask for the registration key. Try some random key and it will print “_Invalid key.”_

![enter image description here](https://lh3.googleusercontent.com/rkLgkPHoo24QF3AxuBvHvdKr9AgOAfXqERkBPkzKEvXD-Yel7vfX74zLrLTpFPq8Cb2QfIlBiWPk){: .align-center}

**Step 2**:  
I try open it using IDA, but the binary seems like have been obfuscated. Open the file using _PEiD_ software.
![enter image description here](https://lh3.googleusercontent.com/ANPVzlzFAJpZ4qlxTRNQijebhYGNz-DFn5kx_IMbUOvP0F3E0sbvbSS_c28t98s7_cdkH247epq6){: .align-center}

So, it was compiled as [Visual Basic P-code.](http://www.woodmann.com/crackz/Tutorials/Vbpcode.htm) To decompile we must use [VB Decompiler Pro](http://www.vb-decompiler.org/).

**Step 3**:  
Decompile it using _VB Decompiler Pro_ and read the codes. On line `loc_404197`, we can see if text = _“N3f…N3f”_ it will print “_Thank you… bla bla.. “._

![enter image description here](https://lh3.googleusercontent.com/2HGarQp_t0dZsJeh86CSRWJ3XVV5ItQVGOnxPEMWg5KATwujbwehzoY17LcvzkqJzbp8Np7awK7T){: .align-center}

**Step 5**:  
Copy the string, paste it into the text box and click “_Submit_” and we got the flag.

![enter image description here](https://lh3.googleusercontent.com/VG5jZy0imX1-dRkVFu8UTayaoPaSEmv4w6yC-X5g993wWqLwEZ6iKh8dgaZwU4jFthQxuR3EEKA2){: .align-center}

---
&nbsp;
&nbsp;

# 2013-Bin-100
![enter image description here](https://lh3.googleusercontent.com/LTrjT98nQkt6186qqhS_LIiV8pVWsqb2yFnE0XwZj3EbQyWG6Bz2OHAlDPLPyyA67_IjLIq_GA1E){: .align-center}

**Step 1**:  
Run `file` command. It’s an ELF 64-bit.
![enter image description here](https://lh3.googleusercontent.com/Gkf-5mWiprPNefZOKpyjTY0DctbOKudvDc0XFVRJSniSUaSwOqD1x4xjzyupLAGcjqBu824o-wzV){: .align-center}

**Step 2**:  
Because it’s a 64-bit binary, open it using IDA 64-bit. After some exploration into the functions, I saw a string “_Is The Password. Good job!_” being move into `esi` register. But, before it being move, the program will call a function `_Z4passi`.

![enter image description here](https://lh3.googleusercontent.com/GpaxJvMzcV6QannbbPlZkT2-Ai88dTDaw3chOwM-HIZTuQgU3PL5cj2CtdlPQ2OZC-oYAp096axd){: .align-center}

**Step 3**:  
Double click on the function `_Z4passi` and I saw a lot of `mov` instruction into the register `[rbp+var_xx]`. It’s look like a set of char that being move into the `rbp`(s).

![enter image description here](https://lh3.googleusercontent.com/El70F_Hxu-Fen5kOKnQ8PEAxeFlOLM6RwF41lm_ojHfOiJi1zxs-L5YBnIXIelfHvBh9KTiYr7RU){: .align-center}

**Step 4**:  
Click on the hexa value, press _“R”_ and it will automatically convert into ascii character. Do it to all values, and we got the secret string!

![enter image description here](https://lh3.googleusercontent.com/lKxl4ej57KfB001PGPKIm368H49FDhplfmlQNUYVBdIyAtV__Iti1O3sLHBzmXIS1flPNdl4sEvl){: .align-center}

The flag is `Aleph-One`

---
&nbsp;
&nbsp;

# 2013-Bin-50
![enter image description here](https://lh3.googleusercontent.com/hHYt_30XvcW6z4d7ot7eRsNdhlCDUCsFj48UdzDx0PX0TjD14ojQgkUfsWOgVEy5VDQ0ypdNPvny){: .align-center}

**Step 1**:  
Execute the file with some random password, and it’s return a string “_Nothing to see here.”_
![enter image description here](https://lh3.googleusercontent.com/yV3uw_iAv82q4B8iUSWju7XloyIkvl6jFqw57nKyN3bBjBib08FvMc6LkbMqLSemBftt4cUsiVQn){: .align-center}

**Step 2**:  
After some exploration into the functions, I saw a lot of mov instruction into the register`[rbp+var_xx]`. It’s look like a set of characters that being move into the `rbp`s. Click on the hexa value, press _“R”_ and it will automatically convert into ascii character. Do it to all values, and we got the password!

![enter image description here](https://lh3.googleusercontent.com/IHqhqxf8NqRZCMWH4DR4ZRiI80PPfAjQQusZfiQpOT0YI6pB-G7co9S9xT2fYyUhJc8wU5AQ3XYP){: .align-center}

**Step 3**:  
Try the password, and it will print our sha-256 flag.

![enter image description here](https://lh3.googleusercontent.com/3zj6Ki7YDhCtnBcXXLDo-Tbsg9O9FuNOTmdP8XMcY6ITC7bl2k4gP1DKeGpDIDNwUsP_OQ3-A3zQ){: .align-center}
