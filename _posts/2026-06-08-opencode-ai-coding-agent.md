---
layout: post
title: "OpenCode - otevřený AI coding agent"
date: 2026-06-08 11:50:00 +0200
---

*Tento příspěvek byl celý napsán pomocí OpenCode.*

[OpenCode](https://opencode.ai) je open-source AI coding agent, který běží v terminálu,
desktopové aplikaci nebo IDE extension. Má přes 160 000 hvězd na GitHubu, 900+ přispěvatelů
a používá ho 7,5 milionu vývojářů měsíčně. Podporuje desítky LLM providerů - Claude, GPT,
Gemini, OpenCode Zen a další.

## Hlavní vlastnosti

- **Multi-session** - paralelní agenti v jednom projektu, mezi kterými lze přepínat
- **LSP integrace** - automatické načtení LSP serverů pro lepší kontext modelu
- **Plan mód** - analýza a návrh bez zásahu do kódu, přepínání Tabem
- **Undo/Redo** - vrácení změn příkazem `/undo`, vícenásobné vrácení i znovuaplikace
- **Share** - sdílení konverzace odkazem přes `/share`
- **Subagenti** - vestavění agenti (explore, scout, general) pro paralelní průzkum kódu, dokumentací a úloh
- **Vlastní agenti** - definovatelní v `opencode.json` nebo Markdown souborech s vlastním promptem, modelem, teplotou, permissions a barvou
- **Permissions** - jemnozrnné řízení přístupu (allow / ask / deny) pro každý nástroj i konkrétní bash příkazy
- **Nástroje** - bash, edit, write, grep, glob, websearch, webfetch, question, todowrite, skill, lsp, apply_patch
- **Theming, keybindingy, formatter, commands** - rozsáhlé možnosti přizpůsobení

## MCP servery (vlastní nástroje)

OpenCode podporuje **Model Context Protocol (MCP)** - můžete si připojit vlastní lokální nebo
vzdálené servery, které rozšiřují schopnosti agenta o nové nástroje. Podporuje OAuth autentizaci,
včetně dynamické registrace klienta (RFC 7591).

Lokální MCP server:
```json
"mcp": {
  "my-tool": {
    "type": "local",
    "command": ["npx", "-y", "my-mcp-command"]
  }
}
```

Vzdálený MCP server:
```json
"mcp": {
  "my-api": {
    "type": "remote",
    "url": "https://mcp.mej-server.cz/mcp"
  }
}
```

MCP lze zapojit s čímkoli - databází, API třetích stran, monitorováním (Sentry),
vyhledáváním v dokumentaci (Context7) nebo grepem kódu napříč GitHubem (Grep by Vercel).
Nástroje z MCP serverů lze povolovat/zakazovat globálně i per-agent, včetně podpory
glob patternů.

## Ceny

OpenCode jako takový je **zcela zdarma** a open-source (MIT licence). Platíte jen za
používání modelů:

- **Vlastní API klíče** - připojíte si vlastní účet u Anthropic, OpenAI, Google atd.
  a platíte přímo jim
- **OpenCode Zen** - předtestované modely přímo pro coding, platba přes opencode.ai/auth;
  ceny odpovídají spotřebě tokenů dle vybraného modelu
- **GitHub Copilot** - lze se přihlásit přes GitHub účet
- **ChatGPT Plus/Pro** - lze propojit stávající předplatné
- **Lokální modely** - podpora 75+ providerů přes Models.dev včetně úplně lokálních modelů
- **Enterprise** - individuální ceník pro firmy s vlastní infrastrukturou

## Plány a budoucnost

OpenCode se vyvíjí velmi aktivně. Desktopová aplikace je v beta verzi pro macOS, Windows
i Linux. OpenCode Zen přináší ručně testované a benchmarkované modely optimalizované pro
coding agenty. Pro firmy je k dispozici Enterprise varianta s podporou vlastní
infrastruktury, network policy, pokročilou správou oprávnění a vlastními MCP servery
distribuovanými přes `.well-known/opencode` endpoint.

*Pokud vás OpenCode zaujal, instalace je otázkou jednoho příkazu:*
```bash
curl -fsSL https://opencode.ai/install | bash
```
