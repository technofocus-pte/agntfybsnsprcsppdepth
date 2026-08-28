---
lab: 3
title: Build an End-to-End Intelligent Claims Automation Platform
description: AI-powered claims automation for streamlined intake, routing, tracking, and status updates.
level: 300
duration: 120 minutes
islab: true
primarytopics:
- Dataverse
- Power Automate
- Copilot Studio
---

# Lab 3 - Build an End-to-End Intelligent Claims Automation Platform

**Objective**

Build an end-to-end intelligent claims automation platform for
ZavaClaims Insurance using Copilot across the Power Platform, enabling
the claims team to capture, classify, record, route, and acknowledge
incoming claim emails automatically, while giving policyholders a
conversational way to check claim status — reducing manual triage,
rework loops, and assessor assignment delays across the intake process.

**Solution Focus Area**

ZavaClaims Insurance, a general insurance provider handling Motor,
Property and Travel claims, struggles to keep pace with the volume of
claims arriving into its shared mailbox. Claim notifications come in as
free-text emails, and a coordinator has to read each one, decide the
claim type, judge how urgent it is, key the details into a tracker,
notify the right assessment team, and reply to the claimant.

Because triage is manual and claim details live in mailboxes and
personal trackers rather than one record store, claims are
inconsistently classified, urgent cases sit in the queue alongside
routine ones, and claims move back and forth between teams before
landing with the correct assessor. Claimants have no way to check
progress except by calling the claims team, and leadership has no
reliable view of where cases stall or which activities generate the most
rework.

To address these gaps, ZavaClaims aims to first understand where its
intake process actually breaks down, then automate it end to end and
expose the result to claimants — using Copilot in the Dev One developer
environment to move from analysis to a working solution without building
each step by hand.

**Solution**

A Copilot-driven claims automation platform will be implemented to
modernize claims intake at ZavaClaims Insurance by:

- **Analyzing the Current Process:** Copilot in Process Mining will
  import the ZavaClaims event log, generate a process map, and answer
  questions on bottlenecks, rework loops, and end-to-end duration
  differences between Motor and Property claims, so automation targets
  the steps that genuinely fail.

- **Establishing the Data Model:** A **Claims** table will be created in
  Dataverse from the sample claim records, holding Claim Reference,
  Claimant Name, Claimant Email, Claim Type, Status, Urgency, Assigned
  Assessor, Claim Amount, Claim Summary, and Submitted Date as the
  single record store for the solution.

- **Generating the Intake Automation:** Copilot in Power Automate will
  generate the **ZVC Claims Intake Processor** cloud flow from a plain
  language description, triggered only by genuine claim emails and
  writing a uniquely referenced record into the Claims table on every
  arrival.

- **Adding AI Classification:** An AI Builder prompt, **ZVC Claim
  Triage**, will read each claim email and return claim type, urgency,
  sentiment and a one-line summary as structured JSON, which is parsed
  and written straight into the Claims table in place of manual triage.

- **Routing and Acknowledging Claims:** A Switch control will route each
  claim by its AI-classified type, sending tailored internal
  notifications for Motor, Property, Travel and unclassified claims,
  while the claimant receives a personalized acknowledgement carrying
  their claim reference and claim type.

- **Delivering Claimant Self-Service:** The **ZavaAssist** agent in
  Copilot Studio, grounded in the live Claims table, will let
  policyholders check claim status, identify their assigned assessor,
  and view claims by type or urgency, with a ZavaClaims welcome message
  and suggested prompts guiding every conversation.

## Exercise 1: Activate a Power Apps Trial and Select the Dev One Environment

In this exercise, you will activate a Microsoft Power Apps trial and
switch to the Dev One developer environment. This environment contains
Dataverse and will be used for every remaining exercise in this lab.

1.  Open your Edge browser and navigate to
    +++https://www.microsoft.com/en-in/power-platform/products/power-apps+++,
    the Microsoft Power Apps product page.

2.  On the product page, click the **Try for free** button.

    ![](./media/image1.png)

3.  In the **Let's get started** panel, enter the M365 admin tenant ID
    in the email field. Then select the check box. 

4.  Click **Start free** to begin the Developer Plan sign-up. 

    ![](./media/image2.png) 

5.  On the sign-in screen, enter the administrator password and click
    **Sign in**.

    ![](./media/image3.png)

6.  Confirm that the Power Apps home page opens and that the environment
    selector displays **Contoso (default)**.

    ![](./media/image4.png)

7.  Click the **Environment** selector in the top-right corner, then
    under Build apps with Dataverse select **Dev One**.

    ![](./media/image5.png)

8.  Confirm that the Power Apps home page reloads and the environment
    selector now displays **Dev One**.

    ![](./media/image6.png)

9.  The Power Apps trial is active and the Dev One environment is
    selected for all subsequent exercises.

## Exercise 2: Create the Claims Table in Dataverse

In this exercise, you will create the ZavaClaims Claims table by
importing the sample claim records into Dataverse. This table stores
every claim raised in the lab and is used later by the cloud flow and
the Copilot Studio agent.

1.  From the left navigation, select **Tables**.

2.  On the command bar, click **New table**, then select **Create new
    tables**.

    ![](./media/image7.png)

3.  On the Choose an option to create tables page, select **Import an
    Excel file or .CSV**.

    ![](./media/image8.png)

4.  In the Import an Excel or .CSV file dialog, click **Select from
    device**.

    ![](./media/image9.png)

5.  Browse to the **Lab Files** folder on Local Disk (C:), select
    +++ZavaClaims_SampleData_Claims+++ and click **Open**.

    ![](./media/image10.png)

6.  Confirm the file is listed with the Include toggle turned on, then
    click **Import**.

    ![](./media/image11.png)

7.  On the table designer canvas, confirm the imported table is named
    **Claims**.

    ![](./media/image12.png)

8.  Click the **More options (…)** icon on the Claims table card and
    select **View data**.

    ![](./media/image13.png)

9.  Click the **Claim Reference** column heading, and in the Edit column
    pane confirm the Data type is **Single line of text** and the Format
    is **Text**.

    ![](./media/image14.png)

10. Similarly, check or update all other columns and their data types.

    | Column            | Data type              | Format / Notes                          |
    | ----------------- | ---------------------- | --------------------------------------- |
    | Claimant Name     | Single line of text    | —                                       |
    | Claimant Email    | Single line of text    | Email                                   |
    | Claim Type        | Single line of text    | —                                       |
    | Status            | Single line of text    | Must remain text to allow **New** value |
    | Urgency           | Single line of text    | —                                       |
    | Assigned Assessor | Single line of text    | —                                       |
    | Claim Amount      | Decimal                | Not Currency                            |
    | Claim Summary     | Multiple lines of text | —                                       |
    | Submitted Date    | Date and time          | Date only                               |


    ![](./media/image15.png)

11. Click **Save and exit**.

    ![](./media/image16.png)

12. On the Done working? dialog, click **Save and exit** to create the
    table.

    ![](./media/image17.png)

13. The Claims table is created in Dataverse with six sample claim
    records and the correct column data types.

## Exercise 3: Activate a Power Automate Trial

In this exercise, you will activate a Microsoft Power Automate trial
using the same administrator account. Power Automate provides the
process mining, cloud flow and AI Builder capabilities used in the
exercises that follow.

1.  Open a new browser tab and navigate to
    +++https://www.microsoft.com/en-in/power-platform/products/power-automate+++
    the Microsoft Power Automate product page.

2.  On the product page, click the **Try for free** button.

    ![](./media/image18.png)

3.  In the Email field, enter your admin tenant email address and click
    **Next**.

    ![](./media/image19.png)

4.  When prompted that you are already a Microsoft customer, click
    **Sign in**.

    ![](./media/image20.png)

5.  On the Create your account step, select your **Country or Region**,
    enter your **Job title**, enter the **Company name**
    +++ZavaClaims+++, enter a valid **Business phone number**, then
    click **Get Started**.

    ![](./media/image21.png)

6.  On the Confirmation details step, click **Get Started**.

    ![](./media/image22.png)

7.  If the Microsoft Copilot page opens instead of Power Automate, click
    the app launcher, enter +++Power Automate+++ in the search field,
    and select **Power Automate**.

    ![](./media/image23.png)

8.  The Power Automate trial is active, and the Power Automate portal is
    available.

## Exercise 4: Analyse the Claims Process with Copilot in Process Mining

In this exercise, you will import the ZavaClaims event log into Process
Mining, generate a process map, and use Copilot to identify bottlenecks
and rework in the current claims process before building any automation.

1.  In Power Automate, click the **Environments** selector and select
    **Dev One**.

    ![](./media/image24.png)

2.  From the left navigation, select **Process mining**, then under
    Create new process select **Start here**.

    ![](./media/image25.png)

3.  In the New Process dialog, under Process Type select **Case ID
    process mining**, under Data source select **Dataflow**, enter the
    Process name +++ZavaClaims Claims Intake Analysis+++.

4.  Enter the Description +++Analysis of claims intake process to
    identify bottlenecks and automation candidates.+++ and click
    **Continue**.

    ![](./media/image26.png)

5.  On the Choose where to export dialog, leave the destination set to
    **PowerBI embedded** and click **Continue**.

    ![](./media/image27.png)

6.  On Step 1 of 4: Connect to your data, under New sources select
    **Text/CSV**.

    ![](./media/image28.png)

7.  On Step 2 of 4: Connection settings, select **Upload file**, then
    click **Browse…**.

    ![](./media/image29.png)

8.  Browse to the **Lab Files** folder on Local Disk (C:), select
    +++ZavaClaims_SampleEventLog+++ and click **Open**.

    ![](./media/image30.png)

9.  Under Connection credentials, click **Sign in** to authenticate the
    connection.

    ![](./media/image31.png)

10. Confirm the file shows Upload successful and click **Next**.

    ![](./media/image32.png)

11. On the Preview file data page, review the event log columns and
    click **Next**.

    ![](./media/image33.png)

12. On Step 3 of 4: Transform your data, review the loaded rows and
    click **Next**.

    ![](./media/image34.png)

13. On Step 4 of 4: Map your data, open the Attribute type list for
    **ClaimID** and select **Case ID**.

    ![](./media/image35.png)

14. Map the remaining attributes as Activity to **Activity**, Timestamp
    to **Event Start**, EndTimestamp to **Event End**, Assignee to
    **Resource** and ClaimType to **Case Level Attribute (first
    event)**, then click **Save and analyze**.

    ![](./media/image36.png)

15. On the Process overview tab, review the generated process map
    together with the case count, variant count, event count and
    activity count.

    ![](./media/image37.png)

16. Select the **Summary** tab and review the average case duration,
    self-loop, loop, and rework percentages.

    ![](./media/image38.png)

17. Click **Copilot** in the top-right corner, enter +++What are the top
    three bottlenecks in this claims process?+++ in the question field
    and click the **send icon.**

    ![](./media/image39.png)

18. Review the bottleneck analysis returned by Copilot.

    ![](./media/image40.png)

19. In the question field, enter +++How many cases involve rework and
    which activity causes the most rework loops?+++ and click the **send
    icon.**

    ![](./media/image41.png)

20. Review the rework analysis returned by Copilot.

    ![](./media/image42.png)

21. In the question field, enter +++What is the average end-to-end
    duration for Motor claims compared to Property claims?+++ and click
    the **send icon.**

    ![](./media/image43.png)

22. Review the duration comparison returned by Copilot.

    ![](./media/image44.png)

23. In the question field, enter +++Which step has the highest case
    frequency but also the longest average wait time before the next
    activity?+++ and click the **send icon.**

    ![](./media/image45.png)

24. The claims process is analysed and Copilot has identified the
    bottlenecks and rework loops that the automation in the following
    exercises will address.

## Exercise 5: Create the Claims Intake Flow with Copilot

In this exercise, you will use Copilot in Power Automate to generate the
Claims Intake Processor cloud flow from a plain language description,
and rename it ready for configuration.

1.  From the left navigation, select **Home**.

2.  In the Create your automation with Copilot field, enter +++When a
    new email arrives in my Outlook inbox, create a new row in the
    Dataverse Claims table with the email subject and the sender
    address, then send a reply email to the sender confirming that the
    claim has been received.+++ and click **Generate**.

    ![](./media/image46.png)

3.  Review the suggested flow, which contains the email trigger, an Add
    a new row action and a Reply to email action, then click **Keep it
    and continue**.

    ![](./media/image47.png)

4.  Make sure everything's ready page, confirm that Office 365 Outlook
    and Microsoft Dataverse both show a green check, then click **Create
    flow**.

    ![](./media/image48.png)

5.  Click the flow name in the top-left corner and rename the flow to
    +++ZVC Claims Intake Processor+++.

    ![](./media/image49.png)

6.  The Claims Intake Processor flow is generated by Copilot and is
    ready to be configured.

## Exercise 6: Configure the Email Trigger and the Dataverse Action

In this exercise, you will tighten the email trigger so that it only
fires for claim emails and map the Dataverse action so that every
incoming claim creates a correctly referenced record in the Claims
table.

1.  Click the **When a new email arrives** trigger, then click **Show
    all** beside Advanced parameters and confirm the Folder is set to
    **Inbox**.

    ![](./media/image50.png)

2.  Set **Include Attachments** to **Yes**, enter +++Claim+++ in the
    **Subject Filter** field, and set **Only with Attachments** to
    **No**.

    ![](./media/image51.png)

3.  Click the **Add a new row** action, set the Table name to
    **Claims**, then click **Show all** beside Advanced parameters.

    ![](./media/image52.png)

4.  Click in the **Claim Reference** field and select the **fx** icon to
    open the expression editor.

    ![](./media/image53.png)

5.  Enter the expression +++concat('ZVC-',
    formatDateTime(utcNow(),'yyyyMMdd-HHmmss'))+++ and click **Add**.

    ![](./media/image54.png)

6.  Set **Claim Summary** to the dynamic content **Body** and set
    **Claimant Email** to the dynamic content **From**.

    ![](./media/image55.png)

7.  Enter +++New+++ in the **Status** field and +++Medium+++ in the
    **Urgency** field.

    ![](./media/image56.png)

8.  Enter +++Unassigned+++ in the **Assigned Assessor** field.

    ![](./media/image57.png)

9.  Click in the **Submitted Date** field and select the **fx** icon.

    ![](./media/image58.png)

10. Enter the expression +++utcNow()+++ and click **Add**.

    ![](./media/image59.png)

11. Review the mapped fields and confirm Claim Reference, Claim Summary,
    Submitted Date, Status, Urgency and Assigned Assessor are all
    populated.

12. Click **Save**.

    ![](./media/image60.png)

13. The trigger only fires for claim emails and every claim email
    creates a Dataverse record with a unique ZVC claim reference.

## Exercise 7: Add AI Classification with AI Builder

In this exercise, you will add an AI Builder prompt that reads each
claim email and returns the claim type, urgency, sentiment and a short
summary as JSON. You will then parse that JSON and write the AI values
into the Claims table, replacing manual triage with AI classification.

1.  Click the **+** icon between the email trigger and the Add a new row
    action, search for +++AI Builder+++ and select **Run a prompt**.

    ![](./media/image61.png)

2.  Open the **Prompt** list and select **New custom prompt**.

    ![](./media/image62.png)

3.  In the Prompt name field, enter +++ZVC Claim Triage+++.

    ![](./media/image63.png)

4.  In the Instructions field, enter +++You are a claims triage
    assistant for ZavaClaims Insurance. Read the claim email below and
    classify it. Return only a valid JSON object with exactly these four
    keys: claim_type, urgency, sentiment, summary. claim_type must be
    Motor, Property, Travel or Unknown. urgency must be High, Medium or
    Low. sentiment must be Distressed, Neutral or Positive. summary must
    be one short sentence describing the incident. Do not use markdown
    code blocks and do not add any preamble or explanation.+++

5.  Cursor should be at the end of the instruction and then click **Add
    content**.

    ![](./media/image64.png)

6.  In the Input list, select **Text**.

    ![](./media/image65.png)

7.  In the Text input pane, enter the Name +++EmailSubject+++ and the
    Sample data +++Motor Claim - Rear end collision, vehicle
    undriveable+++ then click **Close**.

    ![](./media/image66.png)

8.  Click **Add content** again to add the second input.

    ![](./media/image67.png)

9.  Enter the Name +++EmailBody+++ and the Sample data +++My car was hit
    from behind this morning at a traffic light and it is undriveable.
    This is urgent, I am stranded and need immediate assistance. Policy
    ZVC-POL-88442.+++ then click **Close**.

    ![](./media/image68.png)

10. Open the **Output** list in the Model response pane and select
    **JSON**.

    ![](./media/image69.png)

11. Click **Test** to run the prompt against the sample data.

    ![](./media/image70.png)

12. Confirm the model response returns valid JSON containing claim_type,
    urgency, sentiment, and summary, then click **Save**.

    ![](./media/image71.png)

13. In the Run a prompt action, set **EmailBody** to the dynamic content
    **Body** and **EmailSubject** to the dynamic content **Subject**.

    ![](./media/image72.png)

14. Click the **+** icon below the Run a prompt action, search for
    +++Parse JSON+++ and select **Parse JSON** from Data Operation.

    ![](./media/image73.png)

15. Set the Content field to the **Text** output of the Run a prompt
    action, and in the Schema field enter :

    +++{"type":"object","properties":{"claim_type":{"type":"string"},"urgency":{"type":"string"},"sentiment":{"type":"string"},"summary":{"type":"string"}}}+++

    ![](./media/image74.png)

16. Open the **Add a new row** action and set **Claim Type** to the
    Parse JSON dynamic output **claim_type**.

    ![](./media/image75.png)

17. Set **Urgency** to the Parse JSON dynamic output **urgency**,
    replacing the static value entered earlier.

    ![](./media/image76.png)

18. Set **Claim Summary** to the Parse JSON dynamic output **summary**.

    ![](./media/image77.png)

19. Review the completed flow structure and confirm the Run a prompt and
    Parse JSON actions sit above the Add a new row action.

20. Click **Save**.

    ![](./media/image78.png)

21. Each claim email is now classified by AI Builder and the claim type,
    urgency and summary are written directly into the Claims table.

## Exercise 8: Route Claims with a Switch and Send Notifications

In this exercise, you will add a Switch control that routes each claim
according to the AI-classified claim type, send a different internal
notification for Motor, Property, Travel and unclassified claims, and
send a personalised acknowledgement to the claimant.

1.  Click the **+** icon below the Add a new row action, search for
    +++Switch+++ and select **Switch** from Control.

    ![](./media/image79.png)

2.  In the **On** field, select the **fx** icon, enter the expression
    +++body('Parse_JSON')?\['claim_type'\]+++ and click **Add**.

    ![](./media/image80.png)

3.  Click the **+** icon inside the Switch control to add the first
    case.

    ![](./media/image81.png)

4.  In the Case pane, enter +++Motor+++ in the **Equals** field.

    ![](./media/image82.png)

5.  Click the **+** icon inside the Motor case, search for +++Send an
    email+++ and select **Send an email (V2)** from Office 365 Outlook.

    ![](./media/image83.png)

6.  Set **To** to the administrator account.

7.  In the **Subject** field, enter the following text and insert the
    **Claim Reference** dynamic content at the end: 

    +++\[MOTOR CLAIM\] New claim received – +++ 

8.  Click into the **Body** field and enter the following text,
    replacing each bracketed value with dynamic content where
    indicated: 

    +++A new Motor claim has been logged by the Claims Intake Processor.
    Reference: \[Claim Reference\] Claimant: \[Claimant Email\] Urgency:
    \[urgency\] Summary: \[summary\] Please assign an assessor within 4
    business hours.+++ 

    ![](./media/image84.png)

1.  Click the **+** icon inside the Switch control to add the Second
    case. Enter +++Property+++ in the equal field.

    ![](./media/image85.png)

2.  Click the **+** icon on the second case, search for +++Send an
    email+++ and select **Send an email (V2)**.

    ![](./media/image86.png)

1.  Configure the Property notification with the Subject +++\[PROPERTY
    CLAIM\] New claim received -+++ followed by the **Claim Reference**
    dynamic content, and a body that asks for a site inspection and a
    property assessor within 8 business hours. Replace each bracketed
    value with dynamic content where indicated: 

    Body: +++A new Property claim has been logged by the Claims Intake
    Processor. Reference: \[Claim Reference\] Claimant: \[Claimant Email\]
    Urgency: \[urgency\] Summary: \[summary\]. Please arrange a site
    inspection and assign a property assessor within 8 business hours.+++

    ![](./media/image87.png)

2.  Click the **+** icon beside the existing cases to add a third case.

    ![](./media/image88.png)

3.  In the Case 3 pane, enter +++Travel+++ in the **Equals** field.

    ![](./media/image89.png)

4.  Click the **+** icon inside the Travel case and select **Send an
    email (V2)**.

    ![](./media/image90.png)

1.  Configure the Travel notification with the Subject +++\[TRAVEL
    CLAIM\] New claim received -+++ followed by the **Claim Reference**
    dynamic content, and a body that asks for policy validity and travel
    dates to be confirmed before an assessor is assigned within 8
    business hours. Replace each bracketed value with dynamic content
    where indicated: 

    Body: +++A new Travel claim has been logged by the Claims Intake
    Processor. Reference: \[Claim Reference\] Claimant: \[Claimant Email\]
    Urgency: \[urgency\] Summary: \[summary\]. Please arrange a site
    inspection and assign a property assessor within 8 business hours.+++

    ![](./media/image91.png)

2.  Click the **+** icon inside the **Default** case.

    ![](./media/image92.png)

3.  Search for +++Send an email+++ and select **Send an email (V2)**.

    ![](./media/image93.png)

4.  Configure the unclassified notification with the Subject
    +++\[UNCLASSIFIED CLAIM\] Manual review required -+++ followed by
    the **Claim Reference** dynamic content, and a body that asks the
    team to review the original email and set the correct claim type
    manually.

    Body: +++The Claims Intake Processor could not confidently classify
    this claim. Reference: \[Claim Reference\] Claimant: \[Claimant
    Email\] Returned claim type: \[claim_type\] Urgency: \[urgency\]
    Summary: \[summary\] Original email subject: \[Subject\] Please review
    the original email, set the correct claim type in the Claims table,
    and route it to the appropriate assessment team manually.+++

    ![](./media/image94.png)

5.  Click the **+** icon below the Switch control, search for +++Send an
    email+++ and select **Send an email (V2)**.

    ![](./media/image95.png)

6.  Click the **settings** icon beside the To field and select **Use
    dynamic content**.

    ![](./media/image96.png)

7.  Set **To** to the dynamic content **From**, enter the Subject
    +++Your ZavaClaims claim has been received - Reference:+++ followed
    by the **Claim Reference** dynamic content, and enter an
    acknowledgement body that includes the **Claim Reference** and
    **Claim Type** dynamic content and confirms an assessor will make
    contact within 2 business days.

    Body: +++Dear Claimant, Thank you for contacting ZavaClaims Insurance.
    Your claim has been received and logged under reference \[Claim
    Reference\]. Claim type: \[claim_type\]. A dedicated assessor will be
    in contact within 2 business days. If you need to provide additional
    documents, please reply to this email quoting your reference number.
    ZavaClaims Claims Team+++

    ![](./media/image97.png)

8.  Right-click the **Reply to email** action created by Copilot and
    select **Delete**.

    ![](./media/image98.png)

9.  Click **Save**.

    ![](./media/image99.png)

10. Claims are routed by AI classified claim type, the correct internal
    team is notified, and the claimant receives a personalised
    acknowledgement.

## Exercise 9: Test the Claims Intake Flow End to End

In this exercise, you will trigger the completed flow with a live claim
email and confirm that classification, record creation, routing and
acknowledgement all run successfully.

1.  In the flow designer, click **Test**.

    ![](./media/image100.png)

2.  In the Test Flow pane, select **Manually** and click **Test**.

    ![](./media/image101.png)

3.  Open your personal email and send an email to the admin tenant id
    with the Subject +++Motor Claim - Rear end collision, vehicle
    undriveable+++ and the body +++My car was hit from behind this
    morning at a traffic light and it is undriveable. This is urgent, I
    am stranded and need immediate assistance. Policy ZVC-POL-88442.+++
    then click **Send**.

    ![](./media/image102.png)

4.  Return to Power Automate and confirm the message Your flow ran
    successfully, with a green check on the trigger, Run a prompt, Parse
    JSON, Add a new row, Switch and the Motor case.

    ![](./media/image103.png)

5.  Open the claimant mailbox and confirm the acknowledgement email has
    arrived with the generated claim reference and the classified claim
    type.

    ![](./media/image104.png)

6.  The Claims Intake Processor runs end to end, classifying, recording,
    routing and acknowledging a live claim email.

## Exercise 10: Activate Copilot Studio in the Dev One Environment

In this exercise, you will activate a Copilot Studio trial and point it
at the Dev One environment so that the agent you build can read the
Claims table created earlier.

1.  Open a new browser tab and navigate to
    +++https://www.microsoft.com/en-us/microsoft-365-copilot/microsoft-copilot-studio+++
    the Microsoft Copilot Studio product page. 

2.  Click **Sign in to Copilot Studio**. 

    ![](./media/image105.png) 

3.  Enter M365 admin tenant ID in the **Sign in** field, then click
    **Next**. 

    ![](./media/image106.png) 

4.  Enter the password in the **Enter password** field, then click
    **Sign in**. 

    ![](./media/image107.png) 

5.  When prompted with **Stay signed in?**, click **Yes**. 

    ![](./media/image108.png) 

6.  Wait while Copilot Studio loads. The address bar shows that the
    portal has opened in the **Default** environment. 

    ![](./media/image109.png) 

7.  If copilot studio not able load, open a new browser tab and navigate
    to +++https://admin.powerplatform.microsoft.com+++ the Power
    Platform admin centre. 

8.  From the left navigation, select **Manage**, then select
    **Environments**, then select **Dev One**. 

    ![](./media/image110.png) 

9.  On the **Dev One** details page, copy the **Environment ID** value. 

    ![](./media/image111.png) 

10. Return to the Copilot Studio tab, replace the environment identifier
    in the address bar with the copied **Environment ID**, then press
    **Enter**. 

    ![](./media/image112.png) 

11. On the **Select a team** dialog, click **start a trial**. 

    ![](./media/image113.png) 

12. Under **Let's get you started**, click **Continue**. 

    ![](./media/image114.png) 

13. On the setup screen, provide the required information, then click
    **Get Started**: 

14. Select your **Country or Region** from the dropdown list 

15. Enter your **Job title** to indicate your role 

16. Enter a **Company name** 

17. Enter a valid **Business phone number** 

    ![](./media/image115.png) 

18. On the **Confirmation details** step, click **Get Started**. 

    ![](./media/image116.png) 

19. Confirm the Copilot Studio home page loads and that the environment
    selector shows **Dev One**.

    ![](./media/image117.png)

20. The Copilot Studio trial is active, and the portal is running in the
    same Dev One environment that holds the Dataverse tables.

## Exercise 11: Build the ZavaAssist Agent

In this exercise, you will create the ZavaAssist agent, give it the
instructions that govern how it answers claimants, and connect it to the
Claims table so that every answer is grounded in live Dataverse data.

1.  From the left navigation, select **Agents**, then click **Create
    blank agent**.

    ![](./media/image118.png)

2.  In the Name your agent dialog, enter +++ZavaAssist+++ and click
    **Create**.

    ![](./media/image119.png)

3.  When the agent is provisioned, click **Edit** in the Details
    section.

    ![](./media/image120.png)

4.  In the Description field, enter +++Helps ZavaClaims policyholders
    check the status of an insurance claim, find their assigned
    assessor, and understand what happens next, using live Dataverse
    claim data.+++ and click **Save**.

    ![](./media/image121.png)

5.  In the Instructions section, click **Edit**.

    ![](./media/image122.png)

6.  Enter +++You are ZavaAssist, the claims assistant for ZavaClaims
    Insurance. Answer claimant questions accurately using live Dataverse
    claim data. Response format rules: for a claim status query, always
    return Claim Reference, Claim Type, Status, Urgency, Assigned
    Assessor and Submitted Date. Format currency values with an INR
    prefix. When multiple claims match, return a formatted table. Lead
    every summary with the most important finding first. Behaviour
    rules: only answer from ZavaClaims data and never estimate or invent
    a status, assessor name, amount or date. If a claim reference is not
    found, say that you cannot locate it and ask the claimant to check
    the reference. If the claimant seems distressed or frustrated,
    acknowledge their situation in one sentence before answering. Keep
    responses under 150 words unless a table is clearer. Never discuss
    another policyholder claim. If you cannot help, tell the claimant to
    call ZavaClaims on 1800-ZAVA.+++ and click **Save**.

    ![](./media/image123.png)

7.  In the Knowledge section, click **Add knowledge**.

    ![](./media/image124.png)

8.  In the Add knowledge dialog, select **Dataverse**.

    ![](./media/image125.png)

9.  In the search field enter +++Claims+++, select the **Claims** table
    checkbox, then click **Add to agent**.

    ![](./media/image126.png)

10. Confirm the Claims table is selected before the dialog closes.

    ![](./media/image126.png)

11. Confirm the **Claims** table now appears in the Knowledge section of
    the agent.

    ![](./media/image127.png)

12. The ZavaAssist agent is created with claim handling instructions and
    is grounded in the live Claims table.

## Exercise 12: Configure the Welcome Message and Suggested Prompts

In this exercise, you will replace the default greeting with a
ZavaClaims welcome message and add suggested prompts so that claimants
know what the agent can do as soon as a conversation starts.

1.  Select the **Topics** tab, select the **System** filter, then select
    **Conversation Start**.

    ![](./media/image128.png)

2.  In the Message node, replace the existing text with +++Hello! I am
    ZavaAssist, the ZavaClaims Insurance claims assistant. I can check
    the status of your claim, tell you who your assigned assessor is,
    explain what happens next in the claims process, and summarise
    claims by type or urgency. Please have your claim reference ready -
    it looks like ZVC-2026-0001. What would you like to know?+++ and
    click **Save**.

    ![](./media/image129.png)

3.  Return to the **Overview** tab and click **Add suggested prompts**.

    ![](./media/image130.png)

4.  Enter the Title +++Check a claim+++ with the Prompt +++What is the
    status of claim ZVC-2026-0001?+++, the Title +++Urgent claims+++
    with the Prompt +++Which claims are currently marked as High
    urgency?+++, and the Title +++Motor claims summary+++ with the
    Prompt +++Show me a summary of all Motor claims and their
    assessors+++ then click **Save**.

    ![](./media/image131.png)

5.  Confirm the three suggested prompts are listed in the Suggested
    prompts section.

    ![](./media/image132.png)

6.  The agent opens every conversation with a ZavaClaims welcome message
    and three suggested prompts.

## Exercise 13: Publish and Test ZavaAssist

In this exercise, you will publish the agent and test it against live
claim data to confirm that it retrieves individual claim details and can
summarize claims across the whole table.

1.  In the top-right corner of the agent, click **Publish**.

    ![](./media/image133.png)

2.  In the Publish this agent dialog, click **Publish**.

    ![](./media/image134.png)

3.  In the Test your agent pane, enter +++What is the status of claim
    ZVC-2026-0001?+++ and click the send icon.

    ![](./media/image135.png)

4.  Review the returned claim summary and confirm it lists the Claim
    Reference, Claim Type, Status, Urgency, Assigned Assessor, and
    Submitted Date drawn from the Claims table.

    ![](./media/image136.png)

5.  Enter +++Which claims are currently marked as High urgency?+++ and
    click the send icon.

    ![](./media/image137.png)

6.  Review the response and confirm the agent returns the high urgency
    claims as a formatted table sourced from the Claims knowledge
    source.

    ![](./media/image138.png)

7.  The ZavaAssist agent is published and answers claimant status
    queries using live ZavaClaims Dataverse data.

## Lab Completion

You have successfully completed this lab. Working in the Dev One
environment, you built a complete intelligent claims automation platform
for ZavaClaims Insurance, moving a claim from an unread email all the
way through to a claimant self-service answer without manual
intervention at any stage.

You began by analysing the existing process rather than assuming where
it failed, using Copilot in Process Mining to surface the real
bottlenecks, rework loops and duration differences hidden in the claims
event log. You then created the Claims table in Dataverse as the single
record store for the solution, and used Copilot in Power Automate to
generate the Claims Intake Processor flow from a plain language
description instead of building it action by action. From there, you
strengthened that flow, filtering the trigger so it responds only to
genuine claim emails, generating a unique claim reference through an
expression, and adding an AI Builder prompt that reads each email and
returns the claim type, urgency, sentiment and summary as structured
JSON. That AI classification then drove a Switch control which routed
Motor, Property, Travel and unclassified claims to the correct internal
team, while the claimant received a personalised acknowledgement
containing their reference. Finally, you built and published the
ZavaAssist agent in Copilot Studio, grounding it in the live Claims
table so that policyholders can check status, identify their assessor
and view high-urgency claims on demand.

Across these exercises, you have seen that Copilot is not a single
feature but a capability that runs through the whole platform: it
analyses a process before you automate it, drafts the automation, adds
intelligence where keyword matching would break, and exposes the result
conversationally to the people who need it. The result for ZavaClaims is
a claim that classifies, records, routes and acknowledges itself in
seconds, giving the claims team their time back for the complex
assessments that genuinely need human judgement.
