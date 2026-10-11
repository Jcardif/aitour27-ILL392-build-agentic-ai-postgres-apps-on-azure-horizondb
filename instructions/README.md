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

In this lab, you will build Counsel AI, a legal research assistant that helps investigate a sample property-flooding dispute in Seattle. It uses Washington State court cases stored in Azure HorizonDB, a PostgreSQL database.

You will see how the assistant finds evidence by keyword, by meaning, and through citations between cases. It will extract facts from opinion text, request historical rainfall data from an external service, and remember selected facts from a conversation.

The lab retains its advanced technical exercises, but the instructions explain each capability before you use it. You do not need to write Python for the notebook exercises. Run the prepared code cells, check the results, and consider what the capability would mean for a business application.

Keep these instruction pages open alongside the notebooks. All explanations and tasks are here, so the notebooks can contain only code. Cell references count code cells only and remain valid when notebook markdown is removed.

## What you will build

| What you will build | What you will observe |
|---|---|
| A searchable case dataset | Different search methods find different evidence. |
| A tool-using AI assistant | The model calls prepared tools before drafting an answer. |
| A citation graph | Links between cases add related decisions to the research. |
| Structured case analysis | The AI extracts rulings, issues, statutes, and case outcomes from excerpts. |
| An external weather lookup | The assistant combines database evidence with historical rainfall records. |
| Stored conversation memory | Follow-up questions reuse facts without repeating the intake details. |
| A browser chat app | Tool calls and memory updates appear beside the conversation. |

The cases, search indexes, citation graph, and memories share one HorizonDB database. Azure OpenAI provides model processing, and Open-Meteo provides the weather records. The generated legal analysis is a lab example and needs human review.

## Next steps

> Start with [Part 0](part-0-setup-azure-resources.md) to sign in to Azure and set up your lab resources.