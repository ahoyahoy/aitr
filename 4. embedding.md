# Dynamické embeddingy & Thompson sampling

## Kontext

* U cestovní appky generujeme **nové itineráře a zastávky on-the-fly**.
* Nemůžeme mít všechno předpočítané (long tail destinací je obrovský).
* Potřebujeme systém, který:

  1. Umožní embedovat nová místa dynamicky při vyhledávání.
  2. Kombinuje výsledky ze stávající knowledge base (předpočítané embeddingy) s novými.
  3. Zabrání tomu, aby se uživatel dostal do **„bubliny“** (vidí pořád jen stejné typy míst).

---

## On-the-fly embeddingy

* **Základní pipeline**:

  1. Uživatelský dotaz → AI navrhne potenciální místa (Google Maps API, externí zdroje).
  2. Pro místa, která **nemáme v databázi**, okamžitě vygenerujeme embedding (popis z Google/TripAdvisor/AI summarizer → vector).
  3. Tento embedding se uloží do krátkodobého cache store (Redis / in-memory).
  4. Pokud se místo začne opakovaně používat, přesune se do dlouhodobé vektorové DB.

* Výhoda: systém je **otevřený long tailu** – nové destinace, malé vesnice, festivaly, restaurace.

* Nevýhoda: generování embeddingů je dražší, proto potřebujeme smart caching a reuse.

---

## Kombinace s předpočítanými embeddingy

* **Předpočítaná část**: velká databáze populárních míst, měst, atrakcí, hotelů.
* **On-the-fly část**: dynamické zdroje, které AI navrhne během query.
* Vyhledávání:

  * nejdřív se dotaz vyhodnotí proti předpočítané DB (rychle).
  * pak se doplní o on-the-fly embeddingy.
  * výsledky se sloučí a seřadí podle relevance.

---

## Thompson sampling – proti bublině

* Problém: pokud uživatel 2× klikne na „pláže“, čistá relevance by mu už nabízela **jen pláže**.

* Řešení: **exploration vs. exploitation** pomocí Thompson sampling:

  1. Každá kategorie (pláž, hory, město, kultura, gastronomie, příroda, festivaly) = **bandit arm**.
  2. Když systém nabízí výsledky, nebere vždy jen „nejlepší embedding match“, ale **vzorkuje** podle Beta–Bernoulli distribuce.
  3. To znamená, že občas ukáže i něco, co není top relevance, aby se zjistilo, jestli to uživatele zajímá.
  4. Pokud uživatel klikne → daná „arm“ dostane posílení. Pokud ignoruje → oslabí se.

* Výsledek:

  * uživatel vidí věci, které **očekává** (exploitation), ale i nové návrhy (exploration).
  * systém se **adaptuje** na preference, ale nikdy je nezavře do bubliny.

---

## Příklad flow

1. Uživatel zadá: „chci roadtrip po Itálii na 10 dní“.
2. AI najde 50 možných zastávek (část v DB, část nově stažená z Maps/TripAdvisor).
3. Všech 50 se embeduje → některé z cache, některé on-the-fly.
4. Výsledky se roztřídí do kategorií (historie, gastronomie, pláže, příroda).
5. Thompson sampling rozhodne, kolik procent má být „jistota“ (např. Řím, Florencie) a kolik experiment (např. malá vinařská vesnice).
6. Uživatel dostane itinerář, kde jsou **klasické highlights + překvapivé objevy**.

---

## Shrnutí

* **On-the-fly embeddingy** = otevřenost long tailu a možnost personalizace.
* **Kombinace s předpočítanými embeddingy** = rychlost a základní coverage.
* **Thompson sampling** = dynamické vyvažování mezi preferencí a objevováním → žádná bublina.
