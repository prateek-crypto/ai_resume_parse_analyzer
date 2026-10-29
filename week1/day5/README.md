# Week 1, Day 5: Resume parser

Compare PDF and DOCX resumes against a job description using Groq. The job
description and resumes are local inputs and are excluded from Git.

## Setup

From the repository root, create the Day 5 environment file and add your Groq
API key:

```powershell
if (-not (Test-Path week1\day5\.env)) {
    Copy-Item week1\day5\.env.example week1\day5\.env
}
```

Edit `week1\day5\.env` and set `GROQ_API_KEY`. The optional `GROQ_MODEL`
defaults to `openai/gpt-oss-120b`.

Create `week1\day5\job_description.txt` and paste in a job description you are
authorized to use. Put PDF or DOCX resumes in `week1\day5\resumes\`.

Create the Day 5 environment and install its dependencies:

```powershell
uv sync --project week1\day5
```

## Run

```powershell
uv run --project week1\day5 python week1\day5\resume_parser.py
```

The script sends the job description and resume text to Groq. Do not process
these documents unless you are authorized to share their contents with that
service.
