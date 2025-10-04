# Cíl / scope

* MVP: AI itineráře pro Cancún, důraz na “unique/hidden” tipy, ne jen mainstream resorty.
* Itinerář musí respektovat constrainty (např. auto jen 3 dny).

# Data & zdroje

* Primární zdroje: Google Places (Place Details, Photos, Reviews), Viator (pozor na **unique content policy** – ne scrapovat jejich texty), plus otevřené zdroje (Wikidata, OSM), “long-tail” blogy/Reddit/official weby.
* Potřeba mít **provenienci**: ukládat URL/ID zdroje ke každému POI (kvůli deduplikaci, důvěře, auditu).
* Moderní AI search API + LLM pro extrakci a sumarizaci.

# Taxonomie / “stavební kameny”

* Příroda & voda (pláže, cenoty, laguny, ostrovy)
* Outdoor & adrenalin (hiking, zipline, potápění, snorkel)
* Kultura & historie (maya ruiny, muzea, lokální rituály)
* Gastronomie (food markets, street food, fine)
* Noční život & zábava (bary, kluby, shows)
* Wellness & relax (spa, temazcal, yoga)
* Rodinné & easy (aquaparky, nenáročné aktivity)
* Eventy (opakující se festivaly/období – turtle hatching, whale sharks, storm season windows)
* Praktické moduly (transport, půjčovna, výlety “with/without car”).

# Itineráře

* Nutnost **resource-aware plánování**: dny s autem maximalizovat na vzdálené spoty (Isla Blanca, cenoty mimo hotelovou zónu), bez auta držet věci v Hotel Zone/downtown.
* “Weather/season switches”: deštivá varianta, hurikánová sezóna, sargassum sezóna.
* Sloty: ráno (outdoor), poledne (indoor/food), večer (nightlife), “buffer/siesta”.
* Vkládat “unique hook” denně (1 nezapomenutelný zážitek).

# Hodnocení kvality POI

* Signály: hustota nezávislých zmínek napříč zdroji, čerstvost, sentiment z recenzí, lokální “expert mentions”, vzdálenost/časová náročnost vs. přínos, sezónnost.
* LLM spíš jako **rater/aggregator**, ne jediný zdroj pravdy. Kombinovat s heuristikami + jednoduchým modelem skórování.

# Právní/etické okraje

* Viator: nepřebírat jejich texty/fotky; metadate/IDs a deeplinky.
* Blogy: jen metadata + vlastní sumarizace; respekt fair use.

# Datový model / úložiště

* DynamoDB: jeden **hlavní dokument POI** (denormalizovaný), v něm agregované zdroje.
* Samostatná kolekce **sources** (pro crawl logy, first_seen/last_seen, confidence).
* Ukládat **embeddingy odděleně** (per “thing”: POI, event, neighborhood, query template) kvůli updatům a různým dimenzím.
* Klíče: stabilní `place_ulid`, `source_ulid`; držet i externí ID (Google place_id, OSM/Wikidata).
* Indexy: by region/city, by category, by seasonality window, by “needs car”.

# Pipeline / orchestrace

* Event-driven Lambdy nad Dynamo (CDC):

  * seed city → discover (Places/OSM/Wikidata) → enrich (crawl blog/official) → dedupe/merge → score → slate do itinerary builderu.
* Re-processing, když přibude nový worker/signal.
* Rate-limit/queuing, idempotence, retry DLQ.
* Audity: ukládat “explain” pro skóre (pro debugging a trust).

# “Recurring” věci pro Cancún

* Sezónní okna: whale shark season, turtle nesting/hatching, sargassum peaks, hurikánová sezóna, suché/ vlhké období.
* Opakující se eventy: lokální festivaly, trhy, pravidelné shows. (Je potřeba mít zvlášť entitu **Event** se sezónností/rrule + mapping na POI.)

# Produkt / UX

* Wireframe:

  * Onboarding: preference (tempo, budget, car yes/no days, food rules, no-kid/with-kid).
  * Discovery: bucketované shortlisty (viz taxonomie) s “why picked” a zdrojovými signály.
  * Itinerary view: denní bloky, varianty pro počasí, přehled jízdních časů, hard/soft locks.
  * Export: offline/print/Maps listy.

# Otevřené otázky

* Které Google API přesně: Places Details/Photos/Reviews vs. Local Context vs. Serp proxy.
* Jak často refreshovat sezónní okna/eventy.
* Kde vést “policy guardrails” (per-source) a jak testovat duplicitní content.

# Krátké “next steps”

1. Definovat přesné schéma `POI`, `Event`, `Source`, `ItinerarySlot` (JSON, vč. explain fields).
2. Crawl/discovery pro Cancún (Places → Wikidata/OSM → official/blogs).
3. První scoring pipeline + top-N shortlist pro každou kategorii.
4. Itinerary builder v2: respekt dny s autem, počasí, sezónní switch.
5. UI: jeden městský MVP screen (shortlist + 6denní plán), s provenance badge.
