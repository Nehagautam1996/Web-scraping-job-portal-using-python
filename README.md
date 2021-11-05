# Web-scraping-job-portal-using-python
## PYTHON SCRAPER FOR JOB PORTAL
-------------------------------
This project enables a user to extract job postings of a job role to store in a CSV file. It saves a jobseekers time by providing summary details of all the requested jobs in a tabular format.

## CONTENTS OF THIS FILE
---------------------

 * Tech stack
 * Installation
 * Configuration
 * Process
 * Configuration
 * Troubleshooting
 * FAQ
 * Maintainers

TECH STACK
----------
1.Jupyter notebook from Anaconda distribution 
2.Google chrome 
3.Chrome webdriver

INSTALLATION
------------
Download and install
1.chrome webdriver with -https://chromedriver.chromium.org/downloads Additional packages for anaconda distribution 
2.Selenium: conda install -c conda-forge selenium 
3.bs4 : conda install -c conda-forge bs4 
4.Regex : conda install -c conda-forge regex

CONFIGURATION
-------------
Anaconda distribution:-
	conda version : 4.10.3
	conda-build version : 3.21.4
	python version : 3.8.8.final.0
Google chrome : Version 95.0.4638.54 (Official Build) (64-bit)
Chrome webdriver : ChromeDriver 96.0.4664.18


PROCESS
--------
Step 1.First import all the required packages
---------------------------------------------
'''
import csv
from datetime import datetime
import requests
from bs4 import BeautifulSoup
import time
from selenium import webdriver
import re
import pandas as pd
'''
Step 2. Get the url for desired job role as per user's input
------------------------------------------------------------
'''
def get_url(position,page,post):
    template='https://www.naukri.com/{}-jobs-{}?k={}'
    url=template.format(position,page,post)
    return url
'''
Step 3.Use the chromewebdriver, navigate through a page and get job postings with the help of HTML tree on one page. This code scrapes details from 20 pages.
-------------------------------------------------------------------------------------------------------------------------------------------------------------
'''
url=get_url(position,i,post)#pass i for page
driver = webdriver.Chrome("D:\chromedriver.exe")
driver.get(url)
time.sleep(10)
soup = BeautifulSoup(driver.page_source,'html5lib')
driver.close()
'''
Step 4.Get a job card detail with HTML tree structure.
-------------------------------------------------------
'''
temp = soup.find(class_='list')
cards = temp.find_all('article',class_='jobTuple bgWhite br4 mb-8')
'''
Step 5.Use a job card to scrape other attributes of a job, Some of them are listed below.
-----------------------------------------------------------------------------------------
'''
#company name
comp_a = cards.find('a',class_='subTitle ellipsis fleft')
#skills required
Skill=[]
        sk1 = cards.find(class_='tags has-description')
        sk2 = sk1.find_all('li',class_='fleft fs12 grey-text lh16 dot')
        for li in sk2 :
            li=str(li)
            li=cleanhtml(li)
            Skill.append(li)
'''	    
Step 6.Store all the details in a python dataframe.
---------------------------------------------------
'''
df = pd.DataFrame(columns=['Company','Description','Experience','Location','Salary','Skills'])
'''
Step 7.Store this dataframe in CSV file, say Scrape_Naukri.csv
---------------------------------------------------------------
'''
df.to_csv("D:/Scrape_Naukri.csv",index=False)
'''
Step 8.flatten dataframe column from 2D to 1D .
-----------------------------------------------
'''
sk_set=df['Skills'].to_list()
#flatten list(2d to 1d)
Skills_1d = reduce(lambda z, y :z + y, sk_set)
'''
Step 9. Convert it from list to dictionary using Zip function.Count the frequency of given skills in 20 pages of job posting
-----------------------------------------------------------------------------------------------------------------------------
'''
Skill_count = dict(zip(list(Skills_1d),[list(Skills_1d).count(i) for i in list(Skills_1d)]))
'''
Step 10. Storing this dictionary to a new dataframe S_count
------------------------------------------------------------
'''
S_count = pd.DataFrame.from_dict(Skill_count , orient ='index')
print(S_count) #check this new dataframe to print the skill against its frequency in job postings of 20 pages
'''
Step 11. Store dataframe in a file
-----------------------------------
'''
S_count.to_csv("D:/Skill_count.csv")
'''
