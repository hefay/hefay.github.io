---
layout: post
title:  "TODO Board pro děti #1"
date:   2025-12-24 21:20:00 +0200
excerpt_separator: <!--more-->
---
Doma jsme přibližně ve stejnou dobu narazili na nápad takového checklistu nebo TODO boardu pro děti a hrozně se nám zalíbil.

Myšlenka je jednoduchá. Děti, když přijdou ze školy nebo školky, vidí přímo na destičce, nástěnce, kapsáři nebo čemkoliv takovém, co je čeká a co mají hotové. Až budou mít hotové všechny povinnosti, tak si mohou jít hrát. (Podobně se to dá využít cestou do školy. Tedy aby si vzaly klíče, svačinu a podobně.)
<!--more-->

A jelikož naše děti mají rády různá světélka a tlačítka, tak jsme se rozhodli jim to udělat trochu v jejich stylu. Řekli jsme si, že úkolů bude maximálně 8. Máme tři děti, kdy každé má svou sadu úkolů. Proto by naše deska měla obsahovat tlačítka a světélka ve třech řádcích po osmi sloupcích. Na konci každého řádku by pak měla být nějaká konečná informace o tom, zda děti už mají volno, anebo musí ještě něco splnit.

Výsledný produkt by tedy mohl vypadat nějak takto:

![Koncept TODO desky pro děti](/assets/images/board.svg "Koncept")

Možná si teď říkáte, proč prostě nepoužít tablet. Důvod je jednoduchý. Nechce se nám a ani nechceme děti vázat na tablet. Zároveň věcí na baterie máme až až, a tak by to chtělo napájení ze sítě, ale nějaké bezpečné.

USB nabíječek na mobilní telefony a podobně máme doma dostatek, tak by nebylo špatné, kdyby se zařízení napájelo z USB. Pokud pomineme PD přes USB, tak tedy počítejme s 5 V a maximálním proudem kolem 1 A.

Původně jsem chtěl objednat všechny potřebné součástky na , zejména kvůli přehlednému vyhledávání a datasheetu u každé položky. Bohužel to vycházelo o hodně dráž, než jsem byl ochoten obětovat na takový malý projekt. A tak jsem objednal součástky na .

Základem objednávky byla vývojová deska ESP32, step-up měnič, pár multiplexorů, 3× zelené signální světlo, 3× červené signální světlo, 30 žlutých LED diod, tranzistorové pole a pár rezistorů různých hodnot (zejména kvůli nedostatečnému popisu LED).

Měnič je potřeba zejména kvůli signálním světlům, která vyžadují 12 V.

Dále je nutné v nějakém hobby marketu koupit překližku, ze které se vytvoří krabice, ve které tato elektronika bude žít, ale to si nechám na příští příspěvek.

[GME]: https://www.gme.cz/
[dratek]: https://dratek.cz/
