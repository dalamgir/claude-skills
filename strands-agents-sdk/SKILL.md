---
name: strands-agents-sdk
description: Build AI agents with Strands SDK using Bedrock, Anthropic, OpenAI, Gemini, or Llama models. Use when creating agents with tools, managing conversation context, or integrating multiple LLM providers. Includes installation, model configuration, custom tools, and troubleshooting.
metadata:
  author: AWS
  version: 1.0.0
  category: ai-development
  tags: [agents, ai, llm, bedrock, anthropic, openai, gemini, strands, tools]
---

# Strands Agents SDK - Building AI Agents

Build AI agents quickly with the Strands Agents SDK. Create agents that use tools, maintain conversation context, and work with multiple LLM providers including Amazon Bedrock (default), Anthropic Claude, OpenAI GPT, Google Gemini, and Meta Llama.

## When to Use This Skill

Use this skill when you need to:
- Create a new AI agent with tool-calling capabilities
- Integrate multiple LLM providers (Bedrock, Anthropic, OpenAI, Gemini, Llama)
- Build agents with custom tools and functions
- Manage conversation history and context
- Troubleshoot agent installation or configuration issues
- Implement prompt caching for long system prompts

Do NOT use this skill for:
- General Python programming questions (use general coding skills)
- AWS infrastructure setup (use AWS-specific skills)
- Non-agent AI/ML tasks

## Prerequisites

**CRITICAL:** Before using this skill, ensure:
- Python 3.8 or higher installed
- pip package manager available
- API credentials for your chosen model provider

## Quick Start Pattern

When building an agent, follow these steps automatically:

1. **Install packages** based on model provider
2. **Configure API credentials** 
3. **Create agent file**
4. **Run immediately** to verify

## Installation

```bash
# Core SDK (includes Amazon Bedrock as default)
pip install strands-agents

# Model Provider Extensions (install only what you need)
pip install 'strands-agents[anthropic]'  # Anthropic Claude
pip install 'strands-agents[openai]'     # OpenAI GPT
pip install 'strands-agents[gemini]'     # Google Gemini
pip install 'strands-agents[llamaapi]'   # Meta Llama

# Community tools (recommended)
pip install strands-agents-tools
```

## Basic Agent Pattern

```python
from strands import Agent

agent = Agent(
    model=model_instance,      # Optional, defaults to Bedrock Claude 4 Sonnet
    tools=[tool1, tool2],      # Optional tools
    system_prompt="Instructions",  # Optional system prompt
)

response = agent("Your question here")
```

## Model Providers

### Amazon Bedrock (Default - No Extra Install)

**API Key Setup (Development):**
```bash
# Get key from: https://console.aws.amazon.com/bedrock → API keys
export AWS_BEDROCK_API_KEY=your_bedrock_api_key
```

**AWS Credentials (Production):**
```bash
aws configure
# OR
export AWS_ACCESS_KEY_ID=your_access_key
export AWS_SECRET_ACCESS_KEY=your_secret_key
export AWS_REGION=us-west-2
```

**Usage:**
```python
from strands import Agent
from strands.models import BedrockModel
from strands_tools import calculator, python_repl, http_request

# Simplest - uses Claude 4 Sonnet by default
agent = Agent(
    tools=[calculator, python_repl, http_request],
    system_prompt="You are a helpful assistant."
)

# With model ID string
agent = Agent(
    model="anthropic.claude-sonnet-4-20250514-v1:0",
    tools=[calculator, python_repl, http_request]
)

# Full configuration
model = BedrockModel(
    model_id="anthropic.claude-sonnet-4-20250514-v1:0",
    region_name="us-west-2",
    temperature=0.7,
    max_tokens=2048,
)

agent = Agent(model=model, tools=[calculator, python_repl, http_request])
```

**Popular Bedrock Models:**
- `anthropic.claude-sonnet-4-20250514-v1:0` (default)
- `anthropic.claude-3-5-sonnet-20241022-v2:0`
- `us.amazon.nova-premier-v1:0`
- `us.amazon.nova-pro-v1:0`
- `us.meta.llama3-2-90b-instruct-v1:0`

**Important:** Enable model access in Bedrock Console → Model access → Manage model access

### Anthropic (Direct)

**Setup:**
```bash
pip install 'strands-agents[anthropic]'
# Get key from: https://console.anthropic.com/
export ANTHROPIC_API_KEY=your_anthropic_api_key
```

**Usage:**
```python
from strands import Agent
from strands.models.anthropic import AnthropicModel
from strands_tools import calculator, python_repl, http_request
import os

model = AnthropicModel(
    client_args={"api_key": os.environ["ANTHROPIC_API_KEY"]},
    model_id="claude-sonnet-4-20250514",
    max_tokens=1028,
    params={"temperature": 0.7}
)

agent = Agent(
    model=model,
    tools=[calculator, python_repl, http_request],
    system_prompt="You are a helpful assistant."
)
```

**Popular Models:**
- `claude-sonnet-4-20250514`
- `claude-3-5-sonnet-20241022`
- `claude-3-opus-20240229`

### OpenAI

**Setup:**
```bash
pip install 'strands-agents[openai]'
# Get key from: https://platform.openai.com/api-keys
export OPENAI_API_KEY=your_openai_api_key
```

**Usage:**
```python
from strands import Agent
from strands.models.openai import OpenAIModel
import os

model = OpenAIModel(
    client_args={"api_key": os.environ["OPENAI_API_KEY"]},
    model_id="gpt-5-mini",
)

agent = Agent(model=model, tools=[calculator, python_repl, http_request])
```

**Popular Models:**
- `gpt-5-mini`
- `gpt-5.1`

### Google Gemini

**Setup:**
```bash
pip install 'strands-agents[gemini]'
# Get key from: https://aistudio.google.com/apikey
export GOOGLE_API_KEY=your_google_api_key
```

**Usage:**
```python
from strands import Agent
from strands.models.gemini import GeminiModel
import os

model = GeminiModel(
    client_args={"api_key": os.environ["GOOGLE_API_KEY"]},
    model_id="gemini-2.5-pro",
)

agent = Agent(model=model, tools=[calculator, python_repl, http_request])
```

**Popular Models:**
- `gemini-3-pro-preview`
- `gemini-2.5-pro`
- `gemini-2.5-flash`

### Meta Llama

**Setup:**
```bash
pip install 'strands-agents[llamaapi]'
# Get key from: https://llama.developer.meta.com/
export LLAMA_API_KEY=your_llama_api_key
```

**Usage:**
```python
from strands import Agent
from strands.models.llamaapi import LlamaAPIModel
import os

model = LlamaAPIModel(
    client_args={"api_key": os.environ["LLAMA_API_KEY"]},
    model_id="Llama-4-Maverick-17B-128E-Instruct-FP8",
    temperature=0.7,
    max_completion_tokens=2048
)

agent = Agent(model=model, tools=[calculator, python_repl, http_request])
```

**Popular Models:**
- `Llama-4-Maverick-17B-128E-Instruct-FP8`
- `Llama-3.3-70B-Instruct`
- `Llama-3.2-90B-Vision-Instruct`

## Creating Custom Tools

```python
from strands import Agent, tool

@tool
def get_weather(location: str) -> str:
    """Get weather for a location.
    
    Args:
        location: City name
    """
    return f"Weather in {location}: Sunny, 72°F"

@tool
def calculate_tip(bill_amount: float, tip_percentage: float = 15.0) -> float:
    """Calculate tip amount.
    
    Args:
        bill_amount: Total bill amount in dollars
        tip_percentage: Tip percentage (default 15%)
    """
    return bill_amount * (tip_percentage / 100)

agent = Agent(tools=[get_weather, calculate_tip])
```

**Tool Best Practices:**
- Use clear, descriptive docstrings (models read them)
- Include Args section with parameter descriptions
- Keep tools focused on single responsibilities
- Return simple data types when possible

## Community Tools

```python
from strands_tools import calculator, python_repl, http_request

agent = Agent(tools=[calculator, python_repl, http_request])
```

**Available Community Tools:**
- `calculator` - Perform mathematical calculations
- `python_repl` - Execute Python code
- `http_request` - Make HTTP requests

## Conversation History

Agents automatically maintain conversation context:

```python
agent = Agent()

agent("My name is Alice")
response = agent("What's my name?")
print(response)  # Will remember "Alice"

# Continue the conversation
agent("I like Python programming")
response = agent("What do I like?")
print(response)  # Will remember "Python programming"
```

## Advanced Features

### Prompt Caching (Long System Prompts)

For system prompts over 1024 tokens, use caching to reduce costs:

```python
from strands import Agent
from strands.models import SystemContentBlock

long_instructions = """
[Your very long system prompt here - 1024+ tokens]
"""

agent = Agent(
    system_prompt=[
        SystemContentBlock(
            text=long_instructions,
            cachePoint=True  # Cache this content
        )
    ]
)
```

### Temperature Control

```python
from strands.models import BedrockModel

# Factual/deterministic tasks
model = BedrockModel(temperature=0.1)

# Creative tasks
model = BedrockModel(temperature=0.9)

# Balanced (default)
model = BedrockModel(temperature=0.7)
```

### Token Limits

```python
model = BedrockModel(
    max_tokens=4096  # Increase for complex tool calls
)
```

## Complete Example

```python
from strands import Agent, tool
from strands.models import BedrockModel
from strands_tools import calculator, python_repl, http_request
import os

# Custom tool
@tool
def get_user_info(user_id: str) -> dict:
    """Get user information by ID.
    
    Args:
        user_id: The user's unique identifier
    """
    return {"id": user_id, "name": "John Doe", "email": "john@example.com"}

# Configure model
model = BedrockModel(
    model_id="anthropic.claude-sonnet-4-20250514-v1:0",
    temperature=0.7,
    max_tokens=2048,
)

# Create agent
agent = Agent(
    model=model,
    tools=[calculator, python_repl, http_request, get_user_info],
    system_prompt="You are a helpful assistant with access to tools."
)

# Use the agent
response = agent("What's 15% of 250?")
print(response)

response = agent("Get info for user ID 12345")
print(response)
```

## Troubleshooting

### Module not found error
```bash
# Install the specific provider extension
pip install 'strands-agents[anthropic]'
pip install 'strands-agents[openai]'
pip install 'strands-agents[gemini]'
pip install 'strands-agents[llamaapi]'
```

### Bedrock access denied
1. Enable model access: Bedrock Console → Model access → Manage model access
2. Check AWS credentials: `aws sts get-caller-identity`
3. Verify IAM permissions for Bedrock API
4. For API keys: Check expiration (30-day limit for long-term keys)

### Invalid API key
```bash
# Verify environment variable is set
echo $AWS_BEDROCK_API_KEY
echo $ANTHROPIC_API_KEY
echo $OPENAI_API_KEY
echo $GOOGLE_API_KEY
echo $LLAMA_API_KEY

# Regenerate key if needed from provider console
```

### Token limit exceeded
```python
# Increase max_tokens
model = BedrockModel(max_tokens=4096)

# Or simplify tool specifications
# Or break complex tasks into smaller steps
```

### Tool not found or execution failed
1. Verify tool is in tools list: `Agent(tools=[your_tool])`
2. Check `@tool` decorator is present
3. Ensure proper docstring with Args section
4. Test tool independently before adding to agent

### Agent gives incorrect responses
1. Lower temperature for factual tasks: `temperature=0.1`
2. Make system prompt more specific
3. Add examples in system prompt
4. Verify tools work correctly independently

## Best Practices

### Do:
- Use Bedrock as default (included in base package)
- Install provider extensions only when needed
- Always install community tools: `pip install strands-agents-tools`
- Set API keys as environment variables
- Use clear docstrings for custom tools
- Test agents immediately after creation
- Use lower temperature (0.1-0.3) for factual tasks
- Enable model access in Bedrock console before using
- Use `SystemContentBlock` with `cachePoint` for long prompts (1024+ tokens)

### Don't:
- Install all provider extensions if you only need one
- Hardcode API keys in code
- Skip tool docstrings
- Use production AWS credentials for development (use Bedrock API keys)
- Forget to enable model access in Bedrock console
- Set max_tokens too low
- Ignore conversation history

## Quick Reference

**Default Agent (Bedrock):**
```python
from strands import Agent
agent = Agent()
response = agent("Hello!")
```

**With Tools:**
```python
from strands import Agent
from strands_tools import calculator
agent = Agent(tools=[calculator])
```

**Custom Model:**
```python
from strands import Agent
from strands.models import BedrockModel
model = BedrockModel(model_id="anthropic.claude-sonnet-4-20250514-v1:0")
agent = Agent(model=model)
```

**Full Configuration:**
```python
from strands import Agent, tool
from strands.models import BedrockModel

@tool
def my_tool(param: str) -> str:
    """Tool description.
    
    Args:
        param: Parameter description
    """
    return f"Result: {param}"

model = BedrockModel(
    model_id="anthropic.claude-sonnet-4-20250514-v1:0",
    temperature=0.7,
    max_tokens=2048
)

agent = Agent(
    model=model,
    tools=[my_tool],
    system_prompt="You are a helpful assistant."
)

response = agent("Your question")
```

---

**Source:** AWS Strands Agents SDK
**License:** MIT
**Documentation:** Use Strands documentation tools for detailed API reference
