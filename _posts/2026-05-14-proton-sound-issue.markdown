---
layout: post
title: "Steam/Proton a problém se zvukem"
date: 2026-05-14 20:04:00 +0100
categories: technika
---

Na Steamu mám [HOMM: Olden Era][HOMM] a [AoE II: DE][AOE] a obě ty hry jsou skvělé! Největší problém, alespoň momentálně, je ten, že ani jedna z nich neběží mimo Windows. U HOMM je stále ještě naděje, že Unfrozen vydá hru později i pro Linux, ale u AoE se asi nedočkáme. 

Nevadí! Tedy vadí, ale ne moc! Máme přece Proton/Wine. Jenže na mám PC jsem narazil na problém, kdy zvuk ve hře funguje správně pokud používám bezdrátová sluchátka, ale nefunguje, pokud použiji jack. Proč? Hra naběhne, chvilku se tváři, že zvuk jde a pak přestane. 

Zkusil jsem restartovat PC, udělat rescan sběrnice, různé verze Protonu a nevím co ještě, ale vše bylo marné. Nakonec se ukázalo, že jde zřejmé o problém moderního HW, kdy Proton a PipeWire nespolupracuje úplně správně a při změně vzorkovací frekvence, aby se šeťrila energie. Tedy problém někde u SST, SOF, DSP? Asi jo. 

Řešení? Vynucení pevné frekvence pro komunikaci s PipeWire serverem.


```
# Vytvoření cesty, pokud neexistuje
mkdir -p ~/.config/pipewire/pipewire-pulse.conf.d/

# Vytvoření konfiguračního souboru
cat <<EOF > ~/.config/pipewire/pipewire-pulse.conf.d/fix-proton.conf
pulse.properties = {
    pulse.min.req          = 1024/48000
    pulse.default.tlength  = 1024/48000
    pulse.min.quantum      = 1024/48000
}

pulse.rules = [
    {
        matches = [ { application.process.binary = "wine64-preloader" } ]
        actions = {
            update-props = {
                pulse.min.req = 1024/48000
            }
        }
    }
]
EOF
```

Nyní si mohu užívat zvuk v obou těchto hrách! Tedy... pokud bych měl i čas je hrát.



[HOMM]: https://store.steampowered.com/app/3105440/Heroes_of_Might_and_Magic_Olden_Era/
[AOE]: https://store.steampowered.com/app/813780/Age_of_Empires_II_Definitive_Edition/
