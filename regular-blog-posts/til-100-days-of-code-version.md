# TIL- 100 Days of Code Version

In April, I was looking through my Twitter newsfeed and came across a [tweet](https://twitter.com/simonw/status/1252050046566055937) by Simon Willison that piqued my curiosity.  

I had the good fortune to get to know [Simon](https://github.com/simonw/) when he was the moderator of a ["State of Django" panel](https://youtu.be/TrAFQW7Wza0) that I took part in at [DjangoCon US 2018](https://2018.djangocon.us/) and I've been following him on Twitter ever since. One of the original creators of the [Django](https://en.wikipedia.org/wiki/Django_(web_framework)) web development framework at the Lawrence Journal World, Simon can often be found discovering novel ways of doing things. So, when he comes across an "interesting" idea, I tend to take notice.

While looking at Hacker News, Simon had come across the idea of a self-rewriting README.md. The idea originated from a project called [TIL (Today I Learned)](https://github.com/jbranchaud/til), created by [Josh Branchaud](https://github.com/jbranchaud/) that was inspired by a [thoughtbot](https://github.com/thoughtbot/til) project of the same name. 

<!--
![](til-100-days-of-code-version-images/simon_tweet.png)
-->

Simon re-implemented the idea and wrote about it in a [blog post](https://simonwillison.net/2020/Apr/20/self-rewriting-readme/). 

In a nutshell, Simon's TIL repo contains a README.md and subject folders filled with TIL files. When Simon learns something new, he can open a corresponding subject folder, create a new markdown TIL file, and fill in what he has learned. When the new file is saved, a program runs and finds the change. In the README.md, each folder name is listed as a category with the names of the files contained in the folder listed below the category as TIL hyperlinks. The program rewrites the README.md, adding the new TIL file name under its category.

## Lightbulb Moment

There is so much to be learned from tech that for years now, I've been curating what I've learned in README.mds the old fashioned way, by hand, as a memory device. I've long known there was undoubtedly a more efficient way of doing it, but hadn't come across a specific better way, and my way worked well enough for the time being. 

Now that I knew about the self-writing README.md, it was clear that a similar implementation could be very helpful to me. I bookmarked the idea as something I would revisit.

## TIL- 100 Days of Code Version

Around the time of Simon's tweet, I was just beginning Stanford's Code in Place course. When Code in Place ended, I wanted to continue building small, fun apps regularly. So, I started taking part in a popular initiative called 100 Days of Code. 

100 Days of Code participants spend 1 hour a day, most days, working on code-related learning and are encouraged to fork the official [100 Days of Code repo](https://github.com/kallaway/100-days-of-code) to keep a record of their work, as well as tweet their progress from day to day, in order to stay accountable. 

It's a great initiative and I can definitely feel my coding muscles becoming stronger over time. 

However, 100 Days of Code is largely self-guided, and at the beginning, I found myself having difficult settling into a routine. After a couple of weeks of feeling disorganized, I suddenly remembered the idea of a self-rewriting TIL. I had an ephiphany that I could create a TIL- 100 Days of Code version, to keep what I had worked on and learned from day to day organized.

I felt super excited at the thought of creating my own TIL implementation, which is a good sign. What better sort of project could there be than to create a programmatic solution to my own problem?

## My TIL Requirements

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

The upside of looking at different TIL implementations and trying them out right away was that it got me experimenting. The downside was that I overlooked a potentially important performance issue. See the "[What I Learned About Performance](https://github.com/KatherineMichel/portfolio/blob/master/regular-blog-posts/til-100-days-of-code-version.md#what-i-learned-about-performance)" section for more information about that.

I considered four major approaches implemented in Python:

### Simon Willison's Implementation

Simon's [TIL implementation](https://github.com/simonw/til) uses [Datasette](https://datasette.readthedocs.io/), an SQL tool he created and develops as part of a John S. Knight Journalism Fellowship at Stanford. 

Although I have no doubt that Simon's implementation is a stellar approach that leverages a very cool and useful tool (I plan to use it in the future in a project of my own!), I wanted to start from a blank slate and beginner mindset. I felt his implementation was too opinionated for my current needs.

### Andrei Cioara's Implementation

Andrei Cioara's [TIL implementation](https://github.com/aicioara/til/) is the simplest of the four. The bulk of the program exists within the `main()` function and can be followed quite easily, like a list of consecutive instructions.

A `content` variable is assigned to an empty string. As the program progresses, using an addition assignment operator, strings are appended to the `content` variable. 

First, the README.md header is appended to `content` through a global `HEADER` variable assigned to a multiline, triple-double-quote string. 

Then, the program uses `os.walk()` to traverse and sort the directories and files, removing the `.git` and `.github` directories as it goes. 

As the program "walks," each directory name is formatted as a category and added to `content`. The program then iterates through each file in each category, formatting the file name as a title, converting it into a hyperlink, and adding it to `content`. 

At the end of the program, the README.md file is opened and all of the `content` is written into it.

### Raegon Kim's Implementation

Raegon Kim's [TIL implementation](https://github.com/raycon/til/) also uses `os.walk()` and achieves a similar outcome, but the program is structured very differently. Unlike the procedural approach taken by Andrei Cioara, Raegon Kim splits the program into functions.

Just before re-writing the README.md, the entire program kicks off when the main function `readme()` is called. 

Within `readme()`, a `lines` list is created. As the program progresses through `readme()`, a number of other functions will be called.

Encapsulated in a function, `os.walk()` is called to create a "Recently Modified" section, then again to create a "Categories" section.

All of these new lines are appended to the `lines` list as strings
 
The `lines` list is returned by `readme()` and written into the README.md.



### KhanhIceTea's Implementation

KhanhIceTea's [implementation](https://github.com/khanhicetea/today-i-learned/) uses `os.listdir()` to iterate through the directories and files, instead of `os.walk()`. 

Each TIL file is passed into a `parse_article()` function and the frontmatter and header are parsed using the Python `find()` function, with a dictionary created that stores the `date`, `category`, `tags`, and `title`. Each TIL is returned and added to both a `cat_articles` list and `all_articles` list. 


## My Journey Through Implementations

I had heard positive things about ```os.walk()``` and immediately thought it would be a great approach for my TIL. So, I began by creating my own TIL implementation loosely based on Andrei Cioara's approach. 

However, I ran into difficulty when I attempted to add a section for most recently modified files, in addition to categories. I realized that `os.walk()` needed to "walk" the directories and files twice. Adding a second `os.walk()` without using a function seemed like overkill.

So, I began to study Raegon Kim's implementation, to see how he had added a "Recently Modified" category using functions. Although I nearly completed a similar implementation, I wasn't quite happy with the result. 

At that point, I began looking through more examples on GitHub and came across KhanhIceTea's Implementation.

I immediately liked the way it looked, because of its fun use of markdown tables and emojis, plus it ticked the boxes of having both "Recently Modified" and "Category" sections.

I'm not a huge fan of using `find()` to parse file frontmatter. However, I found TIL dictionaries to be very straightforward to work with and was able to quickly add a `status` variable and implement auto-status tweet. 

See the "[Changes I Made](https://github.com/KatherineMichel/portfolio/blob/master/regular-blog-posts/til-100-days-of-code-version.md#changes-i-made)" section below for more information.

## What I Learned About Performance

Although based on existing implementations, `os.walk()` seemed like the obvious choice at first, it can perform poorly. This was explained in [PEP 0471](https://www.python.org/dev/peps/pep-0471), which introduced `os.scandir()`, added to the Python Standard Library in Python 3.5.

"This PEP proposes including a new directory iteration function, os.scandir(), in the standard library. This new function adds useful functionality and increases the speed of os.walk() by 2-20 times (depending on the platform and file system) by avoiding calls to os.stat() in most cases.

Python's built-in os.walk() is significantly slower than it needs to be, because -- in addition to calling os.listdir() on each directory -- it executes the stat() system call or GetFileAttributes() on each file to determine whether the entry is a directory or not."

Although my implementation uses `os.listdir()` directly, which is an improvement over `os.walk()`, according to the `listdir()` documentation, `os.scandir()` gives better performance than `os.listdir()` for many common use cases.

For a small, open-source app like mine, performance might not be as much of an issue as it would be for a large-scale system or one that runs the risk of maxing out on resource, such as an app in a private repo using up all of its GitHub Action minutes.

But, knowing what I know now, if I were to start over, I would probably use `os.scandir()`, and perhaps, I will create a new version using it anyway.

## Make Your Own

How would you have done it and why? Let me know on Twitter [@KatiMichel](https://twitter.com/KatiMichel), in a GitHub issue, or by email kthrnmichel@gmail.com.

See my TIL- 100 Days of Code Version template for step-by-step instructions for setting up your own TIL. Have fun!

## Changes I Made

Major changes I made:
* Rather than making updates through an editor locally, updates will be made in the browser
* Instead of using a `README.md.template`, all of the content is generated directly into the README.md from update.py
* Moved the content creation part of the program out of functions and into `main()`, leaving `read_file()` and `parse_til()` functions
* Created `HEADER` and `FOOTER` variables assigned to multiline, triple-double-quote strings
* Changed the formatting to f-strings
* Added newlines to break the markdown table into two sections and create a footer
* Implemented [Twython](https://twython.readthedocs.io/), including adding secret tokens accessed via the GitHub Action
* Added a `status` variable to the post dictionary and passed it into Twython to auto-tweet a status update
* Implemented a GitHub Action to run the script when a commit is made
* Formatted update.py file using [Black](https://black.readthedocs.io/)

Minor changes I made:
* `codecs` and `json` were removed, because they were unnecessary for my approach
* Changed some variable names to make the meaning more clear
* Changed `cwd` to `"."`
* Altered the excluded directories
* Excluded dotfiles from within the `categories` variable
* Changed the markdown header text and emojis; capitalized the category names
* Created a `recent_content` string
