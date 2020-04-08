---
title: "AV comparison on fileless attack"
date: "2020-04-08"
layout: single
tags:
- Malware
categories:
- Blog-post
---

I've test some fileless command in my lab with AV Protection and here's the result.

| Fileless technique | Bitdefender | Avast | Symantec | Webroot | ESET | Karspersky | Sophos | TrendMicro | WinDef | Malwarebytes |
| --- | ---| ---| ---| ---| ---| ---| ---| ---| ---| ---|
| koadic `mshta ` | ✔️| ❌|❌ | ❌| ✔️ |❌ | ❌| ❌| ✔️ |❌ |
| koadic `regsvr ` | ✔️|❌ |❌ | ❌|✔️ |✔️ |✔️ |✔️ | ✔️| ❌|
| koadic `rundll32_js ` | ✔️|❌ |✔️ | ❌|✔️ | ✔️|❌ |✔️ | ✔️| ❌|
| koadic `bitsadmin ` | ❌|✔️ |❌ |❌ |✔️ |❌ |✔️ |✔️ |✔️ |❌ |
|koadic `disk  `|✔️ | ❌| ❌|❌ |✔️ |❌ |✔️ |✔️ |✔️ |❌ |
| koadic `wmic ` | ✔️|❌ |✔️ |❌ |❌ |✔️ |✔️ | ✔️| ✔️|❌ |
| Malicious Powershell line  |✔️ |✔️ |✔️ |❌ | ❌| ✔️|❌ |✔️ | ❌| ❌|
| msvenom `msiexec ` |✔️ |✔️ | ✔️|✔️ |✔️ |✔️ | ✔️| ✔️|✔️ |✔️ |
| msvenom `certutil `| ✔️| ✔️| ✔️|❌ | ✔️| ✔️|✔️ |✔️ |✔️ |✔️ |
| msvenom `batch ` | ✔️| ✔️| ✔️|❌ | ❌|✔️ |❌ |❌  | ✔️|❌ |
| msvenom `cscript `(vbs)  |✔️ |✔️ |✔️ |❌ |✔️ |✔️ |✔️ |✔️ |✔️ | ✔️|
| Powercat | ✔️|✔️ | ✔️|❌ |❌ | ❌|❌ |❌  |✔️ |❌ |
