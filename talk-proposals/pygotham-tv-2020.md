July 7, 2020

Discovering the Next Great Artist: Python

While taking a Stanford Python course recently, I created a Python program that obtains a random image, applies an image filter to it using Pillow, then tweets it, all powered by a GitHub Action. In the process of modifying and tweeting thousands of images, the program created one special image that looked like artwork. Quite surprised and excited by this, I set up another bot to intentionally create more of this "artwork." I immediately wanted to curate some of my favorites into an online or in person exhibit. But there are unanswered questions. Setting aside the shelter-in-place caused by Covid-19, would "art" created by Python even be welcome in a traditional gallery? Would members of the art industry consider it invalid because it was not made by a human or be defensive due to fear that software will "eat" the art world? 
In my talk, I'll explain how my bot works and explore some of the possible answers.
* Background of taking Stanford Python course
* Background of creating Twitter bot
* Architecture of Twitter bot: Pillow, Twython, Requests, powered by a GitHub Action
* How the bot works: commit or cron job causes GitHub Action workflow to run Python program, which downloads photo, applies image algorithm to it, tweets it, then deletes it.
* Looking through photos on Twitter; curating the photos for exhibit (show examples) 
* Research about how Python has been used to create art and reactions to that
* Research into galleries; communication with galleries to find out what is possible
* Next steps

Audience members will come away from the talk having learned how a Twitter bot powered by a GitHub Action works and having seen examples of the artwork created by it.
