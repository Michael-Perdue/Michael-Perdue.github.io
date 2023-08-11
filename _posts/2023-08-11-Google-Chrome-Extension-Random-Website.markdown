---
title: My Google Chrome Extension 'Random Website'
date: 2023-08-11
categories: [Additional Projects, Google Chrome Extension 'Random Website']
tags: [javascript, google]     # TAG names should always be lowercase
---

The published google chrome extension that I made for this project can be found at: [HERE](https://chrome.google.com/webstore/detail/random-websites/ionmnimhlhpjggfanmohgipbefpdmaoa). The code for the project is located at the github repo: [HERE](https://github.com/Michael-Perdue/Random-website-extension).

# The motivation for this project 

The reason why I made this project was that I was curious about how a google chrome extension works from the code side and I wanted to refresh myself on Javascript. So following that I decided the one thing that I could add to Chrome is something to do when bored and the logical conclusion from that is a button that takes me to a random website. 

# How the extension works

>The extension has only default permissions and gathers no data from users
{: .prompt-info }

Upon adding the extension nothing happens there's no new tab opened like most extensions, it only does something when you click the symbol for the extension which is the same as this website's favicon (tab icon). Then when you click the symbol you get a black small window pops up with only The text 'start clicking' and a button with the text 'random website. The popup is as below:

![](https://michael-perdue.github.io/assets/Extension-popup.JPG)

Clicking the button will open a 'random' website in a new tab in the same window. It would be impossible to just open a truly random website and I wouldn't really care for 9/10 websites on the internet so instead I created a list of websites that I would want to visit when I just have to kill time or I'm bored. This list is stored in a JSON file called data and is grouped in the files the extension has when you add it, so the extension loads the URLs from the JSON file and it then randomly selects one from the list and opens it in a new tab.

This extension isn't that complicated but it provides a somewhat useful functionality to me of getting rid of some websites from my favorites/bookmarks bar and giving me an easy way of just randomly surfing the web.

# Demo of the project

{% include embed/youtube.html id='hCbbXT8y3kc' %}



