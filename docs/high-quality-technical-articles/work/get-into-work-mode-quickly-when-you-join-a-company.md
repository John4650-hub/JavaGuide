I don't...
Title: How does a new company get to work quickly?
Category: Excerpts of Technical Articles
Tag:

- Work.
  I don't...

> **Recommended language**: It is strongly recommended that each incoming/in-service partner look at the article and that it will help you to make less of a pit. The whole article is logical and comprehensive!
>
> **Original address**: <https://www.cnblogs.com/hunternet/p/14675348.html>

The annual troupe of gold and silver is about to come to an end, and it is believed that many troupe partners have found their work and are about to start or have begun a new one.

Small partners who believe they've had a jump experience know that each new company may face new business, new technology, new teams... This could break your work thinking, coding habits, and cooperative ways...

And for the company, it's not going to give you months to slowly get acquainted. At this point, it is very important to move quickly to work and to use its value as quickly as possible.

Some may be fortunate that entry companies will have well-developed processes and mechanisms to bring new recruits to work quickly in a short period, including through a combination of training. Some people may not be so lucky, as I did a few years ago when I jumped into a factory, when there was no such perfect mechanism for integrating new people as we are now, catching up with the busiest time in the team, just on the afternoon of my first day in the office, leaving me with a few online questions to sort out without any documentation or training. In a situation where many people are unable to adapt quickly and end up under stress, they may turn back.

![bad175e3a380bea.](https://hunter-picgos.oss-cn-shanghai.aliyuncs.com/picgo/bad175e3a380bea.jpg)

So, **how can we get ourselves into a working state quickly and adapt to the new working rhythm?**

New jobs are faced with a pile of code repositories, and many often feel unable to do so. But looking back on the experience of their work and projects, we can see that they have similarities and commonalities. When a new project begins, it usually goes through a few steps: demand

![Project flow](https://oss.javaguide.cn/github/javaguide/high-equality-technology-articles/work/image-202220704193466.png)

There are four main areas of knowledge in this process: business, technology, project, and team. The first stage of our first phase is to have the capacity to work with the team, so the knowledge points we should have as soon as possible start from these four points.

# Business

Many might think that, as a technologist, it should all be about technology? So when they got into a company, they couldn't wait to study some of the technical files, the system architecture, and even the source code, and then they started to dive in, and if you did, it would be a big mistake! In almost all companies, technology exists as a tool. Important as it is, it is also meant to carry on business, which solves the problem of how to do it, while business tells us what to do and why. Once out of business, the existence of technology would be meaningless.

There are two very important ways to understand business.

**I'm on my own.**

If you join a team with a well-developed business training mechanism and a detailed demand file, you may not need to ask too many questions to understand the business, but this is an ideal situation, and most companies do not have that condition. So we can only ask.

It must be mentioned here that, as a new person, there must be a certain degree of resilience. I have seen a lot of new people who are always embarrassed to ask questions because of their introversion, which has made it difficult for them to be part of the team for a long time and to assume more important responsibilities. Don't be afraid of being disciplined or being held back; I believe that the absolute majority of programmers can communicate well!

**Two on the test**

I think testing is definitely the way people get to know team business very quickly. By testing, we can walk through the overall process of the project for which our team is responsible, and in the course of doing so, we can learn about the core business processes quickly if we meet people who can't walk or can't figure out how to do it.

In understanding business, we should be careful not to allow ourselves to seek too many details. Our aim is to be able to understand business processes as a whole, which users we are looking for and which services we are providing...

# Technology

After we have had an initial understanding of the business, it is time to get to the technology, and perhaps you can't wait to turn on the source code, but we should warn you first.

At this point in time, we should think about what we should do if we are to achieve this system on our own, in light of what we know and based on our own work experience. This is an important step that can be followed by a comparison of the differences in how the system works, the reasons for these differences, what is better and what is not, for which we can form our own point of view, and from which we can absorb learning for our own growth!

Next, we're going to understand the technology, but we're not going to flip through the source code. **Systems should be analyzed from the macro to the specifics, from external to internal.**

First of all, let's just get to know what we're using for our team/project. **Java still exists in ... NET, or in multiple languages, whether the project is back-end or full-service, using MySQL or PostgreSQL, so that we may have some expectation of the technology and framework used and of what we are responsible for, which some may have simply understood during interviews.**

Next, we should understand **the system's macro-business architecture.** Which systems are the primary responsibility of our own teams, which modules are the main components of each system, and which external systems interact with them... For these, it would be preferable to sort them out through a flowchart or a mind map.

Then what we're going to do is look at **what external interfaces or services our own team provides.** What are the functions provided by each interface and service? We can continue to test our own systems, and at this point, we can look at which pages are mainly included in the main process, which interfaces are transferred from each page to the back end, and which code repository is assigned to each back end. (If it is a back-end service, we can look at what services we provide, what upstream services are, and what services each upstream service calls to our own team...) Likewise, we should organize it in the form of a drawing.

Then we need to know which external services **our own systems or services rely on, that is, what external systems are needed to support them, which may be outside the team, outside the company, or from other companies. At this point, we can simply access the code and see how the interaction with the external system works, including the communication framework (REST, RPC), the communication protocol...**

At the code level, we should first understand the hierarchical structure of each module's code, how many layers each module has, what the responsibilities of each level are, and what the initial concept is of the entire design of the system, followed by the catalog structure of the code and the location of the configuration file.

Finally, we can look for an example, an interface, a page, where our thinking follows the path of the following code, from entry to intervention, to go through it in its entirety to verify what we have learned.

And here we can conclude our understanding of the technical aspects.

# Project and team

As we mentioned above, the first phase of the new company is aimed at having the capacity to follow the team in the project, and what we need to know next is how the project works.

We should capture some of the key points throughout the entire process, from the design of the requirements to the end of the code-writing library to the release online. For example, whether the project uses the agile or waterfall model, the length of an iterative cycle, the source of demand and the form of presentation, whether there is a needs assessment, what the code writing standards are, how it is constructed upon completion, how it is built, how it is entered into the library, whether submission specifications exist, how it is delivered, what preparation is needed before publication, how the publication tool is used...

All we have to do with the project is observe our colleagues or go through an iterative development.

While understanding the operation of the project, we should also know the team, starting from the outside, what external teams we have to interact with, for example, where the needs come from, whether or not there's a team outside the company, what the upstream team provides, what the team relies on, how the team communicates with each other, and what the usual mode of communication is...

Then, within the team, we should identify the roles within the team and the responsibilities of each individual, so that we can clearly find our counterparts for help. We should also determine whether there are regular events and meetings, such as daily stand-ups, weekly meetings, agreed rules, internal reviews, and mechanisms for sharing...

# Summarizing

The new company, which faces new job challenges, will provide you with a good start by being able to get to work as soon as possible and realize your value.

As a programmer, being able to start working quickly means that we should first have the ability to work with the team. Here, I would draw up some approaches and experiences from the operational, technical, project, and team development perspectives.

On how to get to work quickly, if you have any good ideas or suggestions, please leave a message in the comment area.

Finally, let us look back at the article with a thought map. If you think this article will help you, please focus on the end of the page, where I will often share some of my experiences, thoughts, and insights into growing, learning, and making progress with you.
