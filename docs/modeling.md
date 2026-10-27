# Modeling

## Document

- **Document Status:** In Review
- **Datum:** 27 oktober 2026
- **Auteur(s):** Jochem Janssen | Rick Voermans
- **Project:** Tumor detectie

---

## 1. Modelselectie & Strategie

### 1.1 Klinische Eisen vs. Trainingssnelheid

In de medische wereld (zoals de identificatie van hersentumoren op MRI-scans) geldt dat **hoge betrouwbaarheid (Recall en F1-score) absolute prioriteit heeft boven trainingstijd**. Het model moet zo zeker mogelijk zijn op wat het teruggeeft en daar hebben we de prioriteiten op gelegd.

---

## 2. Model 1: EfficientNetV2-S

### 2.1 Waarom dit model past bij de casus

EfficientNetV2-S maakt gebruik van _progressive learning_ en geoptimaliseerde _fused-MBConv_ lagen. Dit model is uitermate geschikt voor medische MRI-scans omdat het uitblinkt in het opvangen van zowel fijne textuurverschillen (zoals de infiltrerende, grillige randen van gliomen) als globale vormkenmerken (zoals de symmetrie van de hersenen en de sella turcica bij hypofysetumoren).

### 2.2 Preprocessing & Augmentatie

- **Input Formaat:** `384x384` pixels (conform `EfficientNet_V2_S_Weights.DEFAULT` transformaties).
- **Trainingsaugmentatie:** Lichte willekeurige rotatie (`transforms.RandomRotation(degrees=5)`) om variatie in hoofdkanteling te simuleren zonder de anatomische verhoudingen te vervormen.
- **Normalisatie:** Standaard ImageNet normalisatie (`mean=[0.485, 0.456, 0.406]`, `std=[0.229, 0.224, 0.225]`).
- **Reproduceerbaarheid:** Vaste seed ingesteld (`SEED = 42`) voor Python, NumPy en PyTorch CPU/CUDA.

### 2.3 Trainingsconfiguratie & Two-Phase Training Strategy

Het model is getraind met behulp van Transfer Learning in twee opeenvolgende fases om de vooraf getrainde ImageNet-gewichten niet te beschadigen:

- **Loss Functie:** `CrossEntropyLoss()`
- **Batch Size:** 16 op GPU (CUDA) / 8 op CPU
- **Fase 1: Frozen Backbone (Head Training)**
  - _Instelling:_ `model.features` lagen bevroren (`requires_grad = False`).
  - _Duur:_ 3 Epochs (`HEAD_EPOCHS = 3`).
  - _Optimizer:_ `AdamW` op de nieuwe classificatielaag (`model.classifier`), $LR = 1e-3$, $Weight\ Decay = 1e-4$.
- **Fase 2: Full Network Fine-Tuning**
  - _Instelling:_ Alle lagen ontgrendeld (`requires_grad = True`).
  - _Duur:_ 7 Epochs (`FINETUNE_EPOCHS = 7`).
  - _Optimizer:_ `AdamW` op alle parameters, $LR = 1e-4$, $Weight\ Decay = 1e-4$.

### 2.4 Model Checkpointing & Documentatie

Tijdens de training wordt na elke epoch de _validation loss_ geëvalueerd. Zodra de validatieloss verbetert, wordt het volledige model-state opslagen inclusief metadata (`architecture`, `class_names`, `class_to_idx`, `input_size`, `seed`).

- **Best Validation Loss:** 0.1806 (Behaald in Fine-tuning Epoch 3)
- **Checkpoint Locatie:** `trained_models/efficientnet_v2_s_best.pth`
- **Historie Manifest:** `trained_models/efficientnet_v2_s_training_history.csv`

---

## 3. Model 2: MobileNetV2

### 3.1 Waarom dit model past bij de casus

MobileNetV2 is ontworpen als een extreem lichtgewicht en efficiënte architectuur met _inverted residuals_ en _linear bottlenecks_. In dit project onderzoeken we met MobileNetV2 of een kleiner model met aanzienlijk minder parameters en een extreem lage inferentietijd voldoende nauwkeurigheid kan behalen in vergelijking met zwaardere modellen.

### 3.2 Preprocessing & Augmentatie

- **Input Formaat:** `224x224` pixels (conform `MobileNet_V2_Weights.DEFAULT`).
- **Augmentatie:** Lichte willekeurige rotatie (`transforms.RandomRotation(degrees=5)`).
- **Normalisatie:** Standaard ImageNet normalisatie (`mean=[0.485, 0.456, 0.406]`, `std=[0.229, 0.224, 0.225]`).

### 3.3 Trainingsconfiguratie

- **Loss Functie:** `CrossEntropyLoss()`
- **Batch Size:** 32 / 16
- **Fase 1 (Head Training):** 3 Epochs (`AdamW`, $LR = 1e-3$, $Weight\ Decay = 1e-4$)
- **Fase 2 (Fine-Tuning):** 7 Epochs (`AdamW`, $LR = 1e-4$, $Weight\ Decay = 1e-4$)

### 3.4 Model Checkpointing

- **Checkpoint Locatie:** `trained_models/mobilenet_v2_best.pth`
- **Historie Manifest:** `trained_models/mobilenet_v2_training_history.csv`

---

## 4. Model 3: ResNet18 (Technical Baseline)

### 4.1 Waarom dit model past bij de casus

ResNet18 is een klassiek en stabiel Convolutional Neural Network met 18 lagen dat gebruikmaakt van _residual connections_ (skip-connections). Het dient in dit project als de diepe **technische baseline** om te beoordelen of modernere architecturen (zoals EfficientNetV2-S) daadwerkelijk meerwaarde bieden voor deze specifieke MRI-dataset.

### 4.2 Preprocessing & Augmentatie

- **Input Formaat:** `224x224` pixels (conform `ResNet18_Weights.DEFAULT`).
- **Augmentatie:** Lichte willekeurige rotatie (`transforms.RandomRotation(degrees=5)`).
- **Normalisatie:** Standaard ImageNet normalisatie (`mean=[0.485, 0.456, 0.406]`, `std=[0.229, 0.224, 0.225]`).

### 4.3 Trainingsconfiguratie

- **Loss Functie:** `CrossEntropyLoss()`
- **Batch Size:** 32
- **Fase 1 (Head Training):** 3 Epochs (`AdamW`, $LR = 1e-3$, $Weight\ Decay = 1e-4$)
- **Fase 2 (Fine-Tuning):** 7 Epochs (`AdamW`, $LR = 1e-4$, $Weight\ Decay = 1e-4$)

### 4.4 Model Checkpointing

- **Checkpoint Locatie:** `trained_models/resnet18_best.pth`
- **Historie Manifest:** `trained_models/resnet18_training_history.csv`

---

### Bronnen

- **PyTorch Torchvision Models:** EfficientNetV2-S, MobileNetV2, ResNet18 Pretrained Weights & Transforms.
- **Dataset Splitting:** Pre-partitioned train (`1991`), validation (`427`), and test (`427`) CSV manifests in `prepared_data/`.
