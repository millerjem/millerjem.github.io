---
layout: default
title: Environment Setup
parent: CICD Lab
nav_order: 0
---

# Lab 1 - Environment Setup

### Step 1
Install Jenkins on DC/OS

Within your DC/OS UI, select *Catalog* in the left navigation pane.

![jenkins-select-catalog](screenshots/jenkins-select-catalog.png)

Select Jenkins from the list of certified packages.

![jenkins-select](screenshots/jenkins-select.png)

Select *Review & Run*.

![jenkins-review-and-run](screenshots/jenkins-review-and-run.png)

You do not need to change the default configuration settings. Click *Review & Run* again.

![jenkins-review-and-run2](screenshots/jenkins-review-and-run2.png)

Lastly, click *Run Service* to initiate your Jenkins deployment.

![jenkins-run-service](screenshots/jenkins-run-service.png)

You can monitor the Jenkins launch process in your DC/OS Services tab.

### Step 2
Clone the `cd-demo` repository

Our Jenkins instance will need a GitHub repository to monitor for changes.

Please sign in to GitHub.com and navigate to https://github.com/mesosphere/cd-demo.

Once on the `cd-demo` repository homepage, select *Fork* in the upper righthand corner of the page.

![github-fork](screenshots/github-fork.png)

You should now see the forked repository in your GitHub account.
