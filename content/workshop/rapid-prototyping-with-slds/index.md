+++
date = "2016-07-29T16:18:23+05:30"
draft = true
title = "Rapid prototyping with the Design System Starter Kit and UI Kit"
+++

## Prerequisites

- A recent browser
- Node 6+

### Nice to have

- A GitHub account
- git installed locally

### Optional

- An Heroku account (free)
- Sketch 39+
- Salesforce Sans fonts

****

## What is the Design System Starter Kit?

The starter kit is a rapid prototyping environment using the [Salesforce Lightning Design System](https://www.lightningdesignsystem.com/), HTML, and [Sass](http://www.sass-lang.com/).

It's built for designers, developers, and anyone who wants to build a prototype and dabbles in HTML.

## Get Started

You're going to download the starter kit, install dependencies, and run it locally.

1. Fork the starter kit https://git.io/starter-kit
1. Clone your fork
1. Open the fork in a terminal (`cd path/to/design-system-starter-kit`)
1. Follow the instructions below (also in the Quick Start section of the starter kit's [README.md file](https://github.com/salesforce-ux/design-system-starter-kit/#README))

### `npm install`

Run `npm install` to install npm dependencies, which include the latest public version of the Lightning Design System itself. Depending on the speed of your connection, this may take up to a few minutes.

### `npm run dev`

Run `npm run dev` to start a local development server:
{{% fluid_img "images/npm-run-dev.png" %}}

It will open <http://localhost:3000>, and you should see this something like this:

{{% fluid_img "images/starter-kit-welcome-screen.png" %}}

⚠️ Don't have git installed on your machine? [Download the project as a zip archive](https://github.com/salesforce-ux/design-system-starter-kit/archive/master.zip). Note that you will only be able to run the starter kit locally.

## Get to know the starter kit

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
1. Paste it into `blank.html`, between `{% block content %}` and `{% endblock %}

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

TODO: instructions to show how to edit views and CSS in the browser.

{{% fluid_img "images/add-folder-to-workspace.gif" %}}
{{% fluid_img "images/edit-in-browser.gif" %}}
{{% fluid_img "images/edit-scss-in-browser.gif" %}}

## From Sketch to the browser (section title TBD)

TODO: instructions to copy - paste some components into a view.

## Deploying on Heroku

TODO: instructions on how to deploy a fork to Heroku 

### Linking to GitHub

Now that you’ve got your project deployed, what do you do with any changes thereafter?

Heroku has a really cool feature that integrates with GitHub and does automatic deployments of every pushed commit:

<https://devcenter.heroku.com/articles/github-integration>

Go to “Manage App” › “Deploy” › “GitHub” › “Connect to GitHub” › Authorize if you need to › Search “design-system-starter-kit” and connect” › “Enable Automatic Deploys” 

This means that every time you push a change, your application will be deployed.

So if you edit something and it gets committed and pushed to the master state, a deploy hook will get triggered on Heroku and after a few minutes your application should be updated!

Refresh and check!

Weeee!



****


## Troubleshooting

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
