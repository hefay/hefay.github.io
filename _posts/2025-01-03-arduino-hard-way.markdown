---
layout: post
title:  "Arduino (ATMega) the hard way"
date:   2025-01-03 12:07:00 +0200
categories: "bastl"
tags: ["bast", "avr", "programování"]
---
Včera večer jsem svému synovi chtěl ukázat jak jednoduché je nahrát jednoduchý program do _ATtiny13A_ a během toho jsem zjistil, že jsem úplně zapomněl jak nastavit LSP a clang tak, aby to dávalo pro takový projektík smysl.

Abych to příště nemusel opět zdlouhavě hledat, řekl jsem si, že si to pro jistotu odložím sem. 

Základ komunikace s AVR programátorem, který používám a nastavením clangu je pěkně shrnut v čĺánku [Programming an Arduino the Hard Way][1]. Raději jsem přiložil verzi z archive.org.

Co se týče mé LSP konfigurace v neovimu, tak používám Mason a lspconfig a clang tedy vypadá jen jako
```
require 'lspconfig'.clangd.setup{}
```


[1]: https://web.archive.org/web/20231001155615/https://www.gridbugs.org/programming-an-arduino-the-hard-way/


