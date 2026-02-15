---
name: bedrock-agentcore
description: Build, test, and deploy AI agents using AWS Bedrock AgentCore with local development workflow. Use when creating new agent projects, deploying existing agents, integrating AgentCore Memory for conversation persistence, or setting up AgentCore Gateway for external tools via MCP. Includes agentcore CLI commands, memory management, and gateway configuration.
metadata:
  author: AWS
  version: 1.0.0
  category: ai-development
  tags: [agentcore, bedrock, aws, agents, ai, development, deployment, memory, gateway]
---

# AWS Bedrock AgentCore - Building and Deploying AI Agents

Build, test, and deploy AI agents using AWS Bedrock AgentCore with a complete local development workflow. AgentCore is an agentic platform for building, deploying, and operating effective agents with support for multiple agent SDKs (Strands, Claude, OpenAI) and model providers.

## When to Use This Skill

Use this skill when you need to:
- Create a new AgentCore project from scratch
- Deploy an existing agent to AWS Bedrock AgentCore
- Set up local development environment with hot reloading
- Integrate AgentCore Memory for conversation persistence
- Configure AgentCore Gateway for external tools via MCP
- Troubleshoot AgentCore deployment or configuration issues
- Manage agent lifecycle (create, dev, deploy, destroy)

Do NOT use this skill for:
- General AWS infrastructure questions (use AWS-specific skills)
- Non-AgentCore agent frameworks
- Simple Python scripting without agent deployment

## Prerequisites

**CRITICAL:** Before using this skill, ensure:
- `bedrock-agentcore-starter-toolkit` is installed: `pip install bedrock-agentcore-starter-toolkit`
- AWS credentials are configured (for deployment)
- Python 3.8+ and pip are available

## Two Development Paths

### Path 1: Deploy an Existing Agent

If you already have an agent, DO NOT use `agentcore create`. Instead:

1. Wrap your agent with `BedrockAgentCoreApp`
2. Add `@app.entrypoint` decorator
3. Include `bedrock-agentcore` in requirements.txt
4. Use `agentcore configure` and `agentcore launch`

**Example wrapping existing agent:**

```python
from bedrock_agentcore import BedrockAgentCoreApp
from your_agent import your_existing_agent

app = BedrockAgentCoreApp()

@app.entrypoint
def invoke(payload):
    prompt = payload.get("prompt", "Hello")
    response = your_existing_agent(prompt)
    return {"response": response}

if __name__ == "__main__":
    app.run()
```

### Path 2: Create New Project from Scratch

Use `agentcore create` to initialize a new agent project.

## Creating a New Agent Project

**IMPORTANT:** Always use `--non-interactive` mode when running programmatically.

**Default behavior** (if user doesn't specify options):
- Template: `basic`
- Agent Framework: `Strands`
- Model Provider: `Bedrock`

```bash
# Minimal command (uses all defaults)
agentcore create --non-interactive --project-name MyAgent

# With custom options
agentcore create --non-interactive \
    --project-name MyAgent \
    --template basic \
    --agent-framework Strands \
    --model-provider Bedrock

# Production template with IaC
agentcore create --non-interactive \
    --project-name MyAgent \
    --template production \
    --agent-framework Strands \
    --model-provider Bedrock \
    --iac CDK
```

**Options:**
- `--project-name, -p`: Project name (alphanumeric, max 36 chars, starts with letter)
- `--template, -t`: `basic` (runtime-only) or `production` (with IaC)
- `--agent-framework`: Strands, ClaudeAgents, OpenAI, etc.
- `--model-provider, -mp`: Bedrock, OpenAI, etc.
- `--provider-api-key, -key`: API key for non-Bedrock providers
- `--iac`: CDK or Terraform (required for production template)
- `--non-interactive`: Run without prompts (required for automation)
- `--venv` / `--no-venv`: Auto-create venv (default: true)

**What it creates:**
- Project directory with chosen name
- Agent SDK boilerplate code
- Configuration files (`.bedrock_agentcore.yaml`)
- Dependencies setup (`pyproject.toml` or `requirements.txt`)
- Virtual environment (unless `--no-venv`)

## Local Development Workflow

### 1. Start Development Server

```bash
cd <project-name>
agentcore dev
```

**Options:**
- `--port, -p`: Port for dev server (default: 8080, auto-finds next available)
- `--env, -env`: Set environment variables (format: KEY=VALUE, repeatable)

**What it does:**
- Auto-detects entrypoint from `.bedrock_agentcore.yaml` or uses `src.main:app`
- Starts uvicorn server with hot reloading
- Watches for file changes and auto-reloads
- Available at `http://localhost:<port>/invocations`
- Sets `LOCAL_DEV=1` environment variable

**Examples:**
```bash
# Basic usage
agentcore dev

# Custom port
agentcore dev --port 3000

# With environment variables
agentcore dev --env API_KEY=abc123 --env DEBUG=true
```

**Stop server:** Press `Ctrl+C`

### 2. Test Agent Locally

While dev server is running, test with `agentcore invoke --dev`:

```bash
# JSON payload
agentcore invoke --dev '{"prompt": "What is AWS?"}'

# Plain text (auto-wrapped)
agentcore invoke --dev 'Hello, how are you?'

# Custom port
agentcore invoke --dev --port 3000 '{"prompt": "Hello"}'

# With session ID for conversation continuity
agentcore invoke --dev --session-id abc123 '{"prompt": "Continue our chat"}'
```

**Options:**
- `--dev, -d`: Send to local dev server (required for local testing)
- `--port`: Dev server port (default: 8080)
- `--local, -l`: Send to running local container
- `--session-id, -s`: Session ID for conversation continuity
- `--bearer-token, -bt`: Bearer token for OAuth
- `--user-id, -u`: User ID for authorization
- `--headers`: Custom headers (format: 'Header1:value,Header2:value2')
- `--agent, -a`: Agent name (if multiple agents)

### 3. Development Best Practices

**CRITICAL**: After every code change, test locally using `agentcore invoke --dev` to verify:
- The change works as intended
- No errors are introduced
- Agent responds correctly

**Typical development loop:**
1. Make code changes
2. Save file (dev server auto-reloads)
3. Run `agentcore invoke --dev '{"prompt": "test"}'`
4. Verify response
5. Repeat

## Deploying to AWS

### For New Projects

```bash
# Configure for deployment
agentcore configure --entrypoint src/main.py

# Deploy to cloud
agentcore launch

# Check status
agentcore status

# Invoke deployed agent
agentcore invoke '{"prompt": "Hello"}'

# Stop active session
agentcore stop-session

# Preview what would be destroyed
agentcore destroy --dry-run

# Destroy all resources
agentcore destroy
```

### For Existing Agents

```bash
# Configure
agentcore configure --entrypoint your_agent.py --non-interactive

# Deploy
agentcore launch

# Test
agentcore invoke '{"prompt": "Hello"}'
```

## AgentCore Memory Integration

Memory enables conversation persistence and long-term knowledge retention across sessions.

### Step 1: Install Dependencies

```bash
pip install 'bedrock-agentcore[strands-agents]'
```

Add to `requirements.txt`:
```
bedrock-agentcore[strands-agents]
```

### Step 2: Create Memory Resource (One-Time Setup)

```bash
# Basic memory (STM only)
agentcore memory create my_agent_memory \
    --description "Memory for my agent" \
    --region us-west-2 \
    --wait

# With long-term memory strategies
agentcore memory create my_agent_memory \
    --description "Memory with LTM" \
    --strategies '[
        {
            "summaryMemoryStrategy": {
                "name": "SessionSummarizer",
                "namespaces": ["/summaries/{actorId}/{sessionId}"]
            }
        },
        {
            "userPreferenceMemoryStrategy": {
                "name": "PreferenceLearner",
                "namespaces": ["/preferences/{actorId}"]
            }
        },
        {
            "semanticMemoryStrategy": {
                "name": "FactExtractor",
                "namespaces": ["/facts/{actorId}"]
            }
        }
    ]' \
    --region us-west-2 \
    --wait
```

**Save the memory ID** from the output.

### Step 3: Update Agent Code

```python
import os
from datetime import datetime
from strands import Agent
from bedrock_agentcore import BedrockAgentCoreApp
from bedrock_agentcore.memory.integrations.strands.config import AgentCoreMemoryConfig
from bedrock_agentcore.memory.integrations.strands.session_manager import AgentCoreMemorySessionManager

app = BedrockAgentCoreApp()

@app.entrypoint
def invoke(payload):
    # Memory configuration
    memory_id = "your-memory-id-here"  # From Step 2
    session_id = payload.get("session_id", f"session_{datetime.now().strftime('%Y%m%d%H%M%S')}")
    actor_id = payload.get("actor_id", "default_user")
    
    # Create session manager
    config = AgentCoreMemoryConfig(
        memory_id=memory_id,
        session_id=session_id,
        actor_id=actor_id
    )
    
    session_manager = AgentCoreMemorySessionManager(
        agentcore_memory_config=config,
        region_name="us-west-2"
    )
    
    # Create agent with memory
    agent = Agent(
        system_prompt="You are a helpful assistant with memory.",
        session_manager=session_manager
    )
    
    prompt = payload.get("prompt", "Hello")
    response = agent(prompt)
    
    return {
        "response": response,
        "session_id": session_id,
        "actor_id": actor_id
    }

if __name__ == "__main__":
    app.run()
```

### Step 4: Configure Memory in .bedrock_agentcore.yaml

```yaml
agents:
  YourAgent:
    memory:
      mode: STM_ONLY  # or STM_AND_LTM for long-term memory
      memory_id: your-memory-id-here
      memory_name: MyAgentMemory
      event_expiry_days: 30
```

**CRITICAL DEPLOYMENT NOTE:**
- If deploying a NEW agent with memory, start with `mode: NO_MEMORY` first
- Deploy and test successfully
- Then update to `mode: STM_ONLY` or `mode: STM_AND_LTM`
- Redeploy

### Step 5: Test Memory

```bash
# First message
agentcore invoke '{
    "prompt": "My name is Alice and I like pizza",
    "session_id": "test_session_001",
    "actor_id": "alice"
}'

# Second message - agent should remember
agentcore invoke '{
    "prompt": "What is my name and what do I like?",
    "session_id": "test_session_001",
    "actor_id": "alice"
}'
```

### Memory Management Commands

```bash
# List all memories
agentcore memory list --region us-west-2

# Get memory details
agentcore memory get <memory-id> --region us-west-2

# Check status
agentcore memory status <memory-id> --region us-west-2

# Delete memory (permanent)
agentcore memory delete <memory-id> --region us-west-2 --wait
```

### Memory Strategies

1. **Short-Term Memory (STM)**: Conversation events within a session
2. **Summary Memory**: Automatically summarizes sessions
3. **User Preference Memory**: Learns user preferences across sessions
4. **Semantic Memory**: Extracts and stores factual information

## AgentCore Gateway Integration

Gateway enables your agent to access external tools via MCP (Model Context Protocol), including Lambda functions, OpenAPI services, and AWS services.

### Step 1: Create Gateway

```bash
agentcore gateway create-mcp-gateway \
    --name MyAgentGateway \
    --region us-west-2 \
    --enable_semantic_search
```

**Save the output:**
- Gateway ARN
- Gateway URL
- Gateway ID
- Client ID (from Cognito)
- Client Secret (from Cognito)

### Step 2: Add Gateway Targets

#### Lambda Function Target

Create `lambda_target.json`:

```json
{
    "lambdaArn": "arn:aws:lambda:us-west-2:123456789:function:MyFunction",
    "toolSchema": {
        "inlinePayload": [
            {
                "name": "my_tool",
                "description": "Description of what this tool does",
                "inputSchema": {
                    "type": "object",
                    "properties": {
                        "param1": {
                            "type": "string",
                            "description": "First parameter"
                        }
                    },
                    "required": ["param1"]
                }
            }
        ]
    }
}
```

Add the target:

```bash
agentcore gateway create-mcp-gateway-target \
    --gateway-arn <gateway-arn> \
    --gateway-url <gateway-url> \
    --role-arn <role-arn> \
    --name MyLambdaTarget \
    --target-type lambda \
    --target-payload "$(cat lambda_target.json)" \
    --region us-west-2
```

#### OpenAPI Service Target

```bash
agentcore gateway create-mcp-gateway-target \
    --gateway-arn <gateway-arn> \
    --gateway-url <gateway-url> \
    --role-arn <role-arn> \
    --name MyAPITarget \
    --target-type openApiSchema \
    --target-payload '{
        "openApiSchema": {
            "uri": "https://api.example.com/openapi.json"
        }
    }' \
    --credentials '{
        "api_key": "your-api-key",
        "credential_location": "header",
        "credential_parameter_name": "X-API-Key"
    }' \
    --region us-west-2
```

#### Smithy Model Target (AWS Services)

```bash
agentcore gateway create-mcp-gateway-target \
    --gateway-arn <gateway-arn> \
    --gateway-url <gateway-url> \
    --role-arn <role-arn> \
    --name DynamoDBTarget \
    --target-type smithyModel \
    --region us-west-2
```

### Step 3: Update Agent Code for Gateway

Install dependencies:

```bash
pip install httpx>=0.27.0
```

Create `src/mcp_client/client.py`:

```python
import os
import httpx
from datetime import datetime, timedelta
from mcp.client.streamable_http import streamablehttp_client
from strands.tools.mcp.mcp_client import MCPClient

# Gateway configuration
GATEWAY_MCP_URL = os.environ.get("GATEWAY_MCP_URL")
GATEWAY_CLIENT_ID = os.environ.get("GATEWAY_CLIENT_ID")
GATEWAY_CLIENT_SECRET = os.environ.get("GATEWAY_CLIENT_SECRET")
GATEWAY_TOKEN_ENDPOINT = os.environ.get("GATEWAY_TOKEN_ENDPOINT")
GATEWAY_SCOPE = os.environ.get("GATEWAY_SCOPE")

# Token cache
_token_cache = {"token": None, "expires_at": None}

def get_oauth_token():
    """Get OAuth2 token with caching"""
    if _token_cache["token"] and _token_cache["expires_at"] and datetime.now() < _token_cache["expires_at"]:
        return _token_cache["token"]
    
    response = httpx.post(
        GATEWAY_TOKEN_ENDPOINT,
        data={
            "grant_type": "client_credentials",
            "client_id": GATEWAY_CLIENT_ID,
            "client_secret": GATEWAY_CLIENT_SECRET,
            "scope": GATEWAY_SCOPE
        },
        headers={"Content-Type": "application/x-www-form-urlencoded"}
    )
    
    if response.status_code == 200:
        data = response.json()
        token = data["access_token"]
        expires_in = data.get("expires_in", 3600) - 300
        _token_cache["token"] = token
        _token_cache["expires_at"] = datetime.now() + timedelta(seconds=expires_in)
        return token
    else:
        raise Exception(f"Failed to get OAuth token: {response.status_code}")

def get_streamable_http_mcp_client() -> MCPClient:
    """Returns an MCP Client configured for AgentCore Gateway"""
    access_token = get_oauth_token()
    
    return MCPClient(
        lambda: streamablehttp_client(
            GATEWAY_MCP_URL,
            headers={"Authorization": f"Bearer {access_token}"}
        )
    )
```

Update `src/main.py`:

```python
from strands import Agent, tool
from bedrock_agentcore import BedrockAgentCoreApp
from mcp_client.client import get_streamable_http_mcp_client
from model.load import load_model

@tool
def local_tool(param: str) -> str:
    """A local tool that runs in the agent"""
    return f"Processed: {param}"

app = BedrockAgentCoreApp()

@app.entrypoint
def invoke(payload):
    # Create MCP client
    mcp_client = get_streamable_http_mcp_client()
    
    # Get gateway tools
    with mcp_client:
        gateway_tools = mcp_client.list_tools_sync()
        
        # Create agent with all tools
        agent = Agent(
            model=load_model(),
            tools=[local_tool] + gateway_tools,
            system_prompt="You are a helpful assistant with access to tools."
        )
        
        prompt = payload.get("prompt", "Hello")
        response = agent(prompt)
        
        return {"response": response}

if __name__ == "__main__":
    app.run()
```

### Step 4: Configure Environment Variables

Create `.env`:

```bash
# Gateway Configuration
GATEWAY_MCP_URL=https://your-gateway-id.gateway.bedrock-agentcore.us-west-2.amazonaws.com/mcp
GATEWAY_CLIENT_ID=your-client-id
GATEWAY_CLIENT_SECRET=your-client-secret
GATEWAY_TOKEN_ENDPOINT=https://your-cognito-domain.auth.us-west-2.amazoncognito.com/oauth2/token
GATEWAY_SCOPE=YourGatewayName/invoke

# AWS Configuration
AWS_REGION=us-west-2
```

**IMPORTANT**: Never commit `.env` files with secrets!

### Gateway Management Commands

```bash
# List gateways
agentcore gateway list-mcp-gateways --region us-west-2

# Get gateway details
agentcore gateway get-mcp-gateway --name MyGateway --region us-west-2

# List targets
agentcore gateway list-mcp-gateway-targets --name MyGateway --region us-west-2

# Delete target
agentcore gateway delete-mcp-gateway-target \
    --name MyGateway \
    --target-name MyTarget \
    --region us-west-2

# Delete gateway
agentcore gateway delete-mcp-gateway --name MyGateway --force --region us-west-2
```

### Gateway Target Types

1. **Lambda Target**: Custom business logic, AWS service integrations
2. **OpenAPI Target**: External REST APIs with OpenAPI specs
3. **Smithy Model Target**: AWS services (DynamoDB, S3, etc.)
4. **MCP Server Target**: Existing MCP-compatible services

## Combining Memory and Gateway

```python
from bedrock_agentcore.memory.integrations.strands.config import AgentCoreMemoryConfig
from bedrock_agentcore.memory.integrations.strands.session_manager import AgentCoreMemorySessionManager

@app.entrypoint
def invoke(payload):
    # Memory setup
    memory_config = AgentCoreMemoryConfig(
        memory_id=os.environ.get("AGENTCORE_MEMORY_ID"),
        session_id=payload.get("session_id", "default"),
        actor_id=payload.get("actor_id", "user")
    )
    session_manager = AgentCoreMemorySessionManager(
        agentcore_memory_config=memory_config,
        region_name="us-west-2"
    )
    
    # Gateway setup
    mcp_client = get_streamable_http_mcp_client()
    
    with mcp_client:
        gateway_tools = mcp_client.list_tools_sync()
        
        # Agent with both memory and gateway
        agent = Agent(
            model=load_model(),
            tools=gateway_tools,
            session_manager=session_manager
        )
        
        prompt = payload.get("prompt")
        response = agent(prompt)
        
        return {"response": response}
```

## Troubleshooting

### Dev Server Won't Start
- Verify entrypoint file exists (default: `src/main.py`)
- Check for syntax errors
- Ensure dependencies installed: `uv pip install -e .`
- Check `.bedrock_agentcore.yaml` has valid entrypoint

### Invoke Fails
- Ensure dev server is running
- Check server URL (default: `http://localhost:8080`)
- Verify payload format is valid JSON

### Port Conflicts
- Dev server auto-finds next available port if 8080 is taken
- Check console output for actual port

### Deployment Issues
- **AWS authentication failed**: Run `aws login`
- **Model access denied**: Verify Bedrock model permissions in AWS console

### Memory Not Persisting
- Verify memory ID is correct
- Check session_id is consistent across calls
- Ensure memory mode is not `NO_MEMORY`
- Check CloudWatch logs

### Gateway Tools Not Available
- Verify gateway URL is correct
- Check OAuth token generation
- Ensure gateway status is READY
- Verify target status is READY

## Best Practices

### Development
- Always test locally with `agentcore invoke --dev` after changes
- Use meaningful session IDs for related conversations
- Use unique actor IDs per user
- Start with `STM_ONLY` memory, add LTM as needed

### Security
- Never commit credentials to version control
- Use environment variables for secrets
- Cache OAuth tokens to avoid rate limits
- Use IAM roles in production, not API keys

### Deployment
- Start with `mode: NO_MEMORY` for new agents
- Test successfully before adding memory
- Use `agentcore destroy --dry-run` before destroying resources
- Monitor CloudWatch logs for errors

## Quick Reference

### New Project Workflow
```bash
# Create project
agentcore create --non-interactive --project-name MyAgent
cd MyAgent

# Start dev server
agentcore dev

# Test locally (in another terminal)
agentcore invoke --dev '{"prompt": "test"}'

# Deploy
agentcore configure --entrypoint src/main.py
agentcore launch

# Test deployed
agentcore invoke '{"prompt": "Hello"}'

# Cleanup
agentcore stop-session
agentcore destroy --dry-run
agentcore destroy
```

### Memory Commands
```bash
# Create
agentcore memory create my_memory --wait

# List
agentcore memory list

# Delete
agentcore memory delete <memory-id> --wait
```

### Gateway Commands
```bash
# Create
agentcore gateway create-mcp-gateway --name MyGateway --region us-west-2

# Add target
agentcore gateway create-mcp-gateway-target \
    --gateway-arn <arn> \
    --name MyTarget \
    --target-type lambda \
    --target-payload "$(cat config.json)"

# Delete
agentcore gateway delete-mcp-gateway --name MyGateway --force
```

---

**Source:** AWS Bedrock AgentCore
**Documentation:** https://docs.aws.amazon.com/bedrock-agentcore/
