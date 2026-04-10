# Development Notes - Activity 5

## Quick Start
```bash
# Install dependencies
pip install -r requirements.txt

# Copy and fill environment variables
cp .env.example .env

# Run the pipeline
python app/main.py

# Run tests
pytest tests/ -v
```

## Testing Individual Steps
Test each pipeline step in isolation to debug faster:
```bash
# PII Detection
pytest tests/test_basic.py::test_pii_result_has_redacted_text -v

# Sentiment Analysis  
pytest tests/test_basic.py::test_sentiment_has_valid_value -v

# Translation
pytest tests/test_basic.py::test_translation_has_translated_text -v

# Intent Recognition
pytest tests/test_basic.py::test_intent_has_top_intent -v
```

## Key Points
- **PII must be redacted FIRST** before any other processing
- **Translator key is different** from Language service key — check both env vars if translation fails
- **CLU is optional** — the pipeline falls back to keyword matching if CLU_PROJECT_NAME is not set
- **result.json is auto-generated** — don't commit it manually, run `python app/main.py` to regenerate

## Common Issues
- `401 Unauthorized`: Check API keys and regions match your Azure resources
- `InvalidArgument: text is empty`: Verify complaints.json is loaded properly
- `404 Not Found` (CLU): Confirm CLU_PROJECT_NAME and CLU_DEPLOYMENT_NAME are correct

## Output Structure
The pipeline generates `result.json` with this structure:
```
{
  "pii_results": [...],
  "sentiment_results": [...],
  "translation_results": [...],
  "intent_results": [...],
  "pipeline_summary": {
    "total_complaints": 10,
    "steps_completed": [...],
    "pii_entities_found": ...,
    "languages_detected": [...],
    "translations_performed": ...
  }
}
```
