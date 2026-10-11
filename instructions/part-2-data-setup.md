# Part 2 - Prepare the data for your AI assistant

## 2.1 Understand what you are preparing

An AI assistant needs evidence to answer questions about your business. In this lab, that evidence is a sample of Washington State court cases. You will prepare it for a legal research assistant that helps an attorney investigate a property-flooding dispute.

You will use one Azure HorizonDB database to store the cases and support different ways of finding relevant evidence.

| Capability | What it does for the assistant |
|---|---|
| Tables | Store each case's name, date, court, and written opinion. |
| Keyword search | Find opinions containing relevant legal terms. |
| Search by meaning | Find similar situations even when the question uses different words. |
| Citation graph | Follow links between cases to find related court decisions. |

The notebook contains the Python code. This page contains the explanations, actions, and expected results. You do not need to write Python or SQL to complete the notebook steps. Focus on what each step makes possible for the assistant.

The data model shows the information stored for each case.

![Sample case data model](media/notebook-1-data-model.png)

The citation model shows cases as points and citations as links. A link means one case refers to another.

![Case citation model](media/notebook-1-citation-model.png)

### Open Notebook 1

Before starting, complete Part 1 and confirm that you can connect to your lab database in the VS Code PostgreSQL extension.

| Your environment | Notebook location | Preparation |
|---|---|---|
| Guided lab VM | `C:\Lab392\Code\1-data-setup.ipynb` | The Python packages, `.env` settings, and `Dataset` folder are provided. |
| Your own computer | `src/1-data-setup.ipynb` in this repository | Complete the [self-deployment guide](../setup/SETUP.md) and [database connection steps](on-your-own/part-1-connect-horizondb-to-vscode.md). Follow the dataset-folder instructions in section 2.6 below. |

1. In VS Code, select **Explorer** on the left.
2. Expand `Code` on the guided VM, or `src` on your own computer, and open `1-data-setup.ipynb`.
3. If a kernel is already selected at the top right, confirm that it is **Python 3.13.9** on the guided VM. To change it, select the kernel name, then **Python Environments** > **Python 3.13.9**. On your own computer, choose the `.venv` environment created during setup.
4. Keep this instruction page open alongside the notebook.

![VS Code Explorer icon](media/files-icon.png)

![Notebook 1 in the guided lab's Code folder](media/notebook-1-data-setup.png)

### How to run the cells

A code cell is a box containing Python code with a **Run Cell** play button. Select that button to run the cell, then wait for it to finish before continuing.

**Count code cells only, starting at the top of this notebook.** Ignore any markdown text or diagrams that are still present. The numbers below are positions in the notebook, not the execution numbers such as `[1]` that VS Code displays after a run. These references also work when the notebook contains only code cells.

Run the cells in order. Do not use **Run All**. Some steps take several minutes, and you need to check their results before moving on. If a cell shows an error, stop at that step rather than running the remaining cells.

| Code cell | Purpose | Code to look for |
|---|---|---|
| 1 | Load Python libraries | `import psycopg` |
| 2 | Read connection settings | `load_dotenv(override=True)` |
| 3 | Enable database capabilities | `# Create Extensions Needed for Lab` |
| 4 | Register the AI models | `CHAT_MODEL_ALIAS` |
| 5 | Load the case dataset | `# Initialize dataset: create tables and load cases from CSV` |
| 6 | View five sample cases | `# Test query: select top 5 rows from cases` |
| 7 | Add storage for meaning-based search | `# Add the vector column to the cases table` |
| 8 | Generate the search representations | `BATCH_SIZE = 20` |
| 9 | Build the citation graph | `# Build the case citation graph using Apache AGE` |
| 10 | Build and test keyword search | `CREATE INDEX idx_cases_fts` |
| 11 | Build the meaning-based search index | `CREATE INDEX IF NOT EXISTS idx_cases_diskann` |

> [!WARNING]
> Use only the database created for this lab. Cell 5 deletes and recreates the `cases` and `temp_cases` tables. Cell 8 clears existing case embeddings by default, and cell 9 rebuilds the citation graph. Do not run these steps against production data or another team's database.

## 2.2 Load the Python libraries

The notebook uses libraries to connect to HorizonDB, call Azure OpenAI, and read the configuration file. These are already installed on the guided VM.

1. Run **code cell 1**.
2. On the guided VM, the first time you run a cell without a selected kernel, VS Code asks you to choose a Python environment. Select **Python Environments** > **Python 3.13.9**. The VM also has **Python 3.14.7**; **do not select it for this lab**.
3. Wait for the cell to finish without an error. No printed output is expected.

The first run may take around 10 to 15 seconds. If you see `ModuleNotFoundError`, check that you selected the prepared Python kernel. On your own computer, return to the Python setup steps in the [self-deployment guide](../setup/SETUP.md).

## 2.3 Read the connection settings

The `.env` file contains the addresses and credentials for your lab resources. The notebook reads them so you do not have to paste credentials into the code.

1. Run **code cell 2**.
2. Confirm that three lines appear: the chat deployment name, the embedding deployment name, and the API version.

A deployment is an AI model made available through your Azure resource. The chat model will write answers. The embedding model will prepare text for meaning-based search.

If the cell shows `KeyError`, a required setting is missing. Check your lab `.env` file and the environment setup before continuing. Do not share keys or passwords in screenshots or support requests.

## 2.4 Enable the database capabilities

A PostgreSQL extension adds a capability to the database. This step enables the extensions that the assistant will use.

| Extension | Role in this lab |
|---|---|
| `azure_ai` | Let the database request AI analysis from Azure OpenAI. |
| `vector` | Store the number lists used for meaning-based search. |
| `age` | Store and query links between court cases. |
| `pg_diskann` | Speed up meaning-based search. |
| `pg_textsearch` | Rank keyword-search results. |

1. Run **code cell 3**.
2. Confirm that five extension-ready messages appear, followed by `Done. Extensions Created.`

The guided lab resource is configured to allow these extensions. If an extension fails to load, ask your facilitator for help or use the [optional diagnostics guide](optional-diagnostics.md). Do not continue with only some of the extensions enabled.

## 2.5 Register the AI models

The database needs to know which Azure OpenAI models to call. This step registers two short names, `lab-chat` and `lab-embedding`, using the configuration you loaded earlier.

1. Run **code cell 4**.
2. Confirm that the output reports both registered aliases, `lab-chat` and `lab-embedding`.
3. Check that the verification rows show your configured endpoint and deployment names.

The assistant will later use `lab-chat` to request structured facts from case opinions. Registration connects that short name to the actual Azure model deployment.

## 2.6 Load the court cases

This step imports `cases.csv`. It keeps a raw copy in `temp_cases` and creates a `cases` table with fields the assistant can search. The raw copy is also needed to build the citation links later.

### If you are using your own computer

The current notebook looks for `Dataset/cases.csv`, but this repository stores the file at `data/cases.csv`. Prepare the expected folder without editing the notebook:

1. In the repository's root folder, create a folder named `Dataset`.
2. Copy `data/cases.csv` into it, giving you `Dataset/cases.csv`.
3. Leave the original `data` folder in place. The repository's setup resources still use it.

The guided VM already has the `Dataset` folder, so guided attendees can skip these folder steps.

### Run the load

1. Run **code cell 5**.
2. Check the printed `CSV path:`. It should identify the lab's `Dataset/cases.csv` file.
3. Wait for `Done. <N> cases loaded into the cases table.` The number of cases must be greater than zero.
4. Note that number. You will compare it with the graph's node count in section 2.10.

Loading normally takes around 30 to 60 seconds, but timing depends on your environment. A `FileNotFoundError` means the CSV was not found at the printed path. Resolve the folder location before rerunning this cell.

## 2.7 Check a few loaded cases

Before preparing search, check that the source data is present.

1. Run **code cell 6**.
2. Confirm that five case rows print below the cell.
3. Find a case name, decision date, court, and opinion text in the output.

If no rows appear, return to the loading step. An assistant cannot answer from your dataset if the dataset is empty.

## 2.8 Add storage for meaning-based search

An embedding is a list of numbers that represents text for similarity comparisons. It helps the assistant find a case about a similar situation without requiring the same words in the question.

This step adds an `opinions_vector` column to hold those lists. The lab's embedding model produces 1,536 numbers for each input, so the column has that size.

1. Run **code cell 7**.
2. Confirm that `opinions_vector column added.` appears.

The column exists now, but the next step must populate it before meaning-based search can work.

## 2.9 Generate the embeddings

This step sends case text to Azure OpenAI and stores the returned embeddings in HorizonDB. It processes 20 cases at a time and prints progress after each batch.

1. Run **code cell 8**.
2. Watch the `Progress: X / Y (Z%)` messages.
3. Wait until progress reaches 100% and the output ends with `Done. <N> rows processed.` and a duration summary.

This is one of the longest steps. Allow several minutes and do not start the next cell while it is running.

> [!NOTE]
> Rerunning this cell clears existing embeddings and starts again with its default settings. If it is interrupted or reports an Azure OpenAI error, resolve the error before retrying. Do not rerun the entire notebook as a recovery step.

The lab uses shortened opinion text to keep processing time manageable. In a business application, the team must decide how to include the full documents and assess search quality.

## 2.10 Build the citation graph

A citation connects a court case to another case it references. Following these connections can find related decisions that a keyword search missed.

This step uses Apache AGE to create `case_graph` inside HorizonDB. Each case becomes a node, and each citation between cases in the dataset becomes a `REF` link.

1. Run **code cell 9**.
2. After `Graph nodes created.`, compare `Node count:` with the case count you noted in section 2.6. They should match.
3. Watch the citation-edge progress messages and wait for `Done. Case graph is ready.`

Creating the links can take several minutes. The graph only includes citations whose target cases are also in this sample dataset. It is not a complete record of all legal precedents.

## 2.11 Build and test keyword search

Keyword search is useful when a user knows a relevant legal term. This step builds a search index over the opinion text and tries the sample query `water leaking`.

The index uses BM25, a method for ranking documents against search terms.

1. Run **code cell 10**.
2. Confirm that `pg_textsearch extension is ready.` appears.
3. Look for the sample case matches under `Top matches (lower BM25 scores are more relevant):`.

The sample query returns up to five matches. A lower, more negative BM25 score is a stronger match for this query. The score is not a confidence percentage or a measure of legal correctness.

## 2.12 Build the meaning-based search index

The embeddings let the database compare meaning. An index makes those comparisons faster as the dataset grows. This step creates the DiskANN index on `opinions_vector`.

1. Run **code cell 11**.
2. Wait for `pg_diskann ready: idx_cases_diskann on public.cases(opinions_vector)`.

Index creation can take a couple of minutes. You now have keyword search, meaning-based search, and citation links ready for the assistant.

## 2.13 View the tables and citation graph

These actions take place in the VS Code PostgreSQL extension. There is no additional notebook cell to run.

### View the tables

1. Select the **PostgreSQL** elephant icon on the left.
2. Expand your `lab` connection.
3. Right-click the database configured in `.env`, normally `postgres`, and select **Visualize Schema**.
4. Use the eye icon to hide schemas other than `public`.
5. Find `cases` and confirm that it has an `opinions_vector` column.

![Schema visualizer in the PostgreSQL extension](media/notebook-1-schema-visualizer.png)

![Lab tables in the public schema](media/notebook-1-public-schema.png)

### View the citation graph

1. Right-click the same database and select **New Query**.
2. Copy the following query into the query editor. It is also available in `Code/show_graph.sql` on the guided VM and [src/show_graph.sql](../src/show_graph.sql) in this repository.
3. Select the query editor's **Run** play button.
4. Open the graph view in the results and inspect the case nodes and their `REF` links.

```sql
SET search_path = public, ag_catalog, "$user";

SELECT * FROM cypher('case_graph', $$
    MATCH (n)-[r]->(m)
    RETURN n, r, m
$$) AS (source agtype, edge agtype, target agtype);
```

![Case citation graph in the PostgreSQL extension](media/notebook-1-citation-graph.png)

You have prepared one database with the case records and different ways to retrieve evidence. In the next part, you will see how an assistant uses those capabilities to answer a question.

Continue to [Part 3 - Build and try the AI assistant](part-3-agentic-app-development.md).
