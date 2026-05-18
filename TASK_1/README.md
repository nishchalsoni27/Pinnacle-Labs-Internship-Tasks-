# 🌿 Resume Parser — Pinnacle Labs AI

> An AI-powered system to extract and organize information from resumes,
> enhancing HR processes and candidate selection.

---

## Features

| Feature | Description |
|---|---|
| **Multi-format** | Parses `.pdf`, `.docx`, `.doc`, `.txt` |
| **Contact extraction** | Name, email, phone, LinkedIn, GitHub |
| **Skill matching** | 50+ tech skills detected automatically |
| **Education parser** | Degree + institution + year |
| **Section extraction** | Summary, Experience, Projects, Certifications |
| **Candidate ranking** | Score & rank candidates against a job description |
| **REST API** | FastAPI with auto-generated docs at `/docs` |
| **CLI** | Batch process an entire folder of resumes |
| **CI/CD** | GitHub Actions tests on every push |

---

## Quick Start

### 1. Clone & install

```bash
git clone https://github.com/YOUR_USER/resume-parser.git
cd resume-parser

python -m venv venv
source venv/bin/activate       
pip install -r requirements.txt
python -m spacy download en_core_web_sm
```

### 2. Run the API server

```bash
cd src
python main.py
# → http://localhost:8000
# → Docs: http://localhost:8000/docs
```

### 3. Parse a resume via CLI

```bash
# Single file — pretty table output
python src/cli.py path/to/resume.pdf

# Single file — JSON output
python src/cli.py path/to/resume.pdf --format json

# Batch folder — save to file
python src/cli.py resumes/ --output results.json
```

### 4. Run tests

```bash
pytest tests/ -v
```

---

## API Reference

### `POST /parse/file`
Upload a resume file and get structured JSON back.

```bash
curl -X POST http://localhost:8000/parse/file \
  -F "file=@resume.pdf"
```

**Response:**
```json
{
  "file": "resume.pdf",
  "contact": {
    "name": "Jane Doe",
    "email": "jane@example.com",
    "phone": "+1 555-123-4567",
    "linkedin": "https://linkedin.com/in/janedoe",
    "github": "https://github.com/janedoe"
  },
  "skills": ["AWS", "Docker", "Python", "React"],
  "education": [
    { "degree": "B.Sc Computer Science — State University, 2018", "year": "2018" }
  ],
  "experience_years": 6,
  "sections": {
    "summary": "...",
    "experience": "...",
    "projects": "...",
    "certifications": "..."
  }
}
```

### `POST /parse/text`
Send raw resume text.

```bash
curl -X POST http://localhost:8000/parse/text \
  -H "Content-Type: application/json" \
  -d '{"text": "Jane Doe\njane@example.com\n..."}'
```

### `GET /skills/list`
Returns all 50+ recognized tech skills.

---

## Candidate Ranking

Use `scorer.py` to rank multiple parsed resumes against a job description:

```python
from src.scorer import rank_candidates, print_ranking
from src.parser import parse_resume

resumes = [parse_resume("alice.pdf"), parse_resume("bob.pdf")]

ranked = rank_candidates(
    resumes,
    job_description="Senior Python engineer with Docker and AWS...",
    required_skills=["Python", "Docker", "AWS"],
    preferred_skills=["Kubernetes", "React"],
    min_experience_years=5,
)

print_ranking(ranked)
```

Output:
```
           CANDIDATE RANKING
────────────────────────────────────────────────────────────
#    Name                   Skills    Exp     KW   TOTAL
────────────────────────────────────────────────────────────
1    Bob                       92%    100%    78%     89%
2    Alice                     33%     50%    41%     41%
────────────────────────────────────────────────────────────
```

---

## Project Structure

```
resume-parser/
├── src/
│   ├── parser.py       # Core extraction engine
│   ├── scorer.py       # Candidate ranking
│   ├── main.py         # FastAPI app
│   └── cli.py          # Command-line interface
├── tests/
│   ├── test_parser.py  # Parser unit tests
│   └── test_scorer.py  # Scorer unit tests
├── data/
│   └── samples/        # Sample resumes (gitignored)
├── .github/
│   └── workflows/
│       └── ci.yml      # GitHub Actions CI
├── requirements.txt
├── .gitignore
└── README.md
```

---

## GitHub Setup

```bash
# First push
git add .
git commit -m "feat: initial resume parser implementation"
git remote add origin https://github.com/YOUR_USER/resume-parser.git
git push -u origin main
```

GitHub Actions will automatically run all tests on every push and pull request.

---

## Tech Stack

- **Python 3.11+**
- **FastAPI** — REST API
- **spaCy** — NLP / named entity recognition
- **pdfminer.six** — PDF text extraction
- **python-docx** — Word document parsing
- **pytest** — Testing
- **GitHub Actions** — CI/CD

---

*Built for Pinnacle Labs · AI Intelligence track*
