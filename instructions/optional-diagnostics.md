# Optional - Check the database server configuration

Notebook 3 is optional. Use it to inspect a server setting if keyword search fails to load. It reads settings without changing the database.

## Open Notebook 3

In VS Code Explorer, expand `Code` in `C:\Lab392` and open `3-diagnostics.ipynb`.

Run one code cell at a time using **Run Cell** and wait for it to finish. Cell numbers count code cells from the top, not execution counters.

## Step 1 - Load the libraries

1. Run **code cell 1**.
2. When asked for an environment, select **Python Environments** > **Python 3.13.9**, not **Python 3.14.7**. To change an existing selection, click the kernel name at the top right.
3. Check that the cell finishes without an error. No printed output is expected.

## Step 2 - Read the settings

1. Run **code cell 2**.
2. Check that the chat deployment name, embedding deployment name, and API version print.

This cell uses the prepared `.env` file. If it reports a missing value or another error, stop and ask your facilitator for help.

## Step 3 - Inspect the preloaded libraries

Some extensions need the server to load a library when it starts. The `shared_preload_libraries` setting lists those libraries.

1. Run **code cell 3**.
2. Find `shared_preload_libraries:` and look for `pg_textsearch` in the list beneath it.

If `pg_textsearch` is missing, ask your facilitator to review the configuration. Leave server-setting changes to the resource owner.

This check does not verify the loaded cases or agent tools. Return to the step you were troubleshooting in [Part 2](part-2-data-setup.md) or [Part 3](part-3-agentic-app-development.md).
