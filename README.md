# INFO-H-515 Big Data Project

Distributed Retrieval-Augmented Generation (RAG) system for automatic exam question generation from course PDF materials.

---

# Project Overview

This project implements a distributed RAG pipeline using PySpark and Hugging Face models.

The pipeline is divided into three tasks:

- `notebook_task1.ipynb`
  - PDF ingestion
  - text cleaning
  - chunking
  - embeddings generation

- `notebook_task2.ipynb`
  - semantic retrieval
  - LLM integration
  - question and answer generation

- `notebook_task3.ipynb`
  - evaluation
  - grounding analysis
  - retrieval metrics
  - format validation

---

# Project Structure

```text
.
├── data/
│   ├── data_raw/                 # Input PDF documents
│   └── data_processed/           # Generated outputs
│
├── notebook_task1.ipynb
├── notebook_task2.ipynb
├── notebook_task3.ipynb
│
├── output_task1.ipynb  
├── output_task2.ipynb
├── output_task3.ipynb
│
├── .env
├── README.md
└── .gitignore
```

Place all input PDF files inside:

```text
data/data_raw/
```

---

# Docker Environment

The project runs inside a prepared Docker image containing:

- Ubuntu 22.04
- Spark 3.3.2
- PySpark
- Anaconda Python 3.9
- Jupyter Notebook
- Hugging Face dependencies
- Sentence Transformers
- gensim
- pypdf
- dotenv

The Docker image already contains all required dependencies.

---

# Loading the Docker Image

If the Docker image is provided as a `.tar` file:

```bash
docker load -i infoh515_project_ready.tar
```

Verify the image exists:

```bash
docker images
```

Expected:

```text
infoh515_project_ready   latest
```

---

# Running the Container

Run the container from the root folder of the project.

## Windows PowerShell

```powershell
docker run -it --name infoh515_project -p 8888:8888 -v "${PWD}:/workspace" infoh515_project_ready:latest bash
```

## Linux / macOS

```bash
docker run -it --name infoh515_project -p 8888:8888 -v "$PWD:/workspace" infoh515_project_ready:latest bash
```

Inside the container:

```bash
cd /workspace
```

---

# Restarting the Existing Container

If the container already exists:

```bash
docker start -ai infoh515_project
```

---

# Hugging Face API Token

Task 2 requires a Hugging Face API token.

Create a `.env` file in the root of the project:

```bash
nano .env
```

Add:

```text
HF_API_TOKEN=your_huggingface_token_here
```

Example `.env`:

```text
HF_API_TOKEN=your_token_here
```

---

# Verifying the Environment

Inside the container:

```bash
python - <<'PY'
from pypdf import PdfReader
from dotenv import load_dotenv
from sentence_transformers import SentenceTransformer
from huggingface_hub import InferenceClient

import pyspark
import gensim
import transformers
import huggingface_hub

client = InferenceClient(
    model="Qwen/Qwen2.5-7B-Instruct",
    token="fake"
)

print("All imports OK")
print("PySpark:", pyspark.__version__)
print("gensim:", gensim.__version__)
print("transformers:", transformers.__version__)
print("huggingface_hub:", huggingface_hub.__version__)
print("has chat_completion:", hasattr(client, "chat_completion"))
PY
```

Expected:

```text
All imports OK
has chat_completion: True
```

---

# Running the Notebooks

The notebooks should be run in order:

1. Task 1
2. Task 2
3. Task 3

The commands below create executed output notebooks without modifying the original notebooks.

---

# Run Task 1

```bash
jupyter nbconvert --to notebook --execute notebook_task1.ipynb \
  --output output_task1.ipynb \
  --ExecutePreprocessor.timeout=-1 \
  --ExecutePreprocessor.kernel_name=python3
```

---

# Run Task 2

```bash
jupyter nbconvert --to notebook --execute notebook_task2.ipynb \
  --output output_task2.ipynb \
  --ExecutePreprocessor.timeout=-1 \
  --ExecutePreprocessor.kernel_name=python3
```

---

# Run Task 3

```bash
jupyter nbconvert --to notebook --execute notebook_task3.ipynb \
  --output output_task3.ipynb \
  --ExecutePreprocessor.timeout=-1 \
  --ExecutePreprocessor.kernel_name=python3
```

---

# Run the Entire Pipeline

```bash
jupyter nbconvert --to notebook --execute notebook_task1.ipynb --output output_task1.ipynb --ExecutePreprocessor.timeout=-1 --ExecutePreprocessor.kernel_name=python3 && \
jupyter nbconvert --to notebook --execute notebook_task2.ipynb --output output_task2.ipynb --ExecutePreprocessor.timeout=-1 --ExecutePreprocessor.kernel_name=python3 && \
jupyter nbconvert --to notebook --execute notebook_task3.ipynb --output output_task3.ipynb --ExecutePreprocessor.timeout=-1 --ExecutePreprocessor.kernel_name=python3
```

---

# Running Jupyter Notebook Manually

Inside the container:

```bash
cd /workspace

jupyter notebook \
  --ip=0.0.0.0 \
  --port=8888 \
  --allow-root \
  --no-browser
```

Open the URL displayed in the terminal.

Example:

```text
http://127.0.0.1:8888/?token=...
```

---

# Using VS Code with the Container

Recommended VS Code extensions:

- Dev Containers
- Python
- Jupyter

While the container is running, connect VS Code:

```bash
code --folder-uri "vscode-remote://attached-container+infoh515_project/workspace"
```

The VS Code terminal should show:

```text
root@...:/workspace#
```

If it shows a Windows path such as:

```text
PS C:\Users\...
```

then the code is NOT running inside Docker.

---

# Generated Outputs

## Task 1

Creates embedded chunk datasets in:

```text
data/data_processed/
```

---

## Task 2

Creates generated questions and answers:

```text
data/data_processed/generated_qa.json
```

---

## Task 3

Creates evaluation metrics and reports.


# Troubleshooting

---

## HF_API_TOKEN not found

Ensure `.env` exists in `/workspace` and contains:

```text
HF_API_TOKEN=your_huggingface_token_here
```

# Important Notes

- Run all commands from `/workspace` inside the container.
- Run notebooks in order:
  - Task 1
  - Task 2
  - Task 3
