# Part 3 - Build and try the AI assistant

## 3.1 Build Counsel AI

You will build a legal research assistant for a sample property-flooding dispute in Seattle. An agent is an AI assistant that can call tools to gather evidence before answering.

You will add tools one at a time, then try the assistant in a chat app.

![Legal research assistant architecture](media/notebook-2-architecture.png)

> [!IMPORTANT]
> This is a learning example, not legal advice. The sample dataset and shortened opinion text can produce incomplete answers. Review generated claims against their sources.

### Open Notebook 2

Complete [Part 2](part-2-data-setup.md) first.

1. In VS Code Explorer, expand the `Code` folder in `C:\Lab392`.
2. Open `2-app-development.ipynb` and keep this page alongside it.

![Notebook 2 in the Code folder](media/notebook-2-app-development.png)

### Run the cells

Use **Run Cell** to run one code cell at a time and wait for it to finish. Cell numbers count code cells from the top, not markdown cells or execution counters.

If a cell reports an error, stop and ask your facilitator for help. If an expected tool call is missing, retry that agent cell once before asking for help.

## 3.1.1 Load the application libraries

The installed libraries connect the model, database tools, and chat app.

1. Run **code cell 1**.
2. When asked for an environment, select **Python Environments** > **Python 3.13.9**, not **Python 3.14.7**. To change an existing selection, click the kernel name at the top right.
3. Wait for the cell to finish without an error. No printed output is expected.

## 3.1.2 Read the connection settings

This notebook reads the prepared `.env` settings again for its new Python session.

1. Run **code cell 2**.
2. Check that the chat deployment name, embedding deployment name, and API version print.

## 3.2 Add keyword search

Keyword search finds case opinions containing relevant terms, such as "drainage."

1. Run **code cell 3** to define `keyword_case_search`.
2. Check that it finishes without an error. No printed output is expected yet.

## 3.2.1 Test keyword search

This test calls the tool directly, before the agent uses it.

1. Run **code cell 4**.
2. Look for `keyword_case_search was called` and up to five case rows with names, dates, and opinion snippets.

Lower, more negative BM25 scores indicate stronger keyword matches within this query.

## 3.2.2 Let the agent use keyword search

The sample question describes a Seattle property flooding after road drainage changes. The agent chooses search terms and drafts an answer from the cases.

1. Run **code cell 5**.
2. Look for `keyword_case_search was called` under `// Functions the Agent Called: //`.
3. Read `// Agent Response: //`. Find a case from the search results and check how the answer uses it.

The trace confirms tool use, not that the answer is correct.

## 3.3 Add search by meaning

Meaning-based search can find similar situations even when the question uses different words from the case opinion.

1. Run **code cell 6** to define `semantic_case_search`.
2. Check that it finishes without an error. No printed output is expected yet.

## 3.3.1 Compare the two searches

1. Run **code cell 7**.
2. Compare `Tool 1 ids` and `Tool 2 ids`. Find any cases returned by only one method.
3. Check `Union  ids`, the combined list with duplicates removed.

Using both methods is called hybrid search. Semantic scores closer to 1 indicate greater similarity. They use a different scale from BM25 scores.

## 3.3.2 Let the agent use both searches

1. Run **code cell 8**.
2. Check that the trace includes both keyword and semantic search.
3. Compare the answer with cell 5. Look for evidence added by the second search.

## 3.4 Add citation-graph search

The graph tool follows citations from the candidate cases to find related court decisions.

1. Run **code cell 9** to define `precedent_graph_search`.
2. Check that it finishes without an error. No printed output is expected yet.

## 3.4.1 Test the three search tools

1. Run **code cell 10**.
2. Find `Input ids:`, the cases from keyword and semantic search.
3. Find `RELATED_CASE_IDS:`, the additional cases found through citations. The graph returns up to ten.

## 3.4.2 Let the agent use the citation graph

1. Run **code cell 11**.
2. Check that the trace includes keyword, semantic, and graph search.
3. Find a related case in the answer and compare it with the graph results from cell 10.

## 3.5 Add fact extraction

This tool asks Azure OpenAI to extract the court's ruling, issues, cited laws, and case outcome through HorizonDB's `azure_ai` extension.

1. Run **code cell 12** to define `case_analyst_extract`.
2. Check that it finishes without an error. No printed output is expected yet.

The lab sends only the first 1,200 characters of each opinion to the model. Important facts may be missing from that excerpt.

## 3.5.1 Let the agent extract facts

1. Run **code cell 13**.
2. Check that the trace includes the three search tools and `case_analyst_extract was called`.
3. Compare the answer with cell 11. Look for rulings or issues rather than case names alone.

If a result contains `extraction_failed`, that case's analysis failed. An `extracted_case=` completion line alone does not prove success. Ask your facilitator for help before relying on the answer.

## 3.6 Add the weather tool

The Open-Meteo archive service supplies historical rainfall records when weather matters to the question.

1. Run **code cell 14** to define `get_weather_evidence`.
2. Check that it finishes without an error. No printed output is expected yet.

## 3.6.1 Test the weather service

The sample requests Seattle rainfall for January 10 to January 20, 2023.

1. Run **code cell 15**.
2. Check for `get_weather_evidence was called` and `=== Weather Evidence (pretty) ===`.
3. In `daily_records`, find the dates and `precipitation_mm` values. Identify the day with the most rainfall.

If the result reports `WEATHER_EVIDENCE_ERROR` or no data, ask your facilitator for help before continuing.

## 3.6.2 Let the agent use all five tools

This question includes a February 2024 date so the agent can request weather evidence.

1. Run **code cell 16**.
2. Check that all five tools appear in the trace and that the weather call uses Seattle coordinates and February 2024 dates.
3. Compare the answer with cell 13. Find rainfall evidence and how the answer uses it.

Rainfall records show weather conditions, not who caused the damage.

## 3.7 Set up stored memory

Mem0 saves selected conversation facts in HorizonDB so the assistant can recall them in later turns.

1. Run **code cell 17** once, leaving the code unchanged.
2. Read the intake response and the retrieved and new memory sections. Retrieved memories may be empty on the first run.

You will try the remaining conversation turns in the chat app.

## 3.8 Start the chat app and try memory

1. Run **code cell 18**.
2. Wait for `Running on local URL: http://127.0.0.1:7860`.
3. Open a browser from the desktop and go to [http://localhost:7860](http://localhost:7860).

![Chat app with Tool Trace and Long-term Memory panels](media/notebook-2-chat-app.png)

> [!NOTE]
> Launching this cell clears the app's demo memories and starts a separate conversation from cell 17. Keep it running throughout the exercise.

### Send four messages

Use the chat text box and **Send** button. Wait for each response and memory update before sending the next message.

1. Send the intake message:

   ```text
   Hi, I am an attorney and my client owns a property in the Queen Anne neighborhood of Seattle, Washington. Please remember those details for later. No legal question yet.
   ```

   Check the Memory panel for the property location.

2. Describe the problem without repeating the location:

   ```text
   After a heavy storm during February of 2024 my client's yard floods every time it rains. Right after the storm, the city regraded the road. Does my client have a claim?
   ```

   Check that the assistant uses the Seattle context. Watch the Tool Trace panel and look for storm facts added to memory.

3. Ask a follow-up:

   ```text
   Given what you found, what evidence should I start collecting?
   ```

   Check that the answer builds on the flooding question without repeating intake questions.

4. Check recall:

   ```text
   Remind me what neighborhood my client's property is in, and what was the month of the storm we discussed.
   ```

   Check that the answer says Queen Anne and February 2024.

Refresh the browser page and repeat the recall question. Check that the stored facts still appear in the Memory panel. Keep cell 18 running, because relaunching it clears those memories.

## 3.9 Observe recovery during a database failover

A failover moves the database's primary role to a standby replica. Leave the chat app running for this exercise.

> [!WARNING]
> Use only your isolated lab resource and get your facilitator's approval before triggering failover.

1. Send a normal chat message and check that the app responds and retrieves memories.
2. Open the [Azure portal](https://portal.azure.com). Use the credentials in the **Resources** tab if you need to sign in again.
3. Select **All Resources**.

   ![All Resources in the Azure portal](media/notebook-2-all-resources.png)

4. Open your `horizondb-lab-...` resource.

   ![Select the lab HorizonDB resource](media/notebook-2-horizondb-resource.png)

5. Under **Settings**, select **Replicas**.

   ![HorizonDB Settings navigation](media/notebook-2-settings.png)

   ![Replicas navigation item](media/notebook-2-replicas.png)

6. Select **Forced Failover** and confirm with **Fail over**. If the action is unavailable, ask your facilitator for help.

   ![Forced Failover action](media/notebook-2-forced-failover.png)

7. Immediately return to the chat app and send `What other defendants should I consider?`.
8. Inspect cell 18's output. You may see `Mem0: search attempt 1/5 failed ... Retrying...` if your request reached the database during the interruption.
9. After the portal reports completion, send the recall question again. Check for Queen Anne and February 2024 in both the answer and Memory panel.

If all five memory lookup attempts fail, the app reports `Mem0: search unavailable after 5 attempts, returning no results.` That turn ran without retrieved memories. Wait for failover to finish and check recall again before concluding that memory recovered.

### Finish the lab

Close the app tab and select **Restart Kernel** in Notebook 2 to stop the local app.

[Notebook 3 is optional](optional-diagnostics.md). Use it for the server-configuration check, or finish here. Further learning links are in the [session resources](../README.md#-continue-your-learning).

## If something goes wrong

Check the failed cell's output and ask your facilitator for help. Share the notebook name, code-cell number, and error text without credentials.

If the kernel restarted, the earlier definitions must run again before later cells can work. Ask for help before rerunning Part 2, because its data-loading cells replace existing data.
