+++
date = "2016-07-29T16:18:23+05:30"
draft = true
title = "Build a Suggestion Box App with Lightning Experience"

+++

# Build a Suggestion Box App with Lightning Experience

## Introduction
In this project, you learn how to build an application on App Cloud from start to finish, without writing any code. If you're new to App Cloud, the goal is to introduce you to the basics of app building. These point-and-click skills apply whether or not you plan to write code on the platform. If you're familiar with the App Cloud Admin features—managing users and security, customizing standard objects, and so on—the goal is to apply those skills to developing new applications. You need a Developer Edition org to complete this project. If you don't have one, you can sign up [here](https://developer.salesforce.com/signup).

You will build a suggestion management app that allows employees to submit suggestions and track their status using these steps:

* Create the app definition and a data model for the application. This simple application requires only one custom object.
* Modify the user interface on the browser and on Salesforce1 Mobile using page layouts, compact layouts, and global actions.
* Automate calculations, data quality, and database updates to improve the user experience using formulas, validation rules, and a process builder.
* Define reports, charts, and dashboards to track the status of suggestions and analyze how well the company manages suggestions.

Note: Instead of creating a Suggestions custom object, you could use Cases, a Salesforce Service Cloud standard object. But in this example, we want to explore building a custom app without using Service Cloud.

# Creating a Suggestion Box App

### What You’ll Do

* Create a Force.com app that includes standard collaboration and analytics features.
* Familiarize yourself with the app’s user interface in the browser and in the Salesforce1 Mobile app.

#### Step 1: Create an App and a Custom Object

1. Log in to your Developer Edition (DE) org using your username (in the form of an email address) and password.
2. From the Force.com Home page, click on the gear icon | Setup Home | Platform Tools | Apps | Apps
3. On the Apps page, click on the Quick Start button
4. Fill in the form as follows:
         * App: Suggestion Box
         *  Label: Suggestion
         * Plural Label: Suggestions

5. Click Create. Right away you see some of the functionality that’s automatically added.
6. You’re all set! Close the popup and you can access the App you just created by clicking on the grid icon on the top right

#### Step 2: Tour the App

1. App Launcher — Displays all a user's available Salesforce apps, and any connected apps an administrator installs for the organization
2. App Name — Displays the App Name you have navigated from the App Launcher
3. Items — Displays the list of Tabs available inside the App
4. Navigation menu — provides a streamlined and compact interface to navigate through your organization's various apps and tabs


#### Step 3: Try Out the Mobile App
When you create an app with App Cloud, you automatically create a mobile version of the app. You can access the Salesforce1 mobile app in a number of ways:
* As a downloadable mobile app (Salesforce1) that you install on your phone from the Apple AppStore or Google Play
* By navigating to login.salesforce.com using a mobile browser
* By using the Chrome Developer Tools

For this step, we'll use the last option.

1. Open a new tab in your Chrome browser and open the Developer Tools by clicking View | Developer | Developer Tools
2. Click the Toggle Device Mode button to simulate your browser as a mobile device.

3. To simulate the Salesforce1 app in your browser, copy and paste in the URL from the previous tab. Remove the part of the URL immediately after salesforce.com/. For example:

4. Append /one/one.app to the end of the URL after salesforce.com to start the Salesforce1 Application simulator. For example:

5. If the display is too small, change the size to 100%.

6. Click the  icon to open the left navigation bar.
7. Find the Suggestions object under Recent. You may need to click Show More to see it. Click Suggestions to display your recently viewed suggestions, if you have any.
8. Click New to create a suggestion.

9. Enter a sample suggestion (maybe something like "Locker Rooms"), and click Save.

With just one button click, you're on your way to building a mobile app!

#### Resources
* [ Salesforce Platform Basics > Getting Started with the Platform ] (https://developer.salesforce.com/trailhead/starting_force_com/starting_intro)
* [ Salesforce1 Mobile Basics > Getting Started with the Salesforce1 Mobile App ] (https://developer.salesforce.com/trailhead/salesforce1_mobile_app/salesforce1_mobile_app_intro)

# Adding Custom Fields

### What You’ll Do
* Create custom fields with the Custom Field Wizard and the Schema Builder.
* Try the app in the browser and in the Salesforce1 Mobile app.


By default, your custom object has some standard fields on it. You need to add more fields to store relevant information about each suggestion. Fields can store many types of data. Feel free to explore these later as you extend your app. For now, let’s add a few that we know we need.

You can add a new field this way from the Force.com Home Page:

1. Click on the Gear icon | Setup Home | Platform Tools | Objects and Fields | Object Manager
2. Click the name of your object (in this example, click Suggestion)
3. Scroll down to the Custom Fields & Relationships section.
4. Click New.

#### Step 1: Add a Description field

This field will be used by employees when they explain their suggestion. You’ll create the field as a rich text area so that they can change font styles, add pictures, etc.

Create the Suggestion Description field as follows:

1. For Data Type, select Text Area (Rich), and click Next.
2. Fill in the custom field details:
 * Field Label: Suggestion Description
 * Length: 32,768
 * Visible Lines: 10
 * Field Name: Suggestion_Description
   Help Text: Please enter as much detail - including pictures - around this suggestion so we clearly understand your idea.
3. Click Next.
4. Click Next again to accept the default field visibility and security settings.
5. Click Save & New to add the Suggestion Description to the page layout, save the Suggestion Description field, and return to the first step of the wizard.

#### Step 2: Add a Category Field

This field will help us categorize each suggestion. It will also make it easier for us to report on what types of suggestions we get the most.

Create the Suggestion Category field as follows:

1. For Data Type, select Picklist, and click Next.
2. Fill in the custom field details:
 * Field Label: Suggestion Category
 * Check the radio button next to Enter values for the picklist, with each value separated by a new line.
 * Enter the following values for the picklist (one per line)
       - Customer Service
       - Employee Services
       - Facilities/ IT
       - Kitchen Snacks
       - Other
 * Leave the Sort Values Alphabetically - Unchecked.
 * Leave the Use first value as default value - Unchecked.
 * Leave the defaults for the remaining fields, and click Next.
3. Click Next again to accept the default field visibility and security settings.
4. Click Save to add the Suggestion Category to the page layout and save the Suggestion Category field.

#### Step 3: Try Out the App

Check your work by returning to the Suggestions tab and clicking the New button. You should see that your two custom fields were added to the Suggestion page. Fill in a sample suggestion and click Save.

Try adding a record in the Salesforce1 Mobile App, too. Note that the Suggestion page layout is the same on the browser as it is in the mobile app. That's the power of "build once, deploy anywhere" metadata.

#### Step 4: Viewing Objects in Schema Builder

Schema Builder provides a dynamic environment to add new custom objects, custom fields, and relationships to your Salesforce database. It also eliminates the need to click from page to page to add a new custom field to an object or to find the details of a relationship.

Let’s take a look at the Suggestion custom object in the Schema Builder.

1. Click the gear icon | Setup Home | Search for Schema Builder in the Quick Find on the left.
2. From the Objects Palette (left-hand side) select the Suggestion object and the User object.
3. If you don’t see your objects in the Canvas area, click Auto-Layout and drag them around until you like how they are arranged.

Let’s add more fields so that you can track the progress of a Suggestion.

#### Step 5: Add a Status field

This field will help us track the status of the suggestion. It is also a way for the suggestor to know what is going on with their suggestion.
This field will have unique security around it in that only Admins can change the status.

1. Click the Elements tab in the left-hand side Palette. This displays all of the new things you can add to your database schema: objects and fields.
2. Drag the Picklist field type and drop it on the Suggestions object.
3. Fill in the custom field details:
* Field Label: Status
* Field Name: Status
* Enter the following Values for the picklist (one per line)
        - New
        - Under Consideration
        - In Progress
        - Implemented
Leave the Sort Values Alphabetically - Unchecked.
Check the Use first value as default value.
Click Save to save the Status field. You should see the Status field at the top of the list of fields in the Suggestion object’s box in the schema diagram.
Now, we'll update the Status field's security. While still in the Schema Builder, right-click on the Status field that you just added to the Suggestion object. Click Manage Field Permissions.
Click the Read-Only checkbox at the top of the column to make this field read only for all users.
Uncheck the box in the Read-Only column next to System Administrator, then click Save.
Step 6: Add a Date field
This field will help us track when the Suggestion was Implemented. It’s also important for us because we reference this field in the formula field created in a later exercise.
This field will have unique security around it in that only Admins can change the date.
Drag the Date field type and drop it on the Suggestions object.
Fill in the custom field details:
Field Label: Implemented Date
Field Name: Implemented_Date
Leave the defaults for the remaining fields
Click Save to save the Implemented Date field. You should see the Implemented Date field at the top of the list of fields in the Suggestion object’s box in the schema diagram.
Now, we'll update the Implemented Date field's security. While still in the Schema Builder, right-click on the Implemented Date field that you just added to the Suggestion object. Click Manage Field Permissions.
Click the Read-Only checkbox at the top of the column to make this field read only for all users.
Uncheck the box in the Read-Only column next to System Administrator, then click Save.
Step 7: Try Out the App
Go back to the Suggestions tab in the browser, or to the Suggestions object in the Salesforce1 Mobile app, and create a new Suggestion record.
Notice anything? Where are our two new fields we just created, Status and Implemented Date?
Don’t worry, they are there, just not on the page layout. When you use the Field Wizard, you have the option to add the field to the page layout automatically. When you use the Schema Builder, you need to add the field using the Page Layout Editor. In the next exercise, we’ll do that, and make some other modifications to the user experience, to make the app easier to use.
Resources
Data Modeling > Creating Custom Objects and Fields
Data Modeling > Working with Schema Builder



Modifying the User Experience
What You’ll Do
Modify the page layout.
Modify the mobile user interface with a compact layout.
Add a global action to add suggestions directly from the Feed.
Try the app in the browser or in the Salesforce1 Mobile app.
In the previous exercise you created fields to manage the data. Now, let’s make sure that employees have a great experience logging their Suggestions by arranging the fields in a usable manner, bringing the most important fields to their attention and making common tasks easy to do.
Changing the UI for the browser and mobile pages involves modifying the Suggestion object's page layout. You’ll also modify the Suggestion object's compact layout, which changes only the mobile app UI. Finally, you’ll add a Global Action to make it easy for employees to add suggestions wherever they are in the Salesforce app in both the browser and the mobile app.
Step 1: Modify the Page Layout
Click on the Gear icon | Setup Home | Platform Tools | Objects and Fields | Object Manager
Click the name of your object (in this example, click Suggestion)
Scroll down to the Page Layouts section, and click the Suggestion Layout name. The Page Layout Editor opens.
Scroll down to the Suggestion Detail section.

In the Page Layout Editor, you have can have one or two columns in each section. Let’s add the missing fields and group fields together.
Click the Status field and drag it to the right column in the Information section, under Owner.
Click the Implemented Date field and drag it to the right column in the Information section, under Status.
Click Quick Save.
Now add a new section and rearrange the remaining fields.
Add a section for Description. In the palette, click Section and drag it down until a green bar appears above the System Information section. Drop it there to create the section.
In the Section Properties box, enter Description for the section name.
Choose 1-column for the layout.
Click OK.
Drag the Suggestion Description field into your new section. Your page should look like this when finished.

Click Save.
Step 2: Add a Compact Layout
Mobile app users can now see the Status and Implemented Date fields, but with a small screen size, it’s useful to display important fields at the top of the screen. For custom objects, the Name field displays at the top by default. To add the other fields, you modify the compact layout.
Click Gear icon | Setup Home | Platform Tools | Objects and Fields | Object Manager
Click the name of your object (in this example, click Suggestion)
Scroll down to the Compact Layouts section and click New.
Enter the layout details.
Label: Suggestion
Name: Suggestion
Selected Fields: Suggestion Name, Suggestion Category, Status

Click Save.
Click Compact Layout Assignment.
Click Edit Assignment.
Select Suggestion as the primary compact layout.
Click Save.
Step 3: Add a Global Action
Things are looking pretty good, but from talking to employees about how they work, we’ve found that they think of suggestions while they’re doing other tasks and don’t want to leave what they are doing to submit a suggestion. For example, if an employee is entering a contact name and thought of a customer service suggestion, the employee would have to do the following to enter a suggestion:
Navigate to the Suggestion object
Click New
Fill in the Suggestion information
Save
Navigate back to the Contact record
Try to remember what they were doing!
You want to make it easier for employees by reducing the number of clicks and keeping them in context. You can do this with a Global Action.
Click the gear icon | Setup Home
Search for Global Actions in the Quick Find on the left, choose Global Actions from the result
Click New Action.
Enter the action details.
Action Type: Create a Record
Target Object: Suggestion
Standard Label Type: New [Record]
Name: New_Suggestion

Click Save. This takes you to the Global Action layout so that you can select the fields to display when the action is selected.
Drag Suggestion Category and Suggestion Description onto the New Suggestion layout so that employees can enter those values when creating a suggestion.
Click Save.

In the Predefined Field Values section, click New.
Enter the predefined field value details.
Field Name: Status
A specific value: New
Click Save. You have created a new action, but users cannot see it yet! Let's fix that next.
Click the gear icon | Setup Home | Platform Tools | User Interface | Global Actions | Publisher Layouts
Next to Global Layout, click Edit.
Drag the New Suggestion action into the Quick Actions in the Publisher section and drop it in the second position, after Post.

Click Save.
Step 4: Try Out the App
In the Suggestions tab, click New and fill out a suggestion. Notice how the experience has changed in the page layout. Switch to the mobile experience and add a suggestion from the mobile navigation menu. You should see the page layout changes and the new fields at the top of the screen from the compact layout change. Finally, add a suggestion from the Feed in the mobile app. Does the global action make it easier? If so, consider updating the page layouts for specific objects (like Contact) to add the New Suggestion action to the action bars that display when viewing those records.
Resources
UI Customization > Modifying Page Layouts
Salesforce1 Mobile Basics > Customizing Compact Layouts
Salesforce1 Mobile Basics > Creating Global Quick Actions
Salesforce1 Mobile Basics > Creating Object-Specific Quick Actions


Adding Business Logic
What You’ll Do
Create a custom formula field to calculate the length of time between two dates.
Add a validation rule to enforce that the implemented date is not in the future.
Create a process to update a field when the value of another field changes.
Try the app in the browser or Salesforce1 Mobile app.
So far, your app lets employees securely enter and view data about suggestions on a browser or a mobile device. That’s pretty cool, but let’s make the platform work a little harder for us so that our users don’t have to.
Step 1: Add a Formula Field
A formula field derives its value from other fields, expressions, or values, saving your users a lot of time. Let’s add a formula field to calculate the number of days from suggestion creation to implementation.
Click Gear icon | Setup Home | Platform Tools | Objects and Fields | Object Manager | Suggestion
In the Custom Fields & Relationships section, click New.
For the data type, select Formula and click Next.
Enter the field details, and then click Next.
Field Label: Number of Days Open
Formula Return Type: Number
Decimal Places: 0
Click the Advanced Formula tab.
In the Formula Editor, type the following formula: IF(ISBLANK(Implemented_Date__c), TODAY() - DATEVALUE(CreatedDate), Implemented_Date__c - DATEVALUE(CreatedDate)) 
Click Next and Next again to accept the default field visibility and security settings.
Click Save to save the Number of Days Open field and add it to the Suggestion layout.
Step 2: Add a Validation Rule
Formulas are also used to ensure data quality and enforce business rules. Our employees cannot predict the future, so let’s create a validation rule to make sure that they don’t enter implementation dates that haven’t happened yet. With validation rules, you define the condition for "bad" data. When the system evaluates the rule, it blocks the user from saving the data if the condition is true.
Click Gear icon | Setup Home | Platform Tools | Objects and Fields | Object Manager | Suggestion
Scroll down to the Validation Rules section and click New.
Enter the rule details.
Rule Name: Date_in_Range
Formula editor:
1

Implemented_Date__c > TODAY()
Error Message: The implementation date must be today or in the past, not in the future.
Error Location: Field, Implemented Date

Click Save.
Step 3: Add a Field Update Using Process Builder
Formulas are also useful when automating business processes. Let’s make it easy on the person who has to act on all the suggestions by saving them some time. When a user adds an implemented date, let’s update the status automatically to Implemented.
From Setup Home, Go to Platform Tools | Process Automation | Process Builder
On the Welcome page, click New.
Enter the process details, and then click Save.
Process Name: Update Status
API Name: Update_Status
Description: Updates status to Implemented when Implemented Date field is populated.
Click Add Object and associate the process with the Suggestion object and specify when to start the process. For this process, select when the record is created or edited.

Click Save.
Click Add Criteria.
Name the criteria Implemented?.
Select Formula evaluates to true.
Check whether the Implemented Date field is populated by typing the following in the Formula Editor:NOT(ISBLANK( [Suggestion__c].Implemented_Date__c ))

Click Save.
Click Add Action.
Enter the action details, and then click Choose.
Action Type: Update Records
Action Name: Update Status to Implemented

Record Type: click the magnifying glass to open the modal, then click the radio button next to Select the Suggestion__c record that started your process.
Leave No criteria—just update the records! selected.
Set the new field value.
Field: Status
Type: Picklist
Value: Implemented

Click Save
In the upper right corner, click Activate and then click Confirm.
Step 4: Try Out the App
Your business logic fires whenever a record is created or updated from the browser or the mobile app. Go back to a Suggestion record and enter a date in the Implemented Date field. Try a date in the future. Did the system block you from saving? Now try today’s date. The Status field should change to Implemented.

Resources
Formulas and Validations > Using Formula Fields
Formulas & Validations > Creating Validation Rules
Process Automation > Automating Processes with the Lightning Process Builder
Creating Reports and Dashboards
What You’ll Do
Create a simple tabular report to list open suggestions.
Create a summary report with a report chart.
Create a simple dashboard based on the summary report.
Try the reports and dashboards in the Salesforce1 Mobile app.
The Suggestion Box app you created with the App Quick Start wizard includes a Reports tab and a Dashboards tab, where you can create, edit, and run reports and dashboards. These features make an app more useful by providing the business with valuable insights.
Step 1: Create a Tabular Report
Let’s start with a simple tabular report to show the number of suggestions received and the status of each one. A tabular report presents data in simple rows and columns, much like a spreadsheet. You can use it to show column summaries, like sum, average, maximum, and minimum.
On the Reports tab, click New Report.
In the Quick Find box, enter Suggestions. In the Other Reports folder, choose Suggestions.
Click Create.
In the Report Builder, notice that the Suggestion Name field is already on the report. In the preview screen, you can see the sample Suggestion records you created in previous steps.
From the Fields pane, in the Suggestion: Info folder, drag Status and Number of Days Open onto the preview and drop them to the right of the Suggestion Name field.
Set a row limit for this report so that it doesn’t get out of hand. From the Add drop-down menu next to Filters, chooseRow Limit.

Click OK to accept the default 10-row limit.
To make sure that you’re viewing only suggestions that still need to be implemented, click Add next to Filters and set the filter to Status Not Equal To Implemented. Click OK.
Click the Dashboard Settings button at the top.
For the Name field, choose Status.
For the Value field, choose Number of Days Open and then click OK. (Steps 9, 10, and 11 are needed only if the tabular report is used in a dashboard.)
Click Save, and name your report Suggestions: Status of Open Suggestions.
In the Report Folder drop-down list, select Unfiled Public Reports so that everyone can access it. (If you don’t want this report to be accessible to everyone, create a folder and give different people different levels of access to it.)
Click Save and Run Report.

Step 2: Create a Summary Report and a Report Chart
It’s often a good idea to give users a visual way to understand the data in your report. Tabular reports don’t include charts, so let’s copy the report and make a Summary report. Then, you can add a chart.
From the Report Results view of the Suggestions: Status of Open Suggestions report, click on the drop down on the right and choose Clone
Type Suggestions: By Status as the report name
Choose the Make Public radio button
Click Save
Click Edit
Click Tabular Format and choose Summary. If presented with a warning message, click Yes to proceed.
Drag the Status field column to the shaded area labeled Drop a field here to create a grouping.

From the Report Results view, click on the Pie button to Add chart to this report
Click on the gear icon and choose Doughnut chart
In the Chart Title type Suggestions Status and in Value drop down, choose Record Count. 
Click OK, and then Save.
Click Run Report.
The doughnut chart displays above the report results and shows the total number of open suggestions by status.

Step 3: Create a Dashboard
Dashboards in Salesforce are like a dashboard in your car, showing you important information at a glance. Dashboards can show data in charts, gauges, tables, metrics, or other formats (using a Visualforce page). Dashboards are powered by reports.
Go to the Dashboard tab and click on  New Dashboard
Type the Dashboard Name as Suggestions Dashboard and save it on the default Private folder
Click on the + Add button at the top right to add a component

In the Report Search, look for Suggestions: By Status
Choose the doughnut chart and edit the Title as Suggestions By Status
Click Add
Click Done. 
The Dashboard appears as 


Step 4: Try Out the Mobile App
You are now a mobile app developer! This is a simple but complete app, and from here you can continue to add functionality or build another app with these skills. Let's see how these reports and dashboards look in the Salesforce1 Mobile app.
Click the  icon to open the left navigation bar.
Scroll down and click Reports in the Apps section to display the reports that you recently viewed.
Click either of the reports that you created.
Sort the columns, and click a row in the report to test its functionality.
Now let’s try the dashboard. Click the  icon to open the left navigation bar.
Click Dashboards to display the dashboards that you recently viewed.
Click the Suggestions Dashboard. You should see something like this:

Summary
You did it! You built a simple suggestions management app, all without a single line of code! You started by creating the simple data model and the default user interface for managing suggestions. You then customized the user experience on the browser and mobile app by using drag-and-drop editors. Next, you added business logic with the formula language and process builder. Finally, you created some reports and dashboards so that you could monitor and analyze your business data. You can continue your journey and dive deeper by following the links in the Resources sections of this project.
Resources
Reports & Dashboards > Getting Started with Reports and Dashboards
Reports & Dashboards > Using the Report Builder
Reports & Dashboards > Using Report Formats
Reports & Dashboards > Visualizing Your Data




