---
layout: post
title: 2021-11-13-Two-Years-From-Now
---

Hello there, almost two years from now since I started my career at infosec. I am writing to share my unorthdox journey to cybersecurity - from literally nothing to jumping up and down, pivoting left and right until starting to find my initial foothold and attempt to privilege escalate myself. 

**AS** much as I had gained professional knowledge and experience in my previous career (as a financial auditor), it is sad to say they contribute less and less in my infosec journey. Nevertheless, it is fair to say nothing is not important and it is fairer to say to excel in this industry it's the mentality that matters more - how we keep a "try harder" mentality while staying open and admit one's limitations and hack oneself beyond. And all in all, passion to learn does matter.

I could still remember the very first day I met my first infosec employer, he spoke to me "**You must unlearn what you have learnt before**" I started my stereotypical career in accounting and finance in Asia - where hierachy matters a lot because there exists the necessity of multiple layers of checks and reviews on work in the hopes of avoiding fraud risks and misstatements. The art of hierachy intensifies further in the finance industry (in particularly in Asia) in the sense that I was educated to make decisions and take actions by submission to the higher ranks. On the contrary, in the infosec industry, knowledge trasnfer and exchange prevail over authoritarian hierachy. To quote Eric Steven Raymond's [Freedom is good](http://www.catb.org/~esr/faqs/hacker-howto.html#believe4), > "**it's almost a moral duty for you to share information, solve problems and then give the solutions away just so other hackers can solve new problems instead of having to perpetually re-address old ones**"  


![You must unlearn what you have learnt before](/images/yoda1.png)


Thank you KT, for giving me the opportunity in the first place.

I noticed this becomes more important since I have moved to Europe and recognize the difference between the lateral thinking mindsets (more popular in western societies) and driven mentality (more popular in Asia). Seeing the difference and thanks to the encouragement by [JxTx](https://jxtx.gitlab.io/), I am also learning to blog my experience and learning given my background and unorthdox journey to Cybersecurity, hopefully it'll be an enjoyable read to anyone interested :)


# Situational Awareness
My very first project in cybersecurity is a Behavioral based Anti-virus Implementation Project for a big organization. The large organization faced difficulties to "encourage" its IT consulting employees to install AV solutions and ended up requesting external parties (my assigned role) to step in and facilitate the installation on each domainless project-based computer) - which is ridiculous by the way. :-( 

As embarassing as it was (the fact that it sounds like an IT support / Project Management task, which it was!), I learnt my very first "ping" and "whoami" command there while at the same time being a consultancy project it was required for me to identify the risk areas the organization may face. Questions such as the below become my daily conversation with my client:
- How does the organization keep track of its asset management?
- What were the reasons IT Support face difficulty in such situation? How exactly do they work for the uncontrolled employees. 
- How does the employees of domainless computers work in the organization?

Initially, as a newer to IT / Infosec, it took me a while to understand what is an AD/domain and the different roles of IT personnels within an organization - but then as time goes by these sets of question natuarlly forms a part of "**situational awareness**" which was introduced by my ex-colleague Charz and it appears to be significant to a tester role - whether it's an infrastructure test or a web application test - to apply situational awareness is inevitably crucial.

For instance, to tweak a bit the above sets of questions into red teaming, they might just be:
- What is the IT solution of the organization to keep track of IT assets? ManageEngine? What version is it? Is there any vulnerabilities with this software? 
- If there is no IT solution to keep track of - What will be the human behaviour of keeping track of those assets? (Perhaps an excel in a share folder? If sensitive information are there, they could naturally become some low hanging fruits)
- Which remote protocols do the IT support use to assist his/her clients ? RDP? AnyDesk? Teamviewer? Is there any ways where malicious actors can manipulate?

Likewise, although I am not proud to be saying I haven't test enough web apps to be a proficient web app tester, I do think situational awareness can also be term that can be  applied to web app pentesting (If there is a better terminology, do please let me know, I am happy to put it more correctly)

- What languages are this application using? What is the backbone of this application/server? 
- What are the controls the web developers have already applied to this web applications? URL encoding? Input Validation etc?
- How did the web developer build and maintain this site? How do they perform the work? Can we leverage that to impersonate them and make changes to the server?

I believe these are the very basics to be a pentester . But sometimes, when work piles up (for example, having 10+ projects happening at the same time in a week for a consultant in Asia - unfortunately saying this had happened to myself and it was not a good balance) or when we become lazy by simplying firing up the standard sets tools to perform a test - we ignore the importance of understanding / being aware of what is happening around the situation you were placed into - the test.


## My journey so far

### Dont underestimate yourself but don't be an excessive wanker

#### Going on






Lastly, a little reminder to myself && important 2cents to anyone with zero knowledge/background like me: **Be patient but also be proud of yourself :) **

