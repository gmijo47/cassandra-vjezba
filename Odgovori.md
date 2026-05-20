## Zadatak 1

Cassandra koristi **port 9042** za CQL klijentske konekcije. Nema web browser sučelja jer je distribuirani sistem fokusiran na performanse i skalabilnost, ne na user interface. Umjesto toga, korisnici koriste alate poput DataStax Studioa, cqlsh ili DBeadera.

---

## Zadatak 2

Izbor partition key-a je kritičan jer određuje kako se podaci distribuiraju između čvorova u klasteru. Ako svi zapisi imaju isti partition key, svi podaci će biti smješteni na samo jedan čvor - to je poznato kao **hot partition problem**. To dovodi do neujednače distribucije opterećenja, gdje jedan čvor postaje bottleneck, što sprječava skalabilnost i smanjuje performanse cijelog sustava.

---

## Zadatak 4

**Partition key** određuje koji čvor sadrži podatke i mora biti prisutan u WHERE upitu (`WHERE user_id = ?`), dok **clustering column** sortira podatke unutar particije i može biti korišten s operatorima kao što su `<`, `>`, `<=`, `>=` (`WHERE user_id = ? AND timestamp >= ?`). Partition key je obavezna u WHERE klauzuli jer će upit inače trebati skenirati sve čvorove, dok se clustering colummni mogu koristiti opciono za filtriranje unutar već pronađene particije.

---

## Zadatak 5

**Tombstone** je marker koji Cassandra upisuje umjesto stvarnog brisanja kada se izvrši `DELETE` — sadrži timestamp brisanja i oznaku da je podatak obrisan. Cassandra koristi ovaj pristup jer je distribuirani sustav gdje se podatak može nalaziti na više replike, pa je sigurnije ostaviti marker koji će se propagirati svim čvorovima nego pokušati sinkrono obrisati s njih. **Compaction** proces periodično prolazi kroz SSTable datoteke i fizički uklanja tombstone-ove, ali tek nakon što prođe `gc_grace_seconds` period (zadano 10 dana) — to je dovoljno vremena da se tombstone replicira na sve čvorove u klasteru.

---

## Zadatak 6

**Secondary Index** je bolji izbor za jednostavne, low-cardinality stupce (npr. `kategorija`, `dostupnost`) jer je lak za kreirati i ne zahtijeva duplikaciju podataka — međutim, query mora kontaktirati sve čvorove pa je sporiji u velikim klasterima. **Materialized View** je bolji kada imamo stabilan pristupni obrazac koji se često koristi (npr. uvijek queriramo narudžbe po `statusu`) jer Cassandra automatski održava drugu tablicu s drugačijim primary key-em i query je jednako efikasan kao i na originalnoj tablici — tradeoff je write amplifikacija jer svaki INSERT/UPDATE mora ažurirati i MV.

---

## Završni zadatak

Tablice `tecaj`, `upis` i `lekcija` dizajnirane su prema pristupnim obrascima: `tecaj` koristi `tecaj_id` kao partition key jer uvijek dohvaćamo tečaj po ID-u. `upis` koristi `(student_id, tecaj_id)` jer je najčešći upit "koji tečajevi su upisani za određenog studenta" — student_id je partition key koji garantira da su svi upisi jednog studenta na istom čvoru. `lekcija` koristi `(tecaj_id, redni_broj)` jer uvijek dohvaćamo lekcije određenog tečaja sortirane po rednom broju — tecaj_id grupira sve lekcije na isti čvor a redni_broj je clustering column koji osigurava sortiranje.

Cassandru bih koristio za platformu online tečajeva umjesto PostgreSQL-a kada broj korisnika naraste na desetke milijuna i kada je pisanje događaja (napredak, klikovi, gledanje lekcija) kontinuirano i masovno. Cassandra odlično podnosi visoki write throughput distribuiran na više čvorova bez single point of failure, što je teško postići PostgreSQL replikacijom. TTL za trial pristup (npr. 7 dana besplatnog pristupa) je nativna Cassandra funkcionalnost koja automatski briše zapis bez zasebnog background joba. Globalna distribucija učenika po datacentrima (multi-region) je trivijalna u Cassandri ali kompleksna u relacijskim bazama. Specifičan problem koji bi bio teško riješiti relacijskim pristupom je pisanje svakog video-streamin eventa (pauza, seek, napredak po sekundi) za milijune istovremenih korisnika — PostgreSQL bi se zagušio dok Cassandra to prima bez degradacije performansi.


