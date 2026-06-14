# n8n GraphQL Schema

## Overview

n8n does not currently expose a public GraphQL API. The primary programmatic interface is a REST API documented at [https://docs.n8n.io/api/api-reference/](https://docs.n8n.io/api/api-reference/). However, n8n's internal data model — covering workflows, nodes, executions, credentials, users, projects, webhooks, and more — maps cleanly to a GraphQL type system, and this schema represents a conceptual GraphQL surface over that model.

The schema in `n8n-schema.graphql` is derived from:

- The n8n public REST API (OpenAPI spec)
- The n8n source code (`packages/cli/src/`, `packages/workflow/src/`)
- Community documentation and database entity definitions

## Schema Source

- **Type**: Conceptual / Community-Derived
- **Based on REST API**: https://docs.n8n.io/api/api-reference/
- **Source Repository**: https://github.com/n8n-io/n8n
- **Relevant Source Paths**:
  - `packages/cli/src/databases/entities/`
  - `packages/workflow/src/interfaces.ts`
  - `packages/cli/src/api/`

## Types

| Type | Description |
|---|---|
| Workflow | A saved automation workflow definition |
| WorkflowNode | A single node within a workflow |
| Connection | An edge connecting two nodes |
| NodeType | A registered node type descriptor |
| NodeTypeData | The full metadata payload for a node type |
| Execution | A single run of a workflow |
| ExecutionData | The input/output data for an execution |
| ExecutionStatus | Enum: waiting, running, success, error, canceled, crashed |
| ExecutionMode | Enum: manual, trigger, webhook, retry, integrated, cli |
| WorkflowRun | Summary of a workflow run result |
| Credential | A stored credential entry |
| CredentialType | The schema descriptor for a credential type |
| CredentialTypeDescription | Property descriptors for credential fields |
| Webhook | A webhook endpoint registration |
| WorkflowWebhook | Webhook bound to a specific workflow |
| Tag | A label for organizing workflows |
| WorkflowTag | Association between a workflow and a tag |
| SharedWorkflow | A workflow shared with a user or project |
| SharedCredential | A credential shared with a user or project |
| User | An n8n user account |
| Role | A role assignment for a user |
| MFAConfig | Multi-factor authentication configuration |
| Project | A logical namespace grouping workflows and credentials |
| ProjectRelation | A user's membership and role in a project |
| Team | An alias for project-based collaboration |
| Setting | An instance-level configuration key/value |
| InstalledPackage | A community node package installed on the instance |
| License | License data for the n8n instance |
| Annotation | A note or comment attached to a workflow execution |
| Log | A structured log entry from workflow execution |
| UsageMetric | A usage measurement for billing/quota tracking |
| APIKey | An API key for authenticating against the n8n API |
| ExternalSecret | A secret retrieved from an external secrets provider |
| ExternalSecretsProvider | A configured external secrets backend (e.g., Vault) |
| Queue | The execution queue for concurrent workflow runs |
| QueueItem | A single item waiting in the execution queue |
| Worker | A worker process handling executions |
| WorkerStatus | Status information for a worker process |
| Variable | An instance-level variable (key/value) |
| WorkflowStatistics | Aggregate counts for a workflow |
| ExecutionSummary | A lightweight execution record for list views |
| NodeError | An error emitted by a node during execution |
| WorkflowError | Top-level workflow execution error |
| PushMessage | A real-time push event sent to connected clients |
| BinaryData | Binary (file) data attached to an execution |
| ResourceLocator | A resource identifier for a dynamic node option |
| NodeParameter | A parameter definition within a node |
| NodeCredential | A credential reference within a node configuration |
| ImportWorkflowResult | Result of importing a workflow from JSON |
| ExportWorkflowResult | Result of exporting a workflow to JSON |
| WorkflowActivation | Record of when a workflow was activated/deactivated |
| InsightsSummary | Aggregated execution health metrics |
| InsightsDateRange | Date range filter for insights queries |
| Pagination | Cursor-based pagination metadata |
| SortOrder | Enum for sort direction: ASC, DESC |

## Key Relationships

- A **Workflow** contains many **WorkflowNode** entries and many **Connection** entries.
- A **Workflow** may have many **Execution** records.
- An **Execution** holds **ExecutionData** and may emit **NodeError** entries.
- A **WorkflowNode** references a **NodeType** and optionally references **Credential** entries.
- A **User** belongs to one or more **Project** through a **ProjectRelation**.
- A **Credential** may be shared across projects via **SharedCredential**.
- A **Workflow** may be shared via **SharedWorkflow**.
- A **Webhook** is bound to a **Workflow** via **WorkflowWebhook**.

## Queries (Illustrative)

```graphql
# Fetch all workflows with their tags and node count
query {
  workflows(filter: { active: true }, pagination: { first: 20 }) {
    nodes {
      id
      name
      active
      tags { id name }
      nodeCount
    }
    pageInfo { endCursor hasNextPage }
  }
}

# Fetch executions for a specific workflow
query {
  executions(workflowId: "abc123", status: ERROR, pagination: { first: 10 }) {
    nodes {
      id
      startedAt
      stoppedAt
      status
      mode
    }
    pageInfo { endCursor hasNextPage }
  }
}

# Fetch a user with their project memberships
query {
  user(id: "user-uuid") {
    id
    email
    firstName
    lastName
    role
    projects {
      id
      name
      role
    }
  }
}
```

## Mutations (Illustrative)

```graphql
# Create a new workflow
mutation {
  createWorkflow(input: {
    name: "My New Workflow"
    nodes: []
    connections: {}
    active: false
  }) {
    id
    name
    createdAt
  }
}

# Activate a workflow
mutation {
  activateWorkflow(id: "abc123") {
    id
    active
  }
}

# Create a credential
mutation {
  createCredential(input: {
    name: "My Slack Token"
    type: "slackApi"
    data: { accessToken: "xoxb-..." }
  }) {
    id
    name
    type
  }
}
```
