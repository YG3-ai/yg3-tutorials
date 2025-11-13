# YG3 API Reference

> Complete reference documentation for the YG3 API (also known as CLM or Elysia API)

**Last Updated:** November 2025  
**API Version:** v1  
**Status:** Production

---

## Table of Contents

- [Getting Started](#getting-started)
- [Authentication](#authentication)
- [Base URL](#base-url)
- [Endpoints](#endpoints)
  - [Chat Completions](#chat-completions)
  - [List Models](#list-models)
- [Parameters](#parameters)
- [Response Format](#response-format)
- [Streaming](#streaming)
- [Function Calling](#function-calling)
- [Error Handling](#error-handling)
- [Rate Limits](#rate-limits)
- [Best Practices](#best-practices)
- [SDKs & Libraries](#sdks--libraries)

---

## Getting Started

The YG3 API is **fully compatible with the OpenAI API**, which means you can use the OpenAI SDK and simply change the base URL.

### Quick Example

```python
from openai import OpenAI

client = OpenAI(
    api_key="your-api-key-here",
    base_url="https://elysia-api.ngrok.io/api/public/v1"
)

response = client.chat.completions.create(
    model="elysia",
    messages=[
        {"role": "user", "content": "Hello, world!"}
    ]
)

print(response.choices[0].message.content)
```

---

## Authentication

All API requests require authentication using an API key.

### Getting Your API Key

1. Sign up at [https://app.yg3.ai](https://app.yg3.ai)
2. Navigate to **API Keys** in your dashboard
3. Click **Create New Key**
4. Copy your key (shown only once!)
5. Store it securely

### Using Your API Key

**In Headers:**
```http
Authorization: Bearer YOUR_API_KEY
```

**In Python SDK:**
```python
client = OpenAI(
    api_key="YOUR_API_KEY",
    base_url="https://elysia-api.ngrok.io/api/public/v1"
)
```

**Environment Variables:**
```bash
export YG3_API_KEY="your-api-key-here"
```

```python
import os
api_key = os.getenv("YG3_API_KEY")
```

### Security Best Practices

- ❌ Never commit API keys to version control
- ❌ Never expose keys in client-side code
- ✅ Use environment variables
- ✅ Rotate keys periodically
- ✅ Use separate keys for development/production

---

## Base URL

```
https://elysia-api.ngrok.io/api/public/v1
```

All endpoints are relative to this base URL.

---

## Endpoints

### Chat Completions

Create a chat completion using the YG3 model.

**Endpoint:**
```
POST /v1/chat/completions
```

**Request Body:**

```json
{
  "model": "elysia",
  "messages": [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "Hello!"}
  ],
  "temperature": 0.7,
  "max_tokens": 1000,
  "stream": false
}
```

**Example (Python):**

```python
response = client.chat.completions.create(
    model="elysia",
    messages=[
        {"role": "system", "content": "You are a helpful assistant."},
        {"role": "user", "content": "Explain quantum computing"}
    ],
    temperature=0.7,
    max_tokens=1000
)

print(response.choices[0].message.content)
```

**Example (cURL):**

```bash
curl https://elysia-api.ngrok.io/api/public/v1/chat/completions \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "elysia",
    "messages": [
      {"role": "user", "content": "Hello!"}
    ]
  }'
```

---

### List Models

Get a list of available models.

**Endpoint:**
```
GET /v1/models
```

**Example (Python):**

```python
models = client.models.list()
for model in models:
    print(model.id)
```

**Example (cURL):**

```bash
curl https://elysia-api.ngrok.io/api/public/v1/models \
  -H "Authorization: Bearer YOUR_API_KEY"
```

**Response:**

```json
{
  "object": "list",
  "data": [
    {
      "id": "elysia",
      "object": "model",
      "created": 1677610602,
      "owned_by": "yg3"
    }
  ]
}
```

---

## Parameters

### Chat Completions Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `model` | string | Yes | - | Model ID to use (currently "elysia") |
| `messages` | array | Yes | - | Array of message objects |
| `temperature` | float | No | 0.7 | Sampling temperature (0.0 - 2.0) |
| `max_tokens` | integer | No | 1000 | Maximum tokens in response |
| `top_p` | float | No | 1.0 | Nucleus sampling parameter |
| `n` | integer | No | 1 | Number of completions to generate |
| `stream` | boolean | No | false | Whether to stream responses |
| `stop` | string/array | No | null | Stop sequences |
| `presence_penalty` | float | No | 0.0 | Penalize new topics (-2.0 to 2.0) |
| `frequency_penalty` | float | No | 0.0 | Penalize repetition (-2.0 to 2.0) |
| `tools` | array | No | null | Available functions for tool calling |
| `tool_choice` | string/object | No | "auto" | How to use tools ("auto", "none", or specific) |

### Message Object Structure

```python
{
    "role": "system" | "user" | "assistant" | "tool",
    "content": "Message content here",
    "name": "optional_name",  # For tool messages
    "tool_calls": [...]  # For assistant messages with tool calls
}
```

### Roles Explained

- **system**: Instructions for the model's behavior
- **user**: User input/questions
- **assistant**: Model's responses
- **tool**: Results from function calls

---

## Response Format

### Standard Response

```json
{
  "id": "chatcmpl-abc123",
  "object": "chat.completion",
  "created": 1699564800,
  "model": "elysia",
  "choices": [
    {
      "index": 0,
      "message": {
        "role": "assistant",
        "content": "Response text here..."
      },
      "finish_reason": "stop"
    }
  ],
  "usage": {
    "prompt_tokens": 20,
    "completion_tokens": 50,
    "total_tokens": 70
  }
}
```

### Accessing the Response

**Python:**
```python
# Get the main response text
text = response.choices[0].message.content

# Get token usage
tokens_used = response.usage.total_tokens

# Get finish reason
finish_reason = response.choices[0].finish_reason
```

### Finish Reasons

- **stop**: Natural completion
- **length**: Hit max_tokens limit
- **tool_calls**: Model wants to call a function
- **content_filter**: Filtered due to content policy

---

## Streaming

Stream responses token-by-token for real-time applications.

### Enable Streaming

```python
response = client.chat.completions.create(
    model="elysia",
    messages=[{"role": "user", "content": "Write a story"}],
    stream=True  # Enable streaming
)

for chunk in response:
    if chunk.choices[0].delta.content:
        print(chunk.choices[0].delta.content, end="")
```

### Streaming Response Format

```json
{
  "id": "chatcmpl-abc123",
  "object": "chat.completion.chunk",
  "created": 1699564800,
  "model": "elysia",
  "choices": [
    {
      "index": 0,
      "delta": {
        "content": "token"
      },
      "finish_reason": null
    }
  ]
}
```

### Complete Streaming Example

```python
def stream_response(prompt):
    """Stream a response with real-time output"""
    response = client.chat.completions.create(
        model="elysia",
        messages=[{"role": "user", "content": prompt}],
        stream=True,
        max_tokens=500
    )
    
    full_response = ""
    for chunk in response:
        if chunk.choices[0].delta.content:
            content = chunk.choices[0].delta.content
            print(content, end="", flush=True)
            full_response += content
    
    print()  # New line at end
    return full_response

# Usage
result = stream_response("Explain how APIs work")
```

---

## Function Calling

Enable the model to call functions/tools for taking actions.

### Defining Tools

```python
tools = [
    {
        "type": "function",
        "function": {
            "name": "get_weather",
            "description": "Get the current weather for a location",
            "parameters": {
                "type": "object",
                "properties": {
                    "location": {
                        "type": "string",
                        "description": "City name, e.g. 'San Francisco'"
                    },
                    "unit": {
                        "type": "string",
                        "enum": ["celsius", "fahrenheit"],
                        "description": "Temperature unit"
                    }
                },
                "required": ["location"]
            }
        }
    }
]
```

### Making a Request with Tools

```python
response = client.chat.completions.create(
    model="elysia",
    messages=[
        {"role": "user", "content": "What's the weather in Tokyo?"}
    ],
    tools=tools,
    tool_choice="auto"  # Let model decide when to use tools
)

# Check if model wants to call a function
if response.choices[0].message.tool_calls:
    tool_call = response.choices[0].message.tool_calls[0]
    function_name = tool_call.function.name
    arguments = json.loads(tool_call.function.arguments)
    
    print(f"Model wants to call: {function_name}")
    print(f"With arguments: {arguments}")
```

### Complete Function Calling Flow

```python
import json

def get_weather(location, unit="celsius"):
    """Your actual function implementation"""
    # Call weather API, database, etc.
    return {
        "location": location,
        "temperature": 22,
        "unit": unit,
        "condition": "sunny"
    }

# Step 1: Initial request
messages = [{"role": "user", "content": "What's the weather in Paris?"}]

response = client.chat.completions.create(
    model="elysia",
    messages=messages,
    tools=tools
)

# Step 2: Check for tool calls
message = response.choices[0].message
if message.tool_calls:
    # Add assistant's tool call to messages
    messages.append(message)
    
    # Execute the function
    for tool_call in message.tool_calls:
        function_name = tool_call.function.name
        arguments = json.loads(tool_call.function.arguments)
        
        # Call the actual function
        if function_name == "get_weather":
            result = get_weather(**arguments)
        
        # Add function result to messages
        messages.append({
            "role": "tool",
            "tool_call_id": tool_call.id,
            "content": json.dumps(result)
        })
    
    # Step 3: Get final response with function results
    final_response = client.chat.completions.create(
        model="elysia",
        messages=messages
    )
    
    print(final_response.choices[0].message.content)
```

### Tool Choice Options

- **"auto"** (default): Model decides when to use tools
- **"none"**: Never use tools
- **{"type": "function", "function": {"name": "function_name"}}**: Force specific function

---

## Error Handling

### Common HTTP Status Codes

| Code | Meaning | Action |
|------|---------|--------|
| 200 | Success | Process response |
| 400 | Bad Request | Check request format |
| 401 | Unauthorized | Check API key |
| 429 | Rate Limited | Implement backoff |
| 500 | Server Error | Retry with backoff |
| 503 | Service Unavailable | Retry later |

### Error Response Format

```json
{
  "error": {
    "message": "Invalid API key provided",
    "type": "invalid_request_error",
    "code": "invalid_api_key"
  }
}
```

### Error Handling Example

```python
from openai import OpenAI, APIError, RateLimitError, APIConnectionError

client = OpenAI(
    api_key="your-api-key",
    base_url="https://elysia-api.ngrok.io/api/public/v1"
)

def make_request_with_retry(messages, max_retries=3):
    """Make API request with error handling and retries"""
    import time
    
    for attempt in range(max_retries):
        try:
            response = client.chat.completions.create(
                model="elysia",
                messages=messages,
                timeout=30  # 30 second timeout
            )
            return response
            
        except RateLimitError:
            if attempt < max_retries - 1:
                wait_time = 2 ** attempt  # Exponential backoff
                print(f"Rate limited. Waiting {wait_time}s...")
                time.sleep(wait_time)
            else:
                raise
                
        except APIConnectionError as e:
            print(f"Connection error: {e}")
            if attempt < max_retries - 1:
                time.sleep(1)
            else:
                raise
                
        except APIError as e:
            print(f"API error: {e}")
            raise  # Don't retry on API errors
            
        except Exception as e:
            print(f"Unexpected error: {e}")
            raise

# Usage
try:
    response = make_request_with_retry([
        {"role": "user", "content": "Hello!"}
    ])
    print(response.choices[0].message.content)
except Exception as e:
    print(f"Failed after retries: {e}")
```

---

## Rate Limits

### Default Limits

- **Requests per minute (RPM):** Contact support for your limits
- **Tokens per minute (TPM):** Contact support for your limits

### Rate Limit Headers

Check response headers:
```
x-ratelimit-limit-requests: 60
x-ratelimit-remaining-requests: 59
x-ratelimit-reset-requests: 1s
```

### Handling Rate Limits

```python
import time

def exponential_backoff_retry(func, max_retries=5):
    """Retry with exponential backoff"""
    for i in range(max_retries):
        try:
            return func()
        except RateLimitError:
            if i < max_retries - 1:
                wait_time = min(2 ** i, 60)  # Cap at 60 seconds
                print(f"Rate limited. Waiting {wait_time}s...")
                time.sleep(wait_time)
            else:
                raise

# Usage
response = exponential_backoff_retry(
    lambda: client.chat.completions.create(
        model="elysia",
        messages=[{"role": "user", "content": "Hello"}]
    )
)
```

---

## Best Practices

### 1. Optimize Token Usage

```python
# Keep prompts concise
messages = [
    {"role": "system", "content": "You are a helpful assistant."},
    {"role": "user", "content": "Summarize: [long text]"}
]

# Set appropriate max_tokens
response = client.chat.completions.create(
    model="elysia",
    messages=messages,
    max_tokens=100  # Limit response length
)
```

### 2. Use System Messages Effectively

```python
# Good: Clear, specific instructions
system_message = {
    "role": "system",
    "content": "You are a customer support agent. Be concise and professional."
}

# Better: Add constraints
system_message = {
    "role": "system",
    "content": """You are a customer support agent.
    - Be concise (max 3 sentences)
    - Be professional and friendly
    - If you don't know, say so"""
}
```

### 3. Maintain Conversation Context

```python
conversation_history = []

def chat(user_message):
    """Maintain conversation context"""
    # Add user message
    conversation_history.append({
        "role": "user",
        "content": user_message
    })
    
    # Get response
    response = client.chat.completions.create(
        model="elysia",
        messages=conversation_history
    )
    
    # Add assistant response
    assistant_message = response.choices[0].message.content
    conversation_history.append({
        "role": "assistant",
        "content": assistant_message
    })
    
    return assistant_message

# Usage
print(chat("Hi, I'm interested in your product"))
print(chat("What are the pricing options?"))  # Has context
```

### 4. Implement Timeouts

```python
from openai import OpenAI

client = OpenAI(
    api_key="your-api-key",
    base_url="https://elysia-api.ngrok.io/api/public/v1",
    timeout=30.0  # 30 second timeout
)
```

### 5. Log Requests for Debugging

```python
import logging

logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

def make_request(messages):
    logger.info(f"Request: {messages}")
    
    response = client.chat.completions.create(
        model="elysia",
        messages=messages
    )
    
    logger.info(f"Response: {response.choices[0].message.content}")
    logger.info(f"Tokens used: {response.usage.total_tokens}")
    
    return response
```

### 6. Handle Long Responses

```python
# Option 1: Increase max_tokens
response = client.chat.completions.create(
    model="elysia",
    messages=messages,
    max_tokens=2000  # Allow longer response
)

# Option 2: Check finish_reason and continue
if response.choices[0].finish_reason == "length":
    # Response was cut off, continue conversation
    messages.append(response.choices[0].message)
    messages.append({"role": "user", "content": "Continue"})
    
    continuation = client.chat.completions.create(
        model="elysia",
        messages=messages
    )
```

---

## SDKs & Libraries

### Official SDK

**Python (via OpenAI SDK):**
```bash
pip install openai
```

```python
from openai import OpenAI

client = OpenAI(
    api_key="your-api-key",
    base_url="https://elysia-api.ngrok.io/api/public/v1"
)
```

### Framework Integrations

**LangChain:**
```python
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(
    model="elysia",
    openai_api_key="your-api-key",
    openai_api_base="https://elysia-api.ngrok.io/api/public/v1"
)
```

**LlamaIndex:**
```python
from llama_index.llms.openai_like import OpenAILike

llm = OpenAILike(
    model="elysia",
    api_key="your-api-key",
    api_base="https://elysia-api.ngrok.io/api/public/v1",
    is_chat_model=True
)
```

**JavaScript/TypeScript:**
```bash
npm install openai
```

```typescript
import OpenAI from 'openai';

const client = new OpenAI({
  apiKey: process.env.YG3_API_KEY,
  baseURL: 'https://elysia-api.ngrok.io/api/public/v1'
});
```

---

## Additional Resources

### Documentation
- **Main Docs:** [https://app.yg3.ai/docs](https://help.yg3.ai)
- **Tutorial Hub:** [../README.md](../README.md)
- **Examples:** [../examples/](../examples/)

### Support
- **Email:** support@yg3.ai
- **Community:** [GitHub Discussions](https://github.com/YG3-ai/yg3-tutorials/discussions)
- **Issues:** [GitHub Issues](https://github.com/YG3-ai/yg3-tutorials/issues)

### Updates
- Check the [changelog](https://app.yg3.ai/changelog) for API updates
- Subscribe to API updates at [app.yg3.ai](https://app.yg3.ai)

---

## FAQ

**Q: Is the YG3 API compatible with OpenAI?**  
A: Yes! It's fully compatible. Just change the base URL.

**Q: What models are available?**  
A: Currently "elysia" is the main production model.

**Q: Do you support embeddings?**  
A: Check the latest documentation at app.yg3.ai for current features.

**Q: What's the context window size?**  
A: Contact support or check app.yg3.ai for current limits.

**Q: Can I use this in production?**  
A: Yes! The API is production-ready and designed for scale.

---

<div align="center">

**Have questions?**

[📖 Main Docs](https://app.yg3.ai/docs) | [💬 Community](https://github.com/YG3-ai/yg3-tutorials/discussions) | [📧 Support](mailto:support@yg3.ai)

</div>
