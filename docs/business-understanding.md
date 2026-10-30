# Business Understanding

## Document

- **Document Status:** In Review
- **Datum:** 27 oktober 2026
- **Auteur(s):** Jochem Janssen | Rick Voermans
- **Project:** Tumor detectie

<!-- ---

## Revision

| Versie  | Datum      | Auteur            | Wijzigingsbeschrijving                                        | Status    |
| :------ | :--------- | :---------------- | :------------------------------------------------------------ | :-------- |
| **0.1** | 26-10-2026 | Data Science Team | Eerste opzet zakelijke context                                | Concept   |
| **1.0** | 27-10-2026 | Data Science Team | Verfijning naar zuivere Business Understanding & domeinkennis | In Review | -->

---

## 1. Zakelijke Context & Probleemstelling

### 1.1 Achtergrond

Het handmatig beoordelen van scans vereist specifieke expertise. Hoge werkdruk kan leiden tot vertragingen in het behandelproces of interpretatiefouten.

### 1.2 Doel van de Casus

Het doel van dit project is het ontwikkelen van een AI-ondersteuningssysteem dat automatisch MRI-hersenscans kan classificeren in vier categorieën:

1. `normal`
2. `glioma`
3. `meningioma`
4. `pituitary`

### 1.3 Succescriteria

- **Medisch Succes:** Het minimaliseren van het aantal gemiste tumoren op een efficiente manier. De betrouwbaarheid van de detectie van tumor heeft absolute prioriteit boven snelheid.
- **Functioneel Succes:** Een model dat onderscheid kan maken tussen subtiele weefselstructuren en randafwijkingen van de verschillende tumortypes.

---

## 2. Domeinkennis

Om de visuele patronen te begrijpen die relevat zijn voor deze casus, zijn de medische eigenschappen van de vier klassen in kaart gebracht op basis van grijswaarden MRI-scans:

### 2.1 Vergelijkingstabel Tumoreigenschappen

| Feature / Eigenschap         | Normal Brain                   | Glioma                                         | Meningioma                                    | Pituitary Tumor                                       |
| :--------------------------- | :----------------------------- | :--------------------------------------------- | :-------------------------------------------- | :---------------------------------------------------- |
| **Locatie / Herkomst**       | N/A (Gezond weefsel)           | In het hersenweefsel zelf (intraparenchymaal)  | Buitenste hersenvliezen (dura/arachnoidea)    | Hypofyse aan de schedelbasis (sella turcica)          |
| **Randen / Vorm**            | Strakke, symmetrische anatomie | Infiltrerend, grillige/vage randen             | Scherp omlijnd, rond/lobbig, _dural tail_     | In de sella turcica, vaak zandloper/sneeuwpopvorm     |
| **Contrastversterking**      | Normale doorbloeding           | Variabel (ringvormig bij graad 4 glioblastoom) | Sterke, egale/uniforme versterking            | Matig tot sterk, vergroot de sella turcica            |
| **Omliggend Oedeem (vocht)** | Geen                           | Vaak uitgebreid vingervormig vocht (oedeem)    | Variabele druk-oedeem direct rondom de massa  | Minimaal, tenzij doorgroei naar boven                 |
| **Signaaleigenschappen**     | Basale lage waarden            | Bovenmatige celactiviteit / verhoogde waarden  | Lagere signaalwaarden dan hooggradige gliomen | Lokaal afwijkend weefselprofiel op specifieke locatie |

---

### 2.2 Visuele Kenmerken op Grijswaarden MRI-Scans

#### 1. Glioma

- **Locatie:** Bevindt zich midden in het hersenweefsel zelf, vaak in de grote hersenen.
- **Randen:** De randen zijn vaag, grillig en onduidelijk. De tumor vloeit over in het gezonde weefsel doordat tumorcellen tussen gezonde cellen doorgroeien.
- **Grijswaarden:**
  - Op basisscans is de tumor vaak te herkennen als een vage, donkergrijze vlek.
  - Op vochtscans is een grote, helderwitte structuur zichtbaar door tumorvocht en omliggende vochtophopingen (oedeem).
  - Bij een agressief glioblastoom is vaak een donkergrijs/zwart centrum (necrose) zichtbaar met een felwitte, grillige rand eromheen.

#### 2. Meningeoom

- **Locatie:** Bevindt zich aan de buitenrand van de hersenen, vlak tegen de schedel aan, of tussen de twee hersenhelften.
- **Randen:** Scherp, rond of lobbig. De grens tussen tumor en hersenweefsel is duidelijk afgebakend. De tumor duwt het hersenweefsel opzij, vaak met een dun, donker stripje van hersenvocht ertussen.
- **Grijswaarden:**
  - Op gewone scans heeft de tumor vaak exact dezelfde kleur grijs als de hersenschors, waardoor hij met name opvalt door zijn afwijkende vorm.
  - Na contrastmiddel kleurt de hele tumor egaal en fel wit.
  - **Typisch kenmerk:** Een scherpe, witte grijswaarden-streep in het vlies die als een staartje langs de schedelwand loopt vanaf de tumor.

#### 3. Pituitary Tumor / Hypofysetumor (Aan de schedelbasis)

- **Locatie:** Onderin het hoofd, precies in het midden in een holte van de schedelbasis, recht onder de oogzenuwen.
- **Randen:** Goed omlijnd en ingesloten door botstructuren. Bij groei vormt het vaak een karakteristieke zandloper- of sneeuwpopvorm omhoog.
- **Grijswaarden:**
  - Valt op doordat de normale, kleine hypofyse vervormd of vergroot is.
  - Is op grijswaarden vaak iets donkerder of asymmetrisch ten opzichte van gezond weefsel.
  - Binnenin zijn soms donkere vlekken (cysten/bloedinkjes) of lichte kalkspatjes zichtbaar.

#### 4. Normale hersenen (Symmetrie en structuur)

- **Anatomie:** Kenmerkt zich door een sterke links-rechts symmetrie. Structuren in de linkerhersenhelft weerspiegelen die in de rechterhersenhelft.
- **Grijswaarden:** Helder en herkenbaar contrast tussen de buitenste hersenschors (grijze stof = middengrijs) en binnenbanen (witte stof = afwijkende grijstint).
- **Hersenvocht:** Het vocht in de ventrikels (holtes in het midden) is egaal en symmetrisch zonder vervormingen of druk van buitenaf.

---

## 3. Randvoorwaarden van de Casus (Constraints & Scope)

- **Domein-specifieke Eis:** Het model moet rekening houden met subtiele grijswaarden- en vormverschillen. Fouten waarbij een gezonde scan als tumor wordt aangemerkt (False Positive) zijn vervelend, maar fouten waarbij een tumor over het hoofd wordt gezien (False Negative) zijn klinisch onaanvaardbaar.
- **Contextuele Afweging Snelheid vs. Accuraatheid:** In een medische workflow is een reactietijd van een paar seconden per scan volkomen acceptabel, mits dit resulteert in een hoge betrouwbaarheid van de diagnose.

---

### Bronnen

- **Bron 1:** Geef informatiebronnen op
