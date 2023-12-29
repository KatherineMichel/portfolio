# Hacktoberfest PRs- 2020

In the spirit of building in public and using accountability to improve, I decided to write a brief summary of my Hacktoberfest PRs.

## pugdebug PyPI Package

I learned a great deal about packaging recently while overseeing a Pinax release. It was a fun exercise to use some of that knowledge to create the packaging configs for an app called [pugdebug](https://github.com/Mte90/pugdebug/) and test publish and install it. The project was fairly straight forward, which meant that the script could be relatively simple. Based on a [tweet](https://twitter.com/di_codes/status/1306234847812354050) by Dustin Ingram, I used the official PyPA Sample Project as a starting point. Check out the [setup.py](https://github.com/pypa/sampleproject/blob/master/setup.py) file in that project to see some of the other commonly used, optional arguments. :) 

* Issue: https://github.com/Mte90/pugdebug/issues/9
* PR: https://github.com/Mte90/pugdebug/pull/13

Action taken:
* Created a setup.py file
* Based on the project docs and PyPA docs, created the relevant packaging configs
* Test published the package to the PyPI test instance
* Test installed the package using the PyPA packaging tutorial

New setup.py

```python
from setuptools import setup, find_packages
import pathlib

here = pathlib.Path(__file__).parent.resolve()

long_description = (here / 'README.md').read_text(encoding='utf-8')

setup(
    name='pugdebug',
    version='1.1.0',
    description='A standalone debugging client for PHP applications',
    long_description=long_description,
    long_description_content_type='text/markdown',
    url='https://github.com/Mte90/pugdebug',
    classifiers=[
        'Intended Audience :: Developers',
        'Topic :: Software Development :: Debuggers',
        'Operating System :: OS Independent',
        'License :: OSI Approved :: GNU General Public License v3 (GPLv3)',
        'Programming Language :: Python :: 3 :: Only',
        'Programming Language :: Python :: 3.7',
    ],
    python_requires='>=3.7',
    install_requires=['sip', 'PyQt5']
)
```

A few useful resources:
* PyPA Sample Project: https://github.com/pypa/sampleproject
* PyPA Packaging Python Projects tutorial: https://packaging.python.org/tutorials/packaging-projects/
* Real Python "How to Publish an Open-Source Python Package to PyPI" tutorial: https://realpython.com/pypi-publish-python-package/
* PyPI classifiers list: https://pypi.org/classifiers/
* PyPI test instance: https://test.pypi.org/

## All the Places Scraper

For a while now, I've been wanting to learn how to make a scraper, so, when I came across the [All the Places](https://github.com/alltheplaces/) project, which contains a collection of Scrapy scripts (a.k.a "spiders") that can be used to scrape business websites for location data, I decided to make one of my own to contribute to the All the Places project for Hacktoberfest.

I learned so much from this exercise, that I created a blog post about it, "[Building a Taco John's Scraper](https://github.com/KatherineMichel/portfolio/blob/master/regular-blog-posts/building-a-taco-johns-scraper.md)." Check it out if you want an insight into my learning process and how the script works.

Now that I've created a working scraper, I can build on that knowledge by using it in a more complex project.

* PR #1: https://github.com/alltheplaces/alltheplaces/pull/1634
* PR #2: https://github.com/alltheplaces/alltheplaces/pull/1639

Action taken:
* Studied scraper scripts and websites they scrape to see different approaches
* Created a script that would follow CSS classes and elements to identify location page URLs and crawl them
* Tested the script locally to verify that the script returns the scraped data

My Scrapy script

```python
import re

import scrapy

from locations.items import GeojsonPointItem


class TacoJohns(scrapy.Spider):
    name = "taco_johns"
    allowed_domains = ["tacojohns.com"]
    download_delay = 0.2
    start_urls = (
        "https://locations.tacojohns.com/",
    )

    state_pattern = re.compile("^[a-z]{2}(\.html)$")
    city_pattern = re.compile("^[a-z]{2}\/.+(\.html)$")
    location_pattern = re.compile("^[a-z]{2}\/.+\/.+(\.html)$")

    def parse(self, response):
        urls = response.xpath('//li[@class="c-directory-list-content-item"]//@href').extract()
        for url in urls:
            if (self.state_pattern.match(url.strip())):
               yield scrapy.Request(response.urljoin(url), callback=self.parse_state)
            elif (self.location_pattern.match(url.strip())):
                yield scrapy.Request(response.urljoin(url), callback=self.parse_location)
            else:
                yield scrapy.Request(response.urljoin(url), callback=self.parse_city)

    def parse_state(self, response):
        urls = response.xpath('//li[@class="c-directory-list-content-item"]//@href').extract()
        for url in urls:
            if (self.location_pattern.match(url.strip())):
                yield scrapy.Request(response.urljoin(url), callback=self.parse_location)
            else:
                yield scrapy.Request(response.urljoin(url), callback=self.parse_city)

    def parse_city(self, response):
        urls = response.xpath('//*[@class="c-location-grid-item-link page-link hidden-xs"]//@href').extract()
        for url in urls:
            yield scrapy.Request(response.urljoin(url), callback=self.parse_location)

    def parse_location(self, response):

        properties = {
            'ref': response.url,
            'name': response.xpath('//div[@itemprop="name"]//text()').extract_first(),
            'addr_full': response.xpath('normalize-space(//*[@itemprop="streetAddress"]//text())').extract_first(),
            'city': response.xpath('//span[@itemprop="addressLocality"]//text()').extract_first(),
            'state': response.xpath('//*[@itemprop="addressRegion"]//text()').extract_first(),
            'postcode': response.xpath('normalize-space(//span[@itemprop="postalCode"]//text())').extract_first(),
            'country': "USA",
            'phone': response.xpath('//span[@id="telephone"]//text()').extract_first(),
            'website': response.url,
            'lat': response.xpath('//*[@itemprop="latitude"]/@content').extract_first(),
            'lon': response.xpath('//*[@itemprop="longitude"]/@content').extract_first(),
        }

        yield GeojsonPointItem(**properties)
```

A few useful resources:
* "All The Places Data Format": https://github.com/alltheplaces/alltheplaces/blob/master/DATA_FORMAT.md
* Scrapy docs: https://docs.scrapy.org/en/latest/
* Beautiful Soup docs: https://www.crummy.com/software/BeautifulSoup/bs4/doc/
* Mozilla Web docs: https://developer.mozilla.org/en-US/
* JSON docs: https://www.json.org/json-en.html
* Schema docs: https://schema.org/

## YT Higher Lower

While I was looking through Hacktoberfest issues, the [YT Higher Lower](https://github.com/Jack1q/yt-higher-lower/) project piqued my interest. This was admittedly an easy PR. However, it provided a real benefit to a project and gave me the opportunity to try out a Python "game" script made based on a well-known product, and understand how it works.

* Issue: https://github.com/Jack1q/yt-higher-lower/issues/1
* PR: https://github.com/Jack1q/yt-higher-lower/pull/26

Action taken:
* Looked for common YouTube terms and added them to `terms.txt`

Terms I added to `terms.txt`

```txt
tutorial
funniest
talk show
late night
snl
makeup
tech
classics
jimmy fallon
```

A few useful resources:
* The original "Higher Lower Game" website: http://www.higherlowergame.com/
* The original "Higher Lower Game" Android app: https://play.google.com/store/apps/details?id=com.codecomputerlove.higherlowergame
* The original "Higher Lower Game" iPhone/iPad/iPod app: https://apps.apple.com/us/app/the-higher-lower-game/id1130297669

## Django/React Template README.md Improvement

I didn't intend to submit a PR of this type this year, because it would be a smaller step change in learning for me in comparison to some other contributions. However, when I came across this project's README, I couldn't resist submitting a PR to give it a more polished look!

Unfortunately, this PR ended up not counting. Even though the repo owner created an issue welcoming general Hacktoberfest contributions and seemed to happily accept this PR, neither the repo itself nor the PR was ever properly labeled to qualify for Hacktoberfest. Regardless, I ended up with five other qualifying PRs, including two PRs for my All the Places scraper, and a PR improving another project README.

I mainly included this, because it demonstrates an important type of contribution that repo owners tend to be happy to receive, solicited or unsolicited, that can be great for a newcomer to open source who is looking for a way to get started.

* PR: https://github.com/lfsando/django-drf-typescript-react-redux-template/pull/6

Action taken:
* Created a table of contents
* Replaced markdown text quote with language-specific code fencing
* Added features category
* Better organized installation instructions

Snippet from README.md before improvement

```python
## Django + Django Rest Framework + Typescript + React + Redux Template

- Django API (DRF) Backend
- Typescript, React, Redux Frontend

### Install

#### Frontend

> npm i
>
> ###### Production:
>
> npm run build
>
> ###### Development:
>
> npm run dev

# More of the same
```

Snippet from README.md after improvement

```python
# Django + Django Rest Framework + Typescript + React + Redux Template

Table of Contents
-----------------

- [Features](#features)
- [Getting Started](#getting-started)
    - [Frontend](#frontend)
    - [Backend](#backend)
- [Todo](#todo)

## Features

Backend
* Django Rest Framework
* Django Rest Knox
* PyLint

Frontend
* Webpack
* React
* Redux
* TypeScript
* Prettier

## Getting Started

### Frontend

```javascript
npm i
```\

# More of the same (by the way, I included the \ to escape the code somehow!)
```

A few useful resources:
* GitHub Open Source Survey 2017 results (insights about documentation): https://opensourcesurvey.org/2017/#insights
* Pinax sample README.md: https://github.com/pinax/pinax-announcements/blob/master/README.md
* GitHub docs "Basic writing and formatting syntax": https://docs.github.com/en/free-pro-team@latest/github/writing-on-github/basic-writing-and-formatting-syntax
* GitHub docs "Creating and Highlighting Code Blocks": https://docs.github.com/en/free-pro-team@latest/github/writing-on-github/creating-and-highlighting-code-blocks
