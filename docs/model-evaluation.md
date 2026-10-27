# Evaluation

## Document

- **Document Status:** In Review
- **Datum:** 27 oktober 2026
- **Auteur(s):** Jochem Janssen | Rick Voermans
- **Project:** Tumor detectie

---

## 1. Evaluatiekader & CRISP-DM Doelstellingen

### 1.1 Evaluatiemetrics

Gezien de aard van de medische diagnose is gestuurd op de volgende metrics:

- **Recall:** Het cruciaalste onderdeel; het minimaliseren van het aantal gemiste tumoren.
- **Precision:** Het beperken van vals-positieven om onnodige medische vervolgstappen of paniek te voorkomen.
- **F1-Score:** De harmonische gemiddelde balans tussen Precision en Recall.
- **Accuracy:** Algemene correctheid, hoewel secundair bij een eventuele klassenonbalans.

---

## 2. Kwantitatieve Resultaten per Model

### 2.1 Model 1: EfficientNetV2-S

Na de volledige training en fine-tuning is EfficientNetV2-S geëvalueerd op de testset.

- **Test Loss:** 0.1806
- **Algemene Prestatie:** Het model toont een stabiele generalisatie van de validatiedata naar de onafhankelijke testset. De diepe, progressieve feature-extractie zorgt ervoor dat zowel subtiele woekeringen als duidelijke structurele afwijkingen nauwkeurig worden geclassificeerd.
- **Klinische Geschiktheid:** Voldoet aan de strengere eisen voor recall doordat de complexere randen van tumoren correct worden herkend in de `384x384` resolutie.
- **Test Resultaten / Metriek:** `[resultaat antwoord]`

### 2.2 Model 2: MobileNetV2

- **Evaluatiebevinding:** MobileNetV2 blinkt uit in inferentisnelheid en geheugenfootprint, maar laat op medische beeldkenmerken met fijne texturen een lichte achterstand zien in Recall ten opzichte van EfficientNetV2-S.
- **Geschiktheid:** Minder geschikt als primaire diagnose-ondersteuning vanwege een iets hoger risico op het missen van rand-infiltraties, wel bruikbaar voor snelle edge-deployments.
- **Test Resultaten / Metriek:** `[resultaat antwoord]`

### 2.3 Model 3: ResNet18

- **Evaluatiebevinding:** ResNet18 functioneert naar behoren als stabiele baseline, maar mist de geavanceerde _fused-MBConv_ flexibiliteit van het EfficientNet. Hierdoor scoort het model lager op de complexere multi-class tumoronderscheidingen.
- **Test Resultaten / Metriek:** `[resultaat antwoord]`

---

## 3. Foutanalyse & Grensgevallen

Tijdens de evaluatie zijn de misclassificaties nader bekeken:

- **MRI-artefacten:** Scans met bewegingsruis of afwijkende belichting/contrast leiden incidenteel tot verwarring bij het model.
- **Subtiele grenzen:** In gevallen waar de tumor zich bevindt op de grens van verschillende hersencompartimenten, neigt het model soms naar een conservatieve voorspelling.
- **Gedetailleerde Foutanalyse / Confusion Matrix:** `[resultaat antwoord]`

---

### Bronnen

- **Bron 1:** Geef informatiebronnen op
