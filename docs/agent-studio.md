# Agent Studio

!!! NOTE

    This is an enterprise-level feature of Security Onion. Contact Security Onion Solutions, LLC via our website at <https://securityonion.com/pro> for more information about purchasing a [Security Onion Pro](security-onion-pro.md) license to enable this feature.

Agent Studio is an administrative workspace for managing [Onion AI](onion-ai.md) autonomous agents, skills, long-term memories, and background automations.

This screen is located under the **Administration** menu on the left side of the Security Onion Console and requires the `superuser` role. It becomes visible when Onion AI and either agentic features or memory are enabled.

## Configuration Options Panel

Clicking the **Screwdriver and Wrench Icon** in the top-right toolbar opens the global **Configuration Settings** panel. This panel controls delegation limits, execution guardrails, and the long-term memory system across the entire grid.

### Delegation Guardrails

- **Max Delegation Depth**: Specifies how many nested levels deep an agent is allowed to delegate tasks to subordinate agents. For example, a depth of `2` allows an Orchestrator to delegate to an Analyst agent, which can then delegate to a Query agent, but prevents further sub-delegation. Setting this value to `0` disables delegation entirely.
- **Max Sub-Session Tokens**: Sets the maximum token budget allocated to each child delegation session. If a delegated sub-agent consumes this token limit before completing its task, the sub-session terminates and reports its intermediate findings back to the parent agent.

### Memory Configuration

- **Use Memory**: Master switch to enable or disable memory recall during conversations. When enabled, relevant stored facts are automatically appended to prompt contexts based on proximity matching.
- **Use Memory Scanner**: Controls the automated background scanning process. When enabled, the scanner analyzes chat conversations to extract new declarative facts.
- **Scan Interval**: The frequency (in seconds) at which the background memory scanner runs to inspect recent conversation turns for new facts.
- **Memory Proximity Threshold**: A similarity threshold (between `0` and `1`, default `0.8`) used during reconciliation to determine whether a newly discovered fact matches an existing stored memory. Higher values require closer similarity before memories are considered duplicates or updates.
- **Message Proximity Threshold**: A similarity threshold (between `0` and `1`, default `0.5`) used when an analyst sends a prompt. If the similarity between the prompt and a stored memory meets or exceeds this threshold, the memory is included in the conversation context.
- **Memory Extract Batch Size**: The number of conversation turns processed in a single batch during memory extraction.
- **Max Memory Retries**: The maximum number of attempts the memory extractor makes on a session before skipping it.
- **Memory Inclusion Limits**:
    - **Max User Memories to Include**: Maximum number of user-specific memories that can be attached to a single prompt turn.
    - **Max Global Memories to Include**: Maximum number of global (organization-wide) memories that can be attached to a single prompt turn.
- **Memory Reconciliation Limits**:
    - **Max User Memories to Reconcile**: Maximum number of user memories evaluated together during a single reconciliation run.
    - **Max Global Memories to Reconcile**: Maximum number of global memories evaluated together during a single reconciliation run.
- **Model Assignments**:
    - **Memory Model**: The model responsible for analyzing transcripts and extracting discrete facts.
    - **Embed Model**: The model used to calculate semantic vector embeddings for memories and user prompts.
    - **Reconcile Model**: The model responsible for comparing new facts with existing memories and determining whether to add, update, merge, or delete them.
- **Memory Personas**: Clicking the **Memory Personas** button opens a dialog to customize the prompt guidance given to the extraction and reconciliation models:
    - **Memory Model Persona**: Custom system guidance directing what types of facts the extraction model should focus on or ignore (such as internal naming conventions or sensitive credentials).
    - **Reconcile Model Persona**: Custom guidance controlling how the reconciliation model resolves conflicts between old and new facts.

## Agents

The **Agents** tab catalogs all available AI agents, including built-in system agents (such as the Orchestrator and Hunter) and custom agents created by administrators.

### Agents Table Overview

The main table displays:

- **Name**: The display name of the agent.
- **Role**: Indicates whether the agent functions as an **Orchestrator** (coordinates tasks and delegates to specialists) or a **Specialist** (focuses on specific domain tasks).
- **Status**: Visual chip indicating whether the agent is **Active** or **Disabled**. Disabled agents cannot be selected in chat or invoked by automations.
- **Models**: Displays the assigned default model and reasoning model.
- **Skills**: Count of tool skills attached to the agent.
- **Delegation**: Summarizes which other agents this agent can delegate sub-tasks to.

### Adding an Agent

To create a new agent, click the **+ (Plus) Icon** in the toolbar while on the **Agents** tab. The **New Agent** dialog prompts for:

1. **Name**: A unique identifier and display name for the agent (e.g., `Threat Hunter`, `Firewall Specialist`).
2. **Role**: Select either `Orchestrator` or `Specialist`.
3. **Executing Model**: Choose the primary model from your configured providers that powers this agent.
4. **Provider**: Automatically displays the provider associated with the selected model.
5. **Max Concurrent Instances**: The maximum number of concurrent executions permitted for this agent across the grid.
6. **Skills**: Multi-select dropdown to attach skills from the skill catalog that this agent is authorized to use.
7. **Delegates To**: Multi-select dropdown specifying which other agents this agent is permitted to delegate sub-tasks to.
8. **Delegators**: Multi-select dropdown indicating which parent agents are authorized to delegate work to this agent.
9. **Description**: A short summary explaining the agent's purpose and specialization.
10. **Persona**: The core prompt instructions that define the agent's behavior, operational rules, analytical methodology, and tone.

Click **Create** to save the new agent.

### Editing and Managing Agents

Expanding any row in the Agents table opens an inline editor card with five subtabs:

- **Identity Subtab**: Modify the agent display name (custom agents only), role, and description. For system agents, these fields are read-only to preserve platform integrity.
- **Model Subtab**: Change the primary **Executing Model** or assign an optional **Reasoning Model** for deep chain-of-thought analysis.
- **Skills Subtab**: Check or uncheck skills from the catalog to grant or revoke tool access. For system agents, built-in skills are displayed as informative chips.
- **Delegation Subtab**: Adjust the allowed delegation targets to control which agents can receive tasks from this agent.
- **Persona Subtab**: Customize prompt instructions. For custom agents, this defines their persona. For system agents, this field acts as a **Persona Addendum**, allowing administrators to inject organization-specific instructions on top of the built-in system prompt.

#### Agent Actions

The footer of the expanded editor card provides action buttons:

- **Open in Onion AI**: For enabled agents, opens a new conversation directly with that agent in the [Onion AI](onion-ai.md) chat window.
- **Duplicate**: Clones the agent definition into a new draft, allowing administrators to rapidly create specialized variations.
- **Delete**: Deletes a custom agent. System agents cannot be deleted, but they can be toggled off by switching their status toggle to **Disabled**.
- **Save**: Commits changes to the agent definition.

## Skills

The **Skills** tab catalogs the tools and capabilities available to agents. Skills group underlying tool operations (such as event search, case manipulation, detection rule tuning, or alert acknowledgment) with prompt guidance that teaches agents when and how to invoke them.

### Skills Table Overview

- **Name**: Display name of the skill (e.g., `Hunt Queries`, `Case Management`, `Alert Actions`).
- **Tools**: List of individual low-level tool operations included in the skill.
- **Used By**: Chips showing which agents currently have this skill assigned.
- **System Indicator**: Identifies built-in skills provided by Security Onion.

### Adding a Skill

To create a custom skill, click the **+ (Plus) Icon** in the toolbar while on the **Skills** tab:

1. **Name**: A descriptive name for the skill (e.g., `Threat Intel Enrichment`).
2. **Tools**: Multi-select list of available system tools to bundle into this skill (such as `query_events`, `query_cases`, `ack_alerts`, `query_detections`, etc.).
3. **Prompt Guidance**: Detailed instructions and examples explaining to the agent when to call these tools, how to construct input parameters, and how to interpret return values.

Click **Create** to publish the skill into the catalog.

### Editing and Managing Skills

Expanding a skill row displays an editor card with three subtabs:

- **Tools Subtab**: Add or remove low-level tools associated with custom skills. System skills display their bundled tools as read-only chips.
- **Usage Subtab**: Displays chips showing every agent that references this skill, helping administrators gauge the impact of any changes.
- **Persona Subtab**: Edit the prompt guidance for custom skills or add custom addendum instructions for system skills.

#### Skill Actions

- **Duplicate**: Clones an existing skill to create a tailored copy.
- **Delete**: Removes a custom skill (built-in system skills cannot be deleted).
- **Save**: Saves updates made to the skill definition.

## Memories

The **Memories** tab manages the long-term knowledge base that Onion AI references during conversations. Memories capture durable operational knowledge, environment details, analyst preferences, and network topology facts.

### Memories Table Overview

- **Memory**: The declarative text of the stored fact.
- **Scope**: Labeled as **Global** (applies across the entire organization) or **User** (visible only to the specific analyst who authored or established the fact).
- **Owner**: Email address of the user who owns the memory, or `Global` for organization-wide knowledge.
- **User Defined Badge**: Distinguishes facts manually entered by administrators from facts discovered automatically by the background memory scanner.
- **Similarity Percentage**: When searching, displays how closely the memory matches the search query.

### Adding a Memory

To manually add a new fact to long-term memory:

1. Click the **+ (Plus) Icon** in the toolbar while on the **Memories** tab.
2. **Memory Text**: Enter the declarative fact. Follow best practices for phrasing:
    - Keep each memory to a single, self-contained statement.
    - Use declarative phrasing rather than conversational instructions (e.g., `DNS servers for the DMZ are 10.0.1.53 and 10.0.1.54`).
    - Use absolute, durable statements rather than relative time references (e.g., `Log retention was set to 90 days in October 2026`).
3. **Scope**: Choose either `User` (private to your account) or `Global` (accessible by all analysts and automated agents).
4. Click **Create** to store and embed the memory.

### Managing and Filtering Memories

- **Scope Dropdown**: Filter the list by `All`, `Global`, or `User`.
- **Search Bar**: Enter keywords to locate specific memories. Press Enter or click the magnifying glass to search.
- **Row Expansion**: Expanding a memory row reveals:
    - The full editable memory text.
    - **Scope**: Toggle between `User` and `Global`.
    - **Origin**: Displays the chat session ID where the memory was originally discovered (if extracted by the scanner).
    - **Last Recalled**: Timestamp showing when the memory was last injected into a conversation turn.
- **Actions**:
    - **Delete**: Permanently removes the memory.
    - **Save**: Updates the text or scope of the memory.
- **Stale Memory Alert**: If models or embedding configurations change, an alert banner indicates how many memories are awaiting re-embedding.

## Automations

The **Automations** tab configures recurring, autonomous agent workflows that execute in the background without requiring user interaction. A primary example is the built-in **Alert Triage** automation.

### Automations Table Overview

- **Name**: Display name of the automation.
- **Kind**: The automation template or category (e.g., `Alert Triage`).
- **Interval**: How frequently the automation runs (e.g., `60 seconds`, `5 minutes`).
- **Agent**: The assigned agent responsible for executing the workflow.
- **Status**: Switch showing whether the automation is **Enabled** or **Disabled**.

### Adding an Automation

To create a new automation:

1. Click the **+ (Plus) Icon** in the toolbar while on the **Automations** tab.
2. **Name**: Enter a descriptive display name (e.g., `High Severity Alert Triage`).
3. **Kind**: Select the automation kind (e.g., `Alert Triage`).
4. **Agent**: Select the agent from the dropdown that will perform the automated analysis.
5. **Interval**: Specify the execution frequency in seconds (e.g., `60`).
6. **Kind-Specific Parameters**: Configure parameters defined by the automation kind (such as alert filters, grouping fields, or batch limits).
7. **Enabled Switch**: Set whether the automation begins executing immediately upon creation.
8. Click **Create** to schedule the automation.

### Editing and Reviewing Automations

Expanding an automation row opens an editor card with three subtabs:

- **General Subtab**:
    - Edit the display name.
    - Change the assigned agent.
    - Adjust the run interval in seconds.
    - View creator account details for user-defined automations.
- **Settings Subtab**:
    - View and modify automation-specific parameters.
- **Activity Subtab**:
    - **Last Run**: Timestamp when the automation last executed.
    - **Run History Table**: Displays past execution runs, including **Run ID**, **Start Time**, **Duration**, **Items Processed**, and completion **State** (`succeeded`, `failed`, or `running`).
    - **Load More**: Button to fetch older historical runs.

#### Automation Actions

- **Duplicate**: Creates a copy of the automation to apply different parameters or assign a different agent.
- **Delete**: Removes custom automations (system-provided automations cannot be deleted, but can be disabled).
- **Save**: Saves changes to the automation configuration.

To monitor live automated workloads as they execute, navigate to [Agent Monitor](agent-monitor.md).
