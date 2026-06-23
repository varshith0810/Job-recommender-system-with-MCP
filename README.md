# AI Job Recommender System with MCP

An AI-powered resume analysis and job recommendation project that combines a Streamlit web app with an MCP (Model Context Protocol) server. The application extracts text from a PDF resume, uses an LLM to summarize the candidate profile, identifies skill gaps, builds an improvement roadmap, and fetches relevant job listings from LinkedIn and Naukri through Apify actors.

## What This Project Does

- **Resume PDF parsing** using PyMuPDF.
- **AI resume summary generation** focused on skills, education, and experience.
- **Skill-gap analysis** to identify missing skills, certifications, and experience areas.
- **Career roadmap generation** with suggested learning paths and preparation strategy.
- **Job keyword extraction** from the AI-generated resume summary.
- **Job recommendations** from LinkedIn and Naukri using Apify.
- **MCP tool exposure** so compatible AI clients can call the job-fetching tools directly.

## Repository Structure

```text
.
├── app.py              # Streamlit user interface for resume analysis and recommendations
├── mcp_server.py       # MCP server exposing job recommendation tools
├── pyproject.toml      # Project metadata
├── requirments.txt     # Python dependency list
└── src/
    ├── __init__.py
    ├── helper.py       # PDF extraction and LLM helper functions
    └── job_api.py      # Apify integrations for LinkedIn and Naukri jobs
```

> Note: The dependency file is currently named `requirments.txt` in the repository.

## Architecture Overview

```text
PDF Resume
   │
   ▼
Streamlit App (`app.py`)
   │
   ├── Extract text from PDF (`src/helper.py`)
   ├── Send resume text to the configured LLM provider
   ├── Generate summary, skill gaps, and career roadmap
   └── Extract job-search keywords
          │
          ▼
   Job API Layer (`src/job_api.py`)
          │
          ├── LinkedIn Apify actor
          └── Naukri Apify actor
          │
          ▼
   Job recommendations displayed in the UI
```

The MCP server (`mcp_server.py`) exposes the same job-fetching capability as MCP tools for clients that support Model Context Protocol integrations.

## Prerequisites

- Python **3.12+**
- An API key for the configured OpenAI-compatible LLM endpoint
- An Apify API token
- Internet access for LLM and Apify API calls

## Environment Variables

Create a `.env` file in the project root:

```env
OPENAI_API_KEY=your_llm_api_key_here
APIFY_API_TOKEN=your_apify_api_token_here
```

### Variable Details

| Variable | Purpose |
| --- | --- |
| `OPENAI_API_KEY` | Used by `src/helper.py` to call the configured OpenAI-compatible LLM API. |
| `APIFY_API_TOKEN` | Used by `src/job_api.py` to run Apify actors for LinkedIn and Naukri jobs. |

## Installation

1. Clone the repository and enter the project folder:

   ```bash
   git clone <repository-url>
   cd Job-recommender-system-with-MCP
   ```

2. Create and activate a virtual environment:

   ```bash
   python -m venv .venv
   source .venv/bin/activate
   ```

   On Windows PowerShell:

   ```powershell
   python -m venv .venv
   .venv\Scripts\Activate.ps1
   ```

3. Install dependencies:

   ```bash
   pip install -r requirments.txt
   ```

4. Add the required environment variables to `.env`.

## Running the Streamlit App

Start the web application:

```bash
streamlit run app.py
```

Then open the local Streamlit URL shown in your terminal, usually:

```text
http://localhost:8501
```

## How to Use the App

1. Upload a resume in PDF format.
2. Wait for the app to extract resume text.
3. Review the generated:
   - Resume summary
   - Skill-gap analysis
   - Future career roadmap
4. Click **Get Job Recommendations**.
5. Review recommended jobs from:
   - LinkedIn
   - Naukri

## Running the MCP Server

The MCP server exposes two tools:

| Tool | Description |
| --- | --- |
| `fetchlinkedin` | Fetches LinkedIn jobs for a provided keyword or keyword list. |
| `fetchnaukri` | Fetches Naukri jobs for a provided keyword or keyword list. |

Run the MCP server with stdio transport:

```bash
python mcp_server.py
```

A compatible MCP client can then register this command and call the exposed tools.

## Core Modules

### `app.py`

The main Streamlit application. It handles file upload, resume analysis flow, UI rendering, job keyword extraction, and display of job recommendations.

### `src/helper.py`

Contains helper logic for:

- Extracting text from PDF files.
- Sending prompts to the configured LLM endpoint.

### `src/job_api.py`

Contains Apify integration logic for:

- LinkedIn job scraping actor.
- Naukri job scraping actor.

### `mcp_server.py`

Creates a FastMCP server and registers job-fetching functions as MCP tools.

## Troubleshooting

### `OPENAI_API_KEY` is missing

Make sure `.env` exists in the project root and includes:

```env
OPENAI_API_KEY=your_llm_api_key_here
```

### `APIFY_API_TOKEN` is missing

Make sure `.env` includes:

```env
APIFY_API_TOKEN=your_apify_api_token_here
```

### No jobs are returned

Check the following:

- Your Apify token is valid.
- The Apify actors are available and have not changed their input schema.
- The generated keywords are specific enough for the target role.
- Your Apify account has enough credits or quota.

### PDF text extraction looks incomplete

Some resumes are image-based scans. PyMuPDF extracts embedded text, but it does not perform OCR by default. For scanned resumes, add an OCR step before sending text to the LLM.

## Recommended Improvements

- Rename `requirments.txt` to `requirements.txt` for standard Python tooling compatibility.
- Add input validation for missing environment variables.
- Add automated tests for PDF extraction and job API wrappers.
- Add structured logging for API failures.
- Add retry and timeout handling for external API calls.
- Add OCR support for scanned resumes.
- Add filters for location, experience level, remote roles, and salary range.
- Add pagination or ranking for large job result sets.

## Security Notes

- Do not commit `.env` files or API tokens.
- Treat uploaded resumes as sensitive personal data.
- Avoid logging raw resume content in production.
- Review external job scraping provider terms and data usage policies before production deployment.

## License

No license file is currently included. Add a license before distributing or deploying this project publicly.
