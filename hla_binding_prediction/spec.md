# Project Gamma Specification
**Path:** `hla_binding_prediction`

## Global Project Guidelines
All coding within this project **MUST** strictly adhere to the guidelines outlined in [`coding_best_practices.md`](../coding_best_practices.md).

## Project Structure
The computational pipeline will be split across three distinct Jupyter Notebooks to enforce modularity and clear execution sequences:

### Colab 1: Data Preprocessing
- **File:** `preprocessing.ipynb`
- **Input:** `data/raw/mhc_ligand_table_export.csv`
- **Output:** `data/processed/training_data.csv`
- **Goal:** Extract, filter, and prepare the dataset for training, outputting the result to a serialized structured format.

### Colab 2: Model Training
- **File:** `training.ipynb` (To be created)
- **Goal:** Load the dataset from `data/processed/training_data.csv` and fine-tune the pre-trained ESM-2 protein language model.

### Colab 3: Explainability Integration
- **File:** `explainability.ipynb` (To be created)
- **Goal:** Implement the Explainable AI (XAI) visualization layer.
