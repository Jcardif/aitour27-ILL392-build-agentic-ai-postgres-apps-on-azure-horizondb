# Get started

> [!NOTE]
> As you follow the instructions in this pane, whenever you see a `icon`, you can use it to copy text from the instruction pane into the virtual machine interface. This is particularly useful to copy code; but bear in mind you may need to modify the pasted code to fix indent levels or formatting before running it!

## Sign in to Windows

1. Sign in to your VM with the following credentials:

    - **Username: ++@lab.VirtualMachine(Win11-Pro-Base-VM).Username++**

    - **Password: +++@lab.VirtualMachine(Win11-Pro-Base-VM).Password+++**

## Table of contents

1. [Part 0 - Sign in to Azure and setup Azure resources](/instructions/part-0-setup-azure-resources.md)
2. [Part 1 - Connect to your Azure HorizonDB database using VS Code Extension for PostgreSQL](/instructions/part-1-connect-horizondb-to-vscode.md)
    1. [Open VS Code and set up database connection to Azure PostgreSQL](/instructions/part-1-connect-horizondb-to-vscode.md#open-vs-code-and-set-up-database-connection-to-azure-postgresql)
    2. [Create Connection](/instructions/part-1-connect-horizondb-to-vscode.md#create-connection-to-horizondb)
    3. [Explore VS Code Extension for PostgreSQL Dashboard](/instructions/part-1-connect-horizondb-to-vscode.md#explore-vs-code-extension-for-postgresql-dashboard)
3. [Part 2 - Prepare the data for your AI assistant](part-2-data-setup.md)
4. [Part 3 - Build and try the AI assistant](part-3-agentic-app-development.md)
5. [Optional - Check the database server configuration](optional-diagnostics.md)

## Overview

Cassandra Dunn, Caldova's compliance manager, needs to know whether an issue has precedent, what evidence supports the conclusion, what the risk is, and whether the business can proceed. This lab builds the evidence-gathering core of a Compliance Advisor Agent: find similar issues, connect related cases, and extract facts for review.

The prepared notebooks use Washington State court cases and a Seattle property-flooding dispute as the worked example. The domain is different, but the decision pattern is the same: retrieve precedent, gather supporting evidence, and keep a human accountable for the recommendation. Caldova-specific policy analysis, risk assessment, and approval workflows remain outside this lab.

In the wider architecture, Azure Cosmos DB holds conversations and user context, while Azure SQL Database Hyperscale holds regulatory reviews and compliance workflows. This lab builds the HorizonDB reasoning core, not those integrations. Its PostgreSQL database stores the cases, vectors, indexes, citation graph, and Mem0 memories used in the exercises.

Microsoft Agent Framework connects the Azure OpenAI model to five tools. The agent selects tools, supplies their inputs, and uses the returned evidence to draft an answer. Azure OpenAI provides the chat and embedding models, and Open-Meteo supplies external weather evidence.

## What you will build

| Capability | What it does for the assistant |
|---|---|
| BM25 full-text search with `pg_textsearch` | Retrieves opinions matching relevant legal terms. |
| Vector search with pgvector and DiskANN | Finds semantically similar cases, with court and year filters. |
| Graph queries with Apache AGE | Follows citations to related court decisions. |
| AI functions with `azure_ai.extract` | Calls Azure OpenAI from SQL to extract rulings, issues, cited laws, and case outcomes. |
| External evidence with Open-Meteo | Retrieves historical precipitation records for the location and dates in the question. |
| Long-term memory with Mem0 | Stores selected facts in HorizonDB for later conversation turns. |
| A Gradio chat app | Shows the conversation, tool calls, and memory updates side by side. |

Hybrid search combines BM25 keyword precision with vector-based semantic retrieval. Graph expansion adds related precedents, and AI extraction turns opinion text into facts the agent can use. You will inspect the tool calls, evidence, and final answer.

Notebook 1 gives the agent its knowledge: relational data, vectors, indexes, and a citation graph. Notebook 2 turns those capabilities into tools and composes them into the agent. You will then test multi-turn memory in the chat app and application recovery during a controlled database failover.

The third notebook is an optional server-configuration check. The two main notebooks contain the prepared code; follow the instructions on these pages and check each result before continuing.

This is a learning application, not legal advice. Its sample data and shortened opinion text can produce incomplete answers, so generated claims still need review against their sources.

## Next steps

> Start with [Part 0](part-0-setup-azure-resources.md) to sign in to Azure and set up your lab resources.