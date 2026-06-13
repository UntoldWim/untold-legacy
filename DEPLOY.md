# Untold Legacy — deployment & beheer

untold-legacy.com live zetten met Decap CMS. Zelfde stack als rt107mm.be:
GitHub (code) + Netlify (hosting) + Combell (domein/DNS) + Decap CMS via DecapBridge.

---

## Wat zit in het deploy-pakket

```
untold-legacy/
├── index.html          ← de website (leest teksten uit content/site.json)
├── content/
│   └── site.json       ← ALLE bewerkbare teksten (door het CMS beheerd)
├── admin/
│   ├── index.html      ← het CMS (Decap)
│   └── config.yml      ← CMS-configuratie (backend nog invullen, zie stap 5)
├── assets/             ← beelden (portret, klantlogo's) en webfonts (vast in code)
└── DEPLOY.md           ← dit bestand
```

Hoe het werkt: de teksten staan in content/site.json. De site leest dat bestand in bij
het laden en plaatst de teksten in de juiste plekken. Decap CMS bewerkt site.json. Beelden
en logo's staan vast in de code (zoals afgesproken, alleen teksten zijn bewerkbaar).

---

## Belangrijk vooraf: op welk account?

Beslis wie eigenaar is van domein, GitHub en Netlify. Mijn advies: domein op naam van
Wim (hij bezit het merk), GitHub en Netlify op een beheeraccount dat je deelt of dat van
jou is zolang jij onderhoudt. Leg dit vast zodat er later geen ruis over eigenaarschap is.

---

## Stap 1 — Domein registreren bij Combell

1. Log in op combell.com.
2. "Domeinnaam registreren" → zoek `untold-legacy.com`.
3. Reken af (een .com kost rond 12 tot 15 euro per jaar).
4. Wacht tot het domein actief in je account staat.
   (Als untold-world.com de paraplu wordt, registreer die nu mee.)

Doe dit eerst. Je hebt het domein nodig voor stap 4.

---

## Stap 2 — Code op GitHub

1. Maak (of gebruik) een GitHub-account.
2. Maak een nieuwe repository, bv. `untold-legacy`.
   Let op: voor DecapBridge en git-gateway werkt het makkelijkst met een
   PUBLIC repository. Een private repo kan ook maar vraagt extra config.
3. Upload de volledige inhoud van het deploy-pakket:
   "Add file → Upload files", sleep index.html, en de mappen content/, admin/, assets/
   plus DEPLOY.md erin. Commit naar de `main` branch.

Belangrijk: behoud de mapstructuur. content/, admin/ en assets/ moeten als mappen in de
repo staan, niet als losse bestanden in de root.

---

## Stap 3 — Hosting op Netlify

1. Log in op netlify.com (gratis tier volstaat).
2. "Add new site → Import an existing project" → GitHub → selecteer je repo.
3. Build settings:
   - Build command: leeg laten
   - Publish directory: `/` (de root)
4. Deploy. Je krijgt een tijdelijke URL zoals `random-naam.netlify.app`.
5. Open die URL en scroll de hele site door. Alles hoort te staan: hero, Purposeful
   Legacy, Corporate Branding, klantlogo's, donkere contactbalk, footer.

---

## Stap 4 — Domein koppelen (Netlify + Combell DNS)

Aanrader is optie A, dezelfde die voor RT107 het soepelst liep.

### Optie A — Netlify DNS (nameservers verleggen, makkelijkst)

1. In Netlify: "Domain settings → Add a domain" → vul `untold-legacy.com` in.
2. Netlify stelt Netlify DNS voor en toont 4 nameservers, bv.:
   ```
   dns1.p01.nsone.net
   dns2.p01.nsone.net
   dns3.p01.nsone.net
   dns4.p01.nsone.net
   ```
3. Ga naar Combell → je domein → "Nameservers" of "Beheer DNS".
4. Kies "Externe nameservers gebruiken" en vul de 4 Netlify-nameservers in.
   Verwijder de Combell-standaard nameservers.
5. Opslaan. Propagatie 1 tot 24 uur.

### Optie B — DNS bij Combell houden

Gebruik dit alleen als er al mail of andere records aan het domein hangen.
1. In Netlify: "Domain settings → Add a domain" → `untold-legacy.com`.
2. Voeg bij Combell de records toe die Netlify toont:
   - `A`-record voor `@` naar Netlify's IP (Netlify toont het, meestal `75.2.60.5`).
   - `CNAME`-record voor `www` naar je `random-naam.netlify.app`.
3. Opslaan. Propagatie 1 tot 24 uur.

### HTTPS

Zodra de DNS klopt regelt Netlify automatisch een gratis SSL-certificaat. Wachten tot het
groen staat onder "Domain settings → HTTPS".

---

## Stap 5 — Decap CMS activeren via DecapBridge

Net als bij RT107: Netlify Identity en Git Gateway zijn deprecated voor nieuwe sites,
dus we gebruiken DecapBridge als auth-laag. Dit geeft Wim een login op
`untold-legacy.com/admin` waar hij de teksten kan aanpassen.

1. Ga naar https://decapbridge.com en maak een account (of log in).
2. Koppel je GitHub: geef DecapBridge toegang tot de repo `untold-legacy`.
3. Maak in DecapBridge een nieuwe site/config aan:
   - Repository: `JOUW-GEBRUIKER/untold-legacy`
   - Branch: `main`
4. DecapBridge geeft je een backend-block om in `admin/config.yml` te plakken.
   Het ziet er ongeveer zo uit (neem de exacte waarden van DecapBridge over):
   ```yaml
   backend:
     name: git-gateway
     repo: JOUW-GEBRUIKER/untold-legacy
     branch: main
     gateway_url: https://...        # van DecapBridge
     auth_endpoint: ...              # van DecapBridge
   ```
5. Open `admin/config.yml` in je GitHub-repo, vervang het backend-block bovenaan door
   wat DecapBridge je gaf, en commit. Netlify deployt automatisch opnieuw.
6. Nodig Wim (en jezelf) uit als gebruiker via het DecapBridge-dashboard. Hij krijgt een
   mail om een wachtwoord in te stellen.
7. Test: ga naar `untold-legacy.com/admin`, log in, open "Website teksten → Alle teksten",
   pas een zin aan en sla op. Decap commit naar de repo, Netlify deployt, en binnen een
   minuut staat de wijziging live.

Let op: zolang je het backend-block niet hebt vervangen door de DecapBridge-waarden werkt
de login op /admin nog niet. Dat is normaal.

---

## Stap 6 — Na livegang

- Test de site op gsm en desktop. Scroll volledig door, check de contactlinks
  (mail opent mailprogramma, telefoon opent bellen, LinkedIn opent het profiel).
- Controleer dat untold-legacy.com én www.untold-legacy.com allebei werken en naar
  https doorsturen.
- Check dat de footer-links naar untold-books.com en untold-africa.com kloppen.
- Test één keer een CMS-edit van begin tot eind zodat je zeker weet dat de flow werkt.

---

## Hoe Wim later teksten aanpast (via het CMS)

1. Ga naar `untold-legacy.com/admin` en log in.
2. Klik "Website teksten → Alle teksten".
3. Pas de gewenste tekst aan in de velden (intro, Purposeful Legacy, Corporate Branding,
   contactgegevens, enzovoort).
4. Klik "Save" en daarna "Publish". Binnen een minuut staat het live.

Beelden, logo's en de layout zitten in de code, niet in het CMS. Daarvoor kom je bij
Karim, of pas je rechtstreeks index.html / de assets-map aan op GitHub.

---

## Tekst aanpassen zonder CMS (alternatief)

Kan ook rechtstreeks: open `content/site.json` op GitHub, klik het potlood-icoon, pas de
tekst aan, commit. Netlify deployt automatisch. Het CMS is gewoon een vriendelijkere
schil rond exact dit bestand.
