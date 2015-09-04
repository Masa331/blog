---
layout: post
title:  "Make gem using csv database faster"
date:   2015-09-01 21:22:49
comments: true
short: false
author: Přemysl Donát
---
# Gem using CSV database made faster

## Rationale

I'm author of [Czech name](https://github.com/Masa331/czech_name) gem. Simply put it's a database with more than 200k names. Names are stored in 4 CSV files, with the biggest having 100k items. Right now it's terribly slow to get any name from it and it's making the gem unusable.

So i was thinking how to make it faster...  These are some ideas with benchmarks:

## Code

{% gist af4b8b0f8597d9f0bc43 %}

## Benchmarks

| method | real |
|--------------|--------------------|
| Simple db, first name | (  0.000426) |
| Simple db, name in the middle | (  0.421385) |
| Simple db, last name | (  0.764998) |
| Hash db, first name | (  0.955345) |
| Hash db, name in the middle | (  0.000005) |
| Hash db, last name | (  0.000003) |
| Split db, first name in first part | (  0.000561) |
| Split db, last name in first part | (  0.435632) |
| Split db, first name in second part | (  0.819255) |
| Split db, last name in second part | (  0.835236) |
| Alphabet db, first name | (  0.000501) |
| Alphabet db, name in the middle | (  0.084445) |
| Alphabet db, last name | (  0.034563) |
| YAML db, first name | (  1.707125) |
| YAML db, last name | (  1.671748) |

## Result

Important thing to know is that the names are ordered from most to least used. Ok! Let's get started.

First of all **Simple vs. Split** database. Initially i thought that if i split names in two files it will get faster but that's not true at all. **The speed is same**. What does it means? Well.. I guess the file is beeing read lazily? Nice! So it doesn't matter how many files i have if i have to go through them all for the least used name. Also if i look at how long it takes to get middle name and last name, the speed is constant. Ok, that sounds logicall.

**Hash db** is blazingly fast. Once it's in memory it beats everything. But the first access is slow. Unfortunatelly i think most of the time users will search only one name so they won't profit from subsequent cache at all.. :(

**YAML** is slow. And, for such simple data structure, the readability isn't better than CSV.

**Alphabet db** has some nice access times mostly the same for each item. Way better than one big file and the code is really simple yet powerful. It comes with a price of dependency on ActiveSupport and dozen of separate files  but that's not so bad. I know it isn't anything big and i'v probably seen it somewhere before but honestly, i'm proud of it :)

**Do you know about some other way access time could be improved over one big file?**
