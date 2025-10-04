* **LLM to „neví jistě pravidelné eventy“.** Umí vygenerovat typické věci (Día de Muertos, Semana Santa, karneval, želvy, whale sharks), ale bude hádat **konkrétní termíny/rozsahy** a rok od roku se trefí jen někdy.
* **Potřebujeme malou znalostní bázi + pravidla + občasný pull ze zdrojů.** Pak LLM může jen „orchestrálně“ poskládat itinerář.

### Co dát do systému

1. **Pevné svátky (deterministické)**

* Např. 16. 9. Den nezávislosti, 1.–2. 11. Día de Muertos…
  → Statické záznamy pro Mexiko (platí každý rok).

2. **Pohyblivé svátky (počítatelné)**

* **Semana Santa** (týden kolem Velikonoc).
  → Spočítat datum Velikonoc a odvodit rozsah (Po–Ne). Tohle vyřeš bez webu čistě výpočtem.

3. **Sezónní příroda (intervaly s nejistotou)**

* **Želvy (kvě–září, různé pláže)**, **žralok obrovský (čvn–zář)**, **sargassum (většinou jaro–léto, variabilní)**.
  → Jako „pravděpodobné okno“ + `confidence` a `source_hint` (např. místní NGO/SEMARNAT/hotely).

4. **Lokální opakovačky (pattern)**

* **Noche Mexicana v Parque de las Palapas (např. soboty večer)**, farmářské a street-food trhy apod.
  → Vést jako pravidelná RRULE (např. `FREQ=WEEKLY;BYDAY=SA;BYHOUR=19`) s `verify=true`, a při generování zkusit rychlý dotaz do zdroje.

5. **Proměnlivé festivaly (nutný yearly fetch)**

* **Karneval v Cancúnu, municipální festivaly, maratony, kulturní týdny.**
  → Tohle každý rok **stahujeme** (municipio/visitcancun/turistické kalendáře, Eventbrite, Facebook Events, Meetup). Ukládáme s `year`, `last_checked_at`, `url`.

### Minimální architektura

* **Seed dataset (YAML/JSON)**: svátky, výpočty, sezóny (s intervaly + confidence), lokální RRULE.
* **Resolver**:

  * Spočítá pohyblivé svátky.
  * Filter podle `date_range` uživatele.
  * Obohatí sezónní věci tagy (např. wildlife, crowd_level, price_spike).
* **Live fetcher (volitelné při generování)**:

  * Volá 2–3 zdroje pro „verify=true“ položky (fast timeout, cache 24–72 h).
* **Output**:

  * Každé doporučení má: `name`, `type`, `date_or_range`, `location`, `confidence`, `last_verified`, `source`.

### Schéma (náznak)

```json
{
  "name": "Noche Mexicana",
  "city": "Cancún",
  "type": "recurring_local",
  "rrule": "FREQ=WEEKLY;BYDAY=SA;BYHOUR=19;BYMINUTE=0",
  "venue": "Parque de las Palapas",
  "verify": true,
  "confidence": 0.6,
  "last_verified": null,
  "source_hint": ["municipio", "turismo local"]
}
```
