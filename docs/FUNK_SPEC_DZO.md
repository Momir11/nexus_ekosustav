### FUNKCIONALNA SPECIFIKACIJA: Dirigentovo Zapovjedno Okruženje (DZO) v1.0

**Verzija:** 1.0 **Status:** Funkcionalna Specifikacija **Primatelj:** Tim za Implementaciju

---

**Dio 1/3: Arhitektura i Filozofija**

**1.1. Temeljna Arhitektura**

DZO (Dirigentovo Zapovjedno Okruženje) je minimalistička web aplikacija dizajnirana kao \"Filter za Sintezu\". Njezin cilj je zaštititi stanje \"dubokog rada\" Dirigenta filtriranjem 99% operativne buke od AI agenata.

**Komponente:**

1. **Frontend (React/TypeScript):**
   - Single Page Application (SPA) s routingom (React Router).
   - Komponente: AteninKanal, ZapovjedniKanal, JanusovDnevnik.
   - Real-time komunikacija s backendom via WebSockets (Socket.io).
   - UI Framework: Material-UI ili Ant Design za konzistentnost.

2. **Backend (Node.js/Express):**
   - RESTful API za komunikaciju s AI agentima (A-1, Atena, Janus).
   - Autentifikacija: JWT tokens za Dirigenta.
   - Konekcija na AI agente: HTTP API pozivi ili gRPC.
   - Baza podataka: MongoDB ili PostgreSQL za decision log.

3. **Konektori za Agente:**
   - **AtenaKonektor:** Prima brifinge od Atene (JSON format: {timestamp, rizik, preporuka}).
   - **JanusKonektor:** Šalje naredbe Janusu (JANUS_COMMAND format).
   - **A1Konektor:** Prima status izvješća od A-1 (izbjegavati direktnu komunikaciju).

**1.2. Filozofija Sučelja (Principi Dizajna)**

- **Asinkronost:** Sve komunikacije su asinkrone. Dirigent ne čeka odgovore.
- **Minimalizam:** Zadani izgled je prazan ekran s jednim \"Triage Inbox\" u Active Mode.
- **Structure-First:** UI forsira strukturirane odluke (gumbe \"ODOBRI\"/\"ODBIJ\").
- **State-Awareness:** UI se mijenja temeljem Janusove pouzdanosti (Shadow vs Active Mode).

---

**Dio 2/3: Specifikacija UI/UX za Shadow i Active Mode**

**2.1. Shadow Mode (Početni Način)**

Shadow Mode je za trening Janusa. UI ima tri stupca (kao Outlook ili Gmail):

- **Lijevi Stupac: Atenin Kanal (Intuicija)**
  - Prikazuje: Jutarnji Kompas (dnevni brifing od Atene).
  - Format: Markdown brifing s rizicima i upozorenjima.
  - Akcija: Dirigent može \"Označiti kao Pročitano\" ili \"Podijeliti s Janusom\".

- **Srednji Stupac: Zapovjedni Kanal (Volja)**
  - Prikazuje: Naredbe koje Dirigent izdaje A-1 agentu.
  - Forma: Text input s autocomplete (JANUS_COMMAND predlošci).
  - Akcija: \"Pošalji Naredbu\" šalje direktno A-1.

- **Desni Stupac: Janusov Dnevnik Učenja (Model)**
  - Prikazuje: Kako Janus uči iz Dirigentovih odluka.
  - Format: Logovi tipa \"Potvrđeno: Kada Atena prijavi 'Zamku #3', Dirigent preferira re-prioritizaciju. Pouzdanost +5%.\".
  - Akcija: \"Pregled Modela\" otvara detaljni view s trenutnom pouzdanošću (%).

**Prijelaz u Active Mode:** Automatski kada Janus dosegne 95% pouzdanosti, ili manuelno Dirigentovim gumbom.

**2.2. Active Mode (Ciljani Način)**

Active Mode je za produktivnost. UI se transformira u jedan \"Triage Inbox\" (kao email inbox):

- **Jedini Element: Eskalacija Inbox**
  - Prikazuje: Jednu stavku po stavci (ne grupirano).
  - Format Svake Stavke: Sažet brifing s analizom, rizikom, koristi i preporukom.
  - Gumbe: \"ODOBRI\" (zeleno) ili \"ODBIJ\" (crveno).
  - Akcija: Klik na gumb šalje odluku Janusu i briše stavku.

- **Notifikacije:** Push notifikacije za visoke prioritete (npr. \"Eskalacija: Rizik burnouta\").

- **Fallback:** Ako Janus ne šalje eskalacije 24h, UI vraća u Shadow Mode za provjeru.

---

**Dio 3/3: Specifikacija Sigurnosti, State Managementa i Epic-i**

**3.1. Sigurnost (Epic 1)**

- **Autentifikacija:** Multi-factor authentication (2FA) za Dirigenta.
- **Autorizacija:** Role-based access (Admin, Developer, Analyst).
- **Enkripcija:** TLS 1.3 za sve komunikacije.
- **Audit Log:** Sve odluke i komunikacije logirane (GDPR compliant).

**3.2. State Management (Epic 2)**

- **Frontend State:** Redux ili Zustand za UI state.
- **Backend State:** Redis za session state, MongoDB za decision log.
- **Sync:** Real-time sync između agenata i DZO.

**3.3. Model Učenja (Epic 3)**

- **Janusov Model:** ML model koji uči iz Dirigentovih odluka.
- **Feedback Loop:** Dirigent može \"Korigirati\" model s komentarima.
- **Metrics:** Dashboard za % pouzdanosti i false positives.

---

**Zaključak**

Ova specifikacija služi kao temelj za implementaciju DZO-a. Počnite s Epic 1 (sigurnost), zatim Epic 2 (state), na kraju Epic 3 (model). Testirajte s mock agentima prije produkcije.