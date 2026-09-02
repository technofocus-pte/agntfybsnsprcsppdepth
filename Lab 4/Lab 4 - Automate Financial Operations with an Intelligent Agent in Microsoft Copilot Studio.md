<!--
lab: 4
title: Automate financial operations with an intelligent agent in Microsoft Copilot Studio
description: Build an intelligent finance agent to query budgets, submit invoices, and classify spend using Dataverse, Power Automate, and AI Builder.
level: 300
duration: 120 minutes
islab: true
primarytopics:
- Microsoft Copilot Studio
- Microsoft Dataverse
- Power Automate
- AI Builder
- Microsoft 365
- Microsoft Teams
-->

# Lab 4: Automate financial operations with an intelligent agent in Microsoft Copilot Studio

### Objective

Build an intelligent financial operations agent for Zava using Microsoft Copilot Studio and Microsoft Dataverse, enabling budget owners and finance users to check cost centre budget status, submit vendor invoices, and classify spend from a single conversational assistant inside Microsoft 365 and Teams — reducing manual lookups, inconsistent reporting, and the coordination effort carried by the central finance team.

### Solution Focus Area

Zava, a fast-growing retail organisation, manages spend across more than a dozen cost centres spanning IT, Operations, Marketing, and Corporate Finance. Budget owners rely on a patchwork of spreadsheets and email threads to answer even the simplest questions: how much of my allocation is left, which cost centres have already breached their budget, and where does this invoice actually belong?

Because allocation, spend-to-date, and invoice details sit in disconnected files and inboxes rather than one record store, every enquiry travels through the central finance team, who manually look up allocations, reconcile spend figures, chase vendor invoices through mailboxes, and categorise expenses by hand before anything can be approved. The result is slow turnaround, inconsistent reporting formats, and finance analysts spending their time on lookups rather than analysis.

To address these gaps, Zava is introducing **Zava FinOps Pro** — an intelligent financial operations assistant that grounds its answers in live Dataverse data and can act on requests directly, delivered by a Power Platform maker working in the Dev One developer environment without lengthy development cycles.

### Solution

A Copilot Studio agent grounded in Dataverse will be implemented to modernise financial operations at Zava by:
- **Establishing the Data Layer:** The **Budget Allocations** and
  **Invoice** tables will be created in Dataverse by importing ZavaFinOps_BudgetAllocations.csv and ZavaFinOps_Invoices.csv, with budget amounts set as Decimal and Budget Status and invoice Status held as text so downstream filters and flows behave correctly.

- **Defining Agent Behaviour:** The **FinOps Assistant** agent will be
  created with instructions that enforce a consistent response format — Cost Centre, Department, Allocated, Spent, Remaining and Status, INR currency prefixes, \[ON TRACK\], \[AT RISK\] and \[OVER BUDGET\] markers, and the most critical finding first — alongside guardrails that stop it estimating or inventing figures.

- **Grounding Answers in Live Data:** Both the Budget Allocations and
  Invoice tables will be attached as Dataverse knowledge sources, letting the agent answer department budget, spend-to-date, budget owner, and pending invoice queries with citations and without a single authored topic.

- **Enabling the Agent to Act:** The **ZFP Submit Invoice** agent flow
  will write new vendor invoices into the Invoice table with a status of Pending Approval and notify the approval inbox, then be registered as a tool so the agent moves from answering questions to completing transactions.

- **Classifying Unstructured Spend:** The **ZFP Expense Classifier** AI
  Builder prompt tool will read a free-text expense description and return the spend category, confidence, and one-sentence reasoning as structured JSON, replacing manual expense categorisation.

- **Delivering It Where Users Work:** A welcome message and suggested
  prompts will make the agent's capabilities clear from the first screen, and the published agent will be surfaced through the Microsoft 365 and Teams channel so business users at Zava can get answers and take action in the tools they already use every day.


## Exercise 1: Activate the Power Apps Developer Plan and Select the Lab Environment

In this exercise, you will activate the free Power Apps Developer Plan and select the developer environment that hosts every component you build. All later exercises must be completed in this environment.

1. Open your **Edge** browser and navigate to +++https://www.microsoft.com/en-in/power-platform/products/power-apps+++ the Power Apps product page.

1. On the product page, locate and click the **Try for free** button.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image1.png)

1. In the **Let's get started** panel, enter the M365 admin tenant ID in the email field. Then select the check box.

1. Click **Start free** to begin the Developer Plan sign-up.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image2.png)

1. Enter the administrator password in the **Enter password** field, then click **Sign in**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image3.png)

1. Wait for the Power Apps home page to load. The welcome message confirms that the Developer Plan is active.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image4.png)

1. In the top-right corner, click the **Environment** selector.

1. Under **Build apps with Dataverse**, select **Dev One**. Do **not** use the **Contoso (default)** environment — several Copilot Studio features are unavailable there.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image5.png)

1. The Power Apps Developer Plan is active, and the **Dev One** developer environment is selected for use in all subsequent exercises.


## Exercise 2: Create the Budget Allocations Table from CSV

In this exercise, you will create the Budget Allocations Dataverse table by importing a CSV file. Power Apps reads the file, generates the columns and sample records automatically, and no column is created by hand.

1. From the left navigation in Power Apps, select **Tables**.

1. On the command bar, click **New table**, then select **Create new tables**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image6.png)

1. On the **Choose an option to create tables** page, select **Import an Excel file or .CSV**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image7.png)

1. In the **Import an Excel or .CSV file** dialog, click **Select from device**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image8.png)

1. Select **ZavaFinOps_BudgetAllocations.csv** from your **C:\Lab Files** folder, then click **Open**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image9.png)

1. Wait while Power Apps reads the file. The table is generated automatically. Click on the table name and name it **Budget Allocations**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image10.png)

1. On the table card, click the **More options (…)** icon, then select **View data**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image11.png)

1. In the data grid, click the **Cost Centre ID** column header, then select **Edit column**. Confirm the **Data type** is **Single line of text**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image12.png)

1. Click the **Cost Centre Name** column header and confirm the **Data type** is **Single line of text**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image13.png)

1. Click the **Department** column header and confirm the **Data type** is **Single line of text**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image14.png)

1. Click the **Allocated Budget** column header and set the **Data type** to **Decimal**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image15.png)

1. Click the **Spent to Date** column header and set the **Data type** to **Decimal**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image16.png)

1. Click the **Remaining Budget** column header, set the **Data type** to **Decimal**, then click **Update**. The three budget columns must be **Decimal**, not **Currency**, because currency columns require an exchange rate configuration that is unnecessary for this lab.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image17.png)

1. Click the **Budget Status** column header, confirm the **Data type** is **Single line of text**, then click **Update**. This column must remain text rather than a choice column, because it is filtered with a text comparison later in the lab.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image18.png)

1. Click the **Budget Owner** column header and confirm the **Data type** is **Single line of text**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image19.png)

1. Click the **Budget Owner Email** column header and confirm the **Data type** is **Single line of text** with the **Format** set to **Email**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image20.png)

1. Click the **Last Updated** column header and confirm the **Data type** is **Date and time** with the **Format** set to **Date only**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image21.png)

1. On the command bar, click **Save and exit**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image22.png)

1. In the **Done working?** dialog, click **Save and exit**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image23.png)

1. The Budget Allocations table is created from CSV with all columns, data types, and sample cost centre records in place.


## Exercise 3: Create the Invoice Table from CSV

In this exercise, you will create the Invoice table using the same import method. This table receives the records written by the invoice submission tool you build in Exercise 9.

1. From the left navigation, select **Tables**.

1. On the command bar, click **New table**, then select **Create new tables**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image24.png)

1. On the **Choose an option to create tables** page, select **Import an Excel file or .CSV**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image25.png)

1. In the **Import an Excel or .CSV file** dialog, click **Select from device**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image26.png)

1. Select **ZavaFinOps_Invoices.csv** from your C:\Lab Files folder, then click **Open**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image27.png)

1. Confirm the file is listed with **Include** turned on, then click **Import**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image28.png)

1. Wait for the table to be generated. It is named +++Invoice+++ automatically.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image29.png)

1. On the table card, click the **More options (…)** icon, then select **View data**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image30.png)

1. Click the **Invoice Number** column header, then select **Edit column**. Confirm the **Data type** is **Single line of text**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image31.png)

1. Click the **Vendor** column header and confirm the **Data type** is **Single line of text**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image32.png)

1. Click the **Amount** column header, set the **Data type** to **Decimal**, then click **Update**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image33.png)

1. Click the **Cost Centre** column header and confirm the **Data type** is **Single line of text**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image34.png)

1. Click the **Description** column header, set the **Data type** to **Multiple lines of text**, then click **Update**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image35.png)

1. Click the **Status** column header, confirm the **Data type** is **Single line of text**, then click **Update**. This column must remain text so that the invoice submission flow can write the value **Pending Approval** directly.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image36.png)

1. Click the **Submitted Date** column header and confirm the **Data type** is **Date and time** with the **Format** set to **Date only**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image37.png)

1. On the command bar, click **Save and exit**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image38.png)

1. In the **Done working?** dialog, click **Save and exit**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image39.png)

1. Both Dataverse tables are now created from CSV, and the data layer of the Zava FinOps Pro platform is complete.


## Exercise 4: Activate the Copilot Studio Trial and Open the Developer Environment

In this exercise, you will activate the Copilot Studio trial and switch Copilot Studio to the same developer environment used in Power Apps. Copilot Studio opens in the default environment, so the environment must be changed before any agent is created.

1. Open a new browser tab and navigate to +++https://www.microsoft.com/en-us/microsoft-365-copilot/microsoft-copilot-studio+++ the Microsoft Copilot Studio product page.

1. Click **Sign in to Copilot Studio**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image40.png)

1. Enter M365 admin tenant ID in the **Sign in** field, then click **Next**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image41.png)

1. Enter the password in the **Enter password** field, then click **Sign in**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image42.png)

1. When prompted with **Stay signed in?**, click **Yes**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image43.png)

1. Wait while Copilot Studio loads. The address bar shows that the portal has opened in the **Default** environment.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image44.png)

    >[!Note] If Copilot studio navigate to new Copilot Studio portal, Turn off the new exeperience button.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/1a.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/1b.png)

1. Open a new browser tab and navigate to +++https://admin.powerplatform.microsoft.com+++ the Power Platform admin centre.

1. From the left navigation, select **Manage**, then select **Environments**, then select **Dev One**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image45.png)

1. On the **Dev One** details page, copy the **Environment ID** value.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image46.png)

1. Return to the Copilot Studio tab, replace the environment identifier in the address bar with the copied **Environment ID**, then press **Enter**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image47.png)

1. On the **Select a team** dialog, click **start a trial**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image48.png)

1. Under **Let's get you started**, click **Continue**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image49.png)

1. On the setup screen, provide the required information, then click **Get Started**:

    1. Select your **Country or Region** from the dropdown list

    1. Enter your **Job title** to indicate your role

    1. Enter a **Company name**

    1. Enter a valid **Business phone number**

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image50.png)

1. On the **Confirmation details** step, click **Get Started**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image51.png)

1. Confirm the Copilot Studio home page loads and that the environment selector shows **Dev One**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image52.png)

1. The Copilot Studio trial is active and the portal is running in the same Dev One environment that holds the Dataverse tables.


## Exercise 5: Create the FinOps Assistant Agent

In this exercise, you will create the FinOps Assistant agent and write the instructions that control how it formats and constrains its financial answers.

1. From the left navigation, select **Agents**.

1. On the command bar, click **Create blank agent**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image53.png)

1. In the **Name your agent** dialog, enter the following name:

    +++FinOps Assistant+++

1. Click **Create** and wait for the agent to be provisioned.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image54.png)

1. On the agent **Overview** page, in the **Details** card, click **Edit**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image55.png)

1. Click into the **Description** field and enter the following text:

    +++Helps Zava FinOps Pro users query budget status, cost centre allocations, invoice submission, and spend information using live Dataverse data.+++

1. Click **Save**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image56.png)

1. Scroll to the **Instructions** card and click **Edit**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image57.png)

1. Click into the instructions field and enter the following text:

    +++You are the FinOps Assistant for Zava FinOps Pro. Answer financial queries accurately using live Dataverse data. Response format rules: for budget queries, always return Cost Centre, Department, Allocated, Spent, Remaining, and Status. Format currency values with an INR prefix. When multiple cost centres match, return a formatted table. Prefix every status line with \[ON TRACK\], \[AT RISK\], or \[OVER BUDGET\]. Lead every summary with the most critical finding first. Behaviour rules: only answer from Zava FinOps Pro data and never estimate or invent figures. If data is unavailable, reply that you do not have that information and ask them to contact the finance team. Keep responses under 200 words unless a table is clearer. If a query is ambiguous, ask one clarifying question before answering.+++

1. Click **Save**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image58.png)

1. On the command bar in the top-right corner, click **Settings**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image59.png)

1. Under **Moderation**, set the **Content moderation level** slider to **Moderate**, then click **Save**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image60.png)

1. When the **Changes saved** confirmation appears, click **Close (X)** to return to the agent.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image61.png)

1. The FinOps Assistant agent is created and configured with the response format and behaviour rules it applies to every financial answer.


## Exercise 6: Add the Budget Allocations and Invoices Knowledge Source

In this exercise, you will connect the Budget Allocations and invoice table to the agent as a Dataverse knowledge source. This gives the agent live access to cost centre data without any topic being authored.

1. In **FinOps Assistant**, from the command bar, select **Knowledge**.

1. Click **Add knowledge**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image62.png)

1. In the **Add knowledge** dialog, select **Dataverse**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image63.png)

1. In the search field, enter the following text:

    +++Budget Allocations+++

1. Select the checkbox beside **Budget Allocations**, then click **Add to agent**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image64.png)

1. Record the logical table name shown beneath the display name, for example crf9c_BudgetAllocations. This value identifies the table in Dataverse filters and citations.

1. The Budget Allocations table is connected as a knowledge source and the agent can now answer questions from live cost centre data.

1. From the command bar, select Knowledge, then select Add knowledge.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image65.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image66.png)

1. Select Dataverse, enter +++Invoice+++ in the search field, select the checkbox beside Invoice, then select Add to agent.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image67.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image68.png)


## Exercise 7: Set the Agent Welcome Message

In this exercise, you will replace the default greeting with a welcome message that tells users exactly what the agent can do.

1. From the command bar, select **Topics**, then select the **System** tab.

1. Select the **Conversation Start** topic to open it on the canvas.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image69.png)

1. Click the **Message** node, delete the existing text, and enter the following:

    +++Hello! I am the Zava FinOps Pro Assistant. I can help you with budget status for any cost centre or department, spend-to-date and remaining allocation queries, invoice submission, purchase order compliance checks, and currency exchange rate information. What would you like to know?+++

1. On the command bar, click **Save**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image70.png)

1. The agent opens every conversation by stating the specific tasks it can perform.


## Exercise 8: Validate the Agent Against Financial Queries

In this exercise, you will test the agent against five financial queries and confirm that it answers accurately from Dataverse using the required response format.

1. In the top-right corner, click **Test** to open the test panel.

1. In the test panel message box, enter the following query and click the **Send** icon:

    +++What is the budget status for the IT department?+++

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image71.png)

1. Confirm the agent returns a table of IT cost centres with the most critical finding stated first and each row prefixed with a status marker.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image72.png)

1. Enter the following query and click the **Send** icon:

    +++Which cost centres are over budget?+++

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image73.png)

1. Confirm the agent returns only the cost centres marked \[OVER BUDGET\], each showing allocated, spent, and remaining values in INR.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image74.png)

1. Enter the following query and click the **Send** icon:

    +++How much has CC-007 spent to date?+++

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image75.png)

1. Confirm the agent returns a single spend figure for CC-007 and cites the Budget Allocations table as its reference.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image76.png)

1. Enter the following query and click the **Send** icon:

    +++Show me the Operations department budget summary+++

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image77.png)

1. Confirm the agent returns the Operations cost centres and identifies Enterprise Consulting as over budget.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image78.png)

1. Enter the following query and click the **Send** icon:

    +++What is the remaining budget for Priya Lakhani's cost centres?+++

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image79.png)

1. Confirm the agent filters by budget owner and returns the Treasury and Compliance and Corporate Finance Ops cost centres.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image80.png)

1. The FinOps Assistant answers financial questions accurately from live Dataverse data using the required response format.


## Exercise 9: Build and Register the Invoice Submission Flow Tool

In this exercise, you will build an agent flow that writes a new invoice to Dataverse and notifies the approval inbox, then register that flow as a tool on the FinOps Assistant. This moves the agent from answering questions to taking action.

### Create the Agent Flow

1. In **FinOps Assistant**, from the command bar, select **Tools**.

1. Click **Add a tool**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image81.png)

1. In the **Add tool** dialog, under **Create new**, select **Agent flow**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image82.png)

1. On the trigger card **When an agent calls the flow**, click **Add an input**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image83.png)

1. Under **Choose the type of user input**, select **Text**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image84.png)

1. In the input name field, enter the following name:

    +++VendorName+++

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image85.png)

1. Repeat the previous steps to add the following four inputs:

    1. **InvoiceNumber** — Text

    1. **InvoiceAmount** — Number

    1. **CostCentreID** — Text

    1. **InvoiceDescription** — Text

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image86.png)


### Add the Dataverse Row Action

1. On the canvas, click the **+** icon below the trigger card.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image87.png)

1. In the **Add an action** search box, enter +++Add a new row+++ and select **Add a new row** from **Microsoft Dataverse**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image88.png)

1. In the **Table name** dropdown, select **Invoice**, then click **Show all** beside **Advanced parameters**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image89.png)

1. Click into the **Amount** field, click the **Dynamic content** (lightning) icon, and select **InvoiceAmount**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image90.png)

1. Map the remaining fields using **Dynamic content** from the trigger:

    1. **Cost Centre**: CostCentreID

    1. **Description**: InvoiceDescription

    1. **Invoice Number**: InvoiceNumber

    1. **Vendor**: VendorName

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image91.png)

1. In the **Status** field, type the following static value:

    +++Pending Approval+++

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image92.png)

1. Click into the **Submitted Date** field and select the **fx** icon.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image93.png)

1. Enter the following expression and click **Add**:

    +++utcNow()+++

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image94.png)


### Add the Notification Email

1. On the canvas, click the **+** icon below the Dataverse action.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image95.png)

1. Search for +++Send an email+++ and select **Send an email (V2)** from **Office 365 Outlook**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image96.png)

1. In the **To** field, enter your own lab admin ID. The finance approval mailbox does not exist in a trial tenant, so your own address is used for verification.

1. In the **Subject** field, enter the following text and Replace the +++InvoiceNumber+++ with dynamic content at the end:

    +++\[ACTION REQUIRED\] New Invoice Pending Approval – InvoiceNumber** **+++

1. Click into the **Body** field and enter the following text, replace each dynamic content value where indicated:

    +++A new invoice has been submitted via the Zava FinOps Pro Assistant. Invoice Number: \[InvoiceNumber\] Vendor: \[VendorName\] Amount: INR \[InvoiceAmount\] Cost Centre: \[CostCentreID\] Description: \[InvoiceDescription\] Please review and approve in Power Apps.+++

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image97.png)


### Return the Result and Publish the Flow

1. On the **Respond to the agent** card, click **Add an output**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image98.png)

1. Under **Choose the type of output**, select **Text**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image99.png)

1. In the output name field, enter the following name:

    +++SubmissionStatus+++

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image100.png)

1. Click into the value field and enter the following text, inserting the +++InvoiceNumber+++ dynamic content where indicated:

    +++Submitted successfully. Invoice \[InvoiceNumber\] is pending approval+++

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image101.png)

1. On the command bar, click **Save draft**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image102.png)

1. Click the flow name at the top of the designer and enter the following name:

    +++ZFP Submit Invoice+++

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image103.png)

1. On the command bar, click **Save draft** and then **Publish**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image104.png)


### Register the Flow as an Agent Tool

1. Return to **FinOps Assistant**, select **Tools**, then click **Add a tool**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image105.png)

1. Select the **Flow** filter, then select **ZFP Submit Invoice**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image106.png)

1. Click **Add and configure**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image107.png)

1. Click into the **Description** field and enter the following text:

    +++Submits a new vendor invoice to the Zava FinOps Pro approval queue and notifies the finance approval inbox.+++

1. Click **Save**. The description is what tells the orchestrator when to call this tool, so it must describe the action clearly.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image108.png)

1. The FinOps Assistant can now write a new invoice record to Dataverse and notify the approval inbox rather than only answering questions.


## Exercise 10: Build and Register the Expense Classification Prompt Tool

In this exercise, you will create an AI Builder prompt that classifies a free-text expense description into a spend category, and register it on the agent as a prompt tool.

1. On the **Tools** page, click **Add a tool**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image109.png)

1. In the **Add tool** dialog, under **Create new**, select **Prompt**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image110.png)

1. Click the prompt name at the top of the panel and enter the following name:

    +++ZFP Expense Classifier+++

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image111.png)

1. Click into the **Instructions** area and enter the following text:

    +++You are an expense classification assistant for Zava FinOps Pro. Classify the expense below into exactly one of these categories: Travel, Software, Hardware, Consulting, Marketing, Training, Office Supplies, Other. Return only a valid JSON object with the keys category, confidence and reasoning. Confidence must be High, Medium or Low. Reasoning must be one sentence. Do not use markdown code blocks and do not add any preamble.+++

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image112.png)

1. Position your cursor at the end of the instructions, click **Add content**, then select **Text**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image113.png)

1. In the **Name** field, enter +++ExpenseDescription+++ and in the **Sample data** field, enter the following test value, then click **Close**:

    +++Monthly GitHub Copilot Business subscription for 25 developers+++

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image114.png)

1. In the **Output** dropdown, select **JSON**, then click **Test**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image115.png)

1. Review the model response and confirm it returns a JSON object with the category Software, a confidence value, and a one-sentence reason, then click **Save**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image116.png)

1. In the **Add tool** dialog, select **ZFP Expense Classifier**, then click **Add and configure**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image117.png)

1. Click into the **Description** field and enter the following text, then click **Save**:

    +++Classifies a free-text expense description into a Zava FinOps Pro spend category using generative AI.+++

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image118.png)


## Exercise 11: Add Over budget check Suggested Prompts to the Agent

In this exercise, you will configure the prompts that appear on the greeting screen so new users know what the agent can do without typing anything. Estimated time: 6 minutes.

1. From the command bar, select **Overview**.

1. Locate the **Suggested prompts** card and select **Add suggested prompt**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image119.png)

1. In the first prompt row, enter the following title and prompt:

    - Title: +++Over budget check+++
    - Prompt: +++Which cost centres are over budget?+++

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image120.png)


1. In the second prompt row, enter the following title and prompt:

    - Title: +++Submit an invoice+++
    - Prompt: +++Submit invoice INV-2026-1200 from Dell for 85000 against CC-007 for laptop refresh+++


1. In the third prompt row, enter the following title and prompt:

    - Title: +++Classify a spend item+++
    - Prompt: +++Classify this expense: Annual Zoom Pro subscription for 200 users+++


1. Select **Save**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image121.png)


## Exercise 12: Publish the Agent to Microsoft 365

In this exercise, you will publish the FinOps Assistant to the built-in demo website channel and test it outside Copilot Studio, exactly as a business user would experience it.

1. On the agent **Overview** page, click **Publish**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image122.png)

1. In the **Publish this agent** dialog, review the listed items and click **Publish**.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image123.png)

1. Click on the **Channels** and then select **Microsoft 365 and Teams channel.**

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image124.png)

1. Click on the **Add channel.**

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image125.png)

1. After adding channel click on the **See agent in Microsoft 365.**

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image126.png)

1. Click on the **Add** to add the agent in the Microsoft 365.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image127.png)

1. Then the agent is added successfully.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image128.png)


## Exercise 13: Test the agent

1. Select the given below suggested prompt and then click on the send icon.

    Title: Classify a spend item

    Prompt: Classify this expense: Annual Zoom Pro subscription for 200 users

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image129.png)

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image130.png)

1. Review the given copilot response.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image131.png)

1. In the Microsoft 365 FinOps Assistant agent enter the following query in the field and click the **Send** icon:

    +++Which cost centres are over budget?+++

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image132.png)

1. Confirm the agent returns the over-budget cost centres with the critical finding stated first.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image133.png)

1. Enter the following query and click the **Send** icon:

    +++What is the remaining budget for CC-011?+++

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image134.png)

1. Confirm the agent returns a negative remaining balance for CC-011 and cites the Budget Allocations table.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image135.png)

1. Enter the following query and click the **Send** icon:

    +++Submit invoice INV-2026-1105 from TCS for 240000 against CC-011 for cloud migration services+++

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image136.png)

1. Confirm the agent calls the **ZFP Submit Invoice** flow and returns a submission confirmation, then check that a new row exists in the **Invoice** table and that the notification email arrived in your mailbox.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image137.png)

1. Enter the following query and click the **Send** icon:

    +++Classify this expense: Annual Zoom Pro subscription for 200 users+++

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image138.png)

1. Confirm the agent calls the **ZFP Expense Classifier** tool and returns the category Software with a confidence value and reasoning.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image139.png)

1. Enter the following query and click the **Send** icon:

    +++ How many invoices are currently pending approval?+++

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image140.png)

1. Review the copilot response of the pending invoice approvals.

    ![](https://raw.githubusercontent.com/technofocus-pte/agntfybsnsprcsppdepth/refs/heads/main/Lab%204/media/image141.png)


## Conclusion

By delivering Zava FinOps Pro, Zava replaces its spreadsheet-and-email budget enquiries with a single agent grounded in live Dataverse data. Budget owners can check allocations, spend-to-date, and over-budget cost centres in a consistent INR-formatted response, submit vendor invoices straight into the approval queue, and have free-text expenses classified automatically — all from Microsoft 365 and Teams. The central finance team is freed from routine lookups and manual categorisation, reporting becomes consistent across every cost centre, and the same pattern of Dataverse as the source of truth, an agent grounded in that data, and tools that let it act can be extended to purchase order compliance, forecasting, and approval workflows across the wider organisation.
