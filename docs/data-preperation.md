# Data Preparation

## Document

- **Document Status:** In Review
- **Datum:** 27 oktober 2026
- **Auteur(s):** Jochem Janssen | Rick Voermans
- **Project:** Tumor detectie

---

## 1. Doelstelling & Overzicht Data-voorbereiding

De data-voorbereidingsfase heeft als doel de ruwe dataset van 3.096 afbeeldingen om te zetten in een schone, betrouwbare en gestructureerde dataset. Hierbij staan twee kernproblemen centraal die tijdens de Data Understanding zijn vastgesteld:

1. **Data Leakage:** Het voorkomen dat identieke afbeeldingen in zowel de trainings- als validatie/testsets terechtkomen.
2. **Klasse-onbalans:** Het correct omgaan met de scheve verhoudingen (met name het lage aantal unieke beelden in de `normal` klasse).

---

## 2. Inventarisatie van de Data

In de eerste stappen is de mappenstructuur ingelezen, de reproduceerbaarheid vastgelegd met `SEED = 42` en de integriteit van de bestanden gecontroleerd.

- **Totaal aantal ingelezen afbeeldingen:** 3.096.
- **Bestandsformaat & Modus:** 100% JPEG, RGB-kleurmodus.
- **Onverwachte / Beschadigde bestanden:** 0.

---

## 3. Deduplicatie & Voorkomen van Data Leakage

### 3.1 Waarom Deduplicatie Noodzakelijk Is

Wanneer exact dezelfde afbeeldingen in zowel de training- als de testset terechtkomen, kan het model deze beelden tijdens de training "onthouden". Dit leidt tot een kunstmatig hoge overfitting, terwijl het model in de praktijk slecht presteert op nieuwe MRI-scans.

### 3.2 Methode: SHA-256 Hashing

Om exact identieke bestanden op te sporen, is voor elke afbeelding een **SHA-256 cryptografische hash** berekend over de binair inhoud van het bestand.

- **Controle op Label-conflicten:** Er is gecontroleerd of identieke bestanden verschillende klassenlabels hadden. Er zijn **0 labelconflicten** aangetroffen.
- **Deduplicatie-regel:** Van elke groep met identieke hashes is uitsluitend het eerste bestand behouden (`keep='first'`), de overige kopieën zijn verwijderd uit de trainingsverzameling.

### 3.3 Resultaten van de Deduplicatie

| Statistiek                        | Aantal Afbeeldingen |
| :-------------------------------- | :------------------ |
| **Origineel aantal afbeeldingen** | 3.096               |
| **Unieke bestands-hashes**        | 2.845               |
| **Aantal duplicaat-groepen**      | 217                 |
| **Verwijderde extra kopieën**     | **251**             |

---

## 4. Klasse-verdeling Na Deduplicatie

Na het verwijderen van de duplicaten is de verdeling van de unieke afbeeldingen over de vier klassen opnieuw geanalyseerd:

| Klasse                 | Unieke Afbeeldingen | Percentage (%) |
| :--------------------- | :------------------ | :------------- |
| **`glioma_tumor`**     | 885                 | 31,11%         |
| **`pituitary_tumor`**  | 834                 | 29,31%         |
| **`meningioma_tumor`** | 816                 | 28,68%         |
| **`normal`**           | 310                 | 10,90%         |
| **Totaal**             | **2.845**           | **100,00%**    |

Conclusie: De tumorklassen zijn onderling goed gebalanceerd (~29-31% per klasse), maar de `normal` klasse vertegenwoordigt slechts ~10,9% van de unieke data. Dit bevestigt de noodzaak om tijdens de modellering gebruik te maken van _class weights_ of gerichte data-augmentatie.

---

## 5. Dataset Splitting Strategy

Om het model objectief te kunnen evalueren, is de unieke dataset van 2.845 afbeeldingen opgesplitst in drie vaste deelverzamelingen:

- **Trainingset (70%):** Voor het optimaliseren van de modelgewichten.
- **Validatieset (15%):** Voor het monitoren van overfitting en hyperparameter-tuning.
- **Testset (15%):** Voor de uiteindelijke, onafhankelijke prestatie-evaluatie.

Er is gebruikgemaakt van **Stratified Splitting** (`stratify=unique_df['class']`), waardoor de verhouding tussen de vier klassen in alle drie de splits exact gelijk blijft.

### Overzicht van de Data-splits

| Split                | `glioma` | `meningioma` | `normal` | `pituitary` | Totaal Afbeeldingen |
| :------------------- | :------- | :----------- | :------- | :---------- | :------------------ |
| **Train (70%)**      | 619      | 571          | 217      | 584         | **1.991**           |
| **Validation (15%)** | 133      | 122          | 47       | 125         | **427**             |
| **Test (15%)**       | 133      | 123          | 46       | 125         | **427**             |
| **Totaal**           | **885**  | **816**      | **310**  | **834**     | **2.845**           |

---

## 6. Data Leakage

Om te garanderen dat de voorbereiding 100% correct is verlopen, is een geautomatiseerde controle uitgevoerd middels programmeer-asserties (`assert`):

1. **Hash Disjointness:** Gecontroleerd is dat de verzameling hashes in de trainingset, validatieset en testset geen enkele overlap vertonen (`isdisjoint`).
2. **Volledigheid:** Het totaal van de drie splits is exact gelijk aan het aantal unieke beelden (2.845).

Resultaat: `Checks passed: no exact-hash overlap between splits.`

---

## 7. Deliverables

De data-voorbereidingsfase heeft de volgende bestanden gegenereerd in de map `prepared_data/` voor gebruik in de modelleringfase (`Modeling`):

- `unique_images.csv`: Manifest van alle 2.845 unieke afbeeldingen met SHA-256 hashes.
- `data_splits.csv`: Volledig overzicht van alle afbeeldingen inclusief de toegewezen split (`train`, `validation`, `test`).
- `train.csv`, `validation.csv`, `test.csv`: Afzonderlijke manifest-bestanden per subset.

---

### Bronnen

- **Bron 1:** Geef informatiebronnen op
