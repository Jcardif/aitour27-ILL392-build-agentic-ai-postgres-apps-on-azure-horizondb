# Part 1 - Connect to your Azure HorizonDB database using VS Code Extension for PostgreSQL

In this part, we will connect to your Azure HorizonDB database using the VS Code Extension for PostgreSQL. Make sure you have VS Code installed along with the PostgreSQL extension.

## Open VS Code and set up database connection to Azure PostgreSQL

1. On your machine, open **Visual Studio Code (VS Code)**.

1. Once inside VS Code, ensure that you have the repo cloned and opened in your workspace.

    ![Lab Folder](/instructions/media/lab-folder.png)

1. Now, in the **aitour-iLL392-build-agentic-ai-postgres-apps-on-azure-horizondb** folder, look for a **.env** file and double click to open it.

    ![Env File Click](/instructions/media/env-file-click.png)

1. With the **.env** file open, let's look at some of the variables defined.  This file contains all the credentials needed to connect to the Azure OpenAI and Azure HorizonDB instances that were deployed during the creation of this lab.  Most of these credentials we will not need to copy/paste as we will programmatically load them into our code notebook in a later step in this lab.

    But for the next couple steps, we will use the following values of these variables to copy/paste to make our connection to the HorizonDB database from within VS Code:

    - AZURE_PG_HOST
    - AZURE_PG_USER
    - AZURE_PG_PASSWORD

    ![Env Details](/instructions/media/env-details.png)

1. In the next few steps, we are going to use the VS Code Extension for PostgreSQL to add a connection to our HorizonDB database. Leave the **.env** file open, we will use it in the next few steps. On the left navigation, select the **elephant** icon.

    ![Elephant Icon](/instructions/media/elephant-icon.png)

## Create Connection to HorizonDB

1. Once the extension loads, in the **POSTGRESQL** panel select the **Add Connection** button.

    ![VS Code Add Connection](/instructions/media/vs-code-add-conn.png)

1. Fill out the connection form with the following values"

    - For **SERVER NAME**, copy/paste the **AZURE_PG_HOST** value from the `.env` file
        - Example: **horizondb-lab-australiaeast-czhpjspykdk4q.e557d0d51d1e.australiaeast.horizondb.azure.com**
    - For **AUTHENTICATION TYPE**, choose **Password** *(Note: Entra ID is coming soon for HorizonDB)*
    - For **USER NAME**, type **labUser**
    - For **PASSWORD**, copy/paste the **AZURE_PG_PASSWORD** value from the `.env` file
        - Example: **Zcohzrudys5q3e!**
    - For **DATABASE**, leave blank
    - For **CONNECTION NAME**, type **lab**

    ![Add Connection Screen](/instructions/media/add-conn-screen.png)

1. Next, click **"Test Connection"**, and you should see a green check box appear.

    > [!NOTE]
    > **Note:** during the lab creation process we automatically allow-listed this VM's IP address to allow connections into your instance of HorizonDB. In the future, you will need to ensure you take this step to open access to connect to your HorizonDB database either directly with a query editor tool, or programmatically.

    ![Add Connection Test Connection](/instructions/media/add-conn-test-conn.png)

1. Lastly, click **"Save & Connect"** to save the connection and open the connection to the HorizonDB database

    ![Save and Connect](/instructions/media/save-and-connect.png)

**Congratulations, you just signed in to your Azure HorizonDB database using the VS Code Extension for PostgreSQL!**

## Explore VS Code Extension for PostgreSQL Dashboard

1. Now that we have our connection created, let's explore the VS Code Extension for PostgreSQL and our HorizonDB database.  First, right-click on your **"lab"** connection we just created, and select the "Dashboard" option from the context menu:

    ![Select Dashboard](/instructions/media/select-dashboard.png)

1. When the Dashboard loads, you will see it provides a robust set of performance details such as **wait events, disk i/o, transactions, storage, and more**.

    ![Dashboard Main](/instructions/media/dashboard-main.png)

1. To continue exploring the VS Code Extension for PostgrSQL, now expand the **"Databases"** node under the **"lab"** connection.  Look for the **"postgres"** database, right-click it and select **"New Query"** from the context menu.

    ![New Query](/instructions/media/new-query.png)

1. Now run the following query by copying and pasting the following SQL block into the query editor window, then click the green play arrow on the top right to execute the SQL statement.  The purpose of this SQL query is just to illustrate the process of running queries and seeing results using the VS Code Extension for PostgreSQL.

    > [!NOTE]
    > During the course of this lab most SQL queries will be ran programmatically via Python code and the pscyopg Python package.  However, there are a few queries you will need to run using the query editor in the VS Code Extension, so stay tuned for those!

    ```SQL

    SELECT
        current_database() AS database_name,
        current_user AS connected_user,
        now() AS server_time,
        version() AS postgres_version;
    ```

    ![Empty Query Example](/instructions/media/empty-query-example.png)

## Next Step

> Proceed to [Part 2 - Programmatically interact with your Azure HorizonDB database using Python](/instructions/on-your-own/part-2-3-data-setup-agentic-development.md) to continue with the lab.
