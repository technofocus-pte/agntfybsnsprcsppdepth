---
lab:
title: Build a plan and canvas app for a real estate solution with Copilot in Power Apps
description: Build a plan and canvas app for Contoso Realty's property showing
operations using Copilot in Power Apps, enabling agents to schedule,
track, and update client showings from a single app while capturing
structured feedback, reducing scheduling conflicts and manual
coordination effort across the sales team.
level: 300
duration: 60 minutes
islab: true
primarytopics:
- Power Apps
- Copilot Studio
---

# Lab 1: Build a plan and canvas app for a real estate solution with Copilot in Power Apps

**Objective**

Build a plan and canvas app for Contoso Realty's property showing
operations using Copilot in Power Apps, enabling agents to schedule,
track, and update client showings from a single app while capturing
structured feedback, reducing scheduling conflicts and manual
coordination effort across the sales team.

**Solution Focus Area**

Contoso Realty, a residential property brokerage, struggles to
coordinate the growing number of property showings handled by its
agents. Showing requests arrive through calls, emails, and walk-ins, and
are tracked across spreadsheets and personal calendars maintained by
individual agents.

Because showing details such as property, buyer, assigned agent, date,
and current status live in disconnected places, agents frequently
double-book properties, clients receive inconsistent confirmations, and
there is no reliable record of which showings were completed, confirmed,
or cancelled. Sales leadership also has no consolidated view of showing
activity or buyer feedback to guide pricing and property improvement
decisions.

To address these gaps, Contoso Realty aims to define a structured
solution plan and deliver a purpose-built app for its agents, using
Copilot in Power Apps to move from a business description to a working
solution without lengthy design and development cycles.

**Solution**

A Copilot-generated plan and canvas app will be implemented to modernize
showing management at Contoso Realty by:

- **Defining Requirements and Processes**: Copilot's Requirements and
  Processing agents will analyze the business scenario to identify user
  roles, needs, and the core processes for scheduling and managing
  property showings and reviewing property feedback.

- **Designing the Data Model**: The Data Agent will propose Dataverse
  tables to store property, buyer, agent, client, and showing records,
  with a Status choice column standardized to Pending, Confirmed,
  Completed, and Cancelled so every showing has a consistent lifecycle
  state.

- **Proposing the Technology Stack**: The Solution Agent will recommend
  the Power Platform components required to deliver the scenario, which
  are then saved into a solution for ongoing development.

- **Delivering a Canvas App**: A canvas app generated directly from the
  plan will let agents browse existing showings in a records gallery
  displaying the showing title and its current status, create new
  showing requests through a form, and update details as plans change.

- **Storing and Tracking Data**: All showing records and status changes
  will be stored in Dataverse, giving the brokerage a single source of
  truth for reporting on showing activity and improving how properties
  are presented to buyers.

## Exercise 1: Activate the Power Apps Developer Plan and Select the Lab Environment

In this exercise, you set up the foundation for the entire lab. You
navigate to the Power Apps product page, sign up for the free Power Apps
Developer Plan using the M365 admin tenant credentials, and complete the
sign-in process. Once the Power Apps home page loads and the welcome
message confirms the plan is active, you verify that you are working in
the **Dev One** developer environment, switching to it through the
environment selector if needed. Every component you build in the later
exercises is created and stored in this environment.

1.  Open your **Edge** browser and navigate to
    +++https://www.microsoft.com/en-in/power-platform/products/power-apps+++
    the Power Apps product page.

2.  On the product page, locate and click the **Try for free** button.

    ![](./media/image1.png)

3.  In the **Let's get started** panel, enter the M365 admin tenant ID
    in the email field. Then select the check box.

4.  Click **Start free** to begin the Developer Plan sign-up.

5.  Enter the administrator password in the **Enter password** field,
    then click **Sign in**.

    ![](./media/image2.png)

6.  Wait for the Power Apps home page to load. The welcome message
    confirms that the Developer Plan is active.

    ![](./media/image3.png)

7.  Ensure that you are in your developer environment - **Dev One**. If
    not, click on the environment selector and select **Dev One**.

    ![](./media/image4.png)

## Exercise 2: Create a plan

In this exercise, you use Copilot's plan designer to turn a
plain-language business description into a structured solution
blueprint. Starting from the **Plans** area, you enter a prompt to
manage real estate showings and let Copilot's collaborating agents work
through the plan step by step: the Requirements Agent identifies user
roles and needs, the Processing Agent generates the supporting business
processes, and the Data Agent proposes the Dataverse tables that will
hold your business data. You open the data workspace to inspect the
generated tables, review the Property Showing table, and use Copilot
prompts to either update or create the **Status** choice column with the
values Pending, Completed, Confirmed, and Cancelled. Finally, the
Solution Agent proposes the technology components for the scenario, and
you save the plan into a solution.

1.  From the left navigation pane, select **Plans**.

    ![](./media/image5.png)

2.  Select **Create a plan**.

    ![](./media/image6.png)

3.  Enter the given prompt in the text box and then select **Generate**.

    **Prompt**: +++Create a plan to manage real estate showings+++

    ![](./media/image7.png)

4.  Copilot opens the plan and analyzes your business scenario based on
    the description.

    ![](./media/image8.png)

5.  From the top-right corner of the screen, select the **drop-down**
    menu (View all) of the agents list to view the presence status of
    all collaborators, including plan agents.

    ![](./media/image9.png)

6.  When a plan starts, the **Requirements Agent** identifies user needs
    based on your description. Review the user roles and needs.
    Select **"Looks good "** to move to the next step and generate a
    data model.

    ![](./media/image10.png)

7.  The processing agent has generated a set of processes. Here, the
    processing agent has generated the **Schedule and Manage Property
    Showings** and Review and **Improve Property Based on Showing
    Feedback** processes. In your case, the process name can differ.

    ![](./media/image11.png)

8.  Select **Looks good** to proceed to the next step and generate data
    tables.

    ![](./media/image12.png)

9.  Next, the **Data Agent** suggests tables to store your business
    data.

    ![](./media/image13.png)

10. Select **Edit** and use Copilot to describe what you want to change
    or add.

    ![](./media/image14.png)

11. Select **Show details** to view the data in a diagram and edit it.
    It opens the data workspace.

    ![](./media/image15.png)

12. You can see all the tables in the data workspace. 

    ![](./media/image16.png)

13. Select the **Showing** table (here select Property Showing), then
    select **View data** from the top bar.

    ![](./media/image17.png)

14. You can see the data stored in your table.

    ![](./media/image18.png)

15. Check if you have the **Status** column in the **Showing** table
    (here, Property Showing table).

    ![](./media/image19.png)

16. If you have the Status column in the Showing table, but it has the
    choices listed as – Scheduled, completed, or cancelled, then use the
    given prompt to change the options. Select the **Submit** icon.

    **Prompt**: +++Update the Status column to use the following choices:
    Pending, Completed, Confirmed, and Cancelled.+++

    ![](./media/image20.png)

17. You can see that the choices are now updated.

    ![](./media/image21.png)

18. If you don’t have a Status column, then use the given prompt to
    create it.

    **Prompt**: +++Add a column named Status of type Choice with the
    options Pending, Completed, Confirmed, and Cancelled.+++

19. Select **Back**.

    ![](./media/image22.png)

20. When you're ready to generate the technology proposal,
    select **Looks good** to continue.

    ![](./media/image23.png)

21. The **Solution Agent** analyzes the plan and proposes technologies
    tailored to solve your business problem.

    ![](./media/image24.png)

22. Select **Looks good** to accept the proposed technologies.

    ![](./media/image25.png)

23. Select the **Save** icon to save the solution.

    ![](./media/image26.png)

24. Keep the selection as is under the **Select an existing solution**
    option and then select **Save**.

    ![](./media/image27.png)

25. Do not navigate from the current window.

## Exercise 3: Create a canvas app

In this exercise, you generate and refine an app directly from the plan
you created. From the Technology section, you create the canvas app
suggested by the Solution Agent, which opens in Power Apps Studio with
screens already wired to your data. You then customize the Property
Showings screen by changing the record gallery layout to **Title and
subtitle** and binding the subtitle to the Status column, so each
showing displays its current state at a glance. In the second task, you
test the app in play mode, enable the gallery's flexible height so the
**+ New** option appears correctly, and create a new showing record by
filling in the showing title, duration, date and time, status, property,
buyer, agent, and client. You then save the app and return to the Power
Apps home page with a working showing management solution.

### Task 1: Create a canvas app for a real estate solution

1.  Under the **Technology** section, hover over the Canvas app (here,
    the name of the Canvas app is Showing Organizer) and then select the
    **+ icon** to create the app.

    ![](./media/image28.png)

2.  You will now navigate to the Power Apps designer. Select **Skip** on
    the **Welcome to Power Apps Studio** pop-up.

    ![](./media/image29.png)

3.  From the left navigation pane, click the **Property Showings**
    screen.

    ![](./media/image30.png)

4.  Expand the **Property Showings** screen.

    ![](./media/image31.png)

5.  Select the **Record gallery** from the left navigation pane. On the
    canvas, select the **edit** icon. **Property Showings** screen \>
    **ScreenContainer4** \> **BodyContainer4** \> **SidebarContainer4**
    \> **RecordsGallery4**.

    ![](./media/image32.png)

6.  Select **Layout** \> **Title and subtitle**.

    ![](./media/image33.png)

7.  Select the recently added **Subtitle**.

    ![](./media/image34.png)

8.  In the formula bar, set the **Text** value to the given formula.
    Enter **ThisItem.Status** and then select **ThisItem.’Status
    (crc5b_status)’** from the suggestions. Note that the **crc5b**
    value is different in your case.

    ![](./media/image35.png)

9.  You can now see that the **Text** property for the **Subtitle** is
    updated.

    ![](./media/image36.png)

10. Select **Save**.

    ![](./media/image37.png)

### Task 2: Test the app

1.  Make a new request for a property that shows in the app by selecting
    the **Play** button from the top bar of the screen.

    ![](./media/image38.png)

2.  Check if + New option is visible. If not, then close the testing
    window by selecting the **X** in the upper-right corner.

    ![](./media/image39.png)

3.  In the left pane, select the **+ New** button.

    ![](./media/image40.png)

4.  Select the **Properties** icon from the horizontal menu to open the
    **Properties** pane. Select **RecordsGallery4**, and then set the
    **Flexible height** toggle to **On**.

    ![](./media/image41.png)

5.  Now, select the **Play** button.

    ![](./media/image42.png)

6.  To change the app window size, expand the **Browser** option, and
    then select **Canvas size**.

    ![](./media/image43.png)

7.  Fill in the fields with the following information. Select the check
    mark in the upper-right corner of the screen.

    **Showing Title:** Show 7

    **Duration Hours:** 1

    **Showing DateTime:** Any future data

    **Status:** Confirmed (You can select any from the drop-down list)

    **Property:** 123 Maple St (You can choose any from the drop-down
    list)

    **Buyer:** Daniel Martinez

    **Agent:** John Smith (You can choose any from the drop-down list)

    **Client:** Jessica Lee (You can choose any from the drop-down list)

    ![](./media/image44.png)

8.  Select the **X** in the upper-right corner to close out of the app.

    ![](./media/image39.png)

9.  If a dialog appears saying, **Did you know?**, select **OK**.

    ![](./media/image45.png)

10. Select the **Save** button to save the new app that you created.

    ![](./media/image46.png)

11. Exit the app to return to the Power Apps home page.

## Conclusion

In this lab, you built an end-to-end real estate showing management
solution using Copilot in Power Apps. You activated the Power Apps
Developer Plan and worked within the Dev One environment, then created a
plan from a single business prompt and watched Copilot's Requirements,
Processing, Data, and Solution agents translate that description into
user roles, business processes, a Dataverse data model, and a
recommended technology stack. You reviewed and refined the generated
data model by standardizing the Status choice column on the Property
Showing table, then generated a canvas app directly from the plan and
customized its record gallery to display each showing's title and
current status. Finally, you tested the app by creating a new showing
record end to end. Together, these steps demonstrate how a Copilot-first
development approach can take a real business scenario from idea to a
working, data-backed application in a fraction of the time traditional
design and development would require.
