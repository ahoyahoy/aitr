# Scraping

## Cíl

* Postavit automat pro **Cancún** (později další místa), který:

  * prohledá web (blogy, fóra, „hidden gems“, street food),
  * uloží kandidáty POI,
  * spustí **enrichment** (geocoding, hodnocení, fotky, otevírací doby),
  * deduplikuje a zaindexuje pro doporučovací engine,
  * **měří náklady a výkon** každého kroku.

---

## Architektura (high-level)

* **Planner (QStash cron)** → vytváří práci podle „capabilities“ pro dané `place`.
* **Workflows (Upstash Workflows)** → robustní vícekrokové běhy: `discover → fetch → parse → extract → geocode → enrich → dedupe → index`.
* **Event-driven spouštění** (Dynamo Streams) jen pro **vznik/změnu práce**, ne pro každý krok (zamezíme cyklům).
* **Idempotence všude** (idempotencyKey / event_id + conditional writes).
* **Exa** (nebo podobné) primárně pro **discovery/prioritizaci URL**.

---

## Datový model (minimum)

* **Place**

  * `placeId`, `status (pending|active|done|error)`, `capabilityVersion`, `lastPlannedAt`.
* **Task**

  * `taskId`, `placeId`, `type` (např. `discover.hidden_gems`), `status`, `attempt`, `nextRetryAt`, `idempotencyKey`.
* **Artifact**

  * `artifactId`, `placeId`, `kind (url|poi|raw_html|…)`, `provenance`, `hash`, `is_final_poi`.
* **CapabilityRegistry**

  * `version`, `capabilities: string[]` (slouží pro „cap bump“).

---

## Flow (Cancún → vše ostatní)

* **Insert `Place{cancun}`** → Stream → **Planner** vytvoří `Task` pro `discover.*`.
* **QStash** rozdistribuuje tasky → **Workflows** udělají kroky a vytváří další tasky (`enrich.*`).
* **Cap bump** (přidáme nový worker) → `capabilityVersion` < `current` → Planner **doplní nové Tasky** bez duplicit.

---

## Pipelines (detail)

* **Discover**: Exa/klíčová slova („hidden“, „locals only“, „mercado“, „mariscos“…) → URL kandidáti.
* **Fetch**: Playwright/axios (+ S3 pro raw HTML), respekt robots/ToS.
* **Parse/Extract**: cheerio + NER/heuristiky → název, popis, adresa, signály „hidden gem“.
* **Geocode/Enrich**: Places/Yelp/IG meta, ceny, hodiny, fotky, social proof.
* **Dedup**: fuzzy name+addr+coords + embeddingy.
* **Index**: Dynamo (canonical POI) + Vector DB (později).

---

## Orchestrace & spouštění

* **QStash cron**: denní/týdenní plán (discover/recrawl/backfill).
* **Workflows**: durable kroky, retry policy per krok, fan-out/fan-in, branching (např. „bez adresy“ → backfill).
* **Rate-limit** per host (delay/spacing), DLQ pro failované běhy.

---

## Měření nákladů a výkonu (MVP)

* **Ingestion proxy (Timescale)** – rychlá cesta:

  * endpointy: `POST /cost`, `POST /event`, `POST /cost/bulk`.
  * tabulky: `cost_events`, `events` (Timescale hypertables).
  * **Idempotence** (`event_id`), batching, PgBouncer; později `COPY`.
* **Co logujeme** (per krok):

  * `place_id`, `capability`, `task_id`, `workflow_run_id`, `step`, `provider`, `resource`,
  * `usage`, `unit`, `unit_price`, `amount (computed)`, `status`, `ts_end`, `meta`.
* **Dashboardy (minimálně)**:

  * náklady včera/dnes per **place** a per **capability**,
  * **top drahé kroky/modely**,
  * cena za 1 přidaný **final POI** (USD/POI).
* **Kinesis/Firehose**: odložíme; zvažujeme jen při velkých objemech / potřeba replay.

---

## Kvalita a odolnost

* **Idempotence** v Task i Workflows (hash vstupu / artifactu).
* **Retry/backoff** (429/5xx), proxy rotace, UA rotace, caching etag/last-modified.
* **Observability**: Sentry/Rollbar, metriky (pages/s, errors/s, step latency, cost/step).
* **Compliance**: robots.txt/ToS, jen metadata a krátké výtahy, oficiální API kde to dává smysl.

---

## Anti-patterns (čemu se vyhnout)

* Řetězení **každého** kroku čistě přes DB změny (zacyklení, write storm).
* Chybějící idempotence (duplicitní tasky, dvojitý geocode).
* Logování nákladů až „později“ (ztratíme kontrolu nad burnem).

---

## MVP scope (co uděláme hned)

* **Planner + Workflows** skeleton (discover→fetch→parse→extract→geocode→dedupe→index).
* **Timescale proxy v2** (cost/events, idempotence, pgbouncer).
* **Seed Cancún** + ~500 URL discovery, první enrichment.
* **3 agregace**: náklady per place/capability, top kroky, USD/POI.

---

## Další kroky (krátká roadmapa)

* Přidat **cap bump** mechaniku (nové capabilities → auto backfill).
* Zapojit **vector search** (po dedupu).
* Přidat **host-rate limit** a S3 raw snapshot retention.
* Rozšířit heuristiky „hidden gem“ (multi-source mentions, engagement score).
