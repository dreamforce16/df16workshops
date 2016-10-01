+++
date = "2016-07-29T16:18:23+05:30"
draft = true
title = "Rapid Prototyping with the Design System UI Kit and Starter Kit"
+++

## What you’ll learn

By the end of this workshop, you'll know how to:

1. Get your idea into a static prototype in Sketch
2. Create a prototype of your idea using the [Lightning Design System](https://www.lightningdesignsystem.com)
3. Iterate on that prototype directly *in* the browser
4. Deploy the result for free to Heroku in a few clicks only

In a nutshell: getting high definition concepts in front of stakeholders and customers at minimum cost.

## Why Rapid Prototyping?

Rapid prototyping is a very effective technique for testing new ideas and iterating on them. Experimenting and refining concepts lead to a better user experience for your customers and force.com users.

The closer to reality and the more interactive a prototype is, the better. But developing a whole web solution "just for research" can be costly. That's where rapid prototyping comes into play! Both the [Design System UI Kit](https://github.com/salesforce-ux/design-system-ui-kit) and the [Design System Starter Kit](https://github.com/salesforce-ux/design-system-starter-kit) are tools that help reducing that cost while allowing you to build high quality prototypes that look and feel like the Lightning Experience.

[Read more about the benefits of rapid prototyping, Lean UX and agile development on user experience](http://www.uxmatters.com/mt/archives/2013/03/are-rapid-prototyping-lean-ux-and-agile-development-good-for-user-experience.php)

## Prerequisites

- A recent browser (Chrome or Firefox recommended)
- [Node.js](https://nodejs.org/en/) 4.2 or up

### Nice to have

- A [GitHub](https://github.com/) account
- [Git](https://git-scm.com/)
- Not familiar with the command line? Use [GitHub Desktop](https://desktop.github.com/) instead!

### Optional

- An [Heroku](https://www.heroku.com/) account (free)
- [Sketch](https://www.sketchapp.com/) 39 or up
- [Salesforce Sans fonts](https://github.com/salesforce-ux/design-system-ui-kit#salesforce-sans-fonts)

****

## What is the Design System Starter Kit?

The starter kit is a rapid prototyping environment using the [Salesforce Lightning Design System](https://www.lightningdesignsystem.com/), HTML, and [Sass](http://www.sass-lang.com/).

It's built for designers, developers, and anyone who wants to build a prototype and dabbles in HTML.

## Get Started

You're going to download the starter kit, install dependencies, and run it locally.

1. Fork the starter kit
	1. Go to https://git.io/starter-kit
	2. Click the "Fork" button
1. In a terminal window, `cd` into the directory where you'd like to clone the repository (e.g. `cd ~/Documents`)
1. Clone your fork (`git clone https://github.com/[YOUR USERNAME]/design-system-starter-kit.git` – or using the GUI of your choosing)
1. Open the fork in a terminal (`cd path/to/design-system-starter-kit`)

Then, follow the instructions below (also in the Quick Start section of the starter kit's [README.md file](https://github.com/salesforce-ux/design-system-starter-kit/#README))

### 4. Run `npm install`

Run `npm install` to install npm dependencies, which include the latest public version of the Lightning Design System itself. Depending on the speed of your connection, this may take up to a few minutes.

### 5. Run `npm run dev`

Run `npm run dev` to start a local development server:
{{% fluid_img "images/npm-run-dev.png" %}}

It will open <http://localhost:3000>, and you should see this something like this:

{{% fluid_img "images/starter-kit-welcome-screen.png" %}}

⚠️ Don't have git installed on your machine? [Download the project as a zip archive](https://github.com/salesforce-ux/design-system-starter-kit/archive/master.zip). Note that you will only be able to run the starter kit locally.

## Get to know the starter kit

Now you've installed the starter kit locally, let's have a look at what's inside.

The kit's file structure looks like this:

- [`/src/assets/`](https://github.com/salesforce-ux/design-system-starter-kit/tree/master/assets/): images, fonts
- [`/src/scripts/`](https://github.com/salesforce-ux/design-system-starter-kit/tree/master/scripts/): JavaScript files*
- [`/src/styles/`](https://github.com/salesforce-ux/design-system-starter-kit/tree/master/styles/): stylesheets (built using Sass)
- [`/src/views/`](https://github.com/salesforce-ux/design-system-starter-kit/tree/master/views/): markup and data for your pages and templates

\* the Lightning Design System does not include any JavaScript, but the starter kit bundles a few basic interactions for the most common components (e.g. tabs, dropdowns).

## Let's build our first page

Included in the starter kit:

- `index.html`: the welcome screen
- `example.html`: an example that showcases the possibilities offered by the starter kit
- `blank.html`: a blank template ready to host _your_ prototype!

The easiest way to throw a quick prototype together is to copy and paste ready-made components from the Lightning Design System website into `blank.html`.

Let's copy-paste a related list component into `blank.html`:

1. Go to <https://www.lightningdesignsystem.com/>
1. Search for "Related List With Table"
1. Follow the first search result
1. Copy the code (below the component itself)
1. Paste it into `blank.html`, between `{% block content %}` and `{% endblock %}`

Your browser should display this at <http://localhost:3000/blank.html>:

{{% fluid_img "images/first-component-in.png" %}}

## Designing in the browser

### 1. Live reload

You may have seen it when you were adding a component to the markup: any change made to your prototype will display in the browser automatically, without you having to hit the "reload" button!

Try for yourself:

1. Open <http://localhost:3000/blank.html>
1. In `blank.html`, find occurrences of `Contacts (will truncate)`
1. Replace with `Any text you want!`
1. Admire the page as it updates itself with the new text (see animation below)!

{{% fluid_img "images/livereload.gif" %}}

Feel free to add more components to the page and go crazy! You'll see the results appear instantly in the browser. Oh… and one more thing: *all* browsers and devices connected to your local development environment will reload at the same time!

### 2. Directly from Chrome

1. Open <http://localhost:3000>
1. Inspect any element on the page (**right click, inspect element**), this will open the Chrome DevTools
1. In DevTools, go to the **Sources** tab
1. **Right-Click** anywhere in the left pane and select **Add folder to workspace**
1. **Find the design-system-starter-kit directory** (where it was cloned)
1. Hit **Select**
1. Click **Allow** to give the DevTools access to your drive

{{% fluid_img "images/add-folder-to-workspace.gif" %}}

Congrats, you now have access to your directory structure from within Chrome!

Let's see what you can do:

#### Editing pages in the browser

{{% fluid_img "images/edit-in-browser.gif" %}}

#### Editing styles in the browser

{{% fluid_img "images/edit-scss-in-browser.gif" %}}

## From Sketch to the browser (section title TBD)

Let's do some copy and pasting! Errr… some coding!

In the `src/views` directory, there's a file called **`workshop.html`**. That's where we're going to make the magic happen.

TODO: More instructions to copy - paste some components into a view.

## Deploying on Heroku

You've pushed your work to your fork (`git push origin master`), and now it's now time to share your prototype with the world!

Deploying your fork to Heroku is free and only takes 2 minutes.

1. Head to your fork on GitHub
1. Click the **Deploy to Heroku** button
1. **Submit** the form (**Deploy for Free** button)
1. Click the **View** button

That's it! You can now share the link with the rest of the world!

### Linking to GitHub

Now that you’ve got your project deployed, what do you do with any changes thereafter?

Heroku has a really cool feature that integrates with GitHub and does automatic deployments of every pushed commit:

<https://devcenter.heroku.com/articles/github-integration>

Go to “Manage App” › “Deploy” › “GitHub” › “Connect to GitHub” › Authorize if you need to › Search “design-system-starter-kit” and connect” › “Enable Automatic Deploys” 

This means that every time you push a change, your application will be deployed.

So if you edit something and it gets committed and pushed to the master branch, a deploy hook will get triggered on Heroku and after a few minutes your application will automatically update!

Refresh and check!

Weeee!

****


## Troubleshooting

### Issues with Git

Not familiar with the command line? Use [GitHub Desktop](https://desktop.github.com/) instead!

Here's how to clone a repository using GitHub Desktop: <https://help.github.com/desktop/guides/contributing/cloning-a-repository-from-github-desktop/>

#### Permission denied (publickey)

This error happens when you're trying to clone the repository:

```
Initialized empty Git repository in `/Users/username/Documents/design-system-starter-kit/.git/`
Permission denied (publickey).
fatal: The remote end hung up unexpectedly
```

A "Permission denied" error means that the server rejected your connection. There could be several reasons why, and the most common examples are explained below.

- [Follow these instructions to set up your SSH keys](https://help.github.com/articles/generating-an-ssh-key/)
- [Error: Permission denied (publickey)](https://help.github.com/articles/error-permission-denied-publickey/) on GitHub's help pages

### Issues with Node and npm

If you're encountering issues with Node modules, perform those actions in this order:

Verify that you're running Node 6 or up.

```
node --version
# v6.x.x
```

Update npm:

```
npm install npm --global
```

Re-install dependencies:

```
cd path/to/design-system-starter-kit
npm cache clear
rm -Rf node_modules
npm install
```

### Other issues

**Dropdowns and tabs don't work in your browser?** The bundled JavaScript only works in the most recent browsers.
