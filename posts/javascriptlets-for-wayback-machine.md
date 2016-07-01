<!-- 
.. title: Javascriptlets for Wayback Machine
.. slug: javascriptlets-for-wayback-machine
.. date: 2015-11-13 14:44:47 UTC-05:00
.. tags: Productivity, Citing Web Pages, Archiving, Archive.org
.. category: code
.. link: 
.. description: 
.. type: text
-->

Quick post today, the following are two javascriptlets for archiving and retrieving pages from the Internet Archive (originally found [here](https://en.wikipedia.org/wiki/Help:Using_the_Wayback_Machine)). If you aren't familiar with javascriptlets, the general idea is that they function similar to bookmarks but rather than just taking you to a webpage they run some code written in javascript. To add them, open the prompt that you would use to add a webpage to your bookmarks, and copy and paste the code in where the address for the website would typically go.




<center>
![Image of javascriptlet bookmark](http://i.imgur.com/JrJA5FK.png "Example of adding scriptlet to firefox.")
</center>


## Saving Pages to Wayback Machine

This first scriptlet "capture(s) a web page as it appears now for use as a trusted citation in the future."

    javascript:location.href='http://web.archive.org/save/'+location.href

## Get An Archived Version of a Page From The Wayback Machine

This second piece of code is useful for anytime a webpage is down. After adding it you can just click the bookmark you have it saved to, and if someone has previously saved the page to the internet archive, then you can get an archived copy of the webpage. 

    javascript:void(window.open('https://web.archive.org/web/*/'+location.href));
