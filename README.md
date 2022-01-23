# spse

## install

1. apt install ruby [rubygems]
2. gem install mechanize
3. gem install roo --version 2.7.1
4. edit spse.cfg

## usage

```bash
spse export TRIDA PREDMET >> data.csv
```

```bash
spse import [fuzzy] [doit] < data.csv
```

Bez parametru 'doit' to nic opravdu neudela.

Bez parametru 'fuzzy' dojde k aktualizaci jiz existujici znamky pouze na zaklade stejneho scoreId.
S parametrem 'fuzzy' dojde k aktualizaci znamky bud na zaklade shodneho scoreId nebo shodneho popisu.

Mazani znamek: importuj csv s vyexportovanymi scoreId, ale smaz v tech radcich hodnoty (1-5-N).

## export z moodle

1. vytvor template

  ```bash
  $ ./spse new c3a pss > pss-c3a-new.csv
  $ ./spse new c3b pss > pss-c3b-new.csv
  $ ./spse new c3c pss > pss-c3c-new.csv
  ```

  jednotlive csv edituj a odstran zaky, ktere nemas (jejich znamky nelze editovat)

2. exportuj znamky z moodle do souboru napr. 'export-znamek-c3.ods'

3. vytvor stejnojmenny konfigurak s koncovkou yml, napr. 'export-znamek-c3.yml' (viz example)
   v nem jsou definovany ukoly, ktere se maji importovat do portalu (nebo aktualizovat, pokud uz tam jsou)
   nazvy museji matchovat s castmi nazvu sloupcu v tabulce z moodle; pod temito nazvy se znamky ulozi na portal

4. pro kazdy ukol definovany v export-znamek-c3.yml se udela prunik s patricnym jmenem&prijmenim v template csv a vyrobi se nove csv vhodne pro import

   napriklad je template:

   C3B,PSS,novak,Novák Antonín,,cviceni/teorie,popis,1-5/N,small/big 
   C3B,PSS,novotny2,Novotný Ondřej,,cviceni/teorie,popis,1-2-3-4-5-N,small/big 
   ...

   pak

   ```bash
   $ cat pss-c3a-new.csv | ./moodle export-znamek-c3
   ```

   vygeneruje

   C3B,PSS,novak,Novák Antonín,,cviceni,UTP Kabel,1,small 
   C3B,PSS,novotny2,Novotný Ondřej,,cviceni,UTP Kabel,1,small 
   ...

5. nakonec se takto pripravena data importuji s parametrem 'fuzzy'; to proto, ze na portale uz muze existovat znamka se stejnym popisem
   (v moodle nejsou scoreId, takze se pro pripad update hleda na portale znamka se stejnym popisem)


Cely proces pro vsechny c3 tridy muze vypadat takto:

  ```bash
  $ cat pss-c3*-new.csv | ./moodle export-znamek-c3 | ./spse import fuzzy doit
  ```

Nebo jen dve tridy

  ```bash
  $ cat pss-c3a-new.csv pss-c3b-new.csv | ./moodle export-znamek-c3 | ./spse import fuzzy doit
  ```

