# Claude Skills for AWS Development

Three comprehensive Claude skills for building AI agents and AWS infrastructure.

## Skills Included

### 1. Strands Agents SDK (`strands-agents-sdk/`)

Build AI agents with the Strands SDK using multiple LLM providers.

**Use when:**
- Creating agents with tool-calling capabilities
- Integrating Bedrock, Anthropic, OpenAI, Gemini, or Llama models
- Building custom tools and managing conversation context
- Implementing prompt caching for efficiency

**Key features:**
- Complete installation guides for all providers
- Custom tool creation patterns
- Conversation history management
- Troubleshooting guide
- Best practices and quick reference

### 2. Bedrock AgentCore (`bedrock-agentcore/`)

Build, test, and deploy agents using AWS Bedrock AgentCore.

**Use when:**
- Creating new AgentCore projects
- Deploying existing agents to AWS
- Setting up local development with hot reloading
- Integrating AgentCore Memory for persistence
- Configuring AgentCore Gateway for external tools

**Key features:**
- Complete CLI command reference
- Local development workflow
- Memory integration guide
- Gateway configuration for MCP tools
- Deployment best practices

### 3. AWS Infrastructure as Code (`aws-infrastructure-as-code/`)

Build AWS infrastructure with CDK and CloudFormation.

**Use when:**
- Creating CDK applications in TypeScript, Python, Java, C#, or Go
- Validating CloudFormation templates with cfn-lint
- Checking security compliance with cfn-guard
- Troubleshooting failed CloudFormation deployments
- Searching CDK documentation and code samples

**Key features:**
- Complete CDK development workflow
- CloudFormation template validation
- Security compliance checking
- Deployment troubleshooting with pattern matching
- CDK best practices and code examples
- Multi-language support (TypeScript, Python, Java, C#, Go)

## Installation

### For Claude.ai

1. Compress each skill folder as a .zip file:
```bash
cd strands-agents-sdk && zip -r ../strands-agents-sdk.zip . && cd ..
cd bedrock-agentcore && zip -r ../bedrock-agentcore.zip . && cd ..
cd aws-infrastructure-as-code && zip -r ../aws-infrastructure-as-code.zip . && cd ..
```

2. Upload to Claude.ai:
   - Go to Settings → Skills
   - Click "Upload Skill"
   - Select the .zip file

### For Claude Code

1. Copy skill folders to your Claude Code skills directory:
```bash
# macOS/Linux
cp -r strands-agents-sdk ~/.claude-code/skills/
cp -r bedrock-agentcore ~/.claude-code/skills/
cp -r aws-infrastructure-as-code ~/.claude-code/skills/

# Windows
copy strands-agents-sdk %USERPROFILE%\.claude-code\skills\
copy bedrock-agentcore %USERPROFILE%\.claude-code\skills\
copy aws-infrastructure-as-code %USERPROFILE%\.claude-code\skills\
```

2. Restart Claude Code

## Usage Examples

### Strands Agents SDK

```
"Create a Bedrock agent with calculator and weather tools"
"How do I add custom tools to a Strands agent?"
"Set up an Anthropic Claude agent with conversation history"
```

### Bedrock AgentCore

```
"Create a new AgentCore project called MyAgent"
"Deploy my existing agent to AgentCore"
"Set up AgentCore Memory for conversation persistence"
"Configure AgentCore Gateway to connect to my Lambda function"
```

### AWS Infrastructure as Code

```
"Create a CDK app with Lambda and DynamoDB in TypeScript"
"Validate my CloudFormation template for syntax errors"
"Check my template for security compliance issues"
"Troubleshoot why my CloudFormation stack failed to deploy"
"Show me CDK best practices for serverless applications"
```

## Skill Structure

Each skill follows Claude's best practices:

```
skill-name/
├── SKILL.md              # Main skill file with YAML frontmatter
└── (optional files)      # Additional resources if needed
```

Current structure:
```
.
├── README.md
├── strands-agents-sdk/
│   └── SKILL.md
├── bedrock-agentcore/
│   └── SKILL.md
├── aws-infrastructure-as-code/
│   └── SKILL.md
└── The-Complete-Guide-to-Building-Skill-for-Claude.pdf
```

### YAML Frontmatter

Both skills include proper metadata:
- `name`: kebab-case identifier
- `description`: Detailed description with trigger phrases
- `metadata`: Author, version, category, tags

## Best Practices Implemented

Based on "The Complete Guide to Building Skills for Claude":

1. **Clear Descriptions**: Include WHAT the skill does and WHEN to use it
2. **Trigger Phrases**: Specific phrases users would actually say
3. **Negative Triggers**: Explicitly state what NOT to use the skill for
4. **Critical Sections**: Important information marked with **CRITICAL** or **IMPORTANT**
5. **Structured Content**: Clear headers, bullet points, code examples
6. **Quick Reference**: Fast lookup sections at the end
7. **Troubleshooting**: Common issues and solutions

## Updating Skills

To update a skill:

1. Edit the `SKILL.md` file in the skill folder
2. Update the `version` in the metadata section
3. Re-zip and re-upload (Claude.ai) or restart (Claude Code)

## Contributing

To improve these skills:

1. Test with real use cases
2. Collect user feedback
3. Update descriptions if under/over-triggering occurs
4. Add new examples and troubleshooting tips
5. Keep content concise and actionable

## Resources

- [Anthropic Skills Documentation](https://docs.anthropic.com/claude/docs/skills)
- [Strands Agents SDK](https://strandsagents.com/)
- [AWS Bedrock AgentCore](https://docs.aws.amazon.com/bedrock-agentcore/)
- [AWS CDK Documentation](https://docs.aws.amazon.com/cdk/)
- [AWS CloudFormation Documentation](https://docs.aws.amazon.com/cloudformation/)
- [Claude Skills Best Practices](https://docs.anthropic.com/claude/docs/skills-best-practices)

## License

MIT - See individual skill files for details

## Support

For issues or questions:
- Strands SDK: [Strands Documentation](https://strandsagents.com/latest/documentation/)
- AgentCore: [AWS Documentation](https://docs.aws.amazon.com/bedrock-agentcore/)
- AWS IaC: [AWS CDK Documentation](https://docs.aws.amazon.com/cdk/) | [CloudFormation Documentation](https://docs.aws.amazon.com/cloudformation/)
- Skills: [Claude Developers Discord](https://discord.gg/anthropic)
