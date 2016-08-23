+++
date = "2016-07-29T16:18:23+05:30"
draft = true
title = "Build a Suggestion Box App with Lightning Experience"

+++


1. [Introduction](#introduction)
2. [Create the SuggestionBox](#create-the-suggestionbox)
3. [Create the SearchBar component](#create-the-searchbar-component)
4. [Create the SearchKey Event and SuggestionList Component](#create-the-searchkey-event-and-suggestionlist-component)
5. [Create a Suggestion Detail Component](#create-a-suggestion-detail-component)
6. [Summary](#summary)

## Introduction
In this project, you learn how to build a Lightning Application on App Cloud from start to finish.If you're new to App Cloud, the goal is to introduce you to the basics of app building together with introduction to the new lightning platform and basics to develop application using lightning components.If you're familiar with the App Cloud Admin features—managing users and security, customizing standard objects, and so on—the goal is to apply those skills to developing new application and learn how to extend the functionality of these applications using lightning. You need a Developer Edition org to complete this project. If you don't have one, you can sign up [here](https://developer.salesforce.com/signup).

You will build a Suggestion Box Lightning Application that allows employees to submit suggestions, search for existing suggestions and vote for them.All of this with the following steps:

* Install the pre-created building blocks of the Suggestion Box App by clicking [here](http://bit.ly/df16_sb_package)
  This package includes the app definition, data model, validation rules, process and reports and dashboard which together form the basic Suggestion box Application
* Extend this application using Lightning components to design a stand-alone Suggestion Box Application which will look somethng like this:
 
![alt text](https://github.com/sonamraju14/df16workshops/blob/master/content/workshop/suggestion-box/images/SB_app.png)

Each rectangle in this image represents a lightning component:
* Red box: Displays the "add suggestion" functionality
* Yellow box: Displays the "search suggestion" functionality
* Green box: Displays the "results" of the search functionality
* Blue box: Displays the "details of the selected record" in the search functionality
* Purple box: "encampasses all the above functionalities into a single component" which then sits inside a Lightning Application

Let's begin with exploring our prebuilt Suggestion Box App which was installed using the package.

All eyes on the screen!

Now, that we know and understand how we can build a basic application using point-and-click, let us now extend this app using lightning components.

The Lightning Component framework is a UI framework for developing dynamic web apps for mobile and desktop devices. In this workshop, you'll create a simple Lightning Application that is built of smaller components which will help you create, search and vote for existing suggestions in your org. You'll start by creating an Apex controller class, then create the Lightning Components and an event handler and finally render the Application UI using all the components together.

## Create a Server-side Apex Controller Class

Create a class to access data from Suggestion custom object:

1. In your DE environment, click Your **Name | Developer Console**.
2. Select **File | New | Apex Class**.
3. For the class name, enter **SuggestionController** and then click **OK**.
4. In the body of the class (i.e. between the {} braces), enter the following code

```
public class SuggestionController {

  	@AuraEnabled
	public static List<Suggestion__c> getSuggestions() {
		return [SELECT id, Name, Status__c, Suggestion_Category__c, Suggestion_Description__c,	Implemented_Date__c, createdDate 
			FROM Suggestion__c 
			ORDER BY createdDate DESC];
	  }
    
    @AuraEnabled
    public static Suggestion__c saveSuggestion(Suggestion__c suggestion) {
        upsert suggestion;
        return suggestion;
    }
    
    @AuraEnabled
    public static List<Suggestion__c> findAll() {
        return [SELECT id, name, Suggestion_Description__c, Vote_up__c FROM Suggestion__c LIMIT 50];
    }
    
    @AuraEnabled
    public static List<Suggestion__c> findByName(String searchKey) {
        String name = '%' + searchKey + '%';
        return [SELECT id, name, Suggestion_Description__c, Vote_up__c FROM Suggestion__c WHERE name LIKE :name LIMIT 50];
    }  
   
    @AuraEnabled
    public static Suggestion__c findById(String suggestionId) {
        return [SELECT id, name, Status__c, Suggestion_Category__c, Suggestion_Description__c,Vote_up__c
                    FROM Suggestion__c WHERE Id = :suggestionId];
    }
    
    @AuraEnabled
    public static Suggestion__c voteSuggestion(String suggestionId) {
        Suggestion__c s = new Suggestion__c();
        s=[SELECT id, name, Status__c, Suggestion_Category__c, Suggestion_Description__c,Vote_up__c
                    FROM Suggestion__c WHERE Id =:suggestionId];
        
        s.Vote_up__c = s.Vote_up__c + 1;
        upsert s;
        return s;
    } 
} 
```
@AuraEnabled enables client and server-side access to the controller method. Select **File | Save**.


### Create Component 
To be used by employees to submit new suggestions

TODO

## Create the SearchBar component
To be used by employees to search for existing suggestions

TODO

## Create the SearchKey Event and SuggestionList Component
We will create a suggestionlist component to list down the existing components in the system and an event to connect the searchbar and suggestionlist component

TODO

## Create a Suggestion Detail Component
We will create this component to display the suggestion selected by emeployee from the SuggestionList component.This component will have a vote up button so that employees can vote for the suggestions they like.

TODO

# Summary

TODO



