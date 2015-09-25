# PyScholar
This repository provides an python code for scrawling google scholar profile

## Objective
Recently, I am prepareing my EB1 application, for which I need to check my publications and citations from Google Scholar. I found the manually going through those publications and citations is very laboring and boring. As a result, I creates this python function to accomplish this task automatically.

## What it does
This tool extract the goole scholar profile according to your given link. It will extract the scholar name, citation history, optionally the list of publication and even the list of citation for each of publication. The code is written in Python.

## Requirements
1. urllib2
2. BeautifulSoup4
3. selenium. Please choose a browser your like and put the browser driver (can be downloaded here http://www.seleniumhq.org/projects/webdriver/) in your environment path. In the code, I use Internet Explorere (Ie)

## Usage:
```
import PyScholar
info=parse_scholar.extract_scholar(profile_url, verbose)
```

```
@param[in]      profile_url     the link of google scholar profile you want to crawl
@param[in]      verbose         the level of information you want to scrawl. By default, we will scraw the detailed citation list for each of your publicaiton
@return         the profile information as a dictionary
```

## License
This code is free of use or change, as long as my name and email is included.
