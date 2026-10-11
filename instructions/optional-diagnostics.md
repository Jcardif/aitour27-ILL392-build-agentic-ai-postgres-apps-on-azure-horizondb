# Optional - Check the database server configuration

**Notebook 3 is optional.** You do not need to run it to complete the data and application exercises. Use it to inspect a server setting, particularly when an extension-loading step reports an error.

This notebook reads `shared_preload_libraries`, a setting that lists libraries the database server loads when it starts. Some extensions, including the lab's keyword-search extension, need a library loaded this way before you can use them.

The notebook does not change server settings, load data, or run the assistant. It is a narrow configuration check, not a complete health check of the lab.

## Open Notebook 3

You need the lab's configured `.env`, a prepared Python environment, and access to the database. You can run this check before completing the other notebooks if you are investigating a setup problem.

1. In VS Code Explorer, open `C:\Lab392\Code\3-diagnostics.ipynb` on the guided VM, or `src/3-diagnostics.ipynb` on your own computer.
2. If a kernel is already selected at the top right, confirm that it is **Python 3.13.9** on the guided VM. To change it, select the kernel name, then **Python Environments** > **Python 3.13.9**. On your own computer, use the `.venv` environment from the [setup guide](../setup/SETUP.md).
3. Run the three code cells in order using each cell's **Run Cell** play button. Wait for each cell to finish before continuing.

**Count code cells from the top of this notebook.** The cell positions below are not VS Code's execution counters. This notebook already contains only code cells.

| Code cell | Purpose | Code to look for |
|---|---|---|
| 1 | Load the Python libraries | `import nest_asyncio` and `import psycopg` |
| 2 | Read the lab settings | `load_dotenv(override=True)` |
| 3 | Print the server's preloaded libraries | `SHOW shared_preload_libraries;` |

## Step 1 - Load the libraries

1. Run **code cell 1**.
2. On the guided VM, the first time you run a cell without a selected kernel, VS Code asks you to choose a Python environment. Select **Python Environments** > **Python 3.13.9**. The VM also has **Python 3.14.7**; **do not select it for this lab**.
3. Confirm that it finishes without an error. No printed output is expected.

If a package is missing, check the selected kernel first. On your own computer, follow the package-installation steps in the setup guide.

## Step 2 - Read the settings

1. Run **code cell 2**.
2. Confirm that the chat deployment, embedding deployment, and API version print.

Although the final check only queries the database, this cell loads the full lab configuration, including the Azure OpenAI settings. A missing required value produces `KeyError`. Resolve it in your environment setup before proceeding, and do not include secrets in support requests.

## Step 3 - Inspect the preloaded libraries

1. Run **code cell 3**.
2. Find the `shared_preload_libraries:` heading and the library names printed beneath it.
3. Look for `pg_textsearch`, which the lab needs for BM25 keyword search.

| Result | What it tells you |
|---|---|
| `pg_textsearch` appears | The server lists the required keyword-search library as preloaded. You still need Part 2's extension and index steps to succeed. |
| `pg_textsearch` is missing | The server configuration does not meet this lab's keyword-search prerequisite. Ask the facilitator or resource owner to review it before retrying Part 2. |
| A connection or authentication error appears | The query did not run. Check `.env` credentials and database networking using the setup guide. |

Other library names depend on the server configuration. Do not expect all five lab extensions in this list. Not every extension uses `shared_preload_libraries`.

This result does **not** verify that the case data, embeddings, citation graph, model registrations, or agent tools are ready. Check those through the relevant steps in [Part 2](part-2-data-setup.md) and [Part 3](part-3-agentic-app-development.md).

## If configuration needs attention

Guided attendees should share the notebook name, code-cell position, and non-sensitive error text with the facilitator. Do not change shared server settings yourself.

On your own subscription, have the resource owner review the server configuration against the lab setup and [HorizonDB documentation](https://aka.ms/horizondb-docs). Changes to preloaded libraries can require a server restart. Plan that interruption before making changes, then rerun this check.

Return to the exercise you were troubleshooting. If you already completed Part 3 and ran this notebook only to inspect the setting, there are no further required lab steps.
