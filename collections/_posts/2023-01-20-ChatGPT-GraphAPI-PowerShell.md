---
title: "How to boost your Graph API and PowerShell skills with ChatGPT?"
subtitle:
excerpt: "At the beginning of 2023, it is simply impossible to miss the advent of tools using artificial intelligence. ChatGPT is a very good example of how our professional activities will evolve in the coming months or years."    
tags:
  - Graph API
  - PowerShell
  - ChatGPT
header_img : "./assets/img/posts/2023-01-20_ChatGPT_1.png"
---


At the beginning of 2023, it is simply impossible to miss the advent of tools using artificial intelligence (ChatGPT for text generation, Dall-E or MidJourney for image generation). 

Even if I had vaguely heard about ChatGPT at the end of 2022 from fellow developers ("new technological revolution since the iPhone in 2007"), I really started playing with it early January. I was simply amazed by the tool and use it daily since then. 

In this article, I would like to present some use cases that I have encountered in the last few days. 



# What is ChatGPT? 

First things first, for those who still doesn’t know it: what is ChatGPT? ChatGPT is a solution available in beta on the Internet at <https://chat.openai.com/chat/>. I won't write here about how to use the solution, there are already plenty of articles on the subject. 

The easiest way is probably to let ChatGPT introduce itself 

<img src="https://thijoubert.github.io/assets/img/posts/2023-01-20_ChatGPT_1.png">

While "playing" with ChatGPT, I could ask it a lot of funny things (give me a recipe of chocolate cake without sugar, make a summary of the Lord of the Rings in 100 words, write a poem in alexandrine about the battle of Austerlitz...), but I was especially interested to see how he could help me for my job: 

<img src="https://thijoubert.github.io/assets/img/posts/2023-01-20_ChatGPT_2.png">

The aspect "researching and gathering information on [...] Graph API and Powershell" interested me right away. Like many others, I often make scripts to help me in certain tasks. I don't have the background and skills of a developer, and I often spend a lot of time looking for information. 

The documentation aspect leaves me perplexed, although I would love it if ChatGPT could take over some of this time-consuming (albeit very interesting) activity. 

<img src="https://thijoubert.github.io/assets/img/posts/2023-01-20_ChatGPT_3.png">

Interesting. Let's take a look at some concrete use cases to see if the help provided lives up to its promise. Or if we stay on a simple gadget. 



# Use case #1 - Code completion: List users with duplicate licenses

The first thing I thought of when testing the solution was a topic I've encountered a lot in large companies: managing Microsoft 365 / Office 365 licenses. Many users either don't have the right licenses or have duplicate licenses. Better managing licenses would allow to save money while being in compliance with Microsoft. 

If I want to apply for example conditional access rules based on user risk status, I need to know first who could benefit from the functionality (i.e. who has an Azure AD P2 license, directly or via a licensing plan like Microsoft 365 E5). To the question "List the users who have AAD Premium P2 plan assigned", I get the following answer: 

<img src="https://thijoubert.github.io/assets/img/posts/2023-01-20_ChatGPT_4.png">

A complete answer to say the least! I then asked ChatGPT what was the skuid (without satisfactory answer) and the guid of the AAD Premium P2 skui: 

<img src="https://thijoubert.github.io/assets/img/posts/2023-01-20_ChatGPT_5.png">

Well, I did not have the answer directly, but via Graph Explorer and the provided request, I got the right answer: 

<img src="https://thijoubert.github.io/assets/img/posts/2023-01-20_ChatGPT_6.png">

I could then test the previous query: 

<img src="https://thijoubert.github.io/assets/img/posts/2023-01-20_ChatGPT_7.png">

Error! Looking at the advanced queries documentation, I saw that I need to complete my request with $count=true and with a ConsistencyLevel=eventual header.

<img src="https://thijoubert.github.io/assets/img/posts/2023-01-20_ChatGPT_8.png">

This time I have the right answers, far more elegantly than what I would have done initially (exporting all Office 365 license plans and then filtering out the ones I'm interested in). 



# Use case #2 – Explanation of code: Understand the logic behind the advanced queries of Graph API

Use case #1 motivated me to take a closer look at advanced queries that I had not been able to grasp until now. 

<img src="https://thijoubert.github.io/assets/img/posts/2023-01-20_ChatGPT_9.png">

Interesting: what I remember most is that the letter "x" in "x:x" could be any letter. Thinking quickly, I was able to make a Graph API request that I had been looking for and hadn't found until now: How to list all Microsoft 365 Groups that don't have an owner.

<img src="https://thijoubert.github.io/assets/img/posts/2023-01-20_ChatGPT_10.png">



# Use case #3 – Code completion: Build a script powershell to list all the file of a OneDrive

To make reports on OneDrive and its contents, it is often necessary to browse all the files of the mentioned directories. 

<img src="https://thijoubert.github.io/assets/img/posts/2023-01-20_ChatGPT_11.png">

I noticed that ChatGPT would stop in the middle of its response from time to time. A simple "continue" is enough for it to start again!
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

But ChatGPT only gives me here the first level information and not the files in the sub-folders. By asking it to specify its answer, we get the desired script, via a recursive functionality. 

<img src="https://thijoubert.github.io/assets/img/posts/2023-01-20_ChatGPT_12.png">

ChatGPT's memory is very useful when working on a specific use case to ask it to specify certain information or to ask it for another method if the first one fails. 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}

Even if ChatGPT does not provide the exact information for very precise requests



# Use case #4 - Code completion: Create fake data for test purposes

For demo purposes, I often need to generate fake data (e.g. files, users, groups). Not very complicated – even if I appreciate when the data make sense and are not too technical (ex: user 1, filer A) – but it can be quite fastidious. 

ChatGPT is very useful in this case: 

<img src="https://thijoubert.github.io/assets/img/posts/2023-01-20_ChatGPT_13.png">



# Use case #5 – Explanation of code: Increase your technical skills on PowerShell 

Another aspect where ChatGPT is very useful, is to have a clear presentation of some concepts. 

<img src="https://thijoubert.github.io/assets/img/posts/2023-01-20_ChatGPT_14.png">

Also, ChatGPT can explains different methods for the same result. Here, three ways to filter my users who have an Azure AD P2 license. 

<img src="https://thijoubert.github.io/assets/img/posts/2023-01-20_ChatGPT_15.png">

Si je veux savoir commencer parser un fichier JSON présent sur mon poste, une simple question suffit également : 

<img src="https://thijoubert.github.io/assets/img/posts/2023-01-20_ChatGPT_16.png">



# Use case #6 – Documentation : Analyze of a JSON file or a PowerShell function 

I then tried to submit some PowerShell functions or JSON files to document. The result is quite impressive. 

<img src="https://thijoubert.github.io/assets/img/posts/2023-01-20_ChatGPT_17.png">

Note that ChatGPT refuses to take a script containing about 100 lines. So, in real life in can necessary to provide sample or piece of the document to analyze. 
{: #myid .alert .alert-info .p-3 .mx-2 mb-3}



# Which limitations for ChatGPT? 

- From what I've read, ChatGPT is better in some areas than in others, depending on its training, but on Office 365 it has a very good knowledge of the context
- The solution is multilingual, but it is obvious that the answers are much more accurate in English (although this must depend on the question that was asked: a question on French History will be more complete because the training datasets will be larger).
- On several occasions, the context of my request was not taken in its entirety, for example in the generation of JSON files. I had to insist heavily on missing parameters to get them taken into account 
- No memory between the different conversation channels
- ChatGPT today only relies on datasets from before 2022, which in the IT age can seem like an eternity. 
- The provided scripts contained a significant number of errors. ChatGPT suggested for example to use the PATCH method to update the owners of a group, which is not possible. Another case was the name of the sensitivity labels provided in the metadata of a file.


# In conclusion

ChatGPT, even in its current version, is a great tool that will save a lot of time, provided that you : 
- **Think a lot about the requests**: I read last week, that a future valuable competence will be to know how to prompt AI solution like ChatGPT, MidJourney or Dall-E. This solutions give us answers, and as the saying goes “there is no bad answer, only bad questions”. 
- **Do not hesitate to specify or rephrase some questions**: ChatGPT's memory allows it to come back on certain answers provided, rephrase or correct parts. For example, in the case of the file generation script, I had to go over it 5 or 6 times to get a correct result. 
- **Double check the answers**: ChatGPT is a real revolution in the search for information and in the structuring of ideas and code, but it cannot claim to be sufficient on its own.
- **Provide context with the application but not too much**.

Of course, we are only at the beginning of artificial intelligence - AIs are called "weak" because they only rely on (huge) data sets provided by humans during the learning phase - but it is already possible to see how our working methods will be changed.

The result is that even if the AI provides examples of Graph or PowerShell queries with errors, I see a real advantage because the logic is very quickly transformed into code blocks. In the same way that Google has become a must-have, ChatGPT has become a must-have shortcut. 


*While writing this article, I asked myself if it still made sense to continue writing on my blog, if we can have access to simple and clear information so easily. But the mistakes and the lack of experience of the solution make me say that we still have a few years ahead of us before becoming obsolete on this kind of activity.*