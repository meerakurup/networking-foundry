# networking-foundry

Foundry prompt-agent definition for a Microsoft Foundry network isolation expert.

## Runtime File

- `agent.yml` is the deployable Foundry prompt-agent definition.
- The agent is intentionally self-contained: the full behavior and core domain knowledge live in the `instructions` block because Foundry prompt agents do not automatically load local Markdown files.
- `model` is set to `gpt-5.2`. Change this value to the model deployment name available in your Foundry project if needed.
- The agent is connected to the `meera-workiq-toolbox` Foundry toolbox through an MCP tool entry.

## Source Reference Files

- `instructions.md` is the original source/reference prompt material.
- `skills/SKILL.md` is the original domain knowledge source used to build the embedded instructions.
- Updating either reference file does not change the deployed agent until the relevant content is copied into `agent.yml`.

## Deploy Shape

The Foundry prompt-agent definition should keep this shape:

```yaml
kind: prompt
name: network-isolation-expert
model: gpt-5.2
instructions: |
	...self-contained system instructions...
```

Avoid `instructions: file:...` and `knowledgeFiles:` for this package unless the target deployment tool explicitly documents support for them.

## WorkIQ Toolbox

The MCP endpoint is configured in `agent.yml`:

```yaml
tools:
	- name: meera-workiq-toolbox
		kind: mcp
		type: mcp
		server_label: meera-workiq-toolbox
		server_url: https://mkurup-test-resource.services.ai.azure.com/api/projects/mkurup-test/toolboxes/meera-workiq-toolbox/versions/1/mcp?api-version=v1
		headers:
			Foundry-Features: Toolboxes=V1Preview
		require_approval: always
```

Do not commit bearer tokens. Your Python Copilot SDK bridge gets a token with `DefaultAzureCredential`; the Foundry agent deployment path should use the platform-supported identity, user auth, or secret mechanism for toolbox MCP authorization.

## Optional Foundry Metadata

Use `.foundry/agent-metadata.example.yaml` as the starting point for local deployment metadata. Copy it to `.foundry/agent-metadata.yaml` and fill in your project endpoint when you are ready to deploy or invoke the agent from tooling.
