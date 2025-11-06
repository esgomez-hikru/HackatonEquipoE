# Infrastructure Guidelines

## Why LangChain?

This project uses **LangChain** as the primary framework for LLM interactions. Here's why:

### Key Benefits
1. **Provider Agnostic:** Switch between OpenAI, Gemini, Claude, or local models with minimal code changes
2. **Built-in Retry Logic:** Automatic exponential backoff and retry handling
3. **Structured Outputs:** Easy Pydantic integration for type-safe, validated responses
4. **Prompt Management:** Reusable prompt templates with variable substitution
5. **Caching:** Built-in caching to reduce API calls and costs
6. **Monitoring:** Callbacks and LangSmith integration for observability
7. **Async Support:** First-class async support for concurrent processing
8. **Streaming:** Easy streaming responses for better user experience
9. **Composability:** LCEL (LangChain Expression Language) for building complex chains
10. **Community & Ecosystem:** Large ecosystem of integrations and tools

### Simplified Code
Without LangChain, you need provider-specific code:
```python
# Without LangChain (complex, provider-specific)
if provider == "openai":
    response = openai_client.create(...)
elif provider == "gemini":
    response = gemini_client.generate(...)
# Different parsing, retry logic, error handling for each
```

With LangChain (simple, unified):
```python
# With LangChain (simple, provider-agnostic)
chain = prompt | llm | parser
result = chain.invoke({"input": data})
# Same code works for all providers!
```

## Technology Stack

### Core Technologies
- **Programming Language:** Python 3.9+
- **PDF Processing:**
  - `PyPDF2` or `pdfplumber` for PDF text extraction
  - `pypdf` (modern alternative to PyPDF2)
  - Optional: `pytesseract` + `pdf2image` for OCR on scanned PDFs
- **LLM Framework:** 
  - **`langchain`** for LLM orchestration, prompt management, and unified API (PRIMARY)
  - `langchain-openai` for OpenAI integration (GPT-4, GPT-3.5)
  - `langchain-google-genai` for Google Gemini integration (Gemini Pro, Gemini Pro Vision)
  - `langchain-anthropic` for Claude integration (alternative)
  - `langchain-community` for additional integrations (Ollama, HuggingFace, etc.)
  - **Note:** Use LangChain abstractions exclusively - avoid native provider APIs
- **Storage:**
  - Local filesystem as default option
- **Data Processing:**
  - `pandas` for data manipulation
  - `json` for JSON parsing
- **Export:**
  - `csv` module or `pandas.to_csv()` for CSV export

### Recommended Dependencies
```python
# PDF Processing
pdfplumber>=0.10.0  # PDF text extraction (recommended)
# Alternative: PyPDF2>=3.0.0 or pypdf>=3.0.0
# Optional for OCR: pytesseract>=0.3.10, pdf2image>=1.16.0, Pillow>=10.0.0

# Core LLM/AI (Use LangChain exclusively)
langchain>=0.1.0  # LLM orchestration and prompt management
langchain-core>=0.1.0  # Core LangChain abstractions
langchain-openai>=0.0.5  # LangChain OpenAI integration
langchain-google-genai>=0.0.5  # LangChain Google Gemini integration
langchain-anthropic>=0.0.5  # LangChain Anthropic integration (optional)
langchain-community>=0.0.10  # Additional integrations (Ollama, HuggingFace, etc.)

# Optional: LangChain Utilities
langsmith>=0.0.70  # Observability and debugging (optional but recommended)
langserve>=0.0.30  # Deploy LangChain as REST API (optional)

# Note: Do NOT install openai, google-generativeai, or anthropic directly
# LangChain packages include necessary dependencies

# Data Processing
pandas>=2.0.0
numpy>=1.24.0

# Utilities
python-dotenv>=1.0.0  # Environment variables
pydantic>=2.0.0  # Data validation
tenacity>=8.2.0  # Retry logic for API calls
```

## Project Structure

```
HackatonEquipoE/
├── src/
│   ├── __init__.py
│   ├── pdf_processing/
│   │   ├── __init__.py
│   │   ├── pdf_extractor.py      # Extract text from PDFs
│   │   └── pdf_validator.py      # Validate PDF files
│   ├── preprocessing/
│   │   ├── __init__.py
│   │   ├── resume_parser.py      # Parse text to structured JSON resumes
│   │   └── jd_parser.py          # Parse text to structured JSON job descriptions
│   ├── llm/
│   │   ├── __init__.py
│   │   ├── client.py              # LLM client initialization
│   │   ├── analyzer.py            # LLM-based resume/JD analysis
│   │   └── response_parser.py    # Parse LLM structured responses
│   ├── prompts/
│   │   ├── __init__.py
│   │   ├── scoring_prompt.py      # Prompt for scoring candidates
│   │   ├── similarity_prompt.py   # Prompt for similarity analysis
│   │   ├── reason_codes_prompt.py # Prompt for reason code generation
│   │   └── templates/             # Prompt templates directory
│   │       ├── scoring_template.txt
│   │       ├── similarity_template.txt
│   │       └── reason_codes_template.txt
│   ├── scoring/
│   │   ├── __init__.py
│   │   ├── hybrid_scorer.py      # Hybrid scoring system
│   │   └── rule_boosts.py         # Rule-based boosts
│   ├── explainability/
│   │   ├── __init__.py
│   │   ├── reason_codes.py       # Reason code mapping
│   │   └── hit_mapper.py         # Map hits to resume lines
│   ├── export/
│   │   ├── __init__.py
│   │   └── csv_exporter.py       # CSV export functionality
│   ├── storage/
│   │   ├── __init__.py
│   │   ├── storage_client.py     # Storage client abstraction
│   │   └── local_storage.py      # Local filesystem storage
│   ├── config.py                  # Configuration management
│   └── main.py                   # Main application entry point
├── data/
│   ├── resumes/                  # Input resume files
│   │   ├── raw/                  # Original PDF files
│   │   └── processed/            # Extracted JSON files
│   ├── job_descriptions/         # Input JD files
│   │   ├── raw/                  # Original PDF files
│   │   └── processed/            # Extracted JSON files
│   ├── storage/                  # Storage for JSONs
│   │   ├── resumes/              # Stored resume JSONs
│   │   └── job_descriptions/     # Stored JD JSONs
│   └── output/                   # Generated CSV exports
├── prompts/                      # Centralized prompt definitions
│   ├── scoring_prompts.yaml      # YAML format prompts (optional)
│   └── prompt_library.md         # Documentation of all prompts
├── tests/
│   ├── __init__.py
│   ├── test_pdf_extraction.py
│   ├── test_preprocessing.py
│   ├── test_llm.py
│   ├── test_scoring.py
│   └── test_export.py
├── docs/
│   ├── PROJECT_GUIDELINES.md
│   ├── INFRA_GUIDELINES.MD
│   └── REQUIREMENTES.md
├── requirements.txt
├── .env.example
├── .gitignore
└── README.md
```

## Environment Setup

### Python Environment
```bash
# Create virtual environment
python -m venv venv

# Activate virtual environment
# On macOS/Linux:
source venv/bin/activate
# On Windows:
venv\Scripts\activate

# Install dependencies
pip install -r requirements.txt
```

### Environment Variables
Create a `.env` file (use `.env.example` as template):
```env
# LLM Provider Configuration (LangChain Unified)
LLM_PROVIDER=openai  # Options: openai, gemini, anthropic, ollama

# OpenAI Configuration (if LLM_PROVIDER=openai)
OPENAI_API_KEY=your_openai_api_key_here
OPENAI_MODEL=gpt-4-turbo-preview  # Options: gpt-4, gpt-4-turbo-preview, gpt-3.5-turbo

# Google Gemini Configuration (if LLM_PROVIDER=gemini)
GOOGLE_API_KEY=your_google_api_key_here
GEMINI_MODEL=gemini-pro  # Options: gemini-pro, gemini-pro-vision

# Anthropic Configuration (if LLM_PROVIDER=anthropic)
# ANTHROPIC_API_KEY=your_anthropic_key
# ANTHROPIC_MODEL=claude-3-opus-20240229

# Ollama Configuration (if LLM_PROVIDER=ollama)
# OLLAMA_BASE_URL=http://localhost:11434
# OLLAMA_MODEL=llama2

# LLM Configuration
LLM_TEMPERATURE=0.3  # Lower temperature for more consistent scoring
LLM_MAX_TOKENS=2000  # Maximum tokens for LLM responses
LLM_TIMEOUT=60  # Timeout in seconds for API calls

# Scoring Weights (0.0 to 1.0)
SIMILARITY_WEIGHT=0.6
MUST_HAVE_BOOST_WEIGHT=0.3
RECENCY_BOOST_WEIGHT=0.1

# Storage Configuration
STORAGE_TYPE=local  # Local filesystem storage only
STORAGE_PATH=./data/storage  # Local path for storing JSONs

# Output Configuration
OUTPUT_DIR=./data/output
CSV_ENCODING=utf-8

# Retry Configuration
API_MAX_RETRIES=3
API_RETRY_DELAY=2  # seconds
```

## Data Handling

### Input Format

#### PDF Input (Primary)
- **Resume PDFs:** Place PDF files in `data/resumes/raw/` directory
- **Job Description PDFs:** Place PDF files in `data/job_descriptions/raw/` directory
- **Supported Formats:** PDF files with native text (preferred) or scanned PDFs (requires OCR)

#### Resume JSON Structure (After PDF Processing)
```json
{
  "candidate_id": "unique_id",
  "name": "Candidate Name",
  "skills": ["skill1", "skill2", "skill3"],
  "experience": [
    {
      "company": "Company Name",
      "position": "Job Title",
      "start_date": "2020-01",
      "end_date": "2022-12",
      "description": "Job description text"
    }
  ],
  "education": [
    {
      "institution": "University Name",
      "degree": "Degree Type",
      "field": "Field of Study",
      "year": 2020
    }
  ],
  "raw_text": "Full extracted text from resume"
}
```

#### Job Description Format (After PDF Processing)
```json
{
  "jd_id": "unique_jd_id",
  "title": "Job Title",
  "must_have_requirements": [
    "requirement1",
    "requirement2"
  ],
  "nice_to_have": [
    "requirement3"
  ],
  "description": "Full job description text",
  "experience_years_required": 3
}
```

### Output Format

#### CSV Export Structure
```csv
rank,candidate_id,name,overall_score,similarity_score,must_have_hits,recency_boost,reason_codes,matched_requirements
1,id_001,John Doe,0.85,0.72,3,0.13,"SKILL_MATCH,EXPERIENCE_MATCH,RECENT_EXP","Python,5+ years,2023"
```

## Development Workflow

### Code Standards
- **Style:** Follow PEP 8 Python style guide
- **Type Hints:** Use type hints for function signatures
- **Documentation:** Docstrings for all functions and classes
- **Linting:** Use `flake8` or `pylint`
- **Formatting:** Use `black` for code formatting

### Testing Strategy
- **Unit Tests:** Test individual components (PDF extraction, preprocessing, LLM, scoring)
- **Integration Tests:** Test end-to-end workflow (PDF → JSON → Analysis → Export)
- **Test Data:** Use sample PDF resumes and JDs in `tests/fixtures/`

### Version Control
- **Branch Strategy:** Feature branches from `main`
- **Commit Messages:** Clear, descriptive commit messages
- **Git Ignore:** Exclude `venv/`, `__pycache__/`, `.env`, `data/output/`

## Prompt Management

### Prompt Structure
Prompts should be defined in `src/prompts/` directory and follow a consistent structure:

#### Prompt Template Format
```python
# src/prompts/scoring_prompt.py
SCORING_PROMPT = """
You are an expert recruiter analyzing candidate resumes against a job description.

Job Description:
{job_description}

Must-Have Requirements:
{must_have_requirements}

Candidate Resume:
{resume_text}

Please analyze and provide:
1. Overall match score (0-100)
2. Similarity score (0-100)
3. Must-have requirements matched (list)
4. Reason codes explaining the match
5. Specific resume sections that match JD requirements

Format your response as JSON:
{{
    "overall_score": <float>,
    "similarity_score": <float>,
    "must_have_matches": [<list>],
    "reason_codes": [<list>],
    "matched_sections": {{
        "requirement": "resume_section_reference"
    }}
}}
"""
```

### Prompt Organization
- **Centralized Storage:** Store all prompts in `src/prompts/` directory
- **Template Files:** Use `.txt` files for complex multi-line prompts
- **Python Modules:** Use `.py` files for prompts with dynamic variables
- **Version Control:** Track prompt changes in git
- **Documentation:** Document prompt purpose and expected output format

### Prompt Best Practices
- **Clear Instructions:** Provide explicit instructions to the LLM
- **Structured Output:** Request JSON or structured format for parsing
- **Examples:** Include few-shot examples when helpful
- **Constraints:** Specify score ranges, format requirements
- **Context:** Provide sufficient context (JD, resume, requirements)

## Performance Considerations

### LLM API Usage with LangChain
- **Rate Limiting:** LangChain handles rate limiting automatically via `max_retries`
- **Caching:** Use `InMemoryCache` or `SQLiteCache` for automatic response caching
- **Batch Processing:** Use `chain.batch()` for efficient batch processing
- **Async Processing:** Use `chain.ainvoke()` and `chain.astream()` for concurrent processing
- **Token Management:** Track usage via callbacks and LangSmith
- **Streaming:** Use `chain.stream()` for real-time feedback and better UX

### Model Selection
- **Cost vs Quality:** Balance between model cost and accuracy
  - GPT-3.5-turbo: Faster, cheaper, good for initial filtering
  - GPT-4: More accurate, better reasoning, higher cost
  - Gemini Pro: Competitive performance, good cost-performance ratio
  - Claude: Alternative with excellent reasoning capabilities
- **Temperature:** Use lower temperature (0.1-0.3) for consistent scoring
- **LangChain Benefit:** Switch between providers by changing one configuration variable

### Memory Management
- **Streaming:** Process large datasets in batches
- **Response Caching:** Cache parsed LLM responses to avoid reprocessing
- **Resource Limits:** Set memory limits for production
- **Connection Pooling:** Reuse API connections when possible

## Security & Privacy

### Data Protection
- **Sensitive Data:** Do not commit resume data or personal information to git
- **Environment Variables:** Store API keys and secrets in `.env` (not committed)
- **Data Anonymization:** Consider anonymizing candidate data for testing

### Access Control
- **File Permissions:** Restrict access to data directories
- **API Keys:** Rotate keys regularly if using external APIs

## Deployment Considerations

### Local Development
- **Requirements:** Python 3.9+, 4GB+ RAM recommended
- **Dependencies:** Install via `requirements.txt`
- **Data:** Place sample data in `data/` directories

### Production Deployment
- **Containerization:** Consider Docker for consistent environments
- **API Service:** If needed, use FastAPI or Flask for REST API
- **Scaling:** Consider async processing for large batches
- **Monitoring:** Log scoring metrics and performance

## Configuration Management

### Configuration Files
- **LLM Config:** Store LLM provider and model selection in config
- **Prompt Config:** Centralize prompt loading and management
- **Scoring Weights:** Make weights configurable via environment variables
- **File Paths:** Use relative paths with configurable base directory

### Example Configuration Class
```python
# src/config.py
from dataclasses import dataclass
import os
from dotenv import load_dotenv

load_dotenv()

@dataclass
class Config:
    # LLM Configuration (LangChain Unified)
    llm_provider: str = os.getenv("LLM_PROVIDER", "openai")
    
    # OpenAI Configuration
    openai_api_key: str = os.getenv("OPENAI_API_KEY", "")
    openai_model: str = os.getenv("OPENAI_MODEL", "gpt-4-turbo-preview")
    
    # Google Gemini Configuration
    google_api_key: str = os.getenv("GOOGLE_API_KEY", "")
    gemini_model: str = os.getenv("GEMINI_MODEL", "gemini-pro")
    
    # Anthropic Configuration
    anthropic_api_key: str = os.getenv("ANTHROPIC_API_KEY", "")
    anthropic_model: str = os.getenv("ANTHROPIC_MODEL", "claude-3-opus-20240229")
    
    # Ollama Configuration
    ollama_base_url: str = os.getenv("OLLAMA_BASE_URL", "http://localhost:11434")
    ollama_model: str = os.getenv("OLLAMA_MODEL", "llama2")
    
    # Common LLM Parameters
    llm_temperature: float = float(os.getenv("LLM_TEMPERATURE", "0.3"))
    llm_max_tokens: int = int(os.getenv("LLM_MAX_TOKENS", "2000"))
    llm_timeout: int = int(os.getenv("LLM_TIMEOUT", "60"))
    
    # Note: Retry configuration handled by LangChain internally (max_retries parameter)
    
    # Scoring Weights
    similarity_weight: float = float(os.getenv("SIMILARITY_WEIGHT", "0.6"))
    must_have_boost_weight: float = float(os.getenv("MUST_HAVE_BOOST_WEIGHT", "0.3"))
    recency_boost_weight: float = float(os.getenv("RECENCY_BOOST_WEIGHT", "0.1"))
    
    # Output Configuration
    output_dir: str = os.getenv("OUTPUT_DIR", "./data/output")
    csv_encoding: str = os.getenv("CSV_ENCODING", "utf-8")
    
    # Prompt Paths
    prompts_dir: str = os.getenv("PROMPTS_DIR", "./src/prompts")
```

### Prompt Loading Example
```python
# src/prompts/prompt_loader.py
from pathlib import Path
from typing import Dict

class PromptLoader:
    def __init__(self, prompts_dir: str = "./src/prompts"):
        self.prompts_dir = Path(prompts_dir)
        self._prompts: Dict[str, str] = {}
    
    def load_prompt(self, prompt_name: str) -> str:
        """Load prompt from file or cache."""
        if prompt_name in self._prompts:
            return self._prompts[prompt_name]
        
        prompt_file = self.prompts_dir / f"{prompt_name}.txt"
        if prompt_file.exists():
            with open(prompt_file, 'r', encoding='utf-8') as f:
                prompt = f.read()
                self._prompts[prompt_name] = prompt
                return prompt
        
        raise FileNotFoundError(f"Prompt file not found: {prompt_file}")
    
    def format_prompt(self, prompt_name: str, **kwargs) -> str:
        """Load and format prompt with variables."""
        prompt_template = self.load_prompt(prompt_name)
        return prompt_template.format(**kwargs)
```

## Logging & Monitoring

### Logging Setup
```python
import logging

logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('app.log'),
        logging.StreamHandler()
    ]
)
```

### Key Metrics to Track
- Processing time per resume
- LLM API call time and latency
- Token usage per request (input/output)
- API cost per candidate
- Scoring calculation time
- Memory usage
- Error rates and retry counts
- Rate limit hits

## Error Handling

### Exception Handling
- **File Not Found:** Handle missing input files gracefully
- **Invalid JSON:** Validate JSON structure before processing
- **API Errors:** Handle LLM API errors (rate limits, timeouts, invalid responses)
- **Rate Limiting:** Implement exponential backoff for rate limit errors
- **Timeout Errors:** Retry on timeout with exponential backoff
- **Invalid Responses:** Validate LLM response format and retry if invalid
- **Memory Errors:** Catch and handle out-of-memory situations

### Validation
- **Input Validation:** Validate resume and JD structure
- **Score Validation:** Ensure scores are within expected ranges (0-1)
- **Output Validation:** Verify CSV export integrity

## Dependencies Management

### requirements.txt Structure
```
# PDF Processing
pdfplumber>=0.10.0  # PDF text extraction
# Optional for OCR: pytesseract>=0.3.10, pdf2image>=1.16.0, Pillow>=10.0.0

# Core LLM/AI
openai>=1.0.0
google-generativeai>=0.3.0
langchain>=0.1.0
langchain-openai>=0.0.5
langchain-google-genai>=0.0.5
# Alternative: anthropic>=0.18.0  # For Claude API

# Data Processing
pandas>=2.0.0
numpy>=1.24.0

# Utilities
python-dotenv>=1.0.0
pydantic>=2.0.0
pyyaml>=6.0  # For YAML prompt files (optional)
# Note: tenacity not needed - LangChain handles retries internally

# Frontend (Optional - if building web interface)
fastapi>=0.104.0  # Modern web framework
uvicorn>=0.24.0  # ASGI server
jinja2>=3.1.2  # Templating engine
python-multipart>=0.0.6  # For file uploads

# Development
pytest>=7.4.0
black>=23.0.0
flake8>=6.0.0
```

### Dependency Updates
- **Regular Updates:** Keep dependencies updated for security
- **Version Pinning:** Pin major versions, allow minor/patch updates
- **Security Audits:** Run `pip-audit` or `safety` for vulnerability checks

## LLM Integration with LangChain

### Why Use LangChain?
- **Provider Agnostic:** Switch between OpenAI, Gemini, Claude, or Ollama with minimal code changes
- **Built-in Retry Logic:** Automatic retries with exponential backoff
- **Prompt Management:** PromptTemplates and ChatPromptTemplates for structured prompts
- **Output Parsing:** Built-in parsers for JSON, structured data, and more
- **Streaming Support:** Easy streaming responses for better UX
- **Callback Support:** Built-in logging, tracing, and monitoring

### LangChain LLM Client Setup
```python
# src/llm/client.py
from langchain_openai import ChatOpenAI
from langchain_google_genai import ChatGoogleGenerativeAI
from langchain_anthropic import ChatAnthropic
from langchain_community.llms import Ollama
from langchain_core.language_models import BaseChatModel
from langchain_core.prompts import ChatPromptTemplate, SystemMessagePromptTemplate, HumanMessagePromptTemplate
from langchain_core.output_parsers import JsonOutputParser, StrOutputParser
from langchain_core.runnables import RunnablePassthrough
from src.config import Config
import logging

logger = logging.getLogger(__name__)

class LLMClient:
    """Unified LLM client using LangChain abstractions."""
    
    def __init__(self, config: Config):
        self.config = config
        self.llm = self._initialize_llm()
        self.json_parser = JsonOutputParser()
        
    def _initialize_llm(self) -> BaseChatModel:
        """Initialize LLM based on provider configuration."""
        provider = self.config.llm_provider.lower()
        
        # Common parameters
        common_params = {
            "temperature": self.config.llm_temperature,
            "max_tokens": self.config.llm_max_tokens,
            "max_retries": 3,  # LangChain handles retries automatically
        }
        
        if provider == "openai":
            return ChatOpenAI(
                model=self.config.openai_model,
                openai_api_key=self.config.openai_api_key,
                **common_params
            )
        
        elif provider == "gemini":
            return ChatGoogleGenerativeAI(
                model=self.config.gemini_model,
                google_api_key=self.config.google_api_key,
                temperature=self.config.llm_temperature,
                max_output_tokens=self.config.llm_max_tokens,
                max_retries=3,
            )
        
        elif provider == "anthropic":
            return ChatAnthropic(
                model=self.config.anthropic_model,
                anthropic_api_key=self.config.anthropic_api_key,
                **common_params
            )
        
        elif provider == "ollama":
            return Ollama(
                base_url=self.config.ollama_base_url,
                model=self.config.ollama_model,
                temperature=self.config.llm_temperature,
            )
        
        else:
            raise ValueError(f"Unsupported LLM provider: {provider}")
    
    def analyze_candidate(self, prompt: str, parse_json: bool = True) -> dict | str:
        """
        Analyze candidate with LLM.
        
        Args:
            prompt: The prompt text
            parse_json: Whether to parse response as JSON
            
        Returns:
            Parsed JSON dict or raw string response
        """
        try:
            # Create chat prompt template
            chat_prompt = ChatPromptTemplate.from_messages([
                SystemMessagePromptTemplate.from_template(
                    "You are an expert recruiter analyzing candidate resumes. "
                    "Always respond with valid JSON format."
                ),
                HumanMessagePromptTemplate.from_template("{prompt}")
            ])
            
            # Create chain with output parser
            if parse_json:
                chain = chat_prompt | self.llm | self.json_parser
            else:
                chain = chat_prompt | self.llm | StrOutputParser()
            
            # Invoke chain (automatic retry on failure)
            result = chain.invoke({"prompt": prompt})
            
            logger.info(f"Successfully analyzed candidate with {self.config.llm_provider}")
            return result
            
        except Exception as e:
            logger.error(f"Error analyzing candidate: {e}")
            raise
    
    def batch_analyze(self, prompts: list[str], parse_json: bool = True) -> list[dict | str]:
        """
        Batch analyze multiple candidates.
        
        Args:
            prompts: List of prompts
            parse_json: Whether to parse responses as JSON
            
        Returns:
            List of parsed results
        """
        try:
            chat_prompt = ChatPromptTemplate.from_messages([
                SystemMessagePromptTemplate.from_template(
                    "You are an expert recruiter analyzing candidate resumes. "
                    "Always respond with valid JSON format."
                ),
                HumanMessagePromptTemplate.from_template("{prompt}")
            ])
            
            if parse_json:
                chain = chat_prompt | self.llm | self.json_parser
            else:
                chain = chat_prompt | self.llm | StrOutputParser()
            
            # Batch invoke (more efficient than individual calls)
            results = chain.batch([{"prompt": p} for p in prompts])
            
            logger.info(f"Successfully batch analyzed {len(prompts)} candidates")
            return results
            
        except Exception as e:
            logger.error(f"Error in batch analysis: {e}")
            raise
    
    async def analyze_candidate_async(self, prompt: str, parse_json: bool = True) -> dict | str:
        """
        Async version for concurrent processing.
        
        Args:
            prompt: The prompt text
            parse_json: Whether to parse response as JSON
            
        Returns:
            Parsed JSON dict or raw string response
        """
        try:
            chat_prompt = ChatPromptTemplate.from_messages([
                SystemMessagePromptTemplate.from_template(
                    "You are an expert recruiter analyzing candidate resumes. "
                    "Always respond with valid JSON format."
                ),
                HumanMessagePromptTemplate.from_template("{prompt}")
            ])
            
            if parse_json:
                chain = chat_prompt | self.llm | self.json_parser
            else:
                chain = chat_prompt | self.llm | StrOutputParser()
            
            # Async invoke
            result = await chain.ainvoke({"prompt": prompt})
            
            logger.info(f"Successfully analyzed candidate asynchronously")
            return result
            
        except Exception as e:
            logger.error(f"Error in async analysis: {e}")
            raise
    
    def get_provider_info(self) -> dict:
        """Get information about the current LLM provider."""
        return {
            "provider": self.config.llm_provider,
            "model": getattr(self.config, f"{self.config.llm_provider}_model", "unknown"),
            "temperature": self.config.llm_temperature,
            "max_tokens": self.config.llm_max_tokens,
        }
```

### Using LangChain PromptTemplates
```python
# src/llm/analyzer.py
from langchain_core.prompts import PromptTemplate, ChatPromptTemplate
from langchain_core.output_parsers import JsonOutputParser
from langchain_core.pydantic_v1 import BaseModel, Field
from src.llm.client import LLMClient
from typing import List
import logging

logger = logging.getLogger(__name__)

# Define output schema using Pydantic
class CandidateScore(BaseModel):
    """Schema for candidate scoring output."""
    overall_score: float = Field(description="Overall match score (0-100)")
    similarity_score: float = Field(description="Semantic similarity score (0-100)")
    must_have_matches: List[str] = Field(description="List of must-have requirements matched")
    reason_codes: List[str] = Field(description="Reason codes explaining the match")
    matched_sections: dict = Field(description="Mapping of requirements to resume sections")

class ResumeAnalyzer:
    """Analyzer using LangChain prompts and structured output."""
    
    def __init__(self, llm_client: LLMClient):
        self.llm_client = llm_client
        self.parser = JsonOutputParser(pydantic_object=CandidateScore)
        
        # Define scoring prompt template
        self.scoring_template = ChatPromptTemplate.from_messages([
            ("system", 
             "You are an expert recruiter analyzing candidate resumes against job descriptions. "
             "Provide detailed, objective analysis.\n\n"
             "{format_instructions}"
            ),
            ("human",
             "Job Description:\n{job_description}\n\n"
             "Must-Have Requirements:\n{must_have_requirements}\n\n"
             "Candidate Resume:\n{resume_text}\n\n"
             "Please analyze and provide:\n"
             "1. Overall match score (0-100)\n"
             "2. Similarity score (0-100)\n"
             "3. Must-have requirements matched (list)\n"
             "4. Reason codes explaining the match\n"
             "5. Specific resume sections that match JD requirements"
            )
        ])
    
    def score_candidate(self, resume: dict, job_description: dict) -> CandidateScore:
        """
        Score a candidate against a job description using LangChain.
        
        Args:
            resume: Resume dictionary with candidate information
            job_description: Job description dictionary
            
        Returns:
            CandidateScore object with structured scoring results
        """
        try:
            # Create chain with prompt, LLM, and parser
            chain = self.scoring_template | self.llm_client.llm | self.parser
            
            # Invoke chain with structured input
            result = chain.invoke({
                "job_description": job_description["description"],
                "must_have_requirements": "\n".join(job_description["must_have_requirements"]),
                "resume_text": resume["raw_text"],
                "format_instructions": self.parser.get_format_instructions()
            })
            
            logger.info(f"Scored candidate {resume.get('candidate_id', 'unknown')}")
            return result
            
        except Exception as e:
            logger.error(f"Error scoring candidate: {e}")
            raise
    
    def batch_score_candidates(
        self, 
        resumes: List[dict], 
        job_description: dict
    ) -> List[CandidateScore]:
        """
        Score multiple candidates in batch.
        
        Args:
            resumes: List of resume dictionaries
            job_description: Job description dictionary
            
        Returns:
            List of CandidateScore objects
        """
        try:
            chain = self.scoring_template | self.llm_client.llm | self.parser
            
            # Prepare batch inputs
            inputs = [
                {
                    "job_description": job_description["description"],
                    "must_have_requirements": "\n".join(job_description["must_have_requirements"]),
                    "resume_text": resume["raw_text"],
                    "format_instructions": self.parser.get_format_instructions()
                }
                for resume in resumes
            ]
            
            # Batch process
            results = chain.batch(inputs)
            
            logger.info(f"Batch scored {len(resumes)} candidates")
            return results
            
        except Exception as e:
            logger.error(f"Error in batch scoring: {e}")
            raise
    
    async def score_candidate_async(
        self, 
        resume: dict, 
        job_description: dict
    ) -> CandidateScore:
        """Async version for concurrent processing."""
        try:
            chain = self.scoring_template | self.llm_client.llm | self.parser
            
            result = await chain.ainvoke({
                "job_description": job_description["description"],
                "must_have_requirements": "\n".join(job_description["must_have_requirements"]),
                "resume_text": resume["raw_text"],
                "format_instructions": self.parser.get_format_instructions()
            })
            
            return result
            
        except Exception as e:
            logger.error(f"Error in async scoring: {e}")
            raise
```

## LangChain Advanced Features

### Structured Output with Pydantic
LangChain provides excellent support for structured outputs using Pydantic models:

```python
from langchain_core.pydantic_v1 import BaseModel, Field
from langchain_core.output_parsers import PydanticOutputParser

class ResumeAnalysis(BaseModel):
    """Structured resume analysis output."""
    skills_matched: List[str] = Field(description="Skills that match the job requirements")
    experience_years: int = Field(description="Total years of relevant experience")
    education_level: str = Field(description="Highest education level")
    fit_score: float = Field(description="Overall fit score (0-100)", ge=0, le=100)
    strengths: List[str] = Field(description="Key strengths of the candidate")
    gaps: List[str] = Field(description="Gaps or missing requirements")

# Use with chain
parser = PydanticOutputParser(pydantic_object=ResumeAnalysis)
prompt = PromptTemplate(
    template="Analyze this resume:\n{resume}\n\n{format_instructions}",
    input_variables=["resume"],
    partial_variables={"format_instructions": parser.get_format_instructions()}
)
chain = prompt | llm | parser
result = chain.invoke({"resume": resume_text})
```

### Prompt Templates with Few-Shot Examples
Use few-shot prompting for better consistency:

```python
from langchain_core.prompts import FewShotPromptTemplate, PromptTemplate

# Define examples
examples = [
    {
        "resume": "Software Engineer with 5 years Python experience...",
        "jd": "Looking for Python developer with 3+ years...",
        "output": '{"score": 85, "reason": "Strong Python background, exceeds experience requirement"}'
    },
    {
        "resume": "Junior developer with 1 year experience...",
        "jd": "Senior developer needed with 5+ years...",
        "output": '{"score": 35, "reason": "Experience gap, junior level vs senior requirement"}'
    }
]

# Create example template
example_template = PromptTemplate(
    input_variables=["resume", "jd", "output"],
    template="Resume: {resume}\nJob Description: {jd}\nAnalysis: {output}"
)

# Create few-shot template
few_shot_prompt = FewShotPromptTemplate(
    examples=examples,
    example_prompt=example_template,
    prefix="You are an expert recruiter. Here are some examples of good analysis:",
    suffix="Now analyze this:\nResume: {resume}\nJob Description: {jd}\nAnalysis:",
    input_variables=["resume", "jd"]
)

chain = few_shot_prompt | llm | JsonOutputParser()
```

### Caching for Cost Optimization
LangChain supports caching to reduce API calls and costs:

```python
from langchain.cache import InMemoryCache, SQLiteCache
from langchain.globals import set_llm_cache

# In-memory cache (for development)
set_llm_cache(InMemoryCache())

# Or persistent SQLite cache (for production)
set_llm_cache(SQLiteCache(database_path=".langchain.db"))

# Now repeated queries will use cached results
# Huge cost savings when reprocessing same candidates
```

### Callbacks for Monitoring
Track token usage, latency, and errors:

```python
from langchain.callbacks import StdOutCallbackHandler
from langchain.callbacks.manager import CallbackManager

# Create callback handler
callback_manager = CallbackManager([StdOutCallbackHandler()])

# Initialize LLM with callbacks
llm = ChatOpenAI(
    model="gpt-4",
    callbacks=callback_manager,
    verbose=True
)

# Custom callback for tracking metrics
from langchain.callbacks.base import BaseCallbackHandler

class MetricsCallback(BaseCallbackHandler):
    def __init__(self):
        self.total_tokens = 0
        self.total_cost = 0
    
    def on_llm_end(self, response, **kwargs):
        """Track token usage."""
        tokens = response.llm_output.get("token_usage", {})
        self.total_tokens += tokens.get("total_tokens", 0)
        # Calculate cost based on model pricing
        
    def on_llm_error(self, error, **kwargs):
        """Log errors."""
        logger.error(f"LLM error: {error}")

metrics = MetricsCallback()
llm = ChatOpenAI(callbacks=[metrics])
```

### Async Processing for Better Performance
Process multiple candidates concurrently:

```python
import asyncio
from typing import List

async def process_candidates_concurrent(
    analyzer: ResumeAnalyzer,
    resumes: List[dict],
    job_description: dict,
    max_concurrent: int = 5
) -> List[CandidateScore]:
    """Process candidates with controlled concurrency."""
    
    semaphore = asyncio.Semaphore(max_concurrent)
    
    async def score_with_semaphore(resume):
        async with semaphore:
            return await analyzer.score_candidate_async(resume, job_description)
    
    # Process all candidates concurrently
    tasks = [score_with_semaphore(resume) for resume in resumes]
    results = await asyncio.gather(*tasks, return_exceptions=True)
    
    # Filter out errors
    valid_results = [r for r in results if not isinstance(r, Exception)]
    return valid_results

# Usage
results = asyncio.run(process_candidates_concurrent(analyzer, resumes, job_desc))
```

### LangChain Expression Language (LCEL)
Use LCEL for composable chains:

```python
from langchain_core.runnables import RunnableParallel, RunnablePassthrough

# Create parallel processing chain
chain = RunnableParallel(
    {
        "skills_analysis": skills_prompt | llm | JsonOutputParser(),
        "experience_analysis": experience_prompt | llm | JsonOutputParser(),
        "education_analysis": education_prompt | llm | JsonOutputParser(),
    }
)

# All three analyses run in parallel
result = chain.invoke({"resume": resume_text, "jd": job_desc})

# Combine results
final_score = combine_analyses(
    result["skills_analysis"],
    result["experience_analysis"],
    result["education_analysis"]
)
```

### Error Handling and Fallbacks
LangChain provides built-in fallback mechanisms:

```python
from langchain_core.runnables import RunnableLambda

# Define fallback function
def fallback_analysis(inputs):
    """Fallback to rule-based analysis if LLM fails."""
    return {
        "score": 50,
        "reason": "Using fallback analysis due to LLM error",
        "method": "rule-based"
    }

# Create chain with fallback
primary_chain = prompt | llm | parser
fallback_chain = RunnableLambda(fallback_analysis)

# Combine with fallback
chain = primary_chain.with_fallbacks([fallback_chain])

# Will automatically use fallback if primary fails
result = chain.invoke({"resume": resume_text})
```

### Streaming Responses for Better UX
Stream LLM responses for real-time feedback:

```python
# Streaming with LangChain
def stream_analysis(resume: dict, job_description: dict):
    """Stream analysis results in real-time."""
    prompt = ChatPromptTemplate.from_messages([
        ("system", "You are an expert recruiter."),
        ("human", "Analyze this resume: {resume}")
    ])
    
    chain = prompt | llm
    
    # Stream tokens as they arrive
    for chunk in chain.stream({"resume": resume["raw_text"]}):
        print(chunk.content, end="", flush=True)
        # Or yield for web streaming
        yield chunk.content

# Async streaming
async def stream_analysis_async(resume: dict, job_description: dict):
    """Async streaming for web applications."""
    prompt = ChatPromptTemplate.from_messages([
        ("system", "You are an expert recruiter."),
        ("human", "Analyze this resume: {resume}")
    ])
    
    chain = prompt | llm
    
    async for chunk in chain.astream({"resume": resume["raw_text"]}):
        yield chunk.content
```

### LangSmith Integration (Optional)
LangSmith provides observability and debugging:

```python
import os

# Enable LangSmith tracing
os.environ["LANGCHAIN_TRACING_V2"] = "true"
os.environ["LANGCHAIN_API_KEY"] = "your_langsmith_api_key"
os.environ["LANGCHAIN_PROJECT"] = "ai-talent-matcher"

# All chains will now be automatically traced
# View in LangSmith dashboard for debugging and optimization
```

## Storage Integration Example

### Storage Client Abstraction
```python
# src/storage/storage_client.py
from abc import ABC, abstractmethod
from typing import List, Optional, Dict
import json

class StorageClient(ABC):
    """Abstract base class for storage implementations."""
    
    @abstractmethod
    def save_json(self, json_data: dict, file_name: str, file_type: str) -> bool:
        """Save JSON to storage. file_type: 'resume' or 'job_description'."""
        pass
    
    @abstractmethod
    def list_jsons(self, file_type: str, filters: Optional[Dict] = None) -> List[Dict]:
        """List available JSONs. Returns list of metadata dicts."""
        pass
    
    @abstractmethod
    def get_json(self, file_name: str, file_type: str) -> Optional[dict]:
        """Retrieve JSON from storage."""
        pass
    
    @abstractmethod
    def delete_json(self, file_name: str, file_type: str) -> bool:
        """Delete JSON from storage."""
        pass
    
    @abstractmethod
    def search_jsons(self, query: str, file_type: str) -> List[Dict]:
        """Search JSONs by name or content."""
        pass

# src/storage/local_storage.py
from pathlib import Path
from typing import List, Optional, Dict
import json
from datetime import datetime
from .storage_client import StorageClient

class LocalStorage(StorageClient):
    def __init__(self, base_path: str = "./data/storage"):
        self.base_path = Path(base_path)
        self.base_path.mkdir(parents=True, exist_ok=True)
    
    def save_json(self, json_data: dict, file_name: str, file_type: str) -> bool:
        """Save JSON to local filesystem."""
        try:
            file_path = self.base_path / file_type / f"{file_name}.json"
            file_path.parent.mkdir(parents=True, exist_ok=True)
            
            with open(file_path, 'w', encoding='utf-8') as f:
                json.dump(json_data, f, indent=2, ensure_ascii=False)
            return True
        except Exception as e:
            print(f"Error saving JSON: {e}")
            return False
    
    def list_jsons(self, file_type: str, filters: Optional[Dict] = None) -> List[Dict]:
        """List available JSONs with metadata."""
        json_dir = self.base_path / file_type
        if not json_dir.exists():
            return []
        
        json_files = []
        for json_file in json_dir.glob("*.json"):
            try:
                with open(json_file, 'r', encoding='utf-8') as f:
                    data = json.load(f)
                    metadata = {
                        "file_name": json_file.stem,
                        "full_path": str(json_file),
                        "size": json_file.stat().st_size,
                        "modified": datetime.fromtimestamp(json_file.stat().st_mtime).isoformat(),
                        "name": data.get("name") or data.get("title", "Unknown")
                    }
                    json_files.append(metadata)
            except:
                continue
        
        return json_files
    
    def get_json(self, file_name: str, file_type: str) -> Optional[dict]:
        """Retrieve JSON from local storage."""
        file_path = self.base_path / file_type / f"{file_name}.json"
        if not file_path.exists():
            return None
        
        try:
            with open(file_path, 'r', encoding='utf-8') as f:
                return json.load(f)
        except Exception as e:
            print(f"Error reading JSON: {e}")
            return None
    
    def delete_json(self, file_name: str, file_type: str) -> bool:
        """Delete JSON from local storage."""
        file_path = self.base_path / file_type / f"{file_name}.json"
        if file_path.exists():
            file_path.unlink()
            return True
        return False
    
    def search_jsons(self, query: str, file_type: str) -> List[Dict]:
        """Search JSONs by name or content."""
        all_jsons = self.list_jsons(file_type)
        query_lower = query.lower()
        
        results = []
        for json_meta in all_jsons:
            # Search in file name
            if query_lower in json_meta["file_name"].lower():
                results.append(json_meta)
                continue
            
            # Search in JSON content
            json_data = self.get_json(json_meta["file_name"], file_type)
            if json_data:
                json_str = json.dumps(json_data).lower()
                if query_lower in json_str:
                    results.append(json_meta)
        
        return results
```

## PDF Processing Example

### Basic PDF Text Extraction
```python
# src/pdf_processing/pdf_extractor.py
import pdfplumber
from pathlib import Path
from typing import Optional

class PDFExtractor:
    def __init__(self):
        self.supported_formats = ['.pdf']
    
    def extract_text(self, pdf_path: str) -> Optional[str]:
        """Extract text from PDF file."""
        try:
            with pdfplumber.open(pdf_path) as pdf:
                text_parts = []
                for page in pdf.pages:
                    text = page.extract_text()
                    if text:
                        text_parts.append(text)
                return '\n\n'.join(text_parts)
        except Exception as e:
            raise ValueError(f"Error extracting text from PDF: {e}")
    
    def validate_pdf(self, pdf_path: str) -> bool:
        """Validate that PDF can be opened and processed."""
        try:
            with pdfplumber.open(pdf_path) as pdf:
                return len(pdf.pages) > 0
        except:
            return False
```

## Frontend Infrastructure Guidelines

### Overview
The frontend should be a modern, user-friendly, and intuitive web interface built with responsive design principles. The UI must be accessible, fast, and provide a seamless experience for recruiters and hiring managers.

### Technology Stack

#### Recommended Frontend Framework
- **Framework:** React 18+ with TypeScript (recommended) or Vue.js 3+
- **Alternative:** Next.js 14+ for full-stack capabilities with server-side rendering
- **Build Tool:** Vite 5+ for fast development and optimized builds
- **Styling:** 
  - Tailwind CSS 3+ for utility-first styling
  - shadcn/ui or Material-UI (MUI) for pre-built component library
  - Framer Motion for smooth animations

#### State Management
- **React Query (TanStack Query):** For server state and API data caching
- **Zustand or Redux Toolkit:** For client-side state management (if needed)
- **Context API:** For simple global state (theme, user preferences)

#### UI Component Libraries
- **shadcn/ui:** Modern, accessible components built with Radix UI and Tailwind
- **Alternative:** Material-UI (MUI), Ant Design, or Chakra UI
- **Icons:** Lucide React, React Icons, or Heroicons
- **Data Tables:** TanStack Table (React Table) for sortable, filterable tables

#### File Upload & Drag-and-Drop
- **react-dropzone:** Modern file upload with drag-and-drop support
- **Alternative:** uppy for advanced upload features

#### Forms & Validation
- **React Hook Form:** Performant form management with validation
- **Zod or Yup:** Schema validation for form data
- **Integration:** React Hook Form + Zod for type-safe form validation

### Project Structure (Frontend)

```
frontend/
├── public/
│   ├── favicon.ico
│   └── assets/
├── src/
│   ├── assets/
│   │   ├── icons/
│   │   └── images/
│   ├── components/
│   │   ├── ui/              # Reusable UI components (buttons, inputs, cards)
│   │   │   ├── Button.tsx
│   │   │   ├── Card.tsx
│   │   │   ├── Input.tsx
│   │   │   ├── Table.tsx
│   │   │   └── Modal.tsx
│   │   ├── layout/          # Layout components
│   │   │   ├── Header.tsx
│   │   │   ├── Footer.tsx
│   │   │   └── Sidebar.tsx
│   │   ├── features/        # Feature-specific components
│   │   │   ├── FileUpload/
│   │   │   │   ├── FileUploadView.tsx
│   │   │   │   ├── DragDropZone.tsx
│   │   │   │   └── FilePreview.tsx
│   │   │   ├── FormView/
│   │   │   │   ├── FormView.tsx
│   │   │   │   ├── ResumeForm.tsx
│   │   │   │   ├── JobDescriptionForm.tsx
│   │   │   │   └── StorageBrowser.tsx
│   │   │   ├── Processing/
│   │   │   │   ├── ProgressBar.tsx
│   │   │   │   └── StatusIndicator.tsx
│   │   │   └── Results/
│   │   │       ├── ResultsTable.tsx
│   │   │       ├── CandidateDetails.tsx
│   │   │       ├── ScoreVisualization.tsx
│   │   │       └── ExportButton.tsx
│   ├── hooks/              # Custom React hooks
│   │   ├── useFileUpload.ts
│   │   ├── useProcessing.ts
│   │   ├── useStorage.ts
│   │   └── useExport.ts
│   ├── services/           # API services
│   │   ├── api.ts          # Base API configuration
│   │   ├── uploadService.ts
│   │   ├── processingService.ts
│   │   ├── storageService.ts
│   │   └── exportService.ts
│   ├── types/              # TypeScript type definitions
│   │   ├── resume.types.ts
│   │   ├── jobDescription.types.ts
│   │   ├── results.types.ts
│   │   └── api.types.ts
│   ├── utils/              # Utility functions
│   │   ├── formatters.ts
│   │   ├── validators.ts
│   │   └── helpers.ts
│   ├── styles/             # Global styles
│   │   ├── globals.css
│   │   └── tailwind.css
│   ├── pages/              # Page components (if using React Router)
│   │   ├── HomePage.tsx
│   │   ├── UploadPage.tsx
│   │   ├── FormPage.tsx
│   │   └── ResultsPage.tsx
│   ├── App.tsx             # Main app component
│   ├── main.tsx            # Entry point
│   └── router.tsx          # Route configuration
├── .env.example
├── .gitignore
├── index.html
├── package.json
├── tailwind.config.js
├── tsconfig.json
└── vite.config.ts
```

### Frontend Dependencies

```json
{
  "dependencies": {
    "react": "^18.2.0",
    "react-dom": "^18.2.0",
    "react-router-dom": "^6.20.0",
    "@tanstack/react-query": "^5.0.0",
    "axios": "^1.6.0",
    "react-hook-form": "^7.48.0",
    "zod": "^3.22.0",
    "@hookform/resolvers": "^3.3.0",
    "react-dropzone": "^14.2.0",
    "@tanstack/react-table": "^8.10.0",
    "clsx": "^2.0.0",
    "tailwind-merge": "^2.0.0",
    "lucide-react": "^0.292.0",
    "framer-motion": "^10.16.0",
    "date-fns": "^2.30.0"
  },
  "devDependencies": {
    "@types/react": "^18.2.0",
    "@types/react-dom": "^18.2.0",
    "@typescript-eslint/eslint-plugin": "^6.0.0",
    "@typescript-eslint/parser": "^6.0.0",
    "@vitejs/plugin-react": "^4.0.0",
    "autoprefixer": "^10.4.0",
    "eslint": "^8.45.0",
    "eslint-plugin-react-hooks": "^4.6.0",
    "postcss": "^8.4.0",
    "tailwindcss": "^3.3.0",
    "typescript": "^5.0.0",
    "vite": "^5.0.0"
  }
}
```

### UI/UX Design Principles

#### Modern Design Aesthetic
- **Clean & Minimal:** Avoid clutter, focus on essential elements
- **Whitespace:** Use generous spacing for readability
- **Typography:** Clear hierarchy with modern fonts (Inter, Roboto, or system fonts)
- **Color Scheme:** 
  - Primary color for actions and highlights
  - Neutral colors for backgrounds and text
  - Success (green), Warning (yellow), Error (red) for states
  - Dark mode support (optional but recommended)

#### User-Friendly Features
- **Intuitive Navigation:** Clear navigation between views
- **Progress Indicators:** Show processing progress with percentage and time estimates
- **Real-time Feedback:** Instant validation and error messages
- **Responsive Design:** Works seamlessly on desktop, tablet, and mobile
- **Accessibility (a11y):** WCAG 2.1 AA compliant
  - Keyboard navigation support
  - Screen reader support
  - Proper ARIA labels
  - Color contrast ratios

#### Key UI Components

##### 1. File Upload View
```tsx
// Features:
- Drag-and-drop zone with visual feedback
- File type indicators (PDF icon vs JSON icon)
- File size display
- Upload progress bars
- File preview with validation status
- Remove/delete file option
- Support for multiple file selection
- Clear error messages for invalid files
```

##### 2. Form View
```tsx
// Features:
- Tabbed interface (Resume Form / JD Form / Storage Browser)
- Auto-save draft functionality
- Real-time validation with inline error messages
- Multi-step form with progress indicator
- Dynamic field addition (add more experience, education)
- Rich text editor for descriptions (optional)
- Tag input for skills with autocomplete
- "Load from Storage" button with modal search
```

##### 3. Storage Browser
```tsx
// Features:
- Grid or list view toggle
- Search and filter functionality
- Sort by name, date, type
- Preview panel showing JSON structure
- Select multiple items
- Quick actions (view, edit, delete, load)
- Pagination for large datasets
```

##### 4. Processing View
```tsx
// Features:
- Animated progress bar
- Current candidate being processed
- Estimated time remaining
- Pause/cancel option
- Real-time log/status updates
- Error notifications with retry option
```

##### 5. Results Table
```tsx
// Features:
- Sortable columns (click header to sort)
- Color-coded scores (gradient or thresholds)
- Expandable rows for detailed reason codes
- Sticky header for scrolling
- Pagination or virtual scrolling
- Search/filter bar
- Export button (CSV download)
- Column visibility toggles
- Responsive design (card view on mobile)
```

##### 6. Candidate Details Modal
```tsx
// Features:
- Full resume display
- Highlighted matching sections
- Detailed reason codes with explanations
- Score breakdown visualization (donut chart or bar chart)
- Compare with JD side-by-side
- Print/download option
- Navigation to previous/next candidate
```

### Backend API Integration

#### API Endpoints (FastAPI)

```python
# Backend API Routes
POST /api/upload/resumes          # Upload resume PDF/JSON files
POST /api/upload/job-description  # Upload JD PDF/JSON file
POST /api/process                 # Start processing pipeline
GET /api/process/status           # Get processing status
GET /api/results                  # Get ranked results
GET /api/results/{candidate_id}   # Get candidate details
GET /api/storage/resumes          # List stored resumes
GET /api/storage/job-descriptions # List stored JDs
GET /api/storage/search           # Search stored JSONs
POST /api/export/csv              # Generate and download CSV
DELETE /api/storage/{file_id}     # Delete stored JSON
```

#### API Client Example
```typescript
// src/services/api.ts
import axios from 'axios';

const API_BASE_URL = import.meta.env.VITE_API_BASE_URL || 'http://localhost:8000';

export const api = axios.create({
  baseURL: API_BASE_URL,
  headers: {
    'Content-Type': 'application/json',
  },
});

// Upload service
export const uploadService = {
  uploadResumes: (files: File[]) => {
    const formData = new FormData();
    files.forEach(file => formData.append('files', file));
    return api.post('/api/upload/resumes', formData, {
      headers: { 'Content-Type': 'multipart/form-data' },
    });
  },
  
  uploadJobDescription: (file: File) => {
    const formData = new FormData();
    formData.append('file', file);
    return api.post('/api/upload/job-description', formData, {
      headers: { 'Content-Type': 'multipart/form-data' },
    });
  },
};

// Processing service
export const processingService = {
  startProcessing: () => api.post('/api/process'),
  getStatus: () => api.get('/api/process/status'),
};

// Results service
export const resultsService = {
  getResults: () => api.get('/api/results'),
  getCandidateDetails: (id: string) => api.get(`/api/results/${id}`),
};

// Storage service
export const storageService = {
  listResumes: () => api.get('/api/storage/resumes'),
  listJobDescriptions: () => api.get('/api/storage/job-descriptions'),
  search: (query: string) => api.get('/api/storage/search', { params: { q: query } }),
  deleteFile: (id: string) => api.delete(`/api/storage/${id}`),
};

// Export service
export const exportService = {
  exportCSV: () => api.get('/api/export/csv', { responseType: 'blob' }),
};
```

### State Management Example

```typescript
// src/hooks/useProcessing.ts
import { useQuery, useMutation } from '@tanstack/react-query';
import { processingService } from '../services/api';

export const useProcessing = () => {
  const startMutation = useMutation({
    mutationFn: processingService.startProcessing,
  });

  const statusQuery = useQuery({
    queryKey: ['processingStatus'],
    queryFn: processingService.getStatus,
    refetchInterval: 2000, // Poll every 2 seconds
    enabled: startMutation.isSuccess,
  });

  return {
    startProcessing: startMutation.mutate,
    isProcessing: startMutation.isPending || statusQuery.data?.status === 'processing',
    progress: statusQuery.data?.progress || 0,
    currentCandidate: statusQuery.data?.currentCandidate,
    error: startMutation.error || statusQuery.error,
  };
};
```

### Styling with Tailwind CSS

```tsx
// Example: ResultsTable Component
import { Table, TableHeader, TableBody, TableRow, TableCell } from '@/components/ui/Table';
import { Badge } from '@/components/ui/Badge';

export function ResultsTable({ candidates }) {
  return (
    <div className="w-full overflow-auto rounded-lg border border-gray-200 shadow-sm">
      <Table>
        <TableHeader>
          <TableRow className="bg-gray-50">
            <TableCell className="font-semibold">Rank</TableCell>
            <TableCell className="font-semibold">Name</TableCell>
            <TableCell className="font-semibold">Overall Score</TableCell>
            <TableCell className="font-semibold">Must-Have Hits</TableCell>
            <TableCell className="font-semibold">Reason Codes</TableCell>
          </TableRow>
        </TableHeader>
        <TableBody>
          {candidates.map((candidate, index) => (
            <TableRow 
              key={candidate.id}
              className="hover:bg-gray-50 transition-colors cursor-pointer"
            >
              <TableCell className="font-medium">{index + 1}</TableCell>
              <TableCell>{candidate.name}</TableCell>
              <TableCell>
                <Badge 
                  variant={
                    candidate.score > 80 ? 'success' : 
                    candidate.score > 60 ? 'warning' : 
                    'error'
                  }
                >
                  {candidate.score.toFixed(2)}
                </Badge>
              </TableCell>
              <TableCell>{candidate.mustHaveHits}</TableCell>
              <TableCell className="text-sm text-gray-600">
                {candidate.reasonCodes.slice(0, 2).join(', ')}
                {candidate.reasonCodes.length > 2 && '...'}
              </TableCell>
            </TableRow>
          ))}
        </TableBody>
      </Table>
    </div>
  );
}
```

### Performance Optimization

- **Code Splitting:** Use React.lazy() and Suspense for route-based code splitting
- **Memoization:** Use React.memo, useMemo, useCallback for expensive computations
- **Virtual Scrolling:** For large datasets (1000+ rows) use react-virtual or TanStack Virtual
- **Image Optimization:** Use WebP format, lazy loading for images
- **Bundle Size:** Keep bundle < 300KB gzipped for initial load
- **Caching:** Use React Query for smart caching and background refetching

### Accessibility Guidelines

- **Keyboard Navigation:** All interactive elements accessible via Tab key
- **Focus Indicators:** Visible focus states for all focusable elements
- **ARIA Labels:** Proper ARIA labels for screen readers
- **Color Contrast:** Minimum 4.5:1 ratio for text
- **Semantic HTML:** Use proper HTML5 semantic elements
- **Form Labels:** All form inputs must have associated labels
- **Error Messages:** Clear, descriptive error messages announced to screen readers

### Testing Strategy (Frontend)

```javascript
// Testing Tools
- Vitest or Jest for unit testing
- React Testing Library for component testing
- Playwright or Cypress for E2E testing
- Storybook for component documentation and visual testing
- axe-core for accessibility testing
```

### Deployment Considerations

- **Build Command:** `npm run build` or `yarn build`
- **Output Directory:** `dist/` or `build/`
- **Environment Variables:** Use `.env` files with `VITE_` prefix for Vite
- **Static Hosting:** Vercel, Netlify, or serve from FastAPI backend
- **CORS:** Configure CORS in FastAPI backend to allow frontend domain
- **Production Optimizations:** 
  - Minification enabled
  - Tree shaking enabled
  - Source maps for debugging (optional)

### Development Workflow

```bash
# Install dependencies
npm install

# Start development server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview

# Run tests
npm run test

# Lint code
npm run lint

# Format code
npm run format
```

### Quick Start Checklist (Frontend)

- [ ] Set up Vite + React + TypeScript project
- [ ] Install UI library (shadcn/ui or MUI)
- [ ] Install Tailwind CSS
- [ ] Configure React Query for data fetching
- [ ] Set up React Router for navigation
- [ ] Create base API service with axios
- [ ] Implement File Upload view with drag-and-drop
- [ ] Implement Form view with validation
- [ ] Implement Storage Browser component
- [ ] Implement Results Table with sorting
- [ ] Implement Candidate Details modal
- [ ] Add progress indicators and loading states
- [ ] Configure CORS in backend
- [ ] Test end-to-end flow
- [ ] Add error handling and user feedback
- [ ] Optimize for performance and accessibility

## LangChain Quick Reference

### Common Patterns

#### 1. Basic Chain
```python
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI
from langchain_core.output_parsers import StrOutputParser

prompt = ChatPromptTemplate.from_messages([
    ("system", "You are a helpful assistant."),
    ("human", "{input}")
])
llm = ChatOpenAI(model="gpt-4")
parser = StrOutputParser()

chain = prompt | llm | parser
result = chain.invoke({"input": "Hello!"})
```

#### 2. JSON Output Chain
```python
from langchain_core.output_parsers import JsonOutputParser

prompt = ChatPromptTemplate.from_template(
    "Analyze this resume and return JSON: {resume}"
)
llm = ChatOpenAI(model="gpt-4")
parser = JsonOutputParser()

chain = prompt | llm | parser
result = chain.invoke({"resume": resume_text})  # Returns dict
```

#### 3. Structured Output with Pydantic
```python
from langchain_core.pydantic_v1 import BaseModel, Field
from langchain_core.output_parsers import PydanticOutputParser

class Analysis(BaseModel):
    score: float = Field(description="Score from 0-100")
    reason: str = Field(description="Explanation")

parser = PydanticOutputParser(pydantic_object=Analysis)
prompt = ChatPromptTemplate.from_template(
    "Analyze: {text}\n{format_instructions}"
).partial(format_instructions=parser.get_format_instructions())

chain = prompt | llm | parser
result = chain.invoke({"text": "..."})  # Returns Analysis object
```

#### 4. Batch Processing
```python
inputs = [{"input": text} for text in texts]
results = chain.batch(inputs)  # Process all at once
```

#### 5. Async Processing
```python
result = await chain.ainvoke({"input": "..."})
results = await chain.abatch(inputs)
```

#### 6. Streaming
```python
for chunk in chain.stream({"input": "..."}):
    print(chunk, end="", flush=True)

# Async streaming
async for chunk in chain.astream({"input": "..."}):
    print(chunk, end="", flush=True)
```

#### 7. Enable Caching
```python
from langchain.cache import InMemoryCache, SQLiteCache
from langchain.globals import set_llm_cache

set_llm_cache(InMemoryCache())  # or SQLiteCache(".cache.db")
# Identical queries will now use cache
```

#### 8. Switch Providers
```python
# Just change the LLM instance
from langchain_openai import ChatOpenAI
from langchain_google_genai import ChatGoogleGenerativeAI
from langchain_anthropic import ChatAnthropic

llm = ChatOpenAI(model="gpt-4")  # OpenAI
# llm = ChatGoogleGenerativeAI(model="gemini-pro")  # Gemini
# llm = ChatAnthropic(model="claude-3-opus-20240229")  # Claude

chain = prompt | llm | parser  # Same chain works for all!
```

#### 9. Callbacks for Monitoring
```python
from langchain.callbacks.streaming_stdout import StreamingStdOutCallbackHandler

llm = ChatOpenAI(callbacks=[StreamingStdOutCallbackHandler()])
# Or custom callback
class TokenCounter(BaseCallbackHandler):
    def __init__(self):
        self.tokens = 0
    
    def on_llm_end(self, response, **kwargs):
        self.tokens += response.llm_output["token_usage"]["total_tokens"]
```

#### 10. Error Handling with Fallbacks
```python
from langchain_core.runnables import RunnableLambda

def fallback_func(inputs):
    return {"result": "fallback", "error": True}

primary = prompt | llm | parser
fallback = RunnableLambda(fallback_func)

chain = primary.with_fallbacks([fallback])
result = chain.invoke({"input": "..."})  # Uses fallback if primary fails
```

### Switching Between Providers

```python
# src/llm/client.py - Provider factory pattern
def create_llm(provider: str, **kwargs):
    """Factory function to create LLM based on provider."""
    if provider == "openai":
        return ChatOpenAI(
            model=os.getenv("OPENAI_MODEL", "gpt-4"),
            api_key=os.getenv("OPENAI_API_KEY"),
            **kwargs
        )
    elif provider == "gemini":
        return ChatGoogleGenerativeAI(
            model=os.getenv("GEMINI_MODEL", "gemini-pro"),
            google_api_key=os.getenv("GOOGLE_API_KEY"),
            **kwargs
        )
    elif provider == "anthropic":
        return ChatAnthropic(
            model=os.getenv("ANTHROPIC_MODEL", "claude-3-opus-20240229"),
            anthropic_api_key=os.getenv("ANTHROPIC_API_KEY"),
            **kwargs
        )
    else:
        raise ValueError(f"Unsupported provider: {provider}")

# Usage - just change env var!
llm = create_llm(os.getenv("LLM_PROVIDER", "openai"), temperature=0.3)
```

### Best Practices
1. **Always use LangChain abstractions** - Don't import `openai`, `google.generativeai`, etc. directly
2. **Use Pydantic for structured outputs** - Type safety and validation
3. **Enable caching** - Significant cost savings
4. **Use batch processing** - More efficient than sequential
5. **Monitor with callbacks** - Track tokens and costs
6. **Use async for concurrency** - Better performance for multiple requests
7. **Implement fallbacks** - Graceful degradation if LLM fails
8. **Version your prompts** - Track prompt changes in git
9. **Test with multiple providers** - Ensure portability
10. **Use LangSmith** - Debugging and optimization

## Quick Start Checklist

### Backend Setup
- [ ] Set up Python virtual environment
- [ ] Install LangChain dependencies from `requirements.txt`
  - `langchain`, `langchain-core`, `langchain-openai`, `langchain-google-genai`
- [ ] Install PDF processing libraries (`pdfplumber`)
- [ ] Configure `.env` file with LLM provider and API keys
  - Set `LLM_PROVIDER` (openai, gemini, anthropic, or ollama)
  - Set corresponding API key (`OPENAI_API_KEY`, `GOOGLE_API_KEY`, etc.)
- [ ] Create project directory structure (including `src/llm/`, `src/prompts/`)
- [ ] Initialize LangChain LLM client with provider configuration
- [ ] Create LangChain prompt templates using `ChatPromptTemplate`
- [ ] Set up Pydantic models for structured output
- [ ] Configure LangChain caching (`InMemoryCache` or `SQLiteCache`)
- [ ] Place sample resume PDF files in `data/resumes/raw/`
- [ ] Place sample JD PDF files in `data/job_descriptions/raw/`
- [ ] Test PDF extraction with sample files
- [ ] Test LangChain LLM connection with a simple chain
- [ ] Verify JSON output parsing with `JsonOutputParser`
- [ ] Test batch processing with `chain.batch()`
- [ ] (Optional) Set up LangSmith for observability

### Frontend Setup (if building web interface)
- [ ] Set up frontend project (React + Vite + TypeScript)
- [ ] Install UI library (shadcn/ui or Material-UI)
- [ ] Configure Tailwind CSS
- [ ] Set up React Query for API data fetching
- [ ] Implement file upload and form views
- [ ] Connect to backend API endpoints

### Testing & Deployment
- [ ] Run tests to verify LangChain chains work correctly
- [ ] Test with multiple LLM providers (switch via `LLM_PROVIDER` env var)
- [ ] Execute main pipeline with sample data (PDF → JSON → LangChain Analysis → Export)
- [ ] Monitor token usage and costs via callbacks
- [ ] Optimize prompts based on LangChain feedback
