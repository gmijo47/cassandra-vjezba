## Zadatak 1

Cassandra koristi **port 9042** za CQL klijentske konekcije. Nema web browser sučelja jer je distribuirani sistem fokusiran na performanse i skalabilnost, ne na user interface. Umjesto toga, korisnici koriste alate poput DataStax Studioa, cqlsh ili DBeadera.

---

## Zadatak 2

Izbor partition key-a je kritičan jer određuje kako se podaci distribuiraju između čvorova u klasteru. Ako svi zapisi imaju isti partition key, svi podaci će biti smješteni na samo jedan čvor - to je poznato kao **hot partition problem**. To dovodi do neujednače distribucije opterećenja, gdje jedan čvor postaje bottleneck, što sprječava skalabilnost i smanjuje performanse cijelog sustava.

---

## Zadatak 3

