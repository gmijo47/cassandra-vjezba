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


