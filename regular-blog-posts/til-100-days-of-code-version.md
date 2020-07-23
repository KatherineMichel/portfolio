# TIL- 100 Days of Code Version

In April, I was looking through my Twitter newsfeed and came across a [tweet](https://twitter.com/simonw/status/1252050046566055937) by Simon Willison that piqued my curiosity.  

I had the good fortune to get to know [Simon](https://github.com/simonw/) when he was the moderator of a ["State of Django" panel](https://youtu.be/TrAFQW7Wza0) that I took part in at [DjangoCon US 2018](https://2018.djangocon.us/) and I've been following him on Twitter ever since. One of the original creators of the [Django](https://en.wikipedia.org/wiki/Django_(web_framework)) web development framework at the Lawrence Journal World, Simon can often be found discovering novel ways of doing things. So, when he comes across an "interesting" idea, I tend to take notice.

While looking at Hacker News, Simon had come across the idea of a self-rewriting README.md. The idea originated from a project called [TIL (Today I Learned)](https://github.com/jbranchaud/til), created by [Josh Branchaud](https://github.com/jbranchaud/). 

<!--
![](til-100-days-of-code-version-images/simon_tweet.png)
-->

Simon re-implemented the idea and wrote about it in a [blog post](https://simonwillison.net/2020/Apr/20/self-rewriting-readme/). 

In a nutshell, Simon's TIL repo contains a README.md and subject folders filled with TIL files. When Simon learns something new, he can open a corresponding subject folder, create a new markdown TIL file, and fill in what he has learned. When the new file is saved, a program runs and finds the change. In the README.md, each folder name is listed as a category with the names of the files contained in the folder listed below the category as TIL hyperlinks. The program rewrites the README.md, adding the new TIL file name under its category.

## Lightbulb Moment

There is so much to be learned from tech that for years now, I've been curating what I've learned in README.mds the old fashioned way, by hand, as a memory device. I've long known there was undoubtedly a more efficient way of doing it, but hadn't come across a specific better way, and my way worked well enough for the time being. 

Now that I knew about the self-writing README.md, it was clear that a similar implementation could be very helpful to me. I bookmarked the idea as something I would revisit.

### TIL- 100 Days of Code Version

Around the time of Simon's tweet, I was just beginning Stanford's Code in Place course. When Code in Place ended, I wanted to continue building small, fun apps regularly. So, I started taking part in a popular initiative called 100 Days of Code. 

Code in Place participants spend 1 hour a day, most days, working on code-related learning and are encouraged to fork the official [100 Days of Code repo](https://github.com/kallaway/100-days-of-code) to keep a record of their work, as well as tweet their progress from day to day, in order to stay accountable. 

It's a great initiative and I can definitely feel my coding muscles becoming stronger over time. 

However, 100 Days of Code is largely self-guided, and at the beginning, I found myself having difficult settling into a routine. After a couple of weeks of feeling disorganized, I suddenly remembered the idea of a self-rewriting TIL. I had an ephiphany that I could create a TIL- 100 Days of Code version, to keep what I had worked on and learned from day to day organized.

I felt super excited at the thought of creating my own TIL implementation. What better sort of project could there be than to create a programmatic solution to my own problem?

### My TIL Requirements

Requirements for my own TIL:

Basic Features
* TILs listed by category, with a name and hyperlink for each (this is a traditional TIL feature)
* Total number of TILs and total by category (nice to have)
* Ability to add/edit TILs in the browser (I want to be able to do a quick and easy update)
* Powered by a GitHub Action (no need to run this locally)

Special 100 Days of Code Features
* TILs ordered by date, name, and hyperlink, most recently modified first (ordering by date will help keep me accountable)
* Ability to open the most recently modified TIL and tweet a status update (I'll no longer need to manually tweet my progress)

## Implementations I Considered

The first thing I did was spend some time looking at different TIL implementations hosted on GitHub. My intention was not to adopt another person's solution outright, but to get a general idea of what approaches people had taken, and perhaps, why. Ultimately, I wanted to take code apart and go through my own process of discovery and learning, so that I would fully understand how what I was using worked. I also wanted to create an implementation unique to my own needs, including features incorporated specifically for 100 Days of Code. 

I considered four major approaches implemented in Python:
* Simon Willison's [TIL implementation](https://github.com/simonw/til)
* Andrei Cioara's [TIL Implementation](https://github.com/aicioara/til/) 
* Raegon Kim's [TIL implementation](https://github.com/raycon/til/)
* KhanhIceTea's [TIL implementation](https://github.com/khanhicetea/today-i-learned/)

### Simon Willison's Implementation

Simon's [implementation](https://github.com/simonw/til) uses [Datasette](https://datasette.readthedocs.io/), an SQL tool he created and develops as part of a John S. Knight Journalism Fellowship at Stanford. Although I have no doubt that Simon's implementation is a stellar approach that leverages a very cool and useful tool (I plan to use it in the future in a project of my own!), I wanted to start from a blank slate and beginner mindset. I felt his implementation was too opinionated for my current needs.

### Andrei Cioara's Implementation

Andrei Cioara's approach is short and sweet. The bulk of the program exists within the `main()` function and can be followed quite easily, like a list of consecutive instructions.

A `content` variable is assigned to an empty string. As the program progresses, using an addition assignment operator, strings are appended to the `content` variable. First, The README.md header is appended to `content` through a global `HEADER` variable assigned to a multiline, triple-double-quote string. The program then uses `os.walk()` to traverse and sort the directories and files, removing the `.git` and `.github` directories as it goes. As the program "walks," each directory name is formatted as a category and added to the `content`. The program then iterates through each file in each category, formatting the file name as a title, and converting it into a hyperlink. At the end of the program, the README.md file is opened and all of the `content` is written into it.

### Raegon Kim's Implementation

Raegon Kim's [implementation](https://github.com/raycon/til/) also uses `os.walk()` and achieves a similar outcome, but the program is structured very differently. Unlike the procedural approach taken by Andrei Cioara, Raegon Kim splits the program into functions.

At the bottom of the program file, the main function `readme()` is called. Within `readme()`, a `lines` list is created. As the program progresses through the `readme()` function, it creates the "Recently Modified" and "Category" header strings and calls the functions that execute the `os.walk()` and produce the lines for the sections. These new lines are appended to the `lines` list as strings.

The `lines` list is returned by `readme()` and written into the README.md.

### KhanhIceTea's Implementation

### My Journey Through Implementations

```os.walk()``` is a powerful, lesser known function in the Python Standard Library and I thought using it would be a great approach for my TIL. So, I began by creating my own TIL implementation loosely based on Andrei Cioara's approach. 

However, I ran into difficulty when I attempted to add a section for most recently modified files, in addition to categories. I realized that `os.walk()` needed to "walk" the directories and files twice. Adding a second `os.walk()` without using a function seemed like overkill.

So, I began to study Raegon Kim's implementation, to see how he had added a "Recently Modified" category using functions. Although I nearly completed a similar implementation, I wasn't quite happy with the result. 

At that point, I began looking through more examples on GitHub and came across KhanhIceTea's Implementation.

I immediately liked the way it looked, because of its fun use of markdown tables and emojis, plus it ticked the boxes of having both "Recently Modified" and "Category" sections.


To be continued...

## Step By Step Implementation

### Step One: Implement GitHub Actions

### Step Two: Implement a Header and Footer

### Step Three: Implement the Content

### Step Four: Implement Status Tweet
