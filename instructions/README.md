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

You will build Counsel AI, a legal research assistant for a sample property-flooding dispute in Seattle. It searches Washington State court cases in Azure HorizonDB, checks historical rainfall, and remembers facts from the conversation.

The notebooks contain the prepared code. Follow the steps on these pages, run the indicated cells, and check each result before continuing.

## Next steps

> Start with [Part 0](part-0-setup-azure-resources.md) to sign in to Azure and set up your lab resources.