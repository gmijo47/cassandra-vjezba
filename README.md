# Cassandra Vježba

## Pokretanje

```bash
docker compose up -d
```

Pričekati dok Cassandra bude spremna (oko 30–60 sekundi):

```bash
docker compose ps
```

Status mora biti `healthy`.

## Spajanje na cqlsh

```bash
docker exec -it cassandra-vjezba-cassandra-1 cqlsh
```

## Pokretanje upita iz queries.cql

```bash
docker exec -i cassandra-vjezba-cassandra-1 cqlsh < queries.cql
```

Ili copy-paste pojedinih upita direktno u cqlsh konzolu.

## Zaustavljanje

```bash
docker compose down
```

Za brisanje i podataka:

```bash
docker compose down -v
```
