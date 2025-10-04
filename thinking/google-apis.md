### 🔑 Google Maps Platform – relevantní části

1. **Places API / Places API (New)**

   * **Place Search** → vyhledávání POI podle klíčových slov, kategorie, geolokace.
   * **Place Details** → název, adresa, opening hours, fotky, ratingy, recenze (omezeně).
   * **Nearby Search** → co je v určitém radiusu (pláže, bary, kavárny, restaurace).
   * **Text Search** → vyhledání podle textového dotazu („cenote near cancun“).

2. **Maps JavaScript API / Static Maps / Directions API**

   * **Directions API** → kolik trvá přesun mezi body itineráře.
   * **Distance Matrix** → optimalizace pořadí (když máš více POI).

3. **Geocoding / Reverse Geocoding API**

   * když potřebuješ konvertovat adresu ↔ souřadnice (pro mapping aktivit).

---

### 🔹 Použití

* **Places API** je klíč → dá nám seznam *pláže, cenoty, bary, restaurace, markety…* v okolí Cancúnu.
* **Details** k tomu přidají fotky, otevírací dobu, rating, user reviews (ale jen část, Google si recenze hlídá).
* **Directions / Distance Matrix** → reálné cestovní časy, aby itinerář nebyl nesmyslný.
* **Geocoding** je support, abychom mohli míchat Viator (má lat/lon) + Google (místa).

---

⚠️ Pozor na pricing: Places a Directions se účtují per request