# Complete Knowledge Graph on Snowflake - Powered by Snowflake Intelligence

A production-ready knowledge graph system on Snowflake that combines graph data modeling, natural language querying via Cortex Analyst, advanced graph algorithms via custom NetworkX tools, and intelligent orchestration using Cortex Agent.

## 🎯 Overview

This repository contains a complete implementation of a knowledge graph solution for soccer analytics, demonstrating how to:

- **Model graph data** using node-edge architecture on Snowflake
- **Enable natural language queries** via Cortex Analyst with semantic models
- **Deploy custom graph algorithms** (NetworkX) via Snowpark Container Services (SPCS)
- **Orchestrate intelligently** using Cortex Agent to route queries to optimal tools

The architecture handles both simple SQL queries (80% of use cases) and complex graph algorithms (20% requiring advanced analytics), all accessible through natural language.

## 📁 Repository Structure

```
repo_knowledge_graph/
├── 1_setup_sql/           # Phase 1: Database setup and knowledge graph schema
├── 2_cortex_analyst/      # Phase 2: Semantic model for natural language queries
├── 3_mcp_server/          # Phase 3: Custom NetworkX tools deployed to SPCS
├── 4_cortex_agent/        # Phase 4: Agent orchestration and instructions
└── README.md              # This file
```

## 🚀 Quick Start

### Prerequisites

- Snowflake account with access to:
  - Cortex Analyst (for semantic models)
  - Cortex Agent (for orchestration)
  - Snowpark Container Services (SPCS) (for custom tools)
- Python 3.10+ (for MCP server development)
- Docker (for containerizing MCP server)

### Configuration Required

Before deploying, you'll need to replace the placeholder (`XXXXXX`) with your actual Snowflake account identifier:

**Files that need configuration:**
- `3_mcp_server/deploy_spcs.sh` - Replace `XXXXXX` with your Snowflake account identifier (e.g., `abc12345.us-east-1`)
- `3_mcp_server/service.yaml` - Replace `XXXXXX` with your Snowflake account identifier (e.g., `abc12345.us-east-1`)

**Sensitive files (not included in repo):**
- None required - The MCP server uses only static JSON files from `graph_data/` directory

**Note:** The `graph_data/` folder is included in the repo and will be copied into the Docker image during build. No Snowflake connection or authentication is needed for the MCP server to function.

### Step-by-Step Implementation

Follow these phases in order:

#### Phase 1: Setup SQL Database

**📄 Location:** `1_setup_sql/soccer_knowledge_graph.sql`

Execute the SQL script to create:
- Database `KNOWLEDGE_GRAPH_DB` and schema `SOCCER_KG`
- Core tables: `KG_NODE` (entities) and `KG_EDGE` (relationships)
- Convenience views: `V_PLAYER`, `V_CLUB`, `V_MATCH`, `V_PLAYS_FOR`, etc.
- Sample soccer data (players, clubs, matches, contracts)

#### Phase 2: Create Semantic Model

**📄 Location:** `2_cortex_analyst/soccer_semantic_model.yml`

Upload the semantic model to Snowflake to enable Cortex Analyst natural language queries:
- Defines entity views (players, clubs, matches, coaches)
- Defines relationship views (plays_for, coaches, played_in)
- Specifies verified queries for common patterns
- Enables text-to-SQL translation for graph queries

#### Phase 3: Deploy MCP Server to SPCS

**📄 Location:** `3_mcp_server/`

Deploy custom NetworkX graph algorithms as an SPCS service. Follow the complete guide:

**📖 Deployment Guide:** `3_mcp_server/SPCS_DEPLOYMENT_GUIDE.md`

**Quick Summary:**
1. Build and push Docker image (`./deploy_spcs.sh`)
2. Create Snowflake infrastructure (`deploy_to_spcs.sql`)
3. Create Service Functions (`spcs_service_functions.sql`)

**Provides 5 Graph Analytics Tools:**
- `shortest_path_tool` - Find shortest paths between entities
- `centrality_tool` - Identify most influential nodes
- `community_detection_tool` - Detect natural groupings
- `transfer_analysis_tool` - Analyze transfer patterns
- `temporal_analysis_tool` - Examine network evolution

**Key Feature:** Uses static JSON files from `graph_data/` (no database connection required)

#### Phase 4: Configure Cortex Agent

**📄 Location:** `4_cortex_agent/`

Create a Cortex Agent that orchestrates between Cortex Analyst and custom tools.

**Configuration Files:**
- `agent_description.md` - Agent capabilities and limitations
- `tool_descriptions.md` - When to use each tool
- `orchestration_instructions.md` - Decision logic for tool selection
- `response_instructions.md` - Response formatting standards

**Agent Capabilities:**
- Routes simple queries to Cortex Analyst (SQL)
- Routes complex graph queries to NetworkX tools
- Executes multiple tools in parallel when independent
- Synthesizes results into unified responses

**📄 Integration:** See `3_mcp_server/spcs_service_functions.sql` (Step 5) for adding custom tools to Cortex Agent

## 🔍 Example Queries

### Simple SQL Query (via Cortex Analyst)
```
"What is Lionel Messi's complete career trajectory?"
```
**Result:** Returns all clubs, dates, and contract details for Messi's career.

### Complex Graph Query (via NetworkX)
```
"What's the shortest connection between Messi and Haaland?"
```
**Result:** Finds shortest path through shared teammates/clubs.

### Hybrid Query (Both Tools)
```
"Find all clubs within 3 degrees of Real Madrid AND show which ones 
belong to the same transfer market community?"
```
**Result:** Uses Cortex Analyst for recursive CTE (3-degree network) and 
community detection tool for transfer market groupings.

## 📊 Architecture Benefits

- **SQL Speed:** 1-3 seconds for direct relationships via Cortex Analyst
- **Graph Power:** 5-10 seconds for complex algorithms via NetworkX
- **Intelligent Routing:** Agent automatically selects optimal tools
- **Parallel Execution:** Independent tools run simultaneously (2-3x faster)
- **Natural Language:** No SQL or algorithm knowledge required
- **Scalable:** Handles billions of edges with horizontal scaling via SPCS

## 🛠️ Key Components

### Node-Edge Architecture
- **KG_NODE:** All entities (players, clubs, matches, coaches)
- **KG_EDGE:** All relationships (contracts, appearances, matches)
- **VARIANT columns:** Flexible properties per entity/relationship type
- **Temporal support:** Start/end dates for time-bounded relationships

### Convenience Views
Domain-specific views that abstract the underlying node-edge structure:
- `V_PLAYER`, `V_COACH`, `V_CLUB`, `V_MATCH`
- `V_PLAYS_FOR`, `V_COACHES`, `V_PLAYED_IN`
- `V_HOME_TEAM`, `V_AWAY_TEAM`

### Semantic Model
YAML-based definition that enables Cortex Analyst to:
- Understand entity relationships
- Generate optimized SQL queries
- Handle verified query patterns
- Support natural language variations

### Custom Tools (MCP Server)
NetworkX-based graph algorithms deployed as SPCS service:
- Preloads graph data for performance
- Exposes HTTP endpoints
- Wrapped by Snowflake UDFs
- Accessible via Cortex Agent

### Cortex Agent
Intelligent orchestration layer that:
- Analyzes query intent
- Selects appropriate tools
- Executes in parallel when possible
- Synthesizes multi-tool results
- Formats professional responses

## 📚 Documentation

| Component | File | Description |
|-----------|------|-------------|
| Database Setup | `1_setup_sql/soccer_knowledge_graph.sql` | Complete schema and data |
| Semantic Model | `2_cortex_analyst/soccer_semantic_model.yml` | Natural language query model |
| MCP Deployment | `3_mcp_server/SPCS_DEPLOYMENT_GUIDE.md` | Step-by-step SPCS guide |
| Service Functions | `3_mcp_server/spcs_service_functions.sql` | SQL functions and Cortex Agent setup |
| Agent Configuration | `4_cortex_agent/*.md` | Agent orchestration instructions |

## 🎓 Use Cases Beyond Soccer

This architecture applies to any domain where relationships matter:

- **Supply Chain Networks:** Track suppliers, shipments, and contracts
- **Fraud Detection:** Connect accounts, devices, and transactions
- **Customer 360:** Map customer journeys and interactions
- **Cybersecurity:** Trace attack paths and analyze network traffic
- **Recommendation Systems:** Find connections between users and products

## 🔧 Customization

**To extend this implementation:**

- **New Entity Types:** Add to `KG_NODE` (Phase 1), create views, update semantic model (Phase 2)
- **New Relationships:** Add to `KG_EDGE` (Phase 1), create views, update semantic model (Phase 2)
- **New Graph Algorithms:** Implement in `3_mcp_server/soccer_mcp_server.py`, register as MCP tool, update agent configuration (Phase 4)

See individual files for detailed implementation patterns.

## 📝 License

This repository contains example code and configurations for Snowflake knowledge graph implementation. Adapt as needed for your use case.

## 🤝 Contributing

This is a reference implementation. Feel free to:
- Customize for your domain
- Extend with additional tools
- Optimize for your data volume
- Share improvements

## 📧 Support

For questions about:
- **Snowflake features:** Refer to [Snowflake Documentation](https://docs.snowflake.com/)
- **Cortex Analyst:** See [Cortex Analyst Guide](https://docs.snowflake.com/en/user-guide/cortex-analyst)
- **Cortex Agent:** See [Cortex Agent Guide](https://docs.snowflake.com/en/user-guide/cortex-agent)
- **SPCS:** See [SPCS Documentation](https://docs.snowflake.com/en/developer-guide/snowpark-container-services/index)

---

**Built with:** Snowflake Intelligence, Cortex Analyst, Cortex Agent, Snowpark Container Services, NetworkX

