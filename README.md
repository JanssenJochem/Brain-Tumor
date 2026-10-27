# Brain Tumor Detection (Avans: Data Science Challenge)

This repository contains the code, data preparation workflows, and trained models developed for the **Avans Data Science Challenge**. The objective of the project is to automatically analyze and classify brain MRI scans into four distinct categories using deep learning models.

---

## 1. Project Overview & Deliverables

The goal of this project is to assist medical scan classification by categorizing MRI images into four classes:

**Tested tumors:**

- Normal
- Glioma
- Meningioma
- Pituitary

### Primary Project Deliverables

- **Data Pipeline**: Image deduplication via SHA-256 hashing and reproducible stratified dataset splits.
- **Trained Models**: Saved PyTorch weights (`.pth`) for three trained deep learning architectures (EfficientNetV2-S, MobileNetV2, ResNet18).
- **Evaluation Outputs**: Full training logs (`.csv`), test set predictions, and lists of misclassified samples.
- **Documentation**: Crisp-DM documentation and markdown files stored under `docs/`.

---

## 2. How to Set It Up

Because this project uses Jupyter Notebooks (`.ipynb`), you can easily run and review everything without complex installations:

1. **Download or Clone** the repository to your computer:
   `https://github.com/JanssenJochem/Brain-Tumor`
2. **Install Python** and required packages
   - PyTorch
   - Torchvision
   - Pandas
   - Numpy
   - Jupyter.
3. **Open Jupyter Notebook** or **VS Code** in the project folder.
4. Open any of the training or evaluation notebooks (like `03_Evaluate_EfficientNetV2-S.ipynb`) to view the results, or check the `trained_models/` folder directly for pre-computed test outcomes.

---

## 3. Dataset Source

The image dataset used in this project is sourced from Kaggle:

- **Dataset**: [Brain Tumors 256x256 on Kaggle](https://www.kaggle.com/datasets/thomasdubail/brain-tumors-256x256) (by Thomas Dubail)
- **Dataset Size**: 3,096 total images across the four classes, standardized to 256x256 resolution.
