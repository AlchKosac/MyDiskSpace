# MyDiskSpace

Disk space analyzer pro Windows, Linux i macOS postavený na .NET 10 a Avalonia UI.
Jedna codebase pro Windows, Linux i macOS.

![.NET](https://img.shields.io/badge/.NET-10-512BD4)
![Avalonia](https://img.shields.io/badge/Avalonia-11.3-8B44AC)

## Co to umí

- **Paralelní sken disku** – breadth-first průchod adresářů, jeden `FindFirstFile/FindNextFile` cyklus na složku (Windows), bez zbytečných `stat` volání. `C:\Users` (599 000 souborů, 216 GB) naskenuje za ~5 s.
- **Strom složek** seřazený podle velikosti, s podílem z rodiče, počtem souborů a grafickým pruhem velikosti. Rozbalování po jedné úrovni, dvojklik na řádek.
- **Koláčový graf** – top 12 položek + „Ostatní“, kliknutím se do složky ponoříte.
- **Treemap** – squarified layout, popisky se zkracují podle místa, dvojklik na dlaždici = drill-down.
- **Tabulka souborů** – velikost, velikost na disku, datum změny, přípona; otevření / zobrazení v průzkumníku / kopírování cesty.
- **Mazání a přesun** – soubor i celou složku lze smazat (na Windows do koše, jinde trvale) nebo přesunout jinam; po operaci se strom automaticky přeskenuje. Pokud položka patří nainstalované aplikaci, potvrzení nabídne rovnou odinstalaci aplikace a po doběhnutí odinstalátoru se ověří, jestli je aplikace na daném místě ještě detekována. Soubory jde případně smazat i tak. U systémových a programových složek se zobrazí varování.
- **Export** – seznam souborů aktuální složky do CSV nebo samostatný HTML report s přehledem největších složek a souborů.
- **Snapshoty skenu** – uložení výsledku skenu do komprimovaného `.mdsnap` souboru a jeho pozdější načtení bez opětovného procházení disku.
- **Porovnání více snapshotů** – vyber jeden nebo více uložených snapshotů; aktuální sken se přidá jako poslední sloupec. Tabulka ukazuje velikost každé složky ve všech verzích vedle sebe a změnu od nejstarší po nejnovější, seřazenou podle největšího rozdílu. Složka, která v některé verzi chybí, se označí jako `—`.
- **Export porovnání do PDF** – tabulka porovnání jako vícestránkové A4 na šířku (QuestPDF).
- **Hardware** – karta s informacemi o disku, na kterém leží naskenovaná data: model, výrobce, typ (HDD / SSD / NVMe), připojení, kapacita, sériové číslo, firmware, přípojný bod. Detailní údaje čte přibalený `smartctl` (smartmontools 7.5, Windows). U NVMe disků (M.2) se čte celý SMART/Health log bez administrátorských práv: teplota, opotřebení, zapsaná a přečtená data, rezerva, chyby médií, nebezpečná vypnutí, chybový log a výrobce řadiče. U SATA disků jsou bez práv správce jen identita a stav, SMART atributy vyžadují spuštění jako správce. Aplikace si ukládá jedno měření SMART denně (`%APPDATA%\MyDiskSpace\smart-history`) a z něj počítá trend: zápis za den, nárůst opotřebení za rok, spotřebovanou rezervu a to, jestli rostou chybové čítače. Odhad zbývající životnosti je dvojí: podle deklarované výdrže výrobce v TBW (tabulka `EnduranceCatalog`, zatím jen ověřené modely Samsung 990 EVO Plus, 970 PRO a 970 EVO) a podle nárůstu opotřebení. Odhady se zobrazí po dvou dnech měření a jsou orientační. Aktualizace smartctl: `tools/update-smartctl.ps1`; `release.ps1` odmítne balíček, jehož `smartctl.exe` nesedí s `SHA256SUMS`. Varování se zobrazí u opotřebení nad 80 %, teploty nad 55 °C, přemapovaných, čekajících nebo neopravitelných sektorů, chyb CRC a nízké volné rezervy NVMe.
- **500 největších souborů** v celém stromu (min-heap, žádné materializování všech cest).
- **Hledání duplikátů** ve třech fázích: seskupení podle velikosti → hash prvních 16 KB → plný SHA-256. Počítá se z už naskenovaného stromu, takže se znovu nečte celý disk. U každé skupiny lze rádiovým tlačítkem vybrat, kterou kopii zachovat, tlačítkem „Ponechat nejnovější“ vybrat tu s nejnovějším datem změny, a ostatní buď smazat, nebo přesunout do jiné složky. Filtr nad seznamem zobrazí jen skupiny, jejichž kopie leží v dané složce. Kliknutím na kopii se v pravém panelu zobrazí náhled obsahu (text nebo obrázek) a lze ji otevřít či zobrazit v průzkumníku.
- **Lokalizace** – angličtina (default) a čeština, další jazyk = jeden JSON soubor.
- **Vzhled** – světlý, tmavý, nebo automaticky podle Windows (výchozí). Volba se ukládá mezi spuštěními.
- **Zástupci** – volitelně na plochu a do nabídky Start (Windows).
- **Automatické aktualizace** – kontrola nejnovějšího GitHub release při startu (volitelně) nebo ručně z nastavení; na Windows se balíček stáhne a aplikace se po výměně souborů sama restartuje.
- **Multiplatformní** – Windows, Linux, macOS.

## Požadavky

- [.NET 10 SDK](https://dotnet.microsoft.com/download)
- Windows: žádné další závislosti
- Linux: `libX11`, `libICE`, `libSM`, `fontconfig` (standardní desktop)
- macOS: nic navíc

## Spuštění

```bash
dotnet run --project src/MyDiskSpace.App
```

Nebo s rovnou cestou ke skenu:

```bash
dotnet run --project src/MyDiskSpace.App -- "C:\Users"
```

## Build a publikace

```bash
# testy
dotnet test

# release build
dotnet build -c Release

# samostatné binárky (bez nutnosti mít na cíli .NET)
dotnet publish src/MyDiskSpace.App -c Release -r win-x64   --self-contained true -p:PublishSingleFile=true
dotnet publish src/MyDiskSpace.App -c Release -r linux-x64 --self-contained true -p:PublishSingleFile=true
dotnet publish src/MyDiskSpace.App -c Release -r osx-arm64 --self-contained true -p:PublishSingleFile=true
```

## Architektura

```
src/
  MyDiskSpace.Core/          # bez UI závislostí, testovatelné
    Model/                   # DirNode, FileEntry
    Scanning/                # DiskScanner, FileEnumerator, ScanOptions
    Analysis/                # DuplicateFinder, TopFileFinder
    Updates/                 # UpdateSelector, UpdateSignature (bez UI, testované)
    Persistence/             # ScanSnapshot, SnapshotSerializer (.mdsnap), SnapshotComparer, SnapshotSeries (více verzí vedle sebe)
    Hardware/                # DiskHealth, DiskHealthProbe (Windows/Linux/macOS), SmartctlParser, WindowsStorageParser
    Util/                    # SizeFormatter, TreeWalker
  MyDiskSpace.App/           # Avalonia UI
    ViewModels/              # MainViewModel, FlatNode, FileRow, ChartItem, DriveItem, ComparisonRow, Rows (duplicity, hardware)
    Views/                   # MainWindow.axaml, ConfirmDialog.axaml
    Controls/                # PieChart, Treemap, FractionBar (vlastní vykreslování)
    Localization/            # Strings + lang/en.json, lang/cs.json
    Util/                    # ExportService (CSV/HTML), PdfReportService (PDF), FileOperations (mazání/přesun), AppSettingsStore, ShortcutHelper, UpdateService, InstalledAppLocator (Windows registr, Linux balíčky, macOS .app)
tests/
  MyDiskSpace.Core.Tests/    # xUnit
```

### Jak funguje sken

1. Skenuje se po úrovních (BFS). Všechny složky v hloubce N se zpracují paralelně přes `Parallel.ForEachAsync`, čímž vznikne hloubka N+1. Tím je počet živých tasků omezený a nevzniká deadlock na semaforu.
2. Pro každou složku se použije `FileSystemEnumerable<T>` s vlastní transformací – na Windows to je jeden `FindFirstFileEx` cyklus, takže se nezíská jen jméno, ale i velikost, atributy a čas modifikace bez dalších syscallů.
3. Reparse pointy (junctiony, symlinky) se ve výchozím stavu přeskakují, aby nevznikly cykly a data se nepočítala dvakrát.
4. Agregace (velikost, počet souborů/složek, poslední modifikace) proběhne jedním průchodem od nejhlubší úrovně nahoru.
5. Velikost souborů je logická; „na disku“ se aproximuje zaokrouhlením na cluster (4096 B). Přesné `st_blocks` / `GetCompressedFileSize` by šlo doplnit, ale pro domácí použití to nemá cenu.

### Výkon

| Složka | Soubory | Data | Čas |
|---|---:|---:|---:|
| `C:\Program Files` | 66 884 | 22,9 GB | 0,4 s |
| `C:\Users` | 599 033 | 216 GB | 5,3 s |

RAM po skenu `C:\Users`: ~290 MB. Cesty se u souborů neukládají – rekonstruují se z rodičovské složky, takže paměť drží hlavně názvy souborů a struktura stromu.

## Lokalizace

1. Přidej `src/MyDiskSpace.App/Localization/lang/xx.json` (zkopíruj `en.json`).
2. Přidej jazyk do seznamu `Strings.Languages`.
3. Přidej properties do `Strings.cs` (typované accessory pro XAML).

Chybějící klíče se fallbackují na angličtinu.

## Vzhled a nastavení

Ozubené kolo v toolbaru otevírá nastavení:

- **Jazyk** – angličtina nebo čeština, změna se projeví okamžitě.
- **Motiv** – `Systém` (přebírá světlý/tmavý režim z Windows), `Světlý`, `Tmavý`.
- **Zahrnout skryté soubory** – výchozí zapnuto.
- **Následovat symbolické odkazy** – výchozí vypnuto (kvůli cyklům).
- **Zástupci (Windows)** – `Zástupce na plochu` a `Zástupce do nabídky Start`; zaškrtnutím se `.lnk` vytvoří, odškrtnutím odstraní. Stav se čte přímo z disku, takže odpovídá realitě i po ručním smazání.
- **Aktualizace** – `Kontrolovat aktualizace při startu` (výchozí zapnuto) a tlačítko `Zkontrolovat aktualizace` s aktuální verzí. Kontrola se při startu provádí nejvýš jednou za 6 hodin.

Volby se ukládají do `%APPDATA%\MyDiskSpace\settings.json` (na Linuxu/macOS do odpovídající složky Application Data, typicky `~/.config/MyDiskSpace/`) a načtou se při startu. Motiv i jazyk se tak pamatují mezi spuštěními. Barvy jsou definované jako `ThemeDictionaries` v `App.axaml`, takže přidání dalšího motivu je jen otázka jedné dictionary.

## Snapshoty, porovnání a export

- **Snapshot skenu** – `Snapshot → Uložit snapshot skenu…` zapíše strom do gzipovaného `.mdsnap` souboru (magic `MDSNAP01`, verze 1). Uložený snapshot lze otevřít přes `Snapshot → Otevřít snapshot…` a procházet ho stejně jako živý sken.
- **Porovnání** – `Snapshot → Porovnat snapshoty…` otevře výběr jednoho nebo více `.mdsnap` souborů. Aktuální sken se přidá jako poslední sloupec a karta `Změny` ukáže velikost každé složky ve všech verzích, změnu a počet souborů. Tlačítko `Exportovat porovnání do PDF…` uloží stejnou tabulku do PDF.
- **Export CSV** – `Export → Složka jako CSV…` uloží soubory a podsložky aktuální složky včetně velikosti, velikosti na disku a času změny.
- **Export HTML** – `Export → HTML report…` vytvoří samostatný soubor s přehledem, největšími složkami a soubory; otevírá se v libovolném prohlížeči.
- **Mazání** – tlačítko `Smazat` v kartě `Soubory` (soubor) nebo `Smazat složku` nad stromem. Na Windows se maže do koše, na Linuxu/macOS trvale; vždy s potvrzením a počtem položek.
- **Přesun** – `Přesunout…` u souboru i složky otevře výběr cílové složky (startuje v nadřazené složce) a položku tam přesune. Kolize jmen i přesun složky do sebe sama se hlásí chybou.

## Aktualizace

Aplikace čte `releases/latest` z veřejného repozitáře `AlchKosac/MyDiskSpace`, porovná verzi s vlastní a podle RID (`win-x64`, `linux-x64`, `osx-arm64`, `osx-x64`) vybere správný asset.

- **Windows** – ZIP se stáhne do `%TEMP%\MyDiskSpace-update\<verze>`, rozbalí do dočasné složky a pomocný PowerShell skript počká na ukončení aplikace, přepíše soubory v instalační složce a aplikaci znovu spustí. Když do složky nelze zapisovat (např. `Program Files` bez práv), nabídne se otevření stránky ke stažení.
- **Linux / macOS** – stejný mechanismus: tarball se rozbalí, počká se na ukončení procesu, soubory se zkopírují (`cp -a`, zachování práv), spustitelný soubor dostane `chmod +x` a aplikace se restartuje.

Před přepsáním souborů se pořídí záloha. Pokud nová verze do 12 sekund spadne, skript ji vrátí zpět a spustí předchozí verzi; průběh se zapisuje do `install.log` ve složce aktualizace. Když selže ověření podpisu nebo instalace, dialog nabídne otevření stránky vydání pro ruční stažení.

Každý balíček má vedle sebe `.sig` s RSA/SHA-256 podpisem. Veřejný klíč je zkompilovaný v aplikaci (`MyDiskSpace.Core/Updates/UpdateSignature.cs`), soukromý klíč leží mimo repozitář v `%USERPROFILE%\.mydiskspace\update-signing-key.pem`. Bez platného podpisu se balíček neinstaluje – aplikace místo toho nabídne stránku ke stažení.

Vydání nové verze:

```bash
powershell -File tools/release.ps1 -Version 1.7.0
gh release create v1.7.0 --repo AlchKosac/MyDiskSpace --notes-file notes.md artifacts/1.7.0/*
```

## Co (zatím) není

- Síťové share a cloudová úložiště (SMB, OneDrive, Google Drive…)
- Přesná velikost na disku na Unixu (řešeno aproximací na cluster)
- Hardware na Linuxu a macOS bez `smartctl` ukazuje jen základní údaje (model, typ, připojení, kapacitu); detailní SMART vyžaduje nainstalovaný smartmontools (na Windows je přibalený)
- Odinstalace na Linuxu vyžaduje `pkexec` (grafické potvrzení hesla), na macOS se `.app` přesune do koše a balíčky z Homebrew se odinstalují ručně

Tyhle věci se dají doplnit nad existující Core vrstvu bez zásahu do UI.

## Licence

Soukromý projekt pro domácí použití.
