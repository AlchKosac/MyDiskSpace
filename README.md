# MyDiskSpace

Domácí obdoba [TreeSize](https://www.jam-software.com/treesize) postavená na .NET 10 a Avalonia UI.
Jedna codebase pro Windows, Linux i macOS.

Tento repozitář obsahuje **oficiální buildy aplikace**. Zdrojový kód je v soukromém repozitáři.

## Stažení

Nejnovější verze je v sekci [Releases](../../releases):

| Platforma | Soubor |
|---|---|
| Windows x64 | `MyDiskSpace-1.0.0-win-x64.zip` |

Build je self-contained single-file – nepotřebuje nainstalovaný .NET, stačí rozbalit a spustit `MyDiskSpace.exe`.

## Co to umí

- **Paralelní sken disku** – `C:\Users` (599 000 souborů, 216 GB) naskenuje za ~5 s.
- **Strom složek** seřazený podle velikosti, s podílem z rodiče a počtem souborů.
- **Koláčový graf** a **treemap** s drill-down dvojklikem.
- **Tabulka souborů** – velikost, velikost na disku, datum změny; otevření, zobrazení v průzkumníku, kopírování cesty.
- **Mazání a přesun** – smazání do koše (Windows) nebo trvale, přesun do jiné složky.
- **Export** – CSV a samostatný HTML report.
- **Snapshoty skenu** – uložení a načtení `.mdsnap` bez opětovného skenování.
- **Porovnání skenů** – přidané, odebrané a změněné složky proti uloženému snapshotu.
- **500 největších souborů** v celém stromu.
- **Hledání duplikátů** – velikost → hash prvních 16 KB → plný SHA-256.
- **Lokalizace** – angličtina a čeština.
- **Vzhled** – světlý, tmavý, nebo podle systému.
- **Zástupci** – volitelně na plochu a do nabídky Start (Windows).

## Spuštění

Rozbal ZIP a spusť `MyDiskSpace.exe`. Volitelně lze předat cestu ke skenu:

```powershell
.\MyDiskSpace.exe "C:\Users"
```

## Požadavky

- Windows 10/11 x64 (build je self-contained)
- Linux a macOS buildy přidám později

## Co (zatím) není

- Síťové share a cloudová úložiště
- Přesná velikost na disku na Unixu
- Export do PDF
- Porovnání více než dvou snapshotů najednou

## Licence

Soukromý projekt pro domácí použití. Buildy jsou zdarma ke stažení a osobnímu použití.
