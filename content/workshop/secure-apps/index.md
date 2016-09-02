+++
date = "2016-07-29T16:18:23+05:30"
draft = true
title = "Secure Apps - Understand Cross Site Scripting "

+++

## Introduction
Salesforce is constantly striving to make our platform as secure as possible. As a result Force.com has many built in security functions to protect end users: some enabeld by default while others require the developers to enable them within their code. While we aim to bake-in as much security constraints as possible because of the flexibility and extensibility of the platform it is completely possible for developers to write insecure code. 

Of all the issues we commonly see developer introduce into their apps, Cross-Site Scripting (XSS) is one of the most widely seen web application vulnerabilities. As a result we’ve chosen this vulnerability to be the focus of our workshop. Our goal is that by the end of this class you should be able to explain XSS, know how to identify it in your code and remediate the issues. To support that we will have numerous demos, where you’ll get to “be the attacker” spotting and exploiting vulnerabilities as well as “being the defender” remediating them.
