---
title: Summary of Useful GitHub Tips
category: Development Tools
tag:
  - Git
---

I have been using GitHub for over 6 years, and today I am sharing some useful GitHub tips that I believe will be helpful for everyone following JavaGuide.

## One-Click Generation of GitHub Resume & Annual Report

You can generate an online GitHub resume with one click through [https://resume.github.io/](https://resume.github.io/).

When I participated in campus recruitment, I included an online GitHub resume in my personal information. I believe this gives interviewers the impression that you are knowledgeable, which can enhance your overall impression.

However, if you don't have any projects on your GitHub, it's better not to include it in your resume. The generated result is shown in the image below.

![GitHub Resume](https://oss.javaguide.cn/2020-11/image-20201108192205620.png)

Through <https://www.githubtrends.io/wrapped>, you can generate a personal annual report for GitHub, which lists your project contributions for the year, your most used programming languages, and detailed contribution information.

![](https://oss.javaguide.cn/github/dootask/image-20211226144607457.png)

## Personalized GitHub Homepage

GitHub currently supports customizing the display of certain content on your personal homepage. The display effect is shown in the image below.

![Personalized Homepage Display](https://oss.javaguide.cn/java-guide-blog/image-20210616221212259.png)

Achieving this is very simple; you just need to create a repository with the same name as your GitHub account and customize the content of `README.md`.

The custom content displayed on your homepage is the content of `README.md` (_for those who are not familiar with Markdown syntax, take a 5-minute break_).

![Create a Repository with the Same Name as Your GitHub Account](https://oss.javaguide.cn/java-guide-blog/image-20201107110309341.png)

This can also be quite creative! For example, through the open-source project [github-readme-stats](https://hellogithub.com/periodical/statistics/click/?target=https://github.com/anuraghazra/github-readme-stats), you can display dynamically generated GitHub statistics in your README. The display effect is shown in the image below.

![Dynamically Generated GitHub Statistics via github-readme-stats](https://oss.javaguide.cn/java-guide-blog/image-20210616221312426.png)

I won't elaborate further on personalized homepages; interested friends can explore it themselves.

## Custom Project Badges

The project badges you see on GitHub are generated through [https://shields.io/](https://shields.io/). The badges for my JavaGuide project are shown in the image below.

![Project Badges](https://oss.javaguide.cn/2020-11/image-20201107143136559.png)

Moreover, you can not only generate static badges, but shield.io can also dynamically read the status of your project and generate corresponding badges.

![Custom Project Badges](https://oss.javaguide.cn/2020-11/image-20201107143502356.png)

The generated badges describing the project status are shown in the image below.

![Badges Describing Project Status](https://oss.javaguide.cn/2020-11/image-20201107143752642.png)

## Automatically Add Contribution Graph Icons to Projects

Using the tool repobeats, you can add basic contribution charts to GitHub projects, which is quite nice 👍

![](https://oss.javaguide.cn/github/dootask/repobeats.png)

Address: <https://repobeats.axiom.co/>.

## GitHub Emojis

![GitHub Emojis](https://oss.javaguide.cn/2020-11/image-20201107162254582.png)

If you want to use emojis on GitHub, you can find them here: [www.webfx.com/tools/emoji-cheat-sheet/](https://www.webfx.com/tools/emoji-cheat-sheet/).

![Online GitHub Emojis](https://oss.javaguide.cn/2020-11/image-20201107162432941.png)

## Efficiently Reading Source Code of GitHub Projects

Recently, GitHub launched Codespaces, which provides an online IDE similar to VS Code, but it is not fully developed yet.

Here are a few ways I commonly use to read source code of GitHub projects.

### Chrome Extension Octotree

This is a well-known method and one of my favorites. After using Octotree, the webpage sidebar displays the project in a tree structure, giving us an IDE-like experience for reading source code.

![Chrome Extension Octotree](https://oss.javaguide.cn/2020-11/image-20201107144944798.png)

### Chrome Extension SourceGraph

When I
