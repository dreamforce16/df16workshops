+++
date = "2016-07-29T16:18:23+05:30"
draft = true
title = "Dreamforce 2017 MiniHacks"

+++

### Challenges 

- [CHALLENGE 1 : Point and Click App](#challenge-1-point-and-click-app)
- [CHALLENGE 2: Lightning App Builder and Dreamhouse](#challenge-2-lightning-app-builder-and-dreamhouse)
- [CHALLENGE 3: Voting Survey with no Code](#challenge-3-voting-survey-with-no-code)
- [CHALLENGE 4: Lightning BMI Calculator](#challenge-4-lightning-bmi-calculator)
- [CHALLENGE 5: Lightning Data Service, No Apex](#challenge-5-lightning-data-service-no-apex)
- [CHALLENGE 6: Heroku App in Action](#challenge-6-heroku-app-in-action)
- [CHALLENGE 7: Hello Einstein, Search Visually](#challenge-7-hello-einstein-search-visually)
- [CHALLENGE 8: Searchable Contact List](#challenge-8-searchable-contact-list)
- [CHALLENGE 9: SLDS Component](#challenge-9-slds-component)
- [CHALLENGE 10: Deploy DocuSign for Salesforce to get agreements sent out and signed faster](#challenge-10-deploy-docusign-for-salesforce-to-get-agreements-sent-out-and-signed-faster)

#### CHALLENGE 1 : Point and Click App
We need to help an amusement park manage its rides, shows, customers and discounts. Help them by creating a Resort application with automated approval processes, using configuration only. 

###### REQUIREMENTS
1. This challenge is for the Lightning Experience and requires a Force.com Developer Edition: https://sforce.co/2tnM5a7. 
2. No code is allowed. This includes Apex, Visualforce, Lightning Components, custom JavaScript buttons, or integration with external systems.
3. Show object should contain Name, Date, Price, Total Seats and Booked Seats. Each show or ride is limited by number of seats (total seats).
4. Ticket object should capture Customer Name, Number of Adult seats, Number of Child seats and Discount. Price must be derived from the Show object. The Total Cost should be automatically calculated. If the seats are filled, no additional bookings are to be allowed.
5. Discounts are permitted for the following scenarios:
   1. Child seat has a default discount of 25%.
   2. Special discount is permitted through the discount field. If this discount is more than 30%, approval from the manager is required. Record should be locked while under approval process
 
###### Demo Your Solution:
Visit the Mini Hack Basin to verify your results with a tech staffer.

#### CHALLENGE 2: Lightning App Builder and Dreamhouse
Lightning App Builder lets admins build with components. You can combine existing standard and custom components to create a single Lightning page.

###### Requirements:
1. This challenge is for the Lightning Experience UI and requires a Force.com Developer Edition: https://sforce.co/2tnM5a7
2. Install the Dreamhouse Sample app (Refer to the instructions at http://www.dreamhouseapp.io
3. Create a Lightning Page called Property View using Lightning App Builder, and add it to the Dreamhouse App.
4. Property View Page should contain Property Tile List, Price Range, Property Summary, Map, Smart Price Calculator and Days on Market Components.
5. Add Path Component to Property Detail page and configure process with key fields for each stage of the Deal.

###### Demo Your Solution:
Visit the Mini Hack Basin to verify your results with a tech staffer.

#### CHALLENGE 3: Voting Survey with no Code
Help a social organization conduct a Survey to choose their leader based on certain qualities. Create a visual flow of the survey using Cloud Flow Designer.

###### Requirements:
1. This challenge is for Lightning Experience and requires a Force.com Developer Edition: https://sforce.co/2tnM5a7
2. No code is allowed. This includes Apex, Visualforce, Lightning Components, custom JavaScript buttons, or integration with external systems.
3. Contestant object should contain Name, rollup field for Vote Count and Score which is a rollup summary of ratings from all the votes.
4. Vote object which is child of Contestant object should capture rating(1 to 10) for each of the qualities e.g. Honesty, Delegation, Commitment and Leadership etc. A field called score should automatically calculate the score of all qualities.
5. Voting has to be done through a survey[o] via a tab in Lightning Experience using visual flow. This flow should have the Lightning look and feel.

###### Demo Your Solution:
Visit the Mini Hack Basin to verify your results with a tech staffer.

#### CHALLENGE 4: Lightning BMI Calculator
Implement a BMI Calculator Lightning Component using Base Lightning Components. 


###### Requirements:

1. This challenge is for Lightning Experience and requires a Force.com Developer Edition: https://sforce.co/2tnM5a7
2. Create a simple component using Base Lightning Components to accept Height and Weight. It should Calculate and display BMI on click of a button.
3. You may refer to http://sforce.co/2stOMud for help on Base Lightning Components.
4. Show this component working in Lightning Experience.

###### Demo Your Solution:
Visit the Mini Hack Basin to verify your results with a tech staffer.

#### CHALLENGE 5: Lightning Data Service, No Apex

Make it simple with Lightning Data Service. Avoid using a custom server side Apex controller to load, create, edit, or delete a record in your Lightning component.

###### Requirements:

1. This challenge is for Lightning Experience and requires a Force.com Developer Edition: https://sforce.co/2tnM5a7
2. Use Lightning Component code. No Apex Code is allowed.
3. Implement a Component and add it to Contact detail page. The Component should display Contact Name, Phone and Email Values of the record.
4. Refer to the documentation and examples at http://sforce.co/2sBWZML

###### Demo Your Solution:
Visit the Mini Hack Basin to verify your results with a tech staffer.

#### CHALLENGE 6: Heroku App in Action
Deploy an app on Heroku that uses Heroku Connect to display the accounts from Salesforce in your application.

###### Requirements:
1. This challenge requires a Force.com Developer Edition: http://sforce.co/2tnM5a7.
2. Refer to the blog: http://bit.ly/HerokuHack for instructions on installing, running the app locally and deploying to Heroku.
3. Modify the app such that it can display Accounts from Salesforce
4. Redeploy the changes on Heroku.
5. You can refer to the trailhead Heroku Connect Module for help: http://sforce.co/2sbVsud.

###### Demo Your Solution:
Visit the Mini Hack Basin to verify your results with a tech staffer.

#### CHALLENGE 7: Hello Einstein, Search Visually
A picture is worth a thousand words. Search with images using Einstein Vision and Lightning bots. Configure the Dreamhouse app for your customers to support searches for similar properties by uploading images of their choice.

###### Requirements:
1. This challenge is for Lightning Experience and requires a Force.com Developer Edition: https://sforce.co/2tnM5a7.
2. This challenge requires a Einstein Vision Account. You can sign up at bit.ly/2s2xbsd.
3. Install the Dreamhouse sample app (Refer to instructions at http://bit.ly/2stG4fB).
4. Configure the image based search component and bot to search for a house by typing the command ‘search houses like this’ and upload any house image of your choice. You may refer to http://sforce.co/2sc5j2Z for help.

###### Demo Your Solution:
Visit the Mini Hack Basin to verify your results with a tech staffer.

#### CHALLENGE 8: Searchable Contact List
A Searchable Contact List Component for each Account record is required for an Organization. Help the organization by creating a Lightning Component.

###### Requirements:
1. This challenge is for Lightning Experience and requires a Force.com Developer Edition: http://sforce.co/2tnM5a7.
2. Create a Searchable Contact List Component which has to be added to the Account Detail Page.
3. The Component should display all the Contacts of the Account. It should contain a Search Filter to search for a specific Contact by name.

###### Demo Your Solution:
Visit the Mini Hack Basin to verify your results with a tech staffer.

#### CHALLENGE 9: SLDS Component
Enrich Visualforce pages with Lightning Experience by creating Visualforce Components using Salesforce Lightning Design System (SLDS).


###### Requirements:

1. This challenge requires a Force.com Developer Edition: sforce.co/2tnM5a7.
2. Install this package which contains the VisualForce Page with more instructions: https://login.salesforce.com/packaging/installPackage.apexp?p0=04tf4000001MFb5 
3. Implement a Visualforce component with SLDS and replace the standard component in the Visualforce page.
4. Your new SLDS visualforce component should work like standard component.
5. You may refer to https://trailhead.salesforce.com/trails/lex_dev/modules/lex_dev_visualforce for help.

###### Demo Your Solution:
Visit the Mini Hack Basin to verify your results with a tech staffer.

#### CHALLENGE 10: Deploy DocuSign for Salesforce to get agreements sent out and signed faster.

Difficuly : Intermediate
 
###### Requirements:

1. This challenge is for Lightning Experience and requires a Force.com Developer Edition
2. Install and configure the DocuSign for Salesforce managed package from AppExchange: http://bit.ly/2xFgzLt
3. Add the DocuSign Sending Lightning component to your Lightning Page: http://bit.ly/2yg0lrH
4. Send and sign a document with “Send with DocuSign” Lightning component: http://sforce.co/2xEwt3C

Refer to additional information at http://bit.ly/2xFqq3W
 
###### Features to be used:
* Lightning App Builder
* DocuSign for Salesforce
 
###### Demo Your Solution:
Visit the DocuSign Booth in the Developer Forest to verify your results with a DocuSign team member








