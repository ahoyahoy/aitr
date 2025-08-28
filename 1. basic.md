# AI cestovka - basics

## Problémy

* **Legislativa**

  * klasická cestovka = povinné pojištění proti úpadku, spousta regulací
  * pokud půjdeme cestou **zprostředkovatele** (affiliate / whitelabel), regulace je menší, ale zároveň nižší marže

* **Support**

  * zákazníci mají nulovou toleranci → jakýkoli problém (zpoždění, storno) hází vinu na prostředníka
  * příklad **Kiwi.com** - obří růst kvůli supportu, ale reputace nejhorší na světě

* **Marže**

  * lety a ubytování mají nízkou provizi
  * pro B2C by bylo potřeba jít na velký objem nebo přidat vlastní přidanou hodnotu (předplatný appky třeba)

* **Konkurence**

  * existují desítky pokusů o AI cestovky, většina bez úspěchu
  * konverzační UI samo o sobě nestačí → lidi raději kliknou v hezkém UI než si „povídat“ s chatbotem
    - chat konverzace je ve skutečnosti frikce
    - uživatel vybírá destinace pocitově z fotek
  * **hlasové ovládání** je budoucí diferenciátor (až to přijde do mainstreamu - Google repráky s Gemini jsou už skoro ready)
    - tam to vidím tak, že řeknu AI, že chci někam jet a ono mi pošle zprávu s nabídkou
  
  ☢️ Všichni největší hráči sem směřují.
    - google má travel platformu a má AI agenta...
    - booking, expedia, všichni už agenty řeší
      - většina je používá na úkoly pod povrchem... například sumarizace recenzí (což je něco, co bychom museli dělat taky)

* **B2C specifika**

  * zákazníci extrémně citliví na **cenu** - stačí, že Booking ukáže stejný hotel levněji a reputace padá
  * uživatelé si vždy porovnávají víc webů → důvěra se získává jen těžko
    - uživatel navštíví pruměrně 277 webů před tím, než si vybere dovolenou (https://www.hospitality.today/article/consumers-visit-277-websites-before-booking-travel)
  * pohodlí (méně klikání) vyhledá jen malá část lidí, a to jen tehdy, když důvěra ve službu je vysoká
  * extrémně náročný **support** - B2C klienti mají vyšší očekávání než reálné možnosti
  * větsina lidí cestuje jednou/dvakrát ročně

* **Cílovky**

  * Každá potřebuje jiný produkt:
    * **Lidi, co jedou přes cestovku** - čekají full-service, support, někdo to za ně zařídí.
      - starší a míň technická skupina, mín ochoty zkoušet technologie
    * **Lidi, co si organizují sami** - čekají svobodu, nejlepší ceny, inspiraci a pohodlí.
      - jsou to lidi, co použijí kiwi na vyhledání spojů a pak si to jdou koupit na ryanairu
      - multi-hop plánování zabírá příliš času, takže to by uvítali asi
      - nemají rádi resorty

* **Data & integrace**

  * lokální zážitky (GetYourGuide, Viator) pokrývají jen část trhu
  * cestovky mají v resortech domluvený místňáky osobně

* **Platební toky & refundace**

  * při plném OTA modelu držíme cizí peníze a neseme riziko chargebacků
  * Kiwi.com typicky čelí tisícům reklamací denně (a obřímu hejtu a dluhu)

* **Sezónnost & LTV**

  * lidé cestují 1–2× ročně → retention mizerná
  * vysoké akviziční náklady (dražší než SaaS)

* **Marketing & brand**

  * důvěra v novou značku je minimální → lidi nepošlou tisíce EUR neznámému webu
  * PPC na travel je jedny z nejdražších na trhu

* **Krizové situace**

  * zrušené lety, počasí, stávky → vyžadují 24/7 call centrum
  * bez silného supportu reputace padá okamžitě

* **UX & očekávání uživatelů**

  * inspirace funguje hlavně přes fotky, mapy a itineráře
  * chat s AI = frikce, ne zjednodušení

* **Trust & odpovědnost AI**

  * chyba v doporučení (špatný hotel, datum) = reálné finanční škody


## Příležitosti

* **Multi-hop plánování**

  * většina lidí neumí poskládat složité lety / vlaky / busy → AI může ušetřit hodiny hledání
  * Kiwi.com na tom vyrostlo, ale má mizerný UX a support

* **Inspirace & discovery**

  * AI může personalizovat výběr destinací podle preferencí (počasí, ceny, aktivity)
  * klasické OTA mají top destinace generické → šance nabídnout „long tail“ míst
    * klasické OTA (Booking, Expedia) tlačí jen top místa → Paříž, Řím, Barcelona… protože tam je nejvyšší objem a nejjednodušší marketing
    * menší města nebo specifické lokality (např. surf spoty, horské vesnice, lázně, kulturní akce) jsou schované v databázi, ale uživatel se k nim těžko prokliká
    * AI může vytahovat právě tyto méně známé destinace podle preferencí uživatele (např. „chci slunce, ale ne přeplněné pláže“ → nabídne Azory, ne Mallorcu)
    * pro OTA je to marginálně nevýhodné → menší objem, méně recenzí, menší provize → proto je to prostor pro menší hráče
    * uživatelům to může přinést unikátní zážitek, který by na Bookingu nikdy nenašli → **diferenciace od mainstreamu**


* **Itineráře & automatizace**

  * generování den po dni programů (kombinace dopravy, aktivit, restaurací)
    - kde ale pak získat provizi
  * možnost kombinovat více zdrojů (Google Maps, TripAdvisor, eventy) do jednoho přehledu

* **Zážitky & lokální aktivity**

  * potenciál v last-minute (už jsem v destinaci a chci něco podniknout dnes)

* **Premium segment**

  * lidi, co chtějí „někdo to zařídí za mě“ → AI jako travel concierge
  * vyšší marže než low-cost segment, možnost předplatného

* **B2B příležitosti**

  * firemní cesty = opakující se poptávka, méně citliví na cenu než B2C
  * integrace s kalendářem, preferencemi zaměstnance, schvalovacími procesy

* **Voice & hands-free ovládání**

  * příchod Google/Apple hlasových agentů → připravenost na tento shift
  * travel je přirozený use-case („najdi mi let do Barcelony na příští víkend“)

* **Data & agregace**

  * sumarizace recenzí, extrakce sentimentu, AI filtry („hotel vhodný pro digitální nomády“)
  * obří množství obsahu → AI dokáže ušetřit uživateli čas
