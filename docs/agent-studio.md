# Agent Studio

!!! NOTE

    This is an enterprise-level feature of Security Onion. Contact Security Onion Solutions, LLC via our website at <https://securityonion.com/pro> for more information about purchasing a [Security Onion Pro](security-onion-pro.md) license to enable this feature.

Agent Studio is an administrative workspace for managing [Onion AI](onion-ai.md) autonomous agents, skills, long-term memories, and background automations.

This screen is located under the **Administration** menu on the left side of the Security Onion Console and requires the `superuser` role. It becomes visible when Onion AI and either agentic features or memory are enabled.

## Configuration Options Panel

Clicking the **Screwdriver and Wrench Icon** in the top-right toolbar opens the global **Config Settings** panel. This panel controls delegation limits, execution guardrails, and the long-term memory system across the entire grid.

### Delegation Guardrails

- **Max Delegation Depth**: Specifies how many nested levels deep an agent is allowed to delegate tasks to subordinate agents. For example, a depth of `2` allows the Orchestrator to delegate to the Investigator, which can then delegate to the DetectionEngineer, but prevents further sub-delegation. Setting this value to `0` removes the limit.
- **Max Sub-Session Tokens**: Sets the maximum output-token budget allocated to each child delegation session. If a delegated sub-agent consumes this token limit before completing its task, the sub-session terminates and reports its intermediate findings back to the parent agent. Setting this value to `0` removes the limit.

### Automations

- **Check Interval**: How often (in seconds) the automation scheduler checks which automations are due to run.
- **Alert Triage Start**: The earliest alert time (in UTC) that the Alert Triage automation will consider. Older alerts are never triaged.

### Memory Configuration

- **Memory System**: Master switch to enable or disable memory recall during conversations. When enabled, relevant stored facts are automatically appended to prompt contexts based on proximity matching.
- **Memory Scanner**: Controls the automated background scanning process. When enabled, the scanner analyzes chat conversations to extract new declarative facts. Turning it on asks whether to also scan historic conversations.
- **Scan Interval**: The frequency (in seconds) at which the background memory scanner runs to inspect recent conversation turns for new facts.
- **Memory Proximity**: A similarity threshold (between `0` and `1`, default `0.8`) used during reconciliation to determine whether a newly discovered fact matches an existing stored memory. Higher values require closer similarity before memories are considered duplicates or updates.
- **Message Proximity**: A similarity threshold (between `0` and `1`, default `0.5`) used when an analyst sends a prompt. If the similarity between the prompt and a stored memory meets or exceeds this threshold, the memory is included in the conversation context.
- **Messages per Extraction Batch**: The number of unscanned messages processed in a single request during memory extraction.
- **Memory Scan Retries**: How many times a failed memory scan of a session is retried before it is skipped.
- **Memories to Include**:
    - **User**: Maximum number of user-specific memories that can be attached to a single prompt turn.
    - **Global**: Maximum number of global (organization-wide) memories that can be attached to a single prompt turn.
- **Memories to Reconcile**:
    - **User**: Maximum number of existing user memories compared against each new memory during reconciliation.
    - **Global**: Maximum number of existing global memories compared against each new memory during reconciliation.
- **Memory Model**: The model responsible for analyzing transcripts and extracting discrete facts.
- **Embed Agent**: The model used to calculate semantic vector embeddings for memories and user prompts.
- **Reconcile Model**: The model responsible for comparing new facts with existing memories and determining whether to add, update, merge, or delete them.
- **Persona Addenda**: Clicking the **Persona Addenda** button opens a dialog to customize the prompt guidance given to the extraction and reconciliation models. Changes are saved with the **Config Settings** panel's **Save** button:
    - **Memory Model**: Custom system guidance directing what types of facts the extraction model should focus on or ignore (such as internal naming conventions or sensitive credentials).
    - **Reconcile Model**: Custom guidance controlling how the reconciliation model resolves conflicts between old and new facts.

## Agents

The **Agents** tab catalogs all available AI agents, including built-in system agents (Orchestrator, Investigator, AlertTriage, Notifier, and DetectionEngineer) and custom agents created by administrators.

### Agents Table Overview

The main table displays:

- **Name**: The display name of the agent.
- **Role**: Indicates whether the agent functions as an **Orchestrator** (coordinates tasks and delegates to specialists) or a **Specialist** (focuses on specific domain tasks).
- **Model**: The model assigned to the agent.
- **Skills**: The skills attached to the agent.
- **Delegates To**: Which other agents this agent can delegate sub-tasks to.
- **Enabled**: Switch to enable or disable the agent. Disabled agents cannot be selected in chat or invoked by automations. At least one orchestrator must remain enabled.

### Adding an Agent

To create a new agent, click the **+ (Plus) Icon** in the toolbar while on the **Agents** tab. The **New Agent** dialog prompts for:

1. **Name**: A unique identifier and display name for the agent (e.g., `Threat Hunter`, `Firewall Specialist`).
2. **Role**: Select either `Orchestrator` or `Specialist`.
3. **Executing Model**: Choose the primary model from your configured providers that powers this agent.
4. **Provider**: Automatically displays the provider associated with the selected model.
5. **Max Concurrent Sessions**: The maximum number of sessions this agent can run at once, chats and automations combined. `0` is unlimited.
6. **Skills**: Multi-select dropdown to attach skills from the skill catalog that this agent is authorized to use.
7. **Delegates To**: Multi-select dropdown specifying which other agents this agent is permitted to delegate sub-tasks to.
8. **Delegators**: Multi-select dropdown indicating which parent agents are authorized to delegate work to this agent.
9. **Description**: A short summary explaining the agent's purpose and specialization.
10. **Persona**: The core prompt instructions that define the agent's behavior, operational rules, analytical methodology, and tone.

Click **Create** to save the new agent.

### Editing and Managing Agents

Expanding any row in the Agents table opens an inline editor card with five subtabs:

- **Identity Subtab**: Modify the agent display name (custom agents only), role, and description. For system agents, these fields are read-only to preserve platform integrity.
- **Model Subtab**: Change the **Executing Model** and **Max Concurrent Sessions**. The **Provider** of the selected model is displayed.
- **Skills Subtab**: Add or remove skills from the catalog to grant or revoke tool access. For system agents, built-in skills are displayed as informative chips.
- **Delegation Subtab**: Adjust the allowed delegation targets to control which agents can receive tasks from this agent.
- **Persona Subtab**: Customize prompt instructions. For custom agents, this defines their persona. For system agents, this field acts as a **Persona Addendum**, allowing administrators to inject organization-specific instructions on top of the built-in system prompt.

#### Agent Actions

The footer of the expanded editor card provides action buttons:

- **Open in Onion AI**: For enabled agents, opens a new conversation directly with that agent in the [Onion AI](onion-ai.md) chat window.
- **Duplicate**: Saves a copy of a custom agent named `<name> (copy)`, allowing administrators to rapidly create specialized variations. System agents cannot be duplicated.
- **Delete**: Deletes a custom agent. System agents cannot be deleted, but they can be turned off with their **Enabled** switch.
- **Save**: Commits changes to the agent definition.

## Skills

The **Skills** tab catalogs the tools and capabilities available to agents. Skills group underlying tool operations (such as event search, case manipulation, detection rule tuning, or alert acknowledgment) with prompt guidance that teaches agents when and how to invoke them.

### Skills Table Overview

- **Skill**: Display name of the skill (e.g., `Hunt`, `Respond`, `Tuning`).
- **Tools**: List of individual low-level tool operations included in the skill.
- **Used By**: Chips showing which agents currently have this skill assigned.
- **System Indicator**: Identifies built-in skills provided by Security Onion.
- **Enabled**: Switch to enable or disable the skill.

### Adding a Skill

To create a custom skill, click the **+ (Plus) Icon** in the toolbar while on the **Skills** tab:

1. **Name**: A descriptive name for the skill (e.g., `Threat Intel Enrichment`).
2. **Tools**: Multi-select list of available system tools to bundle into this skill (such as `query_events`, `query_cases`, `ack_alerts`, `query_detections`, etc.).
3. **Prompt Guidance**: Detailed instructions and examples explaining to the agent when to call these tools, how to construct input parameters, and how to interpret return values.

Click **Create** to publish the skill into the catalog.

### Editing and Managing Skills

Expanding a skill row displays an editor card with three subtabs:

- **Tools Subtab**: Add or remove low-level tools associated with custom skills. System skills display their bundled tools as read-only chips.
- **Used By Subtab**: Displays chips showing every agent that references this skill, helping administrators gauge the impact of any changes.
- **Persona Subtab**: Edit the prompt guidance for custom skills or add custom addendum instructions for system skills.

#### Skill Actions

- **Duplicate**: Saves a copy of a custom skill named `<name> (copy)`. System skills cannot be duplicated.
- **Delete**: Removes a custom skill (built-in system skills cannot be deleted).
- **Save**: Saves updates made to the skill definition.

## Memories

The **Memory** tab manages the long-term knowledge base that Onion AI references during conversations. Memories capture durable operational knowledge, environment details, analyst preferences, and network topology facts.

### Memories Table Overview

- **Memory**: The declarative text of the stored fact.
- **Scope**: Labeled as **Global** (applies across the entire organization) or **User** (visible only to the specific analyst who authored or established the fact).
- **Owner**: The user who owns the memory, or `All` for organization-wide knowledge.
- **Recalled**: How many times the memory has been included in a conversation.
- **Updated**: When the memory was last changed.
- **User Defined Badge**: Distinguishes facts manually entered by administrators from facts discovered automatically by the background memory scanner.
- **Similarity Percentage**: When searching, displays how closely the memory matches the search query.

### Adding a Memory

To manually add a new fact to long-term memory:

1. Click the **+ (Plus) Icon** in the toolbar while on the **Memory** tab.
2. **Memory Text**: Enter the declarative fact. Follow best practices for phrasing:
    - Keep each memory to a single, self-contained statement.
    - Use declarative phrasing rather than conversational instructions (e.g., `DNS servers for the DMZ are 10.0.1.53 and 10.0.1.54`).
    - Use absolute, durable statements rather than relative time references (e.g., `Log retention was set to 90 days in October 2026`).
3. **Scope**: Choose either `User` (private to your account) or `Global` (accessible by all analysts and automated agents).
4. Click **Create** to store and embed the memory.

### Managing and Filtering Memories

- **Scope Dropdown**: Filter the list by `All`, `Global`, or `User`.
- **Search Bar**: Enter text to find memories by meaning rather than exact wording. Press Enter or click the magnifying glass to search.
- **Row Expansion**: Expanding a memory row reveals:
    - The full editable memory text.
    - **Scope**: Toggle between `User` and `Global`.
    - **Origin**: Displays the chat session ID where the memory was originally discovered (if extracted by the scanner).
    - **Last Recalled**: Timestamp showing when the memory was last injected into a conversation turn.
- **Actions**:
    - **Delete**: Permanently removes the memory.
    - **Save**: Updates the text or scope of the memory. Saving a memory found by the scanner makes it user-defined, so the scanner no longer changes it.
- **Stale Memory Alert**: If models or embedding configurations change, an alert banner indicates how many memories are awaiting re-embedding.

## Automations

The **Automations** tab configures recurring, autonomous agent workflows that execute in the background without requiring user interaction. A primary example is the built-in **Alert Triage** automation.

### Automations Table Overview

- **Name**: Display name of the automation.
- **Kind**: The automation template or category (e.g., `Alert Triage`).
- **Interval**: How frequently the automation runs (e.g., `60 seconds`, `5 minutes`).
- **Handling Agent**: The assigned agent responsible for executing the workflow.
- **Status**: The state of the automation's latest run: **Idle**, **Queued**, **Running**, or **Failed**.
- **Enabled**: Switch to enable or disable the automation.

!!! NOTE

    Creating, duplicating, and deleting custom automations is not yet available. The built-in **Alert Triage** automation can be enabled, configured, and monitored.

### Editing and Reviewing Automations

Expanding an automation row opens an editor card with three subtabs:

- **General Subtab**:
    - Change the assigned agent.
    - Adjust the run interval in seconds.
- **Settings Subtab**:
    - View automation-specific parameters. These are read-only for built-in automations.
- **Activity Subtab**:
    - **Last Run** and **Next Run**: When the automation last ran and is next due.
    - **Backlog**: Counts of work items still **Pending**, **Running**, or **Applying** for this automation.
    - **Run History Table**: Displays past runs, including **Start Time**, work items **Done** and **Failed**, and **Outcome**. Each run has a button to view it in [Agent Monitor](agent-monitor.md).
    - **Load More**: Button to fetch older historical runs.

#### Automation Actions

- **Save**: Saves changes to the automation configuration.

To monitor live automated workloads as they execute, navigate to [Agent Monitor](agent-monitor.md).
