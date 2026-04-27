# Nynch MCP Server

A hosted [Model Context Protocol](https://modelcontextprotocol.io) server that gives AI agents full access to **Nynch** — a relationship-led growth platform for consultants and professional services firms.

**42 tools** across 4 categories: CRM, pipeline management, relationship intelligence, and multi-agent orchestration.

## Endpoint

```
https://crcrwtjzqzinfzokianr.supabase.co/functions/v1/mcp-server
```

**Transport:** JSON-RPC 2.0 over Streamable HTTP + GET `/sse` for real-time subscriptions.

## Quick Start

### 1. Get an API Key

Sign up at [nynch.com](https://nynch.com), then go to **Settings → MCP Server** and create an API key.

**Scopes:**
- `read` — Search, browse, and retrieve data
- `read-write` — Read + create, update, delete records
- `admin` — Full access including agent orchestration

### 2. Configure Your Client

#### Claude Desktop

Add to `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "nynch": {
      "url": "https://crcrwtjzqzinfzokianr.supabase.co/functions/v1/mcp-server",
      "headers": {
        "Authorization": "Bearer YOUR_API_KEY"
      }
    }
  }
}
```

#### Cursor

Add to `.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "nynch": {
      "url": "https://crcrwtjzqzinfzokianr.supabase.co/functions/v1/mcp-server",
      "headers": {
        "Authorization": "Bearer YOUR_API_KEY"
      }
    }
  }
}
```

#### Python (MCP SDK)

```python
from mcp import ClientSession
from mcp.client.streamable_http import streamablehttp_client

async with streamablehttp_client(
    "https://crcrwtjzqzinfzokianr.supabase.co/functions/v1/mcp-server",
    headers={"Authorization": "Bearer YOUR_API_KEY"}
) as (read, write, _):
    async with ClientSession(read, write) as session:
        await session.initialize()
        result = await session.call_tool("search_contacts", {"query": "Acme Corp"})
        print(result)
```

### 3. Start Using Tools

Your AI client discovers available tools automatically. Ask it to search contacts, check your pipeline, create tasks, or analyze relationships.

## Tool Catalog

### CRM Read (10 tools)

| Tool | Description |
|------|-------------|
| `search_contacts` | Search contacts by name, company, or tags |
| `get_contact_context` | Full contact context: profile, interactions, activities, notes, network |
| `search_deals` | Search deals by title, stage, or company |
| `get_deal_context` | Full deal context: stakeholders, learnings, coaching actions |
| `search_deals_by_inactivity` | Find stalled deals with no recent activity |
| `get_pipeline_summary` | Pipeline health: stage distribution, at-risk deals, upcoming closes |
| `get_next_best_actions` | AI-powered recommendations for what to do next |
| `list_buckets` | List all buckets (lists/segments) |
| `get_bucket_contacts` | Get contacts in a specific bucket |
| `get_bucket_game_queue` | Get contacts waiting to be sorted into buckets |

### CRM Write (12 tools)

| Tool | Description |
|------|-------------|
| `log_interaction` | Record a contact interaction (call, email, meeting, note) |
| `update_deal_stage` | Move a deal to a new pipeline stage |
| `create_task` | Create a task linked to a contact or deal |
| `create_note` | Add a note to a contact (supports markdown) |
| `create_contact` | Create a new contact (auto-links to company by domain) |
| `update_contact` | Update fields on an existing contact |
| `add_contact_to_bucket` | Add a contact to a bucket |
| `create_deal` | Create a new deal in the pipeline |
| `create_company` | Create a new company |
| `delete_contact` | Soft-delete a contact (recoverable) |
| `bulk_delete_contacts` | Soft-delete contacts by tag or ID list |
| `update_commitment` | Mark a commitment as completed or update details |

### Relationship Intelligence (11 tools)

| Tool | Description |
|------|-------------|
| `get_authority_score` | Authority score across 6 dimensions |
| `get_commitments` | Commitments with Say/Do tracking and due dates |
| `get_buying_committee` | Stakeholder map: roles, influence, sentiment |
| `get_contact_signals` | Job changes, company changes, buying intent signals |
| `get_interaction_history` | Full interaction timeline with direction and sentiment |
| `get_relationship_score` | 5-factor relationship strength score |
| `export_context_briefing` | AI-ready context briefing for any contact or deal |
| `get_relationship_trends` | Is engagement growing, stable, or declining? |
| `get_at_risk_relationships` | Contacts with dropping scores or no recent contact |
| `get_deal_intelligence` | Deal health: velocity, stakeholder coverage, risk factors |
| `get_weekly_briefing` | Weekly summary: interactions, changes, recommendations |

### Agent Orchestration (8 tools)

| Tool | Description |
|------|-------------|
| `register_agent` | Register an external agent with capabilities and archetype |
| `list_agents` | List registered agents with status |
| `agent_heartbeat` | Confirm agent liveness |
| `write_context` | Write to the shared agent context store |
| `read_context` | Read from context store (supports prefix wildcards) |
| `create_handoff` | Request another agent to perform a task |
| `get_handoffs` | Get pending handoffs for your agent |
| `complete_handoff` | Mark a handoff as completed or failed |

## MCP Resources

| URI Template | Description |
|---|---|
| `nynch://contacts/{id}` | Full contact context: profile, score, interactions, network |
| `nynch://deals/{id}` | Deal context: details, stakeholders, coaching actions |
| `nynch://pipeline/summary` | Pipeline health overview |

## Agent Orchestration

Nynch supports multi-agent workflows where AI agents register, share context, and hand off tasks:

- **Register** with a name, capabilities, and archetype (producer, consumer, coordinator, critic)
- **Share state** via a key-value context store with namespaces and TTLs
- **Route tasks** by agent name or capability matching
- **Track liveness** with heartbeats

## Rate Limits

- 1,000 requests per hour per API key

## Documentation

Full developer documentation: [nynch.com/developers](https://nynch.com/developers)

## About Nynch

[Nynch](https://nynch.com) is a relationship-led growth platform that helps consultants and professional services firms turn relationships into revenue. It combines CRM, relationship intelligence, and AI-powered coaching into a single platform.

## License

This repository contains documentation and configuration for the Nynch MCP Server. The server itself is a hosted service — no local installation required.
