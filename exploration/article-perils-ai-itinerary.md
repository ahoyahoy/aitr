### 📊 Report: „The perils of letting AI plan your next trip“

*(analýza problému a relevance pro náš produkt)*

---

#### 1️⃣ Klíčový problém trhu

* **Generativní AI ve travelu selhává na důvěře.**

  * ChatGPT, Layla, Wonderplan a další generují **vymyšlené destinace, neexistující trasy a neaktuální údaje**.
  * 30–40 % uživatelů reportuje **nesprávné nebo chybějící informace**.
* **Důsledek:**

  * ztráta důvěry v AI itineráře,
  * reputační riziko pro platformy,
  * bezpečnostní riziko pro uživatele.

---

#### 2️⃣ Příčina

* LLM systémy **nejsou napojené na validní datové zdroje**.
* Fungují čistě na **statistické kompozici textu**, bez pochopení fyzické reality.
* Výstupy se tváří přesvědčivě, ale **nemají ground truth layer** (geografie, časy, vzdálenosti, otevírací doby).

---

#### 3️⃣ Co z toho plyne

* Travel AI první generace → **„textové halucinátory“** bez faktické validity.
* Uživatelé se učí, že „AI itinerář = riziko“.
* Objevuje se mezera pro **„verified AI travel layer“** – systém, který kombinuje AI s ověřenými daty.

---

#### 4️⃣ Náš kontrastní přístup

**Cíl:** generovat **fakticky validní itineráře** s měřitelným „feasibility score“.

| Problém v článku              | Naše řešení                                                                 |
| ----------------------------- | --------------------------------------------------------------------------- |
| AI si vymýšlí místa           | POI výhradně z Google Places, OSM, Viator, Booking (API-based ingestion)    |
| Chybí geografická konzistence | OSRM + Google Directions + H3 precompute matrix                             |
| Ignoruje časy a realitu       | OR-Tools VRP/TW solver → respektuje otevírací hodiny, počasí, časové okno   |
| Žádná validace výstupu        | „Truth pass“ = posledních 10–15 legů zvalidováno Google Directions          |
| Nedůvěra v AI výstupy         | Každý krok má citation + reason + confidence score                          |
| Uzavřená bublina doporučení   | Thompson sampling (contextual bandits) → balancuje relevance vs exploration |

---

#### 5️⃣ Technický rámec validace

* **Hybrid retrieval:** Upstash Vector + BM25 + cross-encoder rerank.
* **mastra.ai:** jednotný embedder/chopper s idempotentním ETL, verzováním a cost guardrails.
* **mem0:** persistentní profil usera
* **Routing stack:** OSRM + Valhalla + H3 matrix → rychlé i přesné ETA; Google Directions → truth layer.
* **Explainability:** každá volba má „why“ a zdroj (latence < 2 s, streamovaný skeleton).

---

#### 6️⃣ Strategický význam

* **Problém:** AI travel jako kategorie ztrácí kredibilitu kvůli halucinacím.
* **Naše pozice:** první **„AI-grounded“ travel layer** – spojení AI orchestrace s datovou integritou.
* **Tržní messaging:**

  * *„AI itineraries that can actually happen.“*
  * *„Verified-by-design travel planning.“*

---

#### 7️⃣ Shrnutí

* Tento článek potvrzuje, že **LLM-only přístup je slepá větev**.
* Validace reality (data + solver + truth pass) je **nutná podmínka důvěryhodného AI travel produktu**.
* **Naše architektura** je na tento problém postavená od základu — a představuje **přímou odpověď na selhání první vlny AI cestovek.**
