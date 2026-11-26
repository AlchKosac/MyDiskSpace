# MyDiskSpace

Disk space analyzer pro Windows, Linux i macOS postavený na .NET 10 a Avalonia UI.
Jedna codebase pro Windows, Linux i macOS.

Tento repozitář obsahuje **oficiální buildy aplikace**. Zdrojový kód je v soukromém repozitáři.

## Stažení

Nejnovější verze je v sekci [Releases](../../releases):

| Platforma | Soubor | Poznámka |
|---|---|---|
| Windows x64 | `MyDiskSpace-1.4.0-win-x64.zip` | rozbal a spusť `MyDiskSpace.exe` |
| Linux x64 | `MyDiskSpace-1.4.0-linux-x64.tar.gz` | rozbal a spusť `MyDiskSpace/MyDiskSpace` |
| macOS Apple Silicon | `MyDiskSpace-1.4.0-osx-arm64.tar.gz` | rozbal a spusť `MyDiskSpace/MyDiskSpace` |
| macOS Intel | `MyDiskSpace-1.4.0-osx-x64.tar.gz` | rozbal a spusť `MyDiskSpace/MyDiskSpace` |

Všechny buildy jsou self-contained – nepotřebují nainstalovaný .NET.

Na macOS může Gatekeeper při prvním spuštění hlídat nepodepsanou aplikaci; stačí povolit spuštění v *System Settings → Privacy & Security*.

## Co to umí

- **Paralelní sken disku** – `C:\Users` (599 000 souborů, 216 GB) naskenuje za ~5 s.
- **Strom složek** seřazený podle velikosti, s podílem z rodiče a počtem souborů.
- **Koláčový graf** a **treemap** s drill-down dvojklikem.
- **Tabulka souborů** – velikost, velikost na disku, datum změny; otevření, zobrazení v průzkumníku, kopírování cesty.
- **Mazání a přesun** – soubory i celé složky, do koše (Windows) nebo trvale, přesun do jiné složky. Když položka patří nainstalované aplikaci, nabídne se místo mazání rovnou její odinstalace.
- **Export** – CSV a samostatný HTML report.
- **Snapshoty skenu** – uložení a načtení `.mdsnap` bez opětovného skenování.
- **Porovnání skenů** – přidané, odebrané a změněné složky proti uloženému snapshotu.
- **500 největších souborů** v celém stromu.
- **Hledání duplikátů** – velikost → hash prvních 16 KB → plný SHA-256. U každé skupiny lze vybrat kopii, kterou chceš zachovat, ostatní hromadně smazat nebo přesunout jinam.
- **Automatické aktualizace** – kontrola nové verze při startu nebo ručně z nastavení; na Windows, Linuxu i macOS se balíček stáhne, ověří podpisem a nainstaluje sám. Když nová verze nenaběhne, automaticky se vrátí předchozí.
- **Lokalizace** – angličtina a čeština.
- **Vzhled** – světlý, tmavý, nebo podle systému.
- **Zástupci** – volitelně na plochu a do nabídky Start (Windows).

## Aktualizace

Od verze 1.3.0 se aplikace aktualizuje sama: při startu (nejvýš jednou za 6 hodin) nebo ručně přes **Nastavení → Zkontrolovat aktualizace**.
Stahuje se balíček pro aktuální platformu, před instalací se ověří jeho RSA podpis a teprve pak se nahradí soubory v instalační složce. Zastaralé soubory se při aktualizaci uklidí.
Před přepsáním se pořídí záloha; pokud nová verze do 12 sekund spadne, obnoví se předchozí. Když podpis nesedí nebo instalace selže, nabídne se ruční stažení ze stránky vydání.

> **Používáš 1.2.0 nebo starší?** Tyto verze ještě updater neobsahují – jednou si stáhni 1.3.0 ručně, od té doby už aktualizace probíhají automaticky.

## Spuštění

Windows: rozbal ZIP a spusť `MyDiskSpace.exe`.

Linux / macOS:

```bash
tar -xzf MyDiskSpace-1.4.0-linux-x64.tar.gz
./MyDiskSpace/MyDiskSpace
```

Volitelně lze předat cestu ke skenu:

```bash
./MyDiskSpace/MyDiskSpace "/home"
```

## Požadavky

- Windows 10/11 x64
- Linux x64 s X11 (běžná desktopová distribuce)
- macOS 12+ (Apple Silicon i Intel)

## Co (zatím) není

- Síťové share a cloudová úložiště
- Přesná velikost na disku na Unixu
- Export do PDF
- Porovnání více než dvou snapshotů najednou

## Licence

Soukromý projekt pro domácí použití. Buildy jsou zdarma ke stažení a osobnímu použití.
