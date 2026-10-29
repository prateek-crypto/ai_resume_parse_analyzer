# AI Resume Parser & Analyzer — Day 5

I'm building an AI-powered resume parser and analyzer that compares candidate resumes against a job description and helps identify how closely a candidate's profile matches the role.

For Day 5, I focused on connecting document processing with an LLM, extracting structured information from resumes, and generating candidate match scores.

The goal was to move beyond simply reading resumes and build something that can understand their contents and compare them against actual job requirements.

## What does this project do?

Imagine you have an Amazon Software Development Engineer job description and four different resumes. Reading each resume individually and comparing every skill against the job requirements takes time.

This project automates much of that initial comparison.

It reads PDF and DOCX resumes, extracts relevant candidate information using an LLM, compares the extracted information against a structured job description, and produces a match score with an explanation of matching and missing skills.

### Current Features

* **Job Description Analysis:** Uses an LLM to extract the role, required skills, preferred skills, education requirements, responsibilities, and minimum experience from a job description.
* **PDF Resume Parsing:** Extracts text from PDF resumes using `pypdf`.
* **DOCX Resume Parsing:** Reads paragraphs and tables from Word documents using `python-docx`.
* **AI-Powered Information Extraction:** Uses Groq's API to extract candidate details into structured JSON.
* **Structured Data Validation:** Uses Pydantic models to validate the extracted job description, resume information, and matching results.
* **Skill Matching:** Identifies skills that match the job requirements and highlights important missing skills.
* **Candidate Scoring:** Generates an overall match score between 0 and 100 using an LLM.
* **Candidate Ranking:** Sorts candidates by their scores and displays the top two and bottom two candidates.
* **Environment-Based Configuration:** Loads the API key and model configuration from environment variables rather than hardcoding credentials in the source code.

## How does it work?

The current implementation follows a straightforward pipeline.

```text
              Job Description
                     |
                     v
             Groq LLM Analysis
                     |
                     v
           Structured Job Data
                     |
                     |
                     v
Resume Files --> PDF/DOCX Text Extraction
                     |
                     v
             Resume Text Parsing
                     |
                     v
           Structured Resume Data
                     |
                     v
        Compare Resume Against Job
                     |
                     v
          AI-Generated Match Score
                     |
                     v
            Rank All Candidates
                     |
                     v
          Display Top 2 and Bottom 2
```

### Step 1: Parse the job description

The application reads the job description from `job_description.txt` and sends it to the Groq API.

The LLM extracts information into a predefined Pydantic model.

For example, the resulting structure contains fields such as:

* Role
* Required skills
* Preferred skills
* Minimum experience
* Education requirements
* Responsibilities

This structured representation makes it easier to compare the job requirements against candidate information.

### Step 2: Extract information from resumes

The application scans the `resumes` directory and processes PDF and DOCX files.

For PDF files, it uses `pypdf`. For Word documents, it uses `python-docx`.

The extracted text is sent to the LLM, which attempts to identify:

* Candidate name and contact information
* Technical skills
* Work experience and internships
* Education
* Projects
* Certifications
* Total experience in years

The extracted information is validated using a Pydantic model before being used in the next stage.

### Step 3: Compare the candidate against the job

Once the resume has been parsed, the application sends the structured job description and candidate information to the LLM for comparison.

The model returns an overall score and supporting details, including matching skills, missing important skills, an experience assessment, and a final verdict.

### Step 4: Rank the candidates

After processing the available resumes, the application sorts the results in descending order of match score.

It then displays the top two and lowest two candidates.

The scores are intended to help with an initial comparison. They are AI-generated estimates, not official ATS scores or probabilities of getting hired.

## Tech Stack

| Technology    | Purpose                                                                        |
| ------------- | ------------------------------------------------------------------------------ |
| Python        | Main programming language                                                      |
| Groq API      | LLM-powered job description analysis, resume parsing, and candidate comparison |
| Pydantic      | Structured data models and validation                                          |
| pypdf         | Extracting text from PDF resumes                                               |
| python-docx   | Extracting text from DOCX resumes                                              |
| python-dotenv | Loading environment variables from a `.env` file                               |
| pathlib       | Handling file paths and directories                                            |
| json          | Parsing structured JSON responses                                              |
| time          | Introducing delays between API requests                                        |

## Project Structure

The Day 5 implementation uses the following structure:

```text
day5/
├── resume_parser.py
├── job_description.txt
├── .env
├── .env.example
├── .gitignore
├── README.md
└── resumes/
    ├── candidate1.pdf
    ├── candidate2.pdf
    └── candidate3.docx
```

**Important:** The `.env` file and the `resumes/` directory should remain local and should not be committed to a public repository.

The `.env.example` file can contain placeholder configuration for other developers.

## Getting Started

### 1. Clone the repository

```bash
git clone https://github.com/prateek-crypto/ai_resume_parse_analyzer.git
```

Navigate to the Day 5 directory:

```bash
cd ai_resume_parse_analyzer/week1/day5
```

The commands above assume the repository contains the project under `week1/day5`, as in the current development setup.

### 2. Create a virtual environment

```bash
python -m venv .venv
```

Activate it on Windows PowerShell:

```powershell
.\.venv\Scripts\Activate.ps1
```

### 3. Install the dependencies

```bash
python -m pip install groq pydantic python-dotenv pypdf python-docx
```

### 4. Configure the API key

Create a `.env` file in the same directory as `resume_parser.py`.

Add your own Groq API key:

```env
GROQ_API_KEY=your_groq_api_key_here
GROQ_MODEL=openai/gpt-oss-120b
```

Replace the placeholder with your actual API key.

Keep `.env` out of version control. Never publish your actual API key.

The application uses `openai/gpt-oss-120b` as its default model when `GROQ_MODEL` is not configured.

### 5. Add a job description

Create `job_description.txt` in the same directory as `resume_parser.py`.

Paste the job description you want to analyze into this file.

The application reads this file at runtime, so you can change the target role without editing the Python source code.

### 6. Add resumes

Place the resumes you want to analyze directly inside the `resumes/` directory.

The current implementation supports:

* `.pdf`
* `.docx`

It scans the immediate contents of the directory, so resumes placed inside nested subdirectories will not be discovered by the current code.

### 7. Run the application

```bash
python resume_parser.py
```

The application will analyze the job description, process the available resumes, and print the resulting candidate scores and rankings in the terminal.

## What I learned on Day 5

This implementation helped me understand several concepts that are useful when building AI-powered applications.

**1. Using an LLM for structured extraction**

An LLM can do more than generate conversational responses. It can extract information from unstructured text and return it in a predefined JSON structure.

**2. Working with different document formats**

PDF and DOCX files require different extraction libraries. Converting their contents into text gives the application a common input format for subsequent processing.

**3. Validating AI-generated data**

LLM output should not be accepted blindly. Pydantic provides a way to validate whether the returned data conforms to the application's expected structure.

**4. Separating extraction from comparison**

The application first extracts structured information from the job description and resume. It then compares those representations in a separate step.

This separation makes the workflow easier to understand and provides a foundation for future improvements.

**5. Handling API configuration securely**

Environment variables allow API credentials and model settings to be configured outside the main source code.

**6. Working with multiple documents**

Using `pathlib` and a loop allows the application to process multiple resumes without hardcoding each filename.

## Current Limitations

This is an early version of the project, and there are several areas I want to improve.

* **LLM-generated scores:** The match percentage is generated by an LLM and is not based on a calibrated scoring model.
* **Consistency:** Different model responses may use inconsistent field names or evaluate similar candidates differently.
* **Skill prioritization:** Required qualifications and preferred qualifications need clearer, separate treatment.
* **Experience assessment:** The application needs better handling of unspecified requirements and insufficient evidence.
* **Error handling:** API failures, malformed responses, unreadable documents, and empty resume directories need more robust handling.
* **Scanned PDFs:** The current PDF extraction method does not include OCR for image-based documents.
* **Request efficiency:** The application makes two LLM calls per resume, in addition to the initial job-description analysis. This increases latency and API usage.
* **Result persistence:** Candidate results are currently printed to the terminal rather than saved to a database or downloadable report.
* **Evaluation:** The scoring process needs testing against manually reviewed examples to assess consistency and usefulness.

## What's Next?

My next focus is improving the reliability of the existing implementation rather than adding features just for the sake of it.

Some planned improvements are:

* Introduce more consistent response schemas.
* Improve the distinction between required and preferred skills.
* Make candidate assessments more evidence-based.
* Add robust error handling and input validation.
* Save the results in a structured format for later analysis.
* Test the application with different job descriptions and resume formats.
* Explore ways to evaluate the consistency of generated match scores.

## A Note on Responsible Use

This project is intended as a learning exercise and a prototype for resume analysis.

The extracted information and match scores may contain errors. They should not be treated as definitive measures of a candidate's ability or used as the sole basis for hiring or rejection decisions.

Resumes can contain personal information, so they should be handled carefully. API usage should follow the provider's applicable data-handling terms and privacy requirements.

## Project Status

**Day 5 — Initial working implementation completed.**

The application can read supported resume formats, extract structured information using an LLM, compare candidates against a job description, and display a ranked list of results.

The next stage is to make the analysis more consistent, testable, and reliable.
