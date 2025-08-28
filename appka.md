# appky

* **Ne pro resortový charter**

  * zatím mi AI cestovka nedává smysl pro masovku typu all-inclusive zájezdy

    * offline zážitky řeší resort přímo s místními (žádná online provize)
    * cílovka charterů = starší, méně technická, neočekává AI appku
    * problémy klasické cestovky = legislativa, pojištění, support, vysoké riziko
    * jasné vymezení: **nezaměřujeme se na mainstream**

* **UI / UX koncept**

  * vizuálně by to mělo působit jako **časopis / inspirace**

    * velké fotky, destinace, itineráře → „prohlížím si“ místo, ne „chatuju“
    * generování inspirativního vizuálu (fotky z Unsplash / Pexels), aby i MVP působilo atraktivně

  * chat = doplněk, ne hlavní UI

    * uživatel si může kdykoliv kliknout na bublinu a zeptat se na cokoliv:

      * „proč tahle destinace?“
      * „ukaž mi něco s horama místo moře“
      * „nejsou tam zraloci?“
    * AI funguje jako **interaktivní průvodce na pozadí**, ne jako povinný chatbot

* **Hlavní use-case: multi-hop**

  * **AI road trip planner** – plánování složitých tras, kde selhává většina OTA

    * vstupy: počet dní, rychlost cestování (kolik km/hod nebo max X hodin na den), preference na hotely (centrum vs. periferie, parkování, blízko vlaku)
    * AI navrhne cestu se zajímavými zastávkami a trasou, která dává smysl časově i logisticky

  * **placená appka**

    * první trip zdarma → pak předplatné / kredity
    * lidi si budou tvořit itineráře „jen tak“, i když nakonec nepojedou → appka generuje engagement
    * při reálném rozhodnutí → AI rovnou zařídí booking (hotely, letenky, auta)
    * dynamické aktualizace: appka upozorní, že konkrétní výlet zlevnil → push ke koupi

---

# Perplexity for travel

* agregace zdrojů: lety, hotely, zážitky, mapy, eventy
* AI dělá shrnutí a návrh itineráře, místo abys to musel klikat z 10 různých webů
* chat má stejnou roli jako v Perplexity → doplňkové dotazy, zpřesnění, vysvětlení „proč právě takhle“
* rozdíl proti Perplexity: **výsledek je přímo actionable** → itinerář + booking + sdílení

👉 hlavní diferenciátor: kombinace discovery (časopisový look & feel) + AI odpovědi + možnost rovnou booknout

---

# MVP roadmapa

## MVP 0 – demo / proof of concept

* input: „chci roadtrip 7 dní, začátek v Mnichově, max 300 km denně, preference příroda, autem“
* output: mapa, itinerář, pár možností
* jednoduchý booking hotelu po cestě (affiliate linky)
* sdílení plánu jako odkaz
* zatím bez cenových alertů, kolaborace, paměti uživatele
* cíl: validace, jestli lidi chtějí AI generovaný itinerář

## MVP 1 – použitelná appka

* UI: časopisový look & feel (fotky, itineráře, mapa)
* chat-bublina pro dotazy a změny plánu
* integrace: Google Maps API (časy cest), Booking/Skyscanner affiliate (hotely, lety)
* funkce: uložení itineráře, sdílení, první trip zdarma → pak předplatné/kredity
* cíl: měřit engagement, jestli se lidi vracejí a tvoří víc tripů

## MVP 2 – early produkt

* sledování cen (alerty)
* extra informace k místům (časopisový obsah)
* více variant tripu (AI generuje alternativy)
* kolaborace nad tripy (hlasování o zastávkách)
* booking všeho (lokální zážitky, vstupy, auta)
* nabídky restaurací / míst k jídlu
* dlouhodobá paměť pro uživatele → personalizace
* AI sama v pozadí vytváří tripy a nabízí je jako doporučení
* monetizace: předplatné + provize z bookingů + upsell

---

# pricing

* **SaaS model**

  * free tier = 1 trip zdarma
  * premium 5–10 €/měsíc → zahrnuje tripy, alerty, paměť, AI kredity
  * power-user upsell = extra kredity pro heavy use (víc alertů, víc tripů)

---

# adopce

* **Early adopters** = multi-hop plánovači (mototripy, roadtripy, digitální nomádi) → aktuálně nemají dobrý nástroj
* **Casual users** = lidi s kratšími výlety, kteří si rádi hrají s itineráři
* **Snění / inspirace** = uživatelé, co si tvoří plány i bez reálné cesty (share na soc. sítě → virální růst)

---

# tech

* **Data ingestion**

  * Google Maps, Booking/Skyscanner, externí zdroje zážitků
* **Planning engine**

  * výpočty vzdáleností, časy, validace itineráře
  * AI orchestruje, ale musí mít tvrdá data o dopravě
* **Knowledge / embeddings**

  * fragmentace a opakované použití cest
  * kombinace předpočítaných embeddingů s novými vyhledáváními
  * Thompson sampling s Beta–Bernoulli pro optimalizaci doporučení
* **UX layer**

  * časopisové UI + chat jako doplněk

