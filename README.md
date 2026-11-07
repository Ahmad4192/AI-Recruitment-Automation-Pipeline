# AI-Recruitment-Automation-Pipeline

An end-to-end automated recruitment and candidate evaluation pipeline built with n8n, OpenAI (GPT-4o), and Google Workspace APIs (Gmail, Google Drive, Google Sheets). The workflow automatically ingests job applications from incoming emails, extracts structured data from resume attachments, benchmarks candidate qualifications against job requirements, updates an audit spreadsheet, and routes automated interview invitations or polite rejection responses based on fit score.

---

## Architecture Overview
```
[ Gmail Trigger (Incoming Application Email + CV Attachment) ]
                         │
                         ▼
        [ Google Drive (Save Candidate CV) ]
                         │
                         ▼
    [ OpenAI (GPT-4o) Information Extraction ]
    (Structured parsing: Name, Email, Phone, Skills, Education)
                         │
                         ▼
     [ OpenAI (GPT-4o) Evaluation & Scoring ]
  (Scores 1-10 against Junior AI Automation Engineer criteria)
                         │
                         ▼
                 [ Code Node ]
      (Data normalization, validation, and scoring logic)
                         │
                         ▼
        [ Google Sheets (Append Candidate Row) ]
                         │
                         ▼
            [ If Condition (Score >= 7) ]
            ┌────────────┴────────────┐
            ▼                         ▼
   [ True: Send Interview ]   [ False: Send Rejection ]
```

---

## Features

- **Automated Resume Ingestion:** Polls Gmail for incoming emails containing job-application keywords and extracts attached PDF resumes.
- **Secure File Archiving:** Stores applicant CVs directly into designated Google Drive folders.
- **Structured Information Extraction:** Uses GPT-4o with strict JSON schema constraints to parse applicant contact details, professional experience, and technical skill sets.
- **Objective Candidate Scoring:** Evaluates candidate qualifications against target job requirements, producing a relevance score (1-10) along with a detailed evaluation summary.
- **Centralized Candidate Tracking:** Automatically logs parsed information, candidate scores, and decision rationale into a structured Google Sheets tracker.
- **Conditional Communication Routing:** Directs candidates to either an automated interview invitation or a rejection notification based on predefined threshold criteria.

---

## Tech Stack

- **Workflow Automation:** n8n (Self-hosted or Cloud)
- **Large Language Model:** OpenAI GPT-4o
- **Email Ingestion & Dispatch:** Gmail API
- **Document Storage:** Google Drive API
- **Candidate Logging:** Google Sheets API
- **Data Transformation:** JavaScript (n8n Code Node)

---

## Workflow Data Schema

The Code node normalizes output from OpenAI into the following structured format for downstream logging and routing:

```json
{
  "name": "Candidate Name",
  "email": "candidate@example.com",
  "phone": "+1234567890",
  "skills": "Python, n8n, LangChain, REST APIs, Docker",
  "qualification": "B.S. in Computer Science / IT",
  "score": 8,
  "justification": "Strong practical experience with workflow automation and Python-based LLM integrations. Meets core requirements for Junior AI Automation Engineer."
}
```

---

## Prerequisites

Before deploying the workflow, ensure you have configured:

1. **n8n Instance:** Version 1.x or higher.
2. **OpenAI API Key:** Access to `gpt-4o`.
3. **Google Cloud OAuth 2.0 Credentials:**
   - Gmail API (scopes: read, send)
   - Google Drive API (scope: file creation)
   - Google Sheets API (scope: read/write spreadsheets)
4. **Google Sheet Tracker:** A spreadsheet configured with the following header row:
   `Name | Email | Number | Skill Set | Qualification | Score | Justification`

---

## Installation & Setup

1. **Clone the Repository:**
   ```bash
   git clone [https://github.com/Ahmad4192/AI-Recruitment-Automation-Pipeline.git](https://github.com/Ahmad4192/AI-Recruitment-Automation-Pipeline.git)
   cd AI-Recruitment-Automation-Pipeline
   ```

2. **Import into n8n:**
   - Open your n8n workspace.
   - Navigate to **Workflows** -> **Import from File**.
   - Select the `AI-Recruitment-Automation-Pipeline.json` file from this repository.

3. **Configure Credentials:**
   - Assign your **OpenAI API** credential to the OpenAI nodes.
   - Assign your **Google OAuth2** credentials to the Gmail, Google Drive, and Google Sheets nodes.

4. **Update Node Parameters:**
   - **Google Drive Node:** Select your target storage folder ID.
   - **Google Sheets Node:** Select your spreadsheet and sheet name.
   - **If Node:** Verify the threshold logic (default: `{{ $json.score }} >= 7`).

5. **Activate the Workflow:**
   - Toggle the workflow switch to **Published / Active**.

---

## Security & Credential Handling

Exported workflow files in this repository contain structure and node metadata only. All sensitive tokens, client secrets, and API keys are stripped by n8n during export and must be configured inside your own n8n credentials vault.

---

## License

This project is licensed under the MIT License.