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

In this lab, you will build a Compliance Advisor Agent that helps an attorney investigate a property-flooding dispute in Seattle. It finds relevant Washington State court cases, extracts facts from their opinions, and combines that research with historical rainfall records to draft an answer.

An agent is an AI assistant that can call tools to gather evidence before answering. Microsoft Agent Framework connects the Azure OpenAI chat model to five research tools. You will add those tools one at a time and see how each changes the evidence available to the assistant.

One Azure HorizonDB PostgreSQL database stores the cases, search indexes, citation graph, and conversation memories. Azure OpenAI provides the chat and embedding models, while Open-Meteo supplies weather records. The lab demonstrates how one database can support several kinds of search and agent memory without separate databases for each capability.

## What you will build

| Capability | What it does for the assistant |
|---|---|
| Keyword search with BM25 and `pg_textsearch` | Finds opinions containing relevant legal terms. |
| Meaning-based search with `vector` and DiskANN | Finds similar situations even with different wording, within the selected court and year filters. |
| Citation-graph search with Apache AGE | Follows links between cases to find related court decisions. |
| In-database fact extraction with `azure_ai` | Calls Azure OpenAI from SQL to extract rulings, issues, cited laws, and case outcomes. |
| Weather lookup with Open-Meteo | Adds historical rainfall evidence for the location and dates in the question. |
| Stored conversation memory with Mem0 | Saves selected facts in HorizonDB so follow-up questions can reuse earlier details. |
| A Gradio chat app | Shows the conversation, tool calls, and memory updates side by side. |

Combining keyword and meaning-based results is called hybrid search. The citation graph adds related decisions, and fact extraction supplies details for the draft.

You will first load the dataset and enable the database capabilities, then build and try the assistant. In the chat app, you will test a multi-turn conversation and check that it recalls facts without repeating the intake questions. You will also trigger a controlled database failover and observe whether the app recovers and retrieves its stored memories.

The third notebook is an optional server-configuration check. The two main notebooks contain the prepared code; follow the instructions on these pages and check each result before continuing.

This is a learning application, not legal advice. Its sample data and shortened opinion text can produce incomplete answers, so generated claims still need review against their sources.

## Next steps

> Start with [Part 0](part-0-setup-azure-resources.md) to sign in to Azure and set up your lab resources.