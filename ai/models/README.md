# AI Models

Notes on AI models, local deployments, and comparisons.

## Local Models (Docker Model Runner)

Docker Desktop includes Model Runner for running LLMs locally.

### Running Llama
```bash
docker model run ai/llama3.2
```

### OpenAI-Compatible API
```bash
curl http://localhost:12434/engines/v1/chat/completions \
  -H "Content-Type: application/json" \
  -d '{"model": "ai/llama3.2", "messages": [{"role": "user", "content": "Hello!"}]}'
```

### Python SDK
```python
from openai import OpenAI
client = OpenAI(base_url="http://localhost:12434/engines/v1", api_key="none")
```
