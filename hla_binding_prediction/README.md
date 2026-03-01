# Project Gamma: Deep Learning for Peptide-HLA Binding Prediction

## Overview

This repository contains an example pipeline for training a Transformer-based protein language model
(such as ESM-2 or ProtBERT) to predict the binding affinity between peptides and the HLA-B\*27:05
allele.

The project demonstrates a complete sequence-to-label computational workflow. It showcases how to
classify protein sequences as "Binders" or "Non-binders" and includes components for an interactive
application that surfaces Explainable AI (XAI) insights.

## Project Structure

The pipeline is divided into three main Jupyter Notebooks:

- **Colab 1: Data Preprocessing:** Extracts, filters, and prepares raw binding affinity data from
  the Immune Epitope Database (IEDB).
- **Colab 2: Model Training:** Fine-tunes the pre-trained protein language model on the binary
  classification task.
- **Colab 3: Explainability Integration:** Implements attention visualizations and Integrated
  Gradients to highlight driving amino acids.
