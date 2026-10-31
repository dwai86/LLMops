# EduAssist AI – LLMOps Assignment 2

## Project Overview

This project demonstrates key LLMOps practices for an academic question-answering application called **EduAssist AI**.

The objective is to measure LLM API latency, track token consumption, calculate inference costs, and manage prompt versions using MLflow. Three prompt versions are evaluated against the same five academic questions, and the best-performing version is promoted to production using the MLflow Prompt Registry.

### Technology Stack

| Component | Technology |
|---|---|
| Programming Language | Python 3.13.2 |
| LLM Provider | Groq |
| LLM Model | `openai/gpt-oss-20b` |
| API Interface | Groq Chat Completions API |
| Experiment Tracking | MLflow |
| Prompt Management | MLflow Prompt Registry |
| Data Processing | Pandas, NumPy |
| Environment Management | Python virtual environment, python-dotenv |
| Development Environment | Jupyter Notebook |

## Project Structure

```text
LLMops/
│
├── data/
│   └── questions.json
│
├── notebooks/
│   └── LLMops.ipynb
│
├── outputs/
│   ├── latency_tokens_cost.csv
│   ├── latency_summary.csv
│   ├── prompt_evaluation_results.csv
│   └── prompt_comparison.csv
│
├── prompts/
│   ├── prompt_v1.txt
│   ├── prompt_v2.txt
│   └── prompt_v3.txt
│
├── screenshots/
│   ├── MLflow_Experiment.png
│   ├── MLflow_Traces.png
│   ├── MLflow_Prompt_Registry.png
│   ├── MLflow_Prompt_Comparison.png
│   └── MLflow_Production_Alias.png
│
├── .env
├── .gitignore
├── mlflow.db
├── requirements.txt
└── README.md
```

*Note: The `.env` file and `mlflow.db` are local runtime files and should not contain or expose secrets in the submission.*

## Prerequisites

- Python 3.13.2
- Groq API account and API key
- Internet connectivity for Groq API access
- Git (optional)

## Installation and Environment Setup

### 1. Clone the repository

```bash
git clone https://github.com/dwai86/LLMops.git

cd LLMops
```

### 2. Create a virtual environment

Windows:

```powershell
python -m venv .venv

.venv\Scripts\activate
```

Linux/macOS:

```bash
python -m venv .venv

source .venv/bin/activate
```

### 3. Install dependencies

```bash
pip install -r requirements.txt
```

If installing dependencies individually:

```bash
pip install groq python-dotenv pandas numpy mlflow jupyter ipykernel
```

### 4. Configure environment variables

Create a `.env` file in the project root:

```env
GROQ_API_KEY=your_groq_api_key
```

The API key is loaded using `python-dotenv` and accessed through the environment variable `GROQ_API_KEY`.

**Security:** Never hard-code API keys in the notebook or commit the `.env` file to Git.

## Running the Notebook

Start Jupyter Notebook from the project directory:

```bash
jupyter notebook
```

Open:

```text
notebooks/LLMops.ipynb
```

Execute the notebook cells sequentially.

The notebook expects the working directory to be the `notebooks` folder because it uses relative paths such as:

```python
../data/questions.json
../outputs/
../mlflow.db
```

Ensure that the Groq API key is configured before executing the notebook.

## Assignment Implementation

### Task 1 – API Latency Measurement

The notebook sends five academic questions to the Groq API using the selected LLM.

For each request, it records:

- Question ID
- Prompt version
- Model name
- Start and end timestamps
- Response latency
- Response status

It calculates the following latency statistics:

- Mean
- Median
- Minimum
- Maximum
- Standard deviation

The slowest request is also identified and analysed.

**Output:** `latency_tokens_cost.csv`

### Task 2 – Token Usage and Cost Calculation

Token consumption is extracted from the API response.

The notebook calculates:

- Input token cost
- Output token cost
- Total cost per request
- Average cost per request
- Projected monthly expenditure for 10,000 requests

Cost calculation:

```text
Input Cost =
(Input Tokens / 1,000,000) × Input Price

Output Cost =
(Output Tokens / 1,000,000) × Output Price

Total Cost =
Input Cost + Output Cost

Monthly Cost =
Average Cost per Request × 10,000
```

**Pricing assumptions:**

| Parameter | Value |
|---|---|
| Provider | Groq |
| Model | openai/gpt-oss-20b |
| Currency | USD |
| Input price per million tokens | $0.075 |
| Output price per million tokens | $0.30 |
| Pricing reference date | [Enter verified pricing date] |

Prices are assumptions used for this assignment and should be checked against the provider's published pricing before reuse.

### Task 3 – MLflow Prompt Versioning and Evaluation

An MLflow experiment named:

```text
eduassist_llmops_assignment
```

is created to track prompt evaluation runs.

Three prompt versions are registered in the MLflow Prompt Registry:

| Version | Description |
|---|---|
| v1 | Basic academic question-answering prompt |
| v2 | Structured prompt with academic assistant persona and instructions |
| v3 | Strict JSON-output prompt with a predefined response schema |

Each version is evaluated against the same five academic questions.

The following metrics are logged in MLflow:

- `avg_latency_seconds`
- `avg_input_tokens`
- `avg_output_tokens`
- `avg_cost_per_request`
- `valid_json`

A custom JSON validation function checks whether responses conform to the required schema:

```json
{
  "answer": "string",
  "confidence": "high | medium | low",
  "key_points": ["string"]
}
```

The evaluation results are compared based on latency, token consumption, cost and JSON validity.

**Production promotion:** Version 3 was selected and assigned the MLflow alias:

```text
production
```

The production prompt can be loaded using:

```python
production_prompt = mlflow.genai.load_prompt(
    "prompts:/eduassist_qa_prompt@production"
)
```

## MLflow UI

MLflow uses a local SQLite backend.

The tracking URI configured in the notebook is:

```python
mlflow.set_tracking_uri("sqlite:///../mlflow.db")
```

To launch the MLflow UI, execute the following command from the `root` directory:

```powershell
mlflow ui --backend-store-uri "sqlite:///mlflow.db" --host 127.0.0.1 --port 5000
```

Open the following URL in a browser:

```text
http://127.0.0.1:5000
```

The MLflow UI provides access to:

- Experiment runs and logged metrics
- Prompt Registry and registered versions
- Prompt comparisons
- Evaluation traces
- Production alias

## Generated Outputs

The notebook generates the following CSV files in the `outputs` directory:

| File | Description |
|---|---|
| `latency_tokens_cost.csv` | API latency, token usage and request-level cost |
| `latency_summary.csv` | Latency summary statistics |
| `prompt_evaluation_results.csv` | Individual results for all prompt evaluations |
| `prompt_comparison.csv` | Aggregated performance comparison across v1, v2 and v3 |

MLflow screenshots are included in the `screenshots` directory as supporting evidence.

## Reproducibility Notes

1. Configure the Groq API key in the `.env` file.
2. Install the required Python dependencies.
3. Start Jupyter Notebook from the project directory.
4. Execute the notebook sequentially.
5. The prompt registration logic checks for existing matching templates and reuses registered versions where possible.
6. Prompt evaluation makes API calls and creates MLflow runs. Re-executing the evaluation cells will generate additional API usage and experiment runs.
7. The MLflow SQLite database stores experiment and prompt registry information locally.

For a fresh environment, create a separate MLflow database or use a clean tracking directory if a completely new experiment is required.

## Security and Limitations

- API credentials are loaded through environment variables.
- API keys must never be committed to source control.
- Cost projections depend on the pricing assumptions and token consumption observed during evaluation.
- Latency measurements may vary due to network conditions and provider-side processing.
- JSON validation checks structural compliance, not factual accuracy or academic quality.
- Human review is recommended before deploying prompts in a production application.

## Conclusion

This project demonstrates an end-to-end LLMOps workflow covering API monitoring, token-based cost estimation, prompt version management, automated JSON validation, experiment tracking and production prompt promotion using MLflow.

Version 3 was selected for production based on its JSON compliance, lower output token consumption and lower estimated API cost.