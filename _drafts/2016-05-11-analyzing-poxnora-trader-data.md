---
layout: post
title:  "Analyzing Poxnora trader data"
date:   2016-05-11 21:22:49
comments: true
author: Přemysl Donát
---
# Analyzing Poxnora trader data

## Prolog

Ok, so here is the thing. I got few games which i just love and every once in a while i come back to them and play a bit. They are Half-Life, EVE Online, Original War, Nox and PoxNora. The last one of them, PoxNora, is collectible strategy game. Basically you buy "runes", create pack and then play against other players. Something like MtG(just better really :P). Now there are dozens of runes and it would take a lot of money to buy them. The other ways how to get them is to grind for shards from which you can craft runes or trade them with other player. Every trade has to be done through web interface and is viewable by others. There is nothing like private trades. Super cool thing is that closed trades are also accessible. Not through some API unfortunatelly but still.. Another nice thing is that the trades have sequential key so we can just iterate through all of them. There is like 4m of trades now. I don't know why but trades under like 400000 are not accessible so that means we got like 3.6m trades we can work with. Let's see what we can find out.

## Part I: The scrape

Here i'm gonna describe the technical part of project. If you are not interested how i scraped the site, just skip to next section.

Ok, so my stack is: *Ruby*, typhoeus, nokoggiri and SQLite. And i'm using Rails to view the results. The repo is here: Repo

Notes:
- Interesting: their markup does have different structure across different rune types previews - so i need to use slightly different selectors
- I didn't know there is any html element <font> but they use it for bid status
