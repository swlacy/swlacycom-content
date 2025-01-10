---
title: 'Building This Site'
date: '2025-01-10'
draft: true
---

**TL;DR:** I used [Hugo](https://gohugo.io), [GitHub Actions](https://github.com/features/actions), and [Obsidian](https://obsidian.md) to automate publishing content on this site.

---

In 2025, I want to write more. Even if what I publish gets no visibility, taking the time to organize my thoughts enough to share forces me to more deeply understand what I'm thinking about. I maintained a blog in college (at this same domain) and I had a lot of fun exploring personal projects and discussing the parts I found neat. But, it's been a while, and I wanted to start over.

**Problem:** How can I share my writing without a place to publish it? **Solution:** Use Substack, which already has an awesome community; or Medium, which has great SEO; or [Bear Blog](https://bearblog.dev), which is really cool.

**Problem #2:** I use [Obsidian](https://obsidian.md) extensively for taking notes and it would be inconvenient to juggle multiple tools. **Solution #2:** Use [Obsidian Publish](https://obsidian.md/publish).

Obsidian Publish looks great, but it costs nearly $100 USD per year. Since this site is just for fun and I enjoy tinkering, I wondered if I could create similar basic functionality for free (that is, just some mechanism to make my private words public).

Functional requirements:
1. I shouldn't have to leave Obsidian to create, edit, or remove content (text and assets), and I should be able to do this on desktop and mobile.
2. I shouldn't have to locally install or manage other programs to work in tandem with Obsidian (but [Obsidian plugins](https://obsidian.md/plugins) are okay).
3. I shouldn't have to tinker with hosting infrastructure after the initial configuration (goal: set it and forget it).
4. Publishing content changes should take under a minute.
5. There must be version control.
6. It must be free, either by using open source software or staying within service free tiers.

Here's the tech stack I landed on:
1. Content editor: Obsidian (mandatory) with the [obsidian-git](https://github.com/Vinzent03/obsidian-git) plugin. Obsidian is free to use.
2. Site generator and styling: [Hugo](https://gohugo.io) and TailwindCSS, because I am most familiar with these and they're very simple to use. Both Hugo and TailwindCSS are free.
3. Site hosting: Firebase, because I already use Google Workspace. As of publication, Firebase is free on the [Spark Plan](https://firebase.google.com/pricing) for hosting up to 10 GB of content with 360 MB of transfer per day. I don't expect to get anywhere close to those limits.
4. Version control and deployment automation (CD): GitHub and [GitHub Actions](https://github.com/features/actions). As of publication, GitHub repositories are free, and GitHub Actions on public repositories are free for up to 500 MB of storage and 2,000 compute minutes per month. Deployments of this site are measured in single-digit megabytes and seconds.

(Sidenote: this is not the "best" solution. Firebase could be replaced with Netlify, Hugo with Jekyll, or the whole process with something fundamentally different. I just used the technologies familiar to me so I could iterate quickly and have fun.)

Here's a rough sketch of how all the moving parts interplay: 
![Site content deployment process flow](BuildingThisSiteArch.png)

Deployment flow:
1. I use obsidian-git to commit site content changes to the [swlacy/swlacycom-content](https://github.com/swlacy/swlacycom-content) repository.
2. When a change is made on the `main` branch of swlacy/swlacycom-content, the [trigger-deployment.yml](https://github.com/swlacy/swlacycom-content/blob/main/.github/workflows/trigger-deploy.yml) GitHub Actions workflow is executed.
3. This workflow uses a GitHub Personal Access Token (PAT) to subsequently trigger the [deploy.yml](https://github.com/swlacy/swlacycom/blob/main/.github/workflows/deploy.yml) GitHub Actions workflow in the [swlacy/swlacycom](https://github.com/swlacy/swlacycom) repository, which holds the site structure and configuration.
4. The secondary workflow installs dependencies, combines the head of both repositories, builds the site, then uses a Firebase token to push the build artifacts to Firebase. Committing a test change on one file takes about 12 seconds from the initial git push to public visibility on the site.

I used a two-repo approach instead of a simple monorepo to minimize clutter in my Obsidian vault. A monorepo would have brought all the site configuration files and structure along with the content.

The end result satisfies the requirements: I have a directory in my Obsidian vault which holds the text and assets of this site, and the process of committing changes only takes a few seconds. It costs $0 and GitHub manages version control. I can write and publish entirely within Obsidian, without needing to install local build or deployment tools. Since I am only modifying a few files at a time, this lightweight approach should scale well. Charges from exceeding the Firebase free tier could be incurred if this site gets too much traffic – but I think that would be a happy problem!

_This post was published using the described workflow._

---

Resources:
1. Set up Firebase with GitHub Actions: https://firebase.google.com/docs/hosting/github-integration#set-up.
2. How to set up obsidian-git: https://publish.obsidian.md/git-doc/Installation.
3. GitHub Action to trigger another GitHub Action in a different repo: https://github.com/swlacy/swlacycom-content/blob/main/.github/workflows/trigger-deploy.yml.
4. How to use Hugo mounts, which allow separating site content into a different repo without using git submodules: https://gohugo.io/hugo-modules/configuration. Example configuration: https://github.com/swlacy/swlacycom/blob/main/config/production/hugo.yml.