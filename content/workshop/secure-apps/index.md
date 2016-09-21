+++
date = "2016-07-29T16:18:23+05:30"
draft = true
title = "Secure Apps - Understand Cross Site Scripting "

+++

1. [Introduction](#introduction)
2. [Learning Application Security with a Developer Edition Org](#learning-application-security-with-a-developer-edition-org)
3. [What is Cross-Site Scripting?](#what-is-cross-site-scripting?)
4. [How Does an XSS Attack Work?](#how-does-an-xss-attack-work?)
5. [Try Another Cross-Site Scripting Attack](#try-another-cross-site-scripting-attack)
6. [Force.com XSS Protections](#force-com-xss-protections)
7. [Automatic HTML Encoding](#automatic-html-encoding)
8. [Disabling Automatic HTML Encoding](#disabling-automatic-html-encoding)
9. [Salesforce Default Protections in Different Execution Contexts](#salesforce-default-protections-in-different-execution-contexts)
 * [HTML Context](#html-context)
 * [Script Context](#script-context)
 * [Style Context](#style-context)
10. [Identify potential cross-site scripting vectors](#identify-potential-cross-site-scripting-vectors)
11. [Prevent XSS in Force.com Applications](#prevent-xss-in-force-com-applications)
12. [Platform Encoding in Apex](#platform-encoding-in-apex)
13. [The Force.com ESAPI in Action!](#the-force.com-esapi-in-action!)
14. [Final Challenge](#final-challenge)

## Introduction

Salesforce is constantly striving to make our platform as secure as possible. As a result Force.com has many built in security functions to protect end users: some enabeld by default while others require the developers to enable them within their code. While we aim to bake-in as much security constraints as possible because of the flexibility and extensibility of the platform it is completely possible for developers to write insecure code. 

Of all the issues we commonly see developer introduce into their apps, Cross-Site Scripting (XSS) is one of the most widely seen web application vulnerabilities. As a result we’ve chosen this vulnerability to be the focus of our workshop. Our goal is that by the end of this class you should be able to explain XSS, know how to identify it in your code and remediate the issues. To support that we will have numerous demos, where you’ll get to “be the attacker” spotting and exploiting vulnerabilities as well as “being the defender” remediating them.

## Learning Application Security with a Developer Edition Org

Don’t worry in this workshop—we’re not going to teach you how to write vulnerable Force.com code. Instead, we’ve created a special org that has vulnerable applications already deployed for you to practice in. Your job will be to identify the vulnerabilities and to fix them!

To get set up in the Kingdom Management developer org, you’ll need to sign up:  

1. Go to the custom sign-up page for the Kingdom Management developer org: https://security.secure.force.com/SecDev_Trailhead_Signup
2. Fill out the form using an active email address and click **Sign Me Up**.

    <img src="images/1.png" width="70%" height="70%">

3. Check your email for an activation request. 

    <img src="images/2.png" width="70%" height="70%">

4. Click the link in the email, and complete your registration by setting a new password and challenge question.

    You should now be logged into the Kingdom Management developer org that we’ll be using for this class. This is an org that we’ve expanded from our newly launched Trailhead modules so after you’ve completed this workshop you can use this same org to continue on your secure coding training! For this workshop we’ve expanding the existing trailhead demos to add a few new ones that we’ll use today. 

    Let’s enable those now.

5. Click on the **Setup** link
6. Click *Manage Users -> Profiles -> System Administrator*
7. Click the **Edit** button
8. Click the checkbox next to **DF16 Workshop** to make our custom app visible
9. Click the **Save** button
10. Select the **DF16 Workshop** from the app selector in the top right corner

    <img src="images/3.png" width="70%" height="70%">

If you see something similar to the above screen you are now ready to start learning how to defend your users against XSS attacks by using application security!

**Note**: The code in the Kingdom Management developer org is vulnerable to certain kinds of vulnerabilities. In the developer org these vulnerabilities are benign, but they may pose more serious problems if replicated to a production Salesforce environment. Use the Kingdom Management application for educational purposes only.

## What is Cross-Site Scripting?

Cross-Site Scripting (XSS) is an injection vulnerability that occurs when an attacker can insert unauthorized JavaScript, VBScript, HTML, or other active content into a web page. When other users view the page, the malicious code executes and effects or attacks the user. For example, a malicious script can hijack the user’s session, submit unauthorized transactions as the user, steal confidential information, or simply deface the page.

## How Does an XSS Attack Work?

In the simplest form, XSS attacks occur **when user-supplied input is reflected in the HTML of a web page**. Due to poor separation between code context and user data, the user input is executed as code. 
In the Kingdom Management app, there is a page to create a scroll that can be used in the Kingdom to post announcements. Let’s test it out for XSS vulnerabilities!

**Note**: For each of the demos in this class please **Firefox** as your web browser

1. In your Kingdom Management developer org select the **XSS Basics** tab.
2. In the message text field, enter a basic message like: *Hear ye, hear ye, come one, come all!* and click the **Create Scroll** button.
3. The page should refresh, and you should see the text you entered in the previous step.
4. Right click on the page and select **View source**.
5. Search in the HTML using CTRL+F for your message and you should see HTML like: ```<i>Hear ye, hear ye, come one, come all!</i>```

    Input that we entered into the form was rendered in the page, just what we expected. But what if you tried something different? 
    XSS vulnerabilities result when user input is executed as code. So what happens if we enter code into this message box? Will it execute?

6. Back in the Kingdom Management developer org, enter the following before clicking **Create Scroll**: ```<u> Testing for underline </u>```

    Did the underline effect occur? Yes! It appears that text we enter into the input box is being interpreted as code! 

    Now what happens if we try something a lot more complicated?

7. Enter the following in the message text field: ``` <img src=x onerror="alert(\'I said, HEAR YE, HEAR YE, COME ONE, COME ALL!!\');"/>```
8. Click **Create Scroll**. You will see a little window pop up.

    <img src="images/4.png" width="70%" height="70%">

## Force.com XSS Protections

As a platform, Salesforce offers its customers and developers maximum flexibility for accessing and storing data. We don’t control what you place on our platform, but wherever possible we make sure it’s displayed securely. So the Salesforce approach to XSS defense is to perform output encoding. In fact, by default all merge fields are automatically HTML encoded! We’ll discuss gaps that you’ll need to be aware of and account for in your applications.

## Automatic HTML Encoding

As we mentioned previously, Salesforce automatically HTML encodes any values and merge fields placed in HTML context. This includes all standard functionality, as well as Visualforce pages and components.  

Let’s see this in action in our Kingdom management org.

1. Click on the **XSS Automatic HTML Encoding Demo** tab

    On this tab we have a simple app which has a greeting message to the user

    <img src="images/5.png" width="70%" height="70%">

    You may notice that to construct this greeting the app utilizes the URL parameter name as a input.

2. Try changing value of the **name** URL parameter to something different like *THIS IS A TEST*. 
 
    Your URL should look something like this:
    
    ```
    https://c.[yourinstance].visual.force.com/apex/xss_automatic_html_encoding_demo?name=This is a test
    ```

    When you hit enter the message should have changed to render the value of the URL parameter. Let’s see if this is vulnerable to XSS by using an attack payload similar to one that we’ve used before.

3. Change the value of the name URL parameter to ` <img src="test" onerror="alert(‘THIS IS AN XSS ATTACK’)"/>`

    Wait...the alert message didn’t pop up. Why?

4. Right click on page and select **View Page Source**
5. Search in the source code (using CTRL+F) for THIS IS AN XSS ATTACK

    You should notice that the platform automatically encoded all the values that we entered into their HTML entity equivalents:

    ` Hello &lt;img src="test" onerror="alert(&#8216;THIS IS AN XSS ATTACK&#8217;)"/&gt;!`

This is the automatic HTML encoding in action and as a result it treats the data we entered as text, not code, neutralizing the XSS attack!

## Disabling Automatic HTML Encoding

While it’s great that Salesforce provides this functionality for developers out of the box, there are certain use cases where you may want to have raw HTML embedded in the page. To support this, several Visualforce tags have an optional attribute called “escape” which will allow developers to disable the automatic encoding.

Let’s see what happens when this attribute is set to false in our demo org.

1. Click on the **XSS Disabling HTML Encoding** tab
  
    You’ll see the same app as before. It’s a simple app designed to take in the value of the URL parameter **name** and insert it into a greeting message. However this time the visualforce page is applying some extra styling to the merge field.

2. Click on the **Visualforce Page** link at the bottom to see the code

    ` <apex:outputlabel value="Hello <b>{!name}</b>!" escape="false"/>`
  
    The platform encoding is explicitly disabled by the addition fo the escape = “false” attribute.

3. Navigate back to **XSS Disabling HTML Encoding** tab 
4. Just as we did in the previous demo, set the value of the name URL parameter to one of our XSS attack payloads: ` <img src="test" onerror="alert('THIS IS AN XSS ATTACK')"/>`

    <img src="images/6.png" width="70%" height="70%">

The attack works! 

While disabling encoding may be necessary for certain use cases, you should exercise **extreme caution**. If you disable automatic encoding, you have to rely on other XSS prevention techniques (like whitelisting) to ensure that your code isn’t vulnerable to XSS.
  
## Salesforce Default Protections in Different Execution Contexts

As we’ve seen from our above demos the platform automatically HTML encodes merge fields (assuming you have not explicitly disabled it) protecting you from XSS attacks. However this protection is specific to fields utilized HTML context. What about other contexts like script and style? Let’s find out! 

### Script Context

When inserting merge fields into JavaScript, watch out for XSS vulnerabilities. Lets take a look at an example in our org.

1. Click on the **XSS in Script Context** tab
2. Click on the **Visualforce page** link at the bottom to view the code
 
    ``` html 
     <script>
        var a = '{!$CurrentPage.parameters.userInput}';
     </script>
     
     <apex:outputtext>The value of the userInput parameter is: {!$CurrentPage.parameters.userInput}</apex:outputtext> 
    ```
    We’ve got a user controllable variable userInput being included directly in script context. Is this vulnerable? Let’s find out.

3. Navigate back to the **XSS in Script Context** tab
4. Try typing in the following string for the userInput URL parameter ` ‘;alert(‘XSS’);//` 

    HINT: The URL in a your address bar should look something like this
    
    ```
    https://c.[yourinstance].visual.force.com/apex/XSS_in_Script_Context?userInput=';alert('XSS');//
    ```
 
    <img src="images/8.png" width="70%" height="70%">

Yes it is vulnerable! So keep in mind that any user input is inserted into script context isn’t automatically encoded and is vulnerable to XSS.

### Style Context

CSS (cascading style sheets) is an increasingly complex language that is slowly becoming standardized across browsers. Modern browsers don’t allow JavaScript injection within CSS attribute values. However, some older browsers do. As a result, be careful about utilizing merge fields within style context.

Let see what a vulnerable app would look like in your developer org!

1. Click on the **XSS in Style Context** tab
2. Click on the **Visualforce Page** link at the bottom of the screen to view the source code

    ```
    <style type="text/css">
      p{ background-color: {!color};}
    </style>
    
    <p> <b>This is some styled text </b></p>
    ```

    You’ll notice in this simple app the developer is using the variable “color” in order to add additional style to the paragraph attribute.

3. Navigate back to the **XSS in Style Context** tab and try entering different values for the URL parameter color like green or blue
  
    The background color of the page should change! User controllable input that’s being used directly in the visualforce page....is this vulnerable to XSS attacks?

4. Try entering the following for the color parameter ``` blue</style><img src=x onerror="alert(1)"/>```

    You should see something like this:

    <img src="images/9.png" width="70%" height="70%">
  

So as you can see, the default automatic HTML encoding doesn’t apply here and the application is vulnerable to XSS. Again be very careful about any use of merge fields in style context as it will leave your applications vulnerable to XSS attacks.

  
# Prevent XSS in Force.com Applications

## Platform Provided Encoding Functions 

While the automatic HTML encoding provided by the platform is a fantastic default protection for a majority of use cases, it’s not a complete solution for developers that more customized applications. Luckily Salesforce provides output encoding functions for both Visualforce and Apex that you can leverage to fortify your code.

## Platform Encoding in Visualforce

In Visualforce, the platform has three main encoding functions that developers can use to neutralize potential XSS threats: HTMLENCODE, JSENCODE, and JSINHTMLENCODE. To choose which encoding to use, consider how your browser is parsing the output.

  * If the value is going to parsed by the JavaScript parser, use JSENCODE().
  * If the value is going to parsed by the HTML parser, use HTMLENCODE().
  * If it’s a combination of both use JSINHTMLENCODE().

We’ll give examples to illustrate where and when to apply each encoding function.

## JSENCODE()

**JSENCODE()** is a function that developers can use to perform JavaScript encoding of input prior to reflection in JavaScript context. This function encodes text and merge field values for use in JavaScript by inserting escape characters, such as a backslash before unsafe JavaScript characters, such as the apostrophe ('). The function is used in cases where a merge field is directly used as a JavaScript variable.

## JSENCODE() in Action!

Now let’s learn how to fix a JavaScript-based XSS vulnerabilities using JSENCODE.

1. Click the **XSS Visualforce Mitigations** tab.
2. Click the **Click here to view the JavaScript-based XSS** button to demo the vulnerability.

    You should see a alert box pop up on your screen.

3. Click the **Visualforce Page** and **Apex** links at the bottom of the page to find the vulnerable code.
  
    **Apex**:

    ``` java 
    public pageReference JSXSS(){
        title = 'Test title\'; alert(\'This is a Javascript based XSS!\');//';
        return null;
    }
    ```
    
    **Visualforce**:
    
    ``` html
    <script>
        var vip = '{!title}';
        [...]
    </script>
        [...]
    <apex:commandButton value="Click here to view the JavaScript-based XSS!" action="{!JSXSS}"/>
    ```
    
    You’ll notice that when the button is clicked it calls the JSXSS function in Apex. This function sets the title to a XSS payload which is then rendered in Visualforce. 

    We’ll need to put some encoding around this output in visualforce to prevent this attack!

4. Edit the Visualforce page by wrapping the title merge field in JSENCODE like the following: `var vip = ‘{!JSENCODE(title)}’;`.
5. Click **Save** and navigate back to the **XSS Visualforce Mitigations Demo**.
6. Click the **JavaScript-based XSS** button again.

If no popup appears on the screen, then you’ve successfully mitigated XSS!

## HTMLENCODE()

**HTMLENCODE()** is a function that developers can use to perform additional HTML encoding of input prior to reflection in HTML context. For most use cases, developers don’t need to use this function, but it is required when the default platform encoding is turned off or when you’re adding user-controllable input directly to the DOM.

## HTMLENCODE() in Action

Now let’s learn how to fix HTML-based XSS vulnerabilities using HTMLENCODE().

1. Click the **XSS Visualforce Mitigations Demo** tab.
2. Click the button labeled **Click here to view the HTML-based XSS**.

    An XSS in the form of a embedded image appears on the page. Remember: defacement is also a valid impact of XSS. 

    This code is injected via the user parameter in the URL:  
    https://c.[yourinstance].visual.force.com/apex/xss_visualforce_mitigations_demo?user=THEME+VIOLATION%21%21%21%21+%3Cimg+src%3D%22https%3A%2F%2Fdeveloper.salesforce.com%2Fresource%2Fimages%2Fastro.png%22%2F%3E

3. Use the link at the bottom of the page to view the Visualforce code and you see the following.
  
    ``` html
    <apex:outputText value="Welcome, <b>{!$CurrentPage.Parameters.user}</b>!" escape="false"/>
    ```

    Because of the escape=”false” setting, user-controlled content is rendered directly on the page. 

4. Edit the code, and add your defensive encoding as follows.
  
    ``` html
    <apex:outputText value="Welcome, <b>{!HTMLENCODE($CurrentPage.Parameters.user)}</b>!" escape="false"/> 
    ```

5. Click **Save** and navigate back to the **XSS Visualforce Mitigations Demo** tab.
6. Click the **HTML-based XSS** button again.

The injected image no longer renders. You prevented the XSS!

## JSINHTMLENCODE()

**JSINHTMLENCODE** is a Visualforce encoding function that was introduced when the platform didn’t always automatically HTML encode merge-fields. JSINHTMLENCODE is effectively a combination of HTMLENCODE(JSENCODE()), so before the introduction of auto-HTML encoding, developers called this function when including merge-fields in JavaScript event handlers within HTML (that is, onerror, onload). Now that the platform auto-HTML encodes, it’s sufficient to just call JSENCODE() in most cases. However there are still some cases where you'll want to ensure that you're using the full JSINHTMLENCODE() function. We'll demo one below! 

## JSINHTMLENCODE in Action!

Let’s try out the JSINHTMLENCODE function.

1. In your Kingdom Management developer org, click the **XSS Visualforce Mitigations Demo** tab.
2. Click the **JavaScript + HTML-based XSS** button to demo the vulnerability.

    You should see an image defacing the page.

3. To find the vulnerable code, click the Visualforce and Apex links at the bottom of the page.

    **Visualforce**:
    
    ``` html
    <script>
      [...]
      var html = '<br/><br/><b>---------------------</b>';
            html += '<br/>Personnel Name: {!JSENCODE(name)}';
            html += '<br/>Favorite color: {!JSENCODE(color)}';
            html += '<br/>Favorite animal: {!JSENCODE(animal)}';
            html += '<br/><b>---------------------</b>';
      document.getElementById('{!$Component.output2}').innerHTML = html;
    </script>
    [...]
    <apex:commandButton value="Click here to view the JavaScript + HTML-based XSS!" action="{!JSINHTMLXSS}"/>
    ```

    **Apex**:
    
    ``` java
    public pageReference JSINHTMLXSS(){
      color = 'THEME VIOLATION!!!! <img src="https://developer.salesforce.com/resource/images/astro.png"/>';
          return null;
    }
    ```

    When the user clicks the commandButton the application calls the JSINHTMLXSS function in Apex. This function sets a XSS payload to the **color** variable that is then processed by Visualforce.

    However, this time we’re wrapping the **color** variable in JSENCODE back in the visualforce page before rendering it to the user. So why is this still a vulnerability? 

    JSENCODE is an encoder specific to JavaScript context, so it prevents any JavaScript-based XSS such as “blue’;alert(‘hi’);//”. However, in this application our **color** variable is not just used in JavaScript context; it’s also written directly to the DOM via the innerHTML call, rendering the data as HTML. Therefore, in order to fully protect this application against XSS, both forms of encoding are required: JavaScript and HTML. Luckily, there is a combo encoding method provided by the platform: JSINHTMLENCODE().

4. Modify the Apex to use JSINHTMLENCODE() rather than JSENCODE(). 
  
    Your visualforce should look like:

    ```
    html += '<br/>Favorite color: {!JSINHTMLENCODE(color)}';
    ```

5. Click **Save**.
6. Navigate to the **XSS Visualforce Mitigations Demo** tab.
7. Click the **JavaScript + HTML-based XSS** button again, and this time the embedded image shouldn’t appear.

The important lesson to take away is that there is a form of encoding for every context, and you have to be aware of the context in order to encode properly. Using the wrong encoding method can be dangerous! 

## Platform Encoding in Apex

Up until now we’ve focused completely on preventing XSS by modifying your Visualforce pages. But what if you need to encode in Apex? 
In all honesty, encoding within the controller is strongly discouraged, but nevertheless, you may need to encode within the controller. For example, if you are generating dynamic HTML from within your controller. To do so, anything under the control of a user requires encoding to prevent special characters from being interpreted as code instead of text. Salesforce provides various Apex encoding functions through the Force.com ESAPI, which exports global static methods that you can use in your package to perform security encoding. This package can be installed in any Salesforce org as an unmanaged package. 

## The Force.com ESAPI in Action!

We installed the Force.com ESAPI package in the Kingdom Management developer org, so let’s see how you can use it instead of Visualforce encoding to prevent XSS!

1. Click the **XSS Apex Mitigations Demo** tab.
2. On the Visualforce page, you see some profile functionality and an XSS button. If you inspect the Visualforce code, you see all the merge fields rendered with escape=”false”.
 
    ```
    Title: <apex:outputText value="{!title}" escape="false" /><br/>
    Name: <apex:outputText value="{!name}" escape="false" /><br/>
    Favorite Color: <apex:outputText value="{!color}" escape="false" /><br/>
    Favorite Animal: <apex:outputText value="{!animal}" escape="false" /><br/>
    ```

    You may remember from our discussion earlier that setting the escape attribute to false disables the built in HTML encoding provided by the platform. 

3. View the Apex controller by clicking the link at the bottom of the page to see why the developers have done this.

    Everything is wrapped in the ` <b>` tag in each of the getters! To maintain the designed effect, the encoding will need to occur in Apex, focused specifically on the user controlled values.

4. Edit the controller and wrap each of the getters in the ESAPI.encoder().SFDC_HTMLENCODE() method, as in this example.
  
    `return '<b>' + ESAPI.encoder().SFDC_HTMLENCODE(title) +'</b>';`

5. Click **Save** and return to the **XSS Apex Mitigations Demo** tab
6. Click the **View the HTML-based XSS!** button again

You should see that the encoding functions neutralized that attack payloads but kept the rest of the data bolded as desired. All functionality is maintained even with the addition of security!

## Take Away XSS Challenge ##

Place holder. More details later.