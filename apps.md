---
layout: page
title: Aplikace
permalink: /apps/
---

## Aplikace

### Janibappka
Tato aplikace je určena pro uzavřenou komunitu uživatelů a slouží jako digitální podpora pro terénní aktivity, hry nebo vzdělávací akce. Je navržena pro použití v kombinaci s papírovým seznamem bodů, které představují konkrétní místa v terénu.

Každý bod na seznamu odpovídá určité lokaci, kam se musí uživatel osobně dostavit. Po příchodu na dané místo uživatel v aplikaci zobrazí příslušný bod a je vyzván k odpovědi na otázku nebo ke splnění jednoduchého úkolu. Úkoly mohou mít podobu znalostních otázek, logických výzev nebo krátkých aktivit navázaných na dané místo.

Po úspěšném splnění úkolu je bod v aplikaci označen jako získaný. Uživatel tak má přehled o tom, která místa již navštívil a které body má ještě před sebou. Aplikace podporuje postupné plnění úkolů a motivuje uživatele k aktivnímu pohybu v terénu.

Aplikace není určena pro veřejné použití ani otevřenou registraci. Je určena výhradně pro předem definované skupiny uživatelů, například pro účely her, teambuildingových aktivit, vzdělávacích programů nebo komunitních akcí.

[![Google Play - Janibappka](/assets/images/GetItOnGooglePlay_Badge_Web_color_Czech.png){: width="250" }](https://play.google.com/store/apps/details?id=eu.voriskovi.android.app.janibapka&pcampaignid=web_share)


<br>
# Jezich²
Dostupné na: [jezich.voriskovi.eu](http://jezich.voriskovi.eu)

Jezich² je specializovaná webová aplikace určená k centralizované koordinaci, evidenci a správě dárkových přání v rámci rodinných a sociálních skupin. Cílem systému je eliminovat duplicitu při nákupech a zjednodušit logistiku spojenou s organizací svátků, narozenin a jiných společenských událostí.

Aplikace aktuálně běží ve zkušebním provozu s plně responzivním rozhraním, podporou více jazyků a pokročilými administrativními nástroji pro správu uživatelských účtů.

### Detailní přehled funkcí

* **Správa a koordinace skupin (Group Management)**
  Aplikace umožňuje zakládání tematických či časově ohraničených skupin (např. *Vánoce 2026*). Přístup do skupin je řízen pomocí bezpečně generovaných unikátních URL odkazů s definovanou dobou platnosti, které lze doplňkově distribuovat i formou QR kódů. Součástí rozhraní je schvalovací proces ( workflow pro čekající žádosti), kde administrátor skupiny potvrzuje vstup nových členů.

* **Strukturované seznamy přání (Wishlists)**
  Uživatelé mají k dispozici osobní profily pro evidenci požadovaných položek. Každý záznam přání podporuje vložení metadat: přesný název, orientační cena, volitelný textový komentář a přímý hypertextový odkaz na konkrétní e-shop. Systém validuje a zkracuje externí odkazy pro zachování přehlednosti rozhraní. Výpis přání lze strukturovat podle interních kategorií a priorit.

* **Agregovaný nákupní seznam s řízením stavu**
  Pro nákupčího generuje aplikace konsolidovaný přehled dárků, které se zavázal pořídit pro ostatní členy skupin. Tento seznam funguje jako dynamický to-do list. Položky jsou provázány s databází skupinových přání – aktivací akce „Koupeno“ nebo „Odebrat“ dojde k okamžité aktualizaci stavu u daného dárku, což zamezuje vícenásobnému nákupu stejné položky různými lidmi, aniž by se narušilo překvapení pro obdarovaného.

* **Správa virtuálních uživatelů (Zastoupení dětí a seniorů)**
  Systém řeší problematiku členů rodiny, kteří nemají vlastní digitální identitu, přístup k technologiím nebo dostatečnou technologickou gramotnost. Uživatel s rolí správce může vytvářet podřízené „virtuální uživatele“. 
  * **Modul pro správu dětí:** Umožňuje rodičům plně spravovat přání nezletilých dětí, zadávat jejich požadavky do systému a sledovat rezervace ostatních příbuzných.
  * **Modul pro správu seniorů:** Umožňuje asistovanou správu pro starší členy rodiny, kteří neovládají webové aplikace, ale mají specifická přání, jež je třeba v rodinném kruhu koordinovat.

* **Mechanismus impersonace (Přepínání uživatelských kontextů)**
  Pro efektivní správu virtuálních uživatelů a pokročilou administraci disponuje aplikace funkcí impersonace („Vydat se za uživatele“). Oprávněný uživatel (např. systémový administrátor nebo rodinný správce) může jedním kliknutím přepnout celé aplikační rozhraní do kontextu zvoleného virtuálního či reálného účtu (např. účet dítěte nebo seniora). V tomto režimu dochází k plné emulaci práv daného uživatele, což umožňuje:
  * Zakládat, upravovat a mazat přání přímo jménem zastupované osoby.
  * Kontrolovat přesné zobrazení skupin a položek z perspektivy daného účtu.
  * Provádět technickou asistenci na dálku bez nutnosti znát přihlašovací heslo uživatele.
  Režim impersonace je vizuálně indikován persistentním systémovým bannerem s možností okamžitého návratu do původního administrátorského kontextu.

* **Internacionalizace a lokalizační vrstva (i18n)**
  Aplikace je plně lokalizována do českého a anglického jazyka. Přepínání jazykových mutací probíhá na úrovni front-endu pomocí ovládacích prvků v záhlaví aplikace, přičemž systém si volbu jazyka ukládá do konfigurace uživatelského profilu pro budoucí relace.

### Architektura rozhraní

Uživatelské prostředí je rozděleno do čtyř hlavních modulů přístupných přes persistentní navigační lištu:
1. **Nástěnka (Dashboard):** Výchozí přehled s rozcestníky na aktivní skupiny a rychlým zobrazením stavu vlastního profilu.
2. **Detail skupiny:** Matice členů, správa pozvánek a strukturované výpisy přání jednotlivých osob.
3. **Nákupní seznam:** Osobní kontrolní seznam s dedikovanými akčními tlačítky pro stavové změny položek.
4. **Profil:** Správa autentizačních údajů (změna hesla), úprava uživatelské biografie a sekce pro správu a přepínání virtuálních uživatelů.
