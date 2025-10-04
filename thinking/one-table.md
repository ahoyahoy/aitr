# **DynamoDB + zvlášť embeddingy**
„Komplet vše v jednom dokumentu“ u POI ale **není dobré** dlouhodobě (limit 400 KB, neomezený růst, časté updaty) — rozbijí se náklady i výkon.

## Co dát mimo hlavní dokument (proč)

* **Provenance (sources)** roste bez limitu a mění se často → oddělit.
* **Time-series (crowd, ceny, sargassum…)** → oddělit (TTL, levné zápisy).
* **Media + embeddingy** → držet mimo (S3/Cloudfront) + vektorový index (Qdrant/Weaviate/OpenSearch k-NN/pgvector); v Dynamu jen reference `vector_id`.

## Vzor: single-table design (Dynamo)

Všechno v jedné tabulce, různé typy itemů. Jedním dotazem (Query na `PK`) se stáhne place i jeho sources.

**Keys**

* `PK = PLACE#{place_id}`
* `SK` rozlišuje typ: `PLACE`, `SRC#{ts}`, `TS#{metric}#{ts}`, `MEDIA#{id}` …

**Items**

```json
// Hlavní POI
{
  "PK": "PLACE#plc_ulid",
  "SK": "PLACE",
  "name": "Cenote X",
  "loc": {"lat":21.16,"lon":-86.83},
  "admin": {"country":"MX","state":"Quintana Roo","city":"Cancún"},
  "cats": ["nature/cenote"],
  "opening_hours": "Mo-Su 09:00-18:00",
  "tags": ["sunrise","photo","quiet"],
  "ext_ids": {"osm":"w/123","wikidata":"Q12345"},
  "scores": {"hidden_gem":0.78,"safety":0.7},
  "freshness": {"last_checked":"2025-09-26","confidence":0.82}
}

// Provenance (jeden záznam)
{
  "PK": "PLACE#plc_ulid",
  "SK": "SRC#2025-09-26T00:00:00Z",
  "source_type": "wikidata",
  "source_ref": "Q12345",
  "confidence": 0.9,
  "notes": "…",
  "GSI1PK": "SRCREF#wikidata#Q12345",  // rychlé vyhledání všech míst z daného zdroje
  "GSI1SK":  "PLACE#plc_ulid"
}

// Time-series (př. crowd)
{
  "PK": "PLACE#plc_ulid",
  "SK": "TS#crowd#2025-09-26T14:00:00Z",
  "value": 0.62,
  "source": "est",
  "TTL": 1760000000
}

// Média / embedding reference
{
  "PK": "PLACE#plc_ulid",
  "SK": "MEDIA#ph_ulid",
  "url": "https://…/ph_ulid.jpg",
  "alt": "…",
  "vector_id": "vec_abc123"   // ve VectorDB
}
```

**Dotazy (patterns)**

* Detail místa + zdroje: `Query PK=PLACE#plc_ulid` (vrátí `PLACE`, `SRC#*`, `TS#*`, `MEDIA#*` → filtrovat prefixem `begins_with(SK,'SRC#')`).
* Najít místo podle `wikidata:Q…`: `Query GSI1PK=SRCREF#wikidata#Q12345`.
* Poslední zdroj: `Query … SK begins_with 'SRC#'` + `ScanIndexForward=false` + `Limit=1`.

## Proč ne embedovat `sources` do hlavního dokumentu

* **Růst přes 400 KB** (časté) → write fail.
* **Write amplification**: každé přidání zdroje přepisuje celý item.
* **Současné updaty** (TS + sources) → kolize verzí, vyšší konflikty.

## Embeddingy „úplně zvlášť“

* **Dedikované VectorDB** (Qdrant/Weaviate/OpenSearch/pgvector).
* V Dynamu jen **reference** (`vector_id`, `modhash`, `media_id`).
* Pro textové embeddingy (popisy, tipy) kolekci `DOC#…` itemů se stejným `PK` a `vector_id`.

## Kdy výjimku embedovat do PLACE

* Pár nejdůležitějších **kratkých** věcí pro rychlé čtení (např. 3 nejnovější `source_ref`), ale zbytek jako child items.
* Tj. **cache v PLACE**, **pravda v child items**.

