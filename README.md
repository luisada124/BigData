# INFO-H-515 Big Data Project

Distributed RAG system for automatic exam question generation from course PDF materials.

## 1. Project Structure

```text
.
├── data/
│   ├── data_raw/                 # Input PDF files
│   └── data_processed/           # Generated outputs
├── notebook_task1.ipynb
├── notebook_task2.ipynb
├── notebook_task3.ipynb
├── requirements.txt
├── .env.example
├── .gitignore
└── README.md
```

Place the course PDFs in:

```text
data/data_raw/
```

## 2. Docker Base Image

This project uses the Docker image provided for the course:

```text
yannael/ulb_infoh515:latest
```

The image already contains Spark 3.3.2, PySpark, Python 3.9, Anaconda and Jupyter.

## 3. Start the Container

From the project root folder:

### Windows PowerShell

```powershell
docker run -it --name infoh515_project -p 8888:8888 -v "${PWD}:/workspace" yannael/ulb_infoh515:latest bash
```

### Linux / macOS

```bash
docker run -it --name infoh515_project -p 8888:8888 -v "$PWD:/workspace" yannael/ulb_infoh515:latest bash
```

Inside the container:

```bash
cd /workspace
```

The `-v` mount links the local project folder to `/workspace`, so outputs generated in Docker are saved in the host project folder.

If the container already exists:

```bash
docker start -ai infoh515_project
```

## 4. Install Dependencies

Inside the container, from `/workspace`, install the exact required dependencies:

```bash
python -m pip install --no-cache-dir \
  pypdf==6.1.1 \
  python-dotenv==1.2.1 \
  gensim==4.3.2 \
  huggingface_hub==0.34.6 \
  transformers==4.44.2 \
  tokenizers==0.19.1 \
  safetensors==0.4.5 \
  sentence-transformers==3.0.1
```

Then install requirements with:

```bash
python -m pip install --no-cache-dir -r requirements.txt
```

## 5. Configure Hugging Face Token

Task 2 requires a Hugging Face API token.

Create a `.env` file in the project root:

```bash
nano .env
```

Add:

```text
HF_API_TOKEN=your_huggingface_token_here
```

Save and exit.


## 6. Run the Project

Run the notebooks in order.

### Task 1

```bash
jupyter nbconvert --to notebook --execute notebook_task1.ipynb \
  --output output_task1.ipynb \
  --ExecutePreprocessor.timeout=-1 \
  --ExecutePreprocessor.kernel_name=python3
```

### Task 2

```bash
jupyter nbconvert --to notebook --execute notebook_task2.ipynb \
  --output output_task2.ipynb \
  --ExecutePreprocessor.timeout=-1 \
  --ExecutePreprocessor.kernel_name=python3
```

### Task 3

```bash
jupyter nbconvert --to notebook --execute notebook_task3.ipynb \
  --output output_task3.ipynb \
  --ExecutePreprocessor.timeout=-1 \
  --ExecutePreprocessor.kernel_name=python3
```

### Run All Tasks

```bash
jupyter nbconvert --to notebook --execute notebook_task1.ipynb --output output_task1.ipynb --ExecutePreprocessor.timeout=-1 --ExecutePreprocessor.kernel_name=python3 && \
jupyter nbconvert --to notebook --execute notebook_task2.ipynb --output output_task2.ipynb --ExecutePreprocessor.timeout=-1 --ExecutePreprocessor.kernel_name=python3 && \
jupyter nbconvert --to notebook --execute notebook_task3.ipynb --output output_task3.ipynb --ExecutePreprocessor.timeout=-1 --ExecutePreprocessor.kernel_name=python3
```

## Running Jupyter Manually

Inside the container:

```bash
cd /workspace
jupyter notebook --ip=0.0.0.0 --port=8888 --allow-root --no-browser
```

Open the URL printed in the terminal.

## Expected Outputs

Task 1 generates embedded chunks in:

```text
data/data_processed/
```

Task 2 generates:

```text
data/data_processed/generated_qa.json
```

Task 3 generates evaluation outputs and metrics.

## 12. Notes

- Run all commands from `/workspace` inside the container.
- Run notebooks in order: Task 1 → Task 2 → Task 3.

