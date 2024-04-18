---
layout: post
title:  "Spellcheck ve VIMu"
date:   2024-04-18 09:19:00 +0200
---

Už delší dobu sem tam používám (N)VIM. Respektive ho používám jako hlavní editor pro textové soubory, jen ho nepoužívám jako hlavní IDE. Ne, že bych to nezkoušel. Například pro Python jsem měl nastavený LSP a podobně, ale stále jsem tak trochu zhýčkaný tím, co pro mně dělá IntelliJ. 

V minulosti jsem zkoušel zprovoznit spellchek, ale nedařilo se mi najít správné slovníky a podobné věci, ale tentokrát to bylo o poznáni jednoduší. Stačilo jen nastavit:

```
:localset spell spelllang=en,cs
```

A následně se potřebné slovníky získaly z online zdrojů. Takže základní spellcheck už krásně funguje a zvýrazňuje mi veškerá slova, která neexistují.
