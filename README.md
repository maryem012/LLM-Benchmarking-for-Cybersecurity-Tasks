# LLM Benchmarking for Cybersecurity Tasks

This repository contains the code, datasets, and results for a benchmarking study that evaluates the performance of Large Language Models (LLMs) on cybersecurity-related tasks, with a focus on Cyber Threat Intelligence (CTI).

## Overview

The project assesses how well general-purpose LLMs can handle three core cybersecurity tasks without task-specific training:

| Task | Abbreviation | Description |
|------|-------------|-------------|
| Multiple Choice Questions | **MCQ** | Answer CTI-domain questions based on MITRE ATT&CK and the Diamond Model |
| Root Cause Mapping | **RCM** | Map CVE vulnerability descriptions to their corresponding CWE weakness category |
| Vulnerability Severity Prediction | **VSP** | Predict the CVSS vector string for a given CVE description |

A fine-tuning experiment is also included, using LoRA adapters on **Llama 3.1 8B** for the MCQ task.

## Models Evaluated

- **Gemini 1.0 Pro** (Google)
- **Meta-Llama-3-8B** (Meta)
- **Mistral-7B-Instruct-v0.3** (Mistral AI)
- **Llama 3.1 8B (fine-tuned)** — trained with LoRA adapters using the Unsloth library

## Repository Structure

```
stage-2024/
│
├── LLM-threat-intelligence-Paper (1).pdf   # Reference paper
│
├── mcq/                                    # Multiple Choice Questions task
│   ├── dataset/
│   │   └── myDB (4).tsv                    # Custom CTI MCQ dataset (questions, options, answers, explanations)
│   ├── evaluation/
│   │   ├── gemini/
│   │   │   ├── part2_cti.ipynb             # Gemini evaluation notebook
│   │   │   ├── cti-mcq-gemini-updated.csv  # Gemini results on CTI dataset
│   │   │   └── mydb_answer_GEMINI_updated.csv  # Gemini results on custom dataset
│   │   ├── llama/
│   │   │   ├── llama (1).ipynb             # Llama evaluation notebook
│   │   │   └── predicted_answers.csv       # Llama predictions
│   │   └── mistral/
│   │       ├── mistralctiMcqEVAL.ipynb     # Mistral evaluation notebook
│   │       └── predicted_answers (1).csv   # Mistral predictions
│   └── fine tuning/
│       └── Llama_3_1_8b_finetuning (1).ipynb  # Llama 3.1 8B fine-tuning notebook
│
├── rcm/                                    # Root Cause Mapping task
│   ├── dataset/
│   │   ├── cve_cwe_dataset_filtered.tsv    # CVE-to-CWE mapping dataset
│   │   └── cve_cwe_dataset_filtered.xlsx   # Same dataset in Excel format
│   └── evaluation/
│       └── RCMGeminiMydata.ipynb           # Gemini RCM evaluation notebook
│
└── vsp/                                    # Vulnerability Severity Prediction task
    ├── data/
    │   ├── cve_dataset_with_cvss_vector.tsv    # CVE dataset with ground-truth CVSS vectors
    │   ├── geminaiCVSSresp.tsv                 # Gemini predictions on public dataset
    │   └── geminaiCVSSmydataresp.tsv           # Gemini predictions on custom dataset
    └── evaluation/
        ├── VSPevaluation.ipynb                 # VSP evaluation notebook (public dataset)
        └── VSPevaluationMYdata.ipynb           # VSP evaluation notebook (custom dataset)
```

## Tasks

### 1. MCQ — Multiple Choice Questions

LLMs are prompted to answer 4-option multiple-choice questions drawn from a Cyber Threat Intelligence benchmark. Questions cover concepts from the **MITRE ATT&CK** framework and the **Diamond Model of Intrusion Analysis**.

**Dataset columns:** `question`, `Option_A`, `Option_B`, `Option_C`, `Option_D`, `Correct_Answer`, `Explanation`

**Key results:**
- Gemini 1.0 Pro on CTI dataset: **61.00% accuracy**
- Gemini 1.0 Pro on custom dataset: **68.00% accuracy**

### 2. RCM — Root Cause Mapping

LLMs are given a CVE description and asked to identify the most appropriate CWE (Common Weakness Enumeration) category that describes the root cause of the vulnerability.

**Dataset columns:** `CVE ID`, `CVE Description`, `CWE ID`, `Explanation`, `Description Length`

### 3. VSP — Vulnerability Severity Prediction

LLMs are given a CVE description and asked to predict the full **CVSS v3.1 vector string** (e.g., `CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:H/I:H/A:H`).

**Dataset columns:** `CVE ID`, `Description`, `CVSS Vector`

## Fine-Tuning

The `mcq/fine tuning/` directory contains a notebook for fine-tuning **Llama 3.1 8B** on the MCQ dataset using **LoRA (Low-Rank Adaptation)** adapters via the [Unsloth](https://github.com/unslothai/unsloth) library.

**Training highlights:**
- LoRA rank: 16, applied to attention and feed-forward projection layers
- Trainer: Hugging Face `SFTTrainer`
- Training time: ~58 minutes
- Final training loss: ~0.50
- Final validation loss: ~0.68

## Setup & Usage

All notebooks are designed to run on **Google Colab** with GPU acceleration.

### Prerequisites

```bash
pip install google-generativeai
pip install transformers datasets torch
pip install unsloth[colab-new] trl peft accelerate bitsandbytes
```

### Running the Evaluations

1. Open the relevant notebook in Google Colab.
2. Mount your Google Drive and upload the dataset files.
3. Set your API key (for Gemini) or Hugging Face token (for Llama/Mistral).
4. Run all cells to generate predictions and evaluate accuracy.

### API Keys

- **Gemini:** Obtain an API key from [Google AI Studio](https://makersuite.google.com/) and set `gemini_key` in the notebook.
- **Llama / Mistral:** Set your Hugging Face token as the `HF_TOKEN` environment variable or use `google.colab.userdata`.

## Datasets

| Dataset | Task | Source |
|---------|------|--------|
| CTI MCQ benchmark | MCQ | Public CTI knowledge benchmark |
| Custom MCQ (`myDB`) | MCQ | Manually curated CTI questions |
| CVE-CWE dataset | RCM | NVD / custom filtered |
| CVE CVSS dataset | VSP | NVD / custom filtered |

## Reference

This work is based on research exploring the use of LLMs for automated cyber threat intelligence processing. See the included PDF for the reference paper:

> `LLM-threat-intelligence-Paper (1).pdf`
