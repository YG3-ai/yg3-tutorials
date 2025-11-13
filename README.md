# YG3 API Tutorials

[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Python 3.8+](https://img.shields.io/badge/python-3.8+-blue.svg)](https://www.python.org/downloads/)

> Complete tutorials and examples for building with the YG3 API (also known as CLM or Elysia API). From quick starts to production applications.

[🚀 Quick Start](#quick-start) • [📚 Tutorials](#tutorials) • [💡 Examples](#examples) • [📖 API Docs](https://app.yg3.ai)

---

## 🚀 Quick Start

Get started with the YG3 API in under 2 minutes!

### Step 1: Get Your API Key

Sign up at [https://app.yg3.ai](https://app.yg3.ai) and create an API key.

### Step 2: Install the SDK

```bash
pip install openai
```

The YG3 API is **OpenAI-compatible**, so you can use the familiar OpenAI SDK!

### Step 3: Make Your First Request

```python
from openai import OpenAI

client = OpenAI(
    api_key="your-api-key-here",
    base_url="https://elysia-api.ngrok.io/api/public/v1"
)

response = client.chat.completions.create(
    model="elysia",
    messages=[
        {"role": "user", "content": "Hello! Tell me about yourself."}
    ]
)

print(response.choices[0].message.content)
```

**That's it!** You're now using the YG3 API. 🎉

### Quick Tips

- 💡 The API is **OpenAI-compatible** - swap your base URL and you're good to go
- 🔄 Supports **streaming** responses
- 🛠️ Works with **function calling** and tool use
- 📦 Compatible with **LangChain**, **LlamaIndex**, and other frameworks

---

## 📚 Tutorials

### 🟢 Part 1: Building a ReAct Customer Support Agent
**Status:** ✅ Complete | **Difficulty:** Beginner | **Time:** 10 minutes

Learn how to build an AI agent that can search knowledge bases, check orders, and provide automated customer support using the ReAct (Reasoning + Acting) pattern.

**What You'll Build:**
- Intelligent customer support agent
- Tool calling with function execution
- Interactive Gradio web interface
- Production-ready code

**Key Concepts:**
- ReAct pattern
- Tool/function calling
- Prompt engineering
- Agent loops

👉 [**Start Tutorial →**](./tutorials/react-customer-support-agent/)

**Quick Stats:** This is the #1 most popular agent pattern in 2025 (45.8% of AI agents use it)

---

### 🚧 More Tutorials Coming Soon!

We're building a comprehensive tutorial library covering:
- 📖 Different application types
- 🎯 Various use cases
- 💼 Production patterns
- 🔧 Integration guides
- 🚀 Deployment strategies

**Want to see something specific?** [Open an issue](https://github.com/yourusername/yg3-tutorials/issues) to suggest a tutorial!

---

## 💡 What is the YG3 API?

The **YG3 API** (also known as the **CLM** or **Elysia** API) is a powerful, OpenAI-compatible API for building intelligent applications.

### Why YG3?

- 🔌 **OpenAI Compatible** - Drop-in replacement, use the same code
- ⚡ **Fast & Reliable** - Production-grade infrastructure
- 💰 **Cost-Effective** - Competitive pricing
- 🛠️ **Developer-Friendly** - Excellent documentation and support
- 🎯 **Powerful** - Advanced language understanding and generation

### Key Features

✅ **Chat Completions** - Conversational AI
✅ **Function Calling** - Tool use and actions
✅ **Streaming** - Real-time responses
✅ **Context Windows** - Handle long conversations
✅ **Framework Support** - LangChain, LlamaIndex, and more

---

## 🔧 Integration Examples

### With LangChain

```python
from langchain_openai import ChatOpenAI

llm = ChatOpenAI(
    model="elysia",
    openai_api_key="your-api-key",
    openai_api_base="https://elysia-api.ngrok.io/api/public/v1"
)

response = llm.invoke("What can you help me build?")
print(response.content)
```

### With LlamaIndex

```python
from llama_index.llms.openai_like import OpenAILike

llm = OpenAILike(
    model="elysia",
    api_key="your-api-key",
    api_base="https://elysia-api.ngrok.io/api/public/v1",
    is_chat_model=True
)

response = llm.complete("Explain quantum computing")
print(response)
```

### Function Calling

```python
tools = [
    {
        "type": "function",
        "function": {
            "name": "get_weather",
            "description": "Get the weather for a location",
            "parameters": {
                "type": "object",
                "properties": {
                    "location": {"type": "string"}
                }
            }
        }
    }
]

response = client.chat.completions.create(
    model="elysia",
    messages=[{"role": "user", "content": "What's the weather in Tokyo?"}],
    tools=tools
)
```

---

## 📖 API Reference

### Base URL
```
https://elysia-api.ngrok.io/api/public/v1
```

### Available Models
- `elysia` - Main production model

### Authentication
```python
# Add to header
Authorization: Bearer YOUR_API_KEY

# Or in SDK
client = OpenAI(api_key="YOUR_API_KEY", base_url="...")
```

### Common Parameters

| Parameter | Type | Description |
|-----------|------|-------------|
| `model` | string | Model to use (e.g., "elysia") |
| `messages` | array | Conversation messages |
| `temperature` | float | Randomness (0.0-2.0), default 0.7 |
| `max_tokens` | integer | Max response length |
| `stream` | boolean | Enable streaming responses |
| `tools` | array | Available functions/tools |

Full API documentation: [https://app.yg3.ai/docs](https://app.yg3.ai/docs)

---

## 🏗️ Repository Structure

```
yg3-tutorials/
├── README.md                          # This file
├── tutorials/                         # Complete tutorials
│   └── react-customer-support-agent/ # Part 1: ReAct agent
│       ├── README.md
│       ├── notebook.ipynb
│       ├── minimal_agent.py
│       └── production_agent.py
│
├── examples/                          # Quick examples
│   ├── quickstart/                   # Getting started examples
│   ├── streaming/                    # Streaming responses
│   ├── function-calling/             # Tool use examples
│   └── integrations/                 # Framework integrations
│
└── resources/                         # Additional resources
    ├── api-reference.md              # Detailed API docs
    ├── best-practices.md             # Tips and patterns
    └── troubleshooting.md            # Common issues
```

---

## 🎯 Use Cases

What can you build with the YG3 API?

### Business Applications
- 🤖 **Customer Support** - Automated support agents
- 📧 **Email Automation** - Smart email responses
- 📊 **Data Analysis** - Natural language to insights
- 💼 **Sales Assistance** - Lead qualification and follow-up

### Developer Tools
- 💻 **Code Assistance** - Code generation and review
- 📝 **Documentation** - Auto-generated docs
- 🧪 **Testing** - Test case generation
- 🔍 **Code Search** - Natural language code search

### Content Creation
- ✍️ **Writing** - Blog posts, articles, copy
- 🎨 **Marketing** - Ad copy, social media
- 📚 **Education** - Lesson plans, explanations
- 🌐 **Translation** - Multilingual content

### Advanced Applications
- 🧠 **AI Agents** - Autonomous decision-making systems
- 🔗 **Workflows** - Intelligent automation
- 🎯 **Personalization** - Tailored user experiences
- 📈 **Analytics** - Conversational business intelligence

---

## 🚀 Getting Started Paths

### Path 1: Complete Beginner
**Goal:** Learn the basics

1. Read the [Quick Start](#quick-start)
2. Make your first API call
3. Try different parameters
4. Explore [Tutorial 1](./tutorials/react-customer-support-agent/)

**Time:** 30 minutes

---

### Path 2: Experienced Developer
**Goal:** Build production apps quickly

1. Skim Quick Start
2. Review [API Reference](#api-reference)
3. Check [Integration Examples](#integration-examples)
4. Build your use case
5. Review [Best Practices](./resources/best-practices.md)

**Time:** 15 minutes to first app

---

### Path 3: Framework User
**Goal:** Integrate with existing tools

1. Find your framework in [Integration Examples](#integration-examples)
2. Swap base URL in your existing code
3. Test with YG3 API
4. Deploy

**Frameworks Supported:**
- LangChain
- LlamaIndex
- Haystack
- Semantic Kernel
- AutoGen
- CrewAI

**Time:** 5 minutes

---

## 💼 Production Checklist

Ready to deploy? Make sure you have:

- [ ] **Error Handling** - Catch and handle API errors
- [ ] **Rate Limiting** - Implement backoff strategies
- [ ] **API Key Security** - Never expose in client-side code
- [ ] **Logging** - Track usage and errors
- [ ] **Monitoring** - Alert on failures
- [ ] **Cost Tracking** - Monitor token usage
- [ ] **Fallbacks** - Handle API downtime
- [ ] **Testing** - Unit and integration tests

See [Best Practices](./resources/best-practices.md) for details.

---

## 🤝 Contributing

We welcome contributions! Here's how:

### Add a Tutorial
1. Fork this repository
2. Create a new folder in `tutorials/`
3. Include: README, code, notebook
4. Submit a pull request

### Improve Examples
- Fix bugs
- Add new examples
- Improve documentation
- Share your use case

### Report Issues
- Found a bug? [Open an issue](https://github.com/yourusername/yg3-tutorials/issues)
- Have a question? [Start a discussion](https://github.com/yourusername/yg3-tutorials/discussions)

---

## 💬 Community & Support

### Get Help
- 📖 **Documentation:** [app.yg3.ai/docs](https://app.yg3.ai/docs)
- 💬 **Discussions:** [GitHub Discussions](https://github.com/yourusername/yg3-tutorials/discussions)
- 🐛 **Issues:** [Report bugs](https://github.com/yourusername/yg3-tutorials/issues)
- 📧 **Email:** support@yg3.ai

### Share Your Work
Built something cool? Share it!
- [Show and Tell](https://github.com/yourusername/yg3-tutorials/discussions/categories/show-and-tell)
- Tag `#YG3API` on social media
- Submit to our showcase

---

## 🗺️ Roadmap

### Current
- ✅ Quick Start guide
- ✅ Tutorial 1: ReAct Customer Support Agent
- ✅ Integration examples
- ✅ API reference

### Coming Soon
- 🚧 More comprehensive tutorials
- 🚧 Additional code examples
- 🚧 Video walkthroughs
- 🚧 Community showcase
- 🚧 Advanced patterns and use cases

**Want to influence the roadmap?** [Share your ideas](https://github.com/yourusername/yg3-tutorials/discussions)

---

## 📝 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

You're free to use, modify, and distribute this code for any purpose.

---

## 🙏 Acknowledgments

- **YG3 Team** - For building an amazing API
- **OpenAI** - For the API standard
- **Community Contributors** - Everyone who improves these tutorials
- **You** - For building with YG3!

---

## ⭐ Star This Repo

If these tutorials helped you, give us a star! ⭐

It helps others discover these resources.

---

<div align="center">

**Ready to build something amazing?**

[🚀 Get Your API Key](https://app.yg3.ai) | [📚 Start Tutorial 1](./tutorials/react-customer-support-agent/) | [💬 Join Community](https://github.com/yourusername/yg3-tutorials/discussions)

---

Built for developers, by developers 💙

</div>
