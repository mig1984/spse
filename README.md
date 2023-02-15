# spse

Skrip "spse" umí exportovat a importovat známky z portálu do/z CSV souboru. Import znamená, že porovná aktuální stav na portále s tím co je v CSV a provede pouze změny.

## instalace

1. nainstaluj ruby (na debianu "apt install ruby")
2. nainstaluj gem mechanize ("gem install mechanize")
4. edituj spse.cfg a uprav své přihlašovací jméno, heslo a zkratku

## export/import známek z/na portál

Export známek předmětu PSS C3a do souboru data.csv:

```bash
./spse export C3a PSS > data.csv
```

Lze též exportovat více tříd i předmětů do jediného souboru a pak ho importovat najednou:

```bash
./spse export C3a PSS > data.csv
./spse export C3b PSS >> data.csv
./spse export C3a WA  >> data.csv
./spse export C3b WA  >> data.csv
```

Nyní je možno CSV soubor editovat:
  - Pokud se změní řádky, které mají "scoreId" (IDčka známek na portále), importem se změní právě tyto známky na portále.
  - Pokud se přidá řádek, který nemá nastaveno "scoreId", pak se při importu vytvoří nová známka na portále.
  - Pokud se uvede namísto známky (1-5-N) písmeno 'D', importem bude známka na portálu smazána.

CSV soubor se importuje takto:

```bash
spse import < data.csv
```

Bez argumentu 'doit' se však pouze zobrazí, co by se na portále změnilo. Aby se to opravdu stalo, je třeba použít 'doit':

```bash
spse import doit < data.csv
```

## vytvoření šablony pro import

Občas se hodí vytvořit CSV soubor, který obsahuje všechny žáky nějaké třídy (jehož importem vzniknou na portále nové známky).

Takový soubor se vygeneruje takto:

```bash
./spse new C3a PSS > data.csv
```

## parametr fuzzy

Normálně má každá známka na portále svoje scoreId, které se exportuje do CSV a při importu se hledá známka podle stejného scoreId.
Bohužel v případě exportu známek z moodle žádné scoreId nejsou, a tudíž jediná možnost, jak spárovat známku na portále se známkou na moodle je podle stejného popisu.
Proto existuje argument "fuzzy", který páruje buď podle scoreId nebo jen podle popisu. Viz sekce moodle.

```bash
spse import fuzzy doit < data.csv
```

# moodle

Skript "moodle" exportuje známky z moodle, a to tak, že vygeneruje soubor CSV vhodný pro následný import skriptem "spse".

```bash
./moodle export pss-c3b.yml > data.csv
./spse import fuzzy doit < data.csv
```

nebo totéž pomocí roury:

```bash
./moodle export pss-c3b.yml | ./spse import fuzzy doit
```

## index žáků

Aby to fungovalo, je potřeba nejprve vygenerovat z portálu index všech svých žáků (jednou ročně nebo když někdo odešel/přišel).

Index obsahuje jména, příjmení, třídy a ID žáků. Jelikož se v exportu z moodle vyskytují pouze jména a příjmení, tak názvy tříd a ID žáků je nutno doplňovat - právě z toho indexu.

```bash
./spse build-index > moodle.idx
```

## konfigurační soubor

Pak je nutno vytvořit konfigurační soubor s definicí úkolů z moodle. Bude se jmenovat například pss-c3b.yml.

## export z moodle => import na portál

```bash
./moodle export pss-c3b.yml > data.csv
./spse import fuzzy doit < data.csv
```

nebo totéž pomocí roury:

```bash
./moodle export pss-c3b.yml | ./spse import fuzzy doit
```

Export/import je možno provádět několikanásobně. V takovém případě se změní pouze ty známky na portále, jejichž hodnoty se liší na moodle.

Jelikož nelze párovat známky v moodle s portálem skrze scoreId (v moodle žádné scoreId není), tak jediná možnost je párovat je na základě stejného popisu.
Proto se při importu musí použít argument "fuzzy". Kdyby se fuzzy neuvedl, vytvořila by se pokaždé nová známka na portále, protože v CSV souboru chybí scoreId.
