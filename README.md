# Multi-Agent Ticket Triage Solution

## Introduction
The Multi-Agent Ticket Triage Solution is a collaborative system designed to process incoming support tickets by determining their priority, assigning them to the correct department, and estimating the effort required to resolve them. This solution leverages a team of specialized agents, each with distinct responsibilities, orchestrated by a primary Triage Agent.

## What I Built
I designed a multi-agent system with the following components:

- **Priority Agent**: Assesses the urgency of the ticket, classifying it as High (blocking), Medium (time-sensitive), or Low (cosmetic).
- **Team Agent**: Acts as a dispatcher, analyzing technical details to route the ticket to the appropriate team (Frontend, Backend, Infrastructure, or Marketing).
- **Effort Agent**: Estimates the effort required to resolve the ticket, categorizing it as Small, Medium, or Large.
- **Triage Agent (Primary)**: Coordinates the other agents, invoking them as needed to compile a complete assessment.

## How I Implemented It
The solution is implemented using the Azure AI Agent Service, following a "tool-use" orchestration pattern. The primary Triage Agent communicates with the sub-agents using the `ConnectedAgentTool` class, which wraps the specialized agents and exposes them as executable tools.

### Key Implementation Details
- **ConnectedAgentTool**: Enables the Triage Agent to invoke sub-agents without complex routing logic.
- **Orchestration**: The Triage Agent is equipped with the sub-agents as tools, allowing it to dynamically determine which agent to invoke based on the ticket's requirements.

```python
# Wrapping sub-agents as tools
priority_agent_tool = ConnectedAgentTool(id=priority_agent.id, ...)
team_agent_tool = ConnectedAgentTool(id=team_agent.id, ...)

# Equipping the Triage Agent with the agent squad
triage_agent = agents_client.create_agent(
    model=model_deployment,
    tools=[priority_agent_tool.definitions[0], team_agent_tool.definitions[0], ...]
)
```

## Project Structure
The project is organized as follows:

```
ai-agents/
└── Labfiles/
    └── 03b-build-multi-agent-solution/
        └── Python/
            ├── .env                 # Azure Foundry endpoint & credentials
            ├── agent_triage.py      # Main orchestration script defining the agent squad
            └── requirements.txt     # Dependencies (azure-ai-agents, etc.)
```

## How to Run the Code
Follow these steps to run the solution:

1. **Clone the Repository**: Navigate to the specific directory for this solution:

    ```bash
    cd ai-agents/Labfiles/03b-build-multi-agent-solution/Python
    ```

2. **Install Dependencies**: Ensure all required packages are installed:

    ```bash
    pip install -r requirements.txt
    ```

3. **Run the Triage System**: Execute the main script:

    ```bash
    python agent_triage.py
    ```

4. **Test the System**: Provide a realistic scenario as input, such as:

    ```
    "Users can't reset their password from the mobile app."
    ```

## Example Test Case
When tested with the input:

```
"Users can't reset their password from the mobile app."
```

The system will:
1. Consult the Priority Agent to classify the issue as High, Medium, or Low.
2. Use the Team Agent to assign the ticket to the appropriate team (e.g., Frontend).
3. Invoke the Effort Agent to estimate the required effort (e.g., Medium).

The final output will be a structured report with the severity rating, team assignment, and workload estimate.