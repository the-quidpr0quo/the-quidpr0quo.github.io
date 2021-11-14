---
layout: post
title: 2021-11-13-Two-Years-From-Now
---

Hello there, almost two years from now since I started my career at infosec. I am writing to share my unorthdox journey to cybersecurity - from literally nothing to jumping up and down, pivoting left and right until starting to find my initial foothold and attempt to privilege escalate myself. 

**AS** much as I had gained professional knowledge and experience in my previous career (as a financial auditor), it is sad to say they contribute less and less in my infosec journey. Nevertheless, it is fair to say nothing is not important and it is fairer to say to excel in this industry it's the mentality that matters more - how we keep a "try harder" mentality while staying open and admit one's limitations and hack oneself beyond. And all in all, passion to learn does matter.

I could still remember the very first day I met my first infosec employer, he spoke to me "**You must unlearn what you have learnt before**" I started my stereotypical career in accounting and finance in Asia - where hierachy matters a lot because there exists the necessity of multiple layers of checks and reviews on work in the hopes of avoiding fraud risks and misstatements. The art of hierachy intensifies further in the finance industry (in particularly in Asia) in the sense that I was educated to make decisions and take actions by submission to the higher ranks. On the contrary, in the infosec industry, knowledge trasnfer and exchange prevail over authoritarian hierachy. To quote Eric Steven Raymond's [Freedom is good](http://www.catb.org/~esr/faqs/hacker-howto.html#believe4), > "**it's almost a moral duty for you to share information, solve problems and then give the solutions away just so other hackers can solve new problems instead of having to perpetually re-address old ones**"  


![You must unlearn what you have learnt before](/images/yoda1.png)


Thank you KT, for giving me the opportunity in the first place.

I noticed this becomes more important since I have moved to Europe and recognize the difference between the lateral thinking mindsets (more popular in western societies) and driven mentality (more popular in Asia). Seeing the difference and thanks to the encouragement by [JxTx](https://jxtx.gitlab.io/), [captmeelo](https://captmeelo.com/about) and [1chidan](https://medium.com/@1chidan/zero-to-oscp-concise-edition-b5ecd4a781c3) with whom I have actually met IRL after reading his post 2 years ago,  I am also learning to blog my experience and learning given my background and unorthdox journey to Cybersecurity, hopefully it'll be an enjoyable read to anyone interested :)


# Situational Awareness
My very first project in cybersecurity is a Behavioral based Anti-virus Implementation Project for a big organization. The large organization faced difficulties to "encourage" its IT consulting employees to install AV solutions and ended up requesting external parties (my assigned role) to step in and facilitate the installation on each domainless project-based computer) - which is ridiculous by the way. :-( 

As embarassing as it was (the fact that it sounds like an IT support / Project Management task, which it was!), I learnt my very first "ping" and "hostname" command there while at the same time being a consultancy project it was required for me to identify the risk areas the organization may face. Questions such as the below become my daily conversation with my client:
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

I believe these are the very basics to be a pentester . But sometimes, when work piles up (for example, having 10+ projects happening at the same time in a week for a consultant in Asia - unfortunately saying this had happened to myself and it was not a good balance) or when we become lazy by simplying firing up the standard sets tools to perform a test - we ignore the importance of understanding / being aware of what is happening around the situation you were placed into - the test / being aware to this ever changing post-apocalyptical world.


# My journey so far
I have tried a bit of everything but I'm still proud to say I dunno everything. Back in the days, because we were such a big professional service/consultancy provider, I got to try a bit of everything - from security awareness training, phishing simulations to internal infrastructure testing, red teaming, cyber attacks simulations etc. etc.
Thanks to my old team and special thanks to Hsin and Charz who both have been so patient and open to nurture and grow me during my times at the organization. 

My biggest growth so far I'd still say they were when I got the chance to shadow a very good senior tester and preparing professional exams. For example, at the time of writing, I recalled the concept of "situational awareness" - which can sometimes be forgotten by freshers. As to exams, I do enjoy the structure of a good exam / bootcamp / lab experience where you can try, fail and reflect. Some of them I have attempted more than once - do not take it too hard for a failure on an exam and the fact that the more times you attempted the more experience you have compared to having one trial only. (of course, exam re-attempts could empty your wallets though) I used to have a colleague who attempted the OSCP exam 4 times and he actually becomes a very skillful tester. However if one were to only take exams for certification, that'd be completely wrong. I am putting this into an important note because I personally come from a "fast-food" city where everyone focus excessively into results/ being too result-oriented.

The best learning is in the process not the result. So I believe that's why doing a write-up is sooooooo important. Plus where do we put our memories if we don't compress them?

# Dont underestimate yourself but don't be an excessive wanker
There were many times I felt overwhelmed because there were so much to learn and I regret being late on this table of fun and joys - but it is also important to be patient while taking things slowly to enjoy the process of learning. A certain level of confidence does help you "try harder". And the same kind of certainty of knowledge also helps with solidate / weaponize one's skills. 

That being the case, overconfidence on a paritcular topic might just as well make you blind - the fact that you may overlook stuff you should be looking at in the first place. 


# Going on

The day before writing this blog, I also had a good conversation with two of my ex-"juniors", who are also good CTF players. What inspires me is the linkage of CTF culture with this industry, so tricky but so beneficial in a way that player focuses on concepts and the art of "computing research && discoveries" (..cough..cough, correct me if I have phrased it wrong and forgive me being without a good computing background) All in all, at the moment I am also looking for to partake more CTF competitions and write up my experiences despite the day-to-day work and life - but I'm equally happy to pursue my passion to the next level / challenges.



Lastly, a little reminder to myself && important 2cents to anyone with zero knowledge/background like myself: **Be patient but also be proud of yourself** :-)

