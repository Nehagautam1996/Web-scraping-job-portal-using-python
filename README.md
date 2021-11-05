# Web scraping job portal(Naukri.com)using python
--------------------------------------------------
This project enables a user to extract job postings of a job role to store in a CSV file. It saves a jobseekers time by providing summary details of all the requested jobs in a tabular format.<br /> 

## CONTENTS OF THIS FILE
-------------------------
 * Tech stack<br /> 
 * Installation<br /> 
 * Configuration<br /> 
 * Process<br /> 
 * Configuration<br /> 
 * Troubleshooting<br /> 
 * FAQ<br /> 
 * Maintainers<br /> 
## TECH STACK
--------------
1.Jupyter notebook from Anaconda distribution <br /> 
2.Google chrome <br /> 
3.Chrome webdriver<br /> 
## INSTALLATION
----------------
Download and install<br />
1.chrome webdriver with -https://chromedriver.chromium.org/downloads Additional packages for anaconda distribution <br /> 
2.Selenium: conda install -c conda-forge selenium <br /> 
3.bs4 : conda install -c conda-forge bs4 <br /> 
4.Regex : conda install -c conda-forge regex<br /> 
## CONFIGURATION
-----------------
Anaconda distribution:-<br /> 
	conda version : 4.10.3<br /> 
	conda-build version : 3.21.4<br /> 
	python version : 3.8.8.final.0<br /> 
Google chrome : Version 95.0.4638.54 (Official Build) (64-bit)<br /> 
Chrome webdriver : ChromeDriver 96.0.4664.18<br /> 
## PROCESS
-----------
### Step 1.First import all the required packages
--------------------------------------------------
import csv<br /> 
from datetime import datetime<br /> 
import requests<br /> 
from bs4 import BeautifulSoup<br /> <br /> 
import time<br /> 
from selenium import webdriver<br /> 
import re<br /> 
import pandas as pd<br /> 
### Step 2. Get the url for desired job role as per user's input
-----------------------------------------------------------------
def get_url(position,page,post):<br /> 
    template='https://www.naukri.com/{}-jobs-{}?k={}'<br /> 
    url=template.format(position,page,post)<br /> 
    return url<br />
### Step 3.Use the chromewebdriver, navigate through a page and get job postings with the help of HTML tree on one page. This code scrapes details from 20 pages.
-----------------------------------------------------------------------------------------------------------------------------------------------------------------
url=get_url(position,i,post)#pass i for page<br /> 
driver = webdriver.Chrome("D:\chromedriver.exe")<br /> 
driver.get(url)<br /> 
time.sleep(10)<br /> 
soup = BeautifulSoup(driver.page_source,'html5lib')<br /> 
driver.close()<br /> 
### Step 4.Get a job card detail with HTML tree structure.
----------------------------------------------------------
temp = soup.find(class_='list')<br /> 
cards = temp.find_all('article',class_='jobTuple bgWhite br4 mb-8')<br /> 
### Step 5.Use a job card to scrape other attributes of a job, Some of them are listed below.
---------------------------------------------------------------------------------------------
#company name<br /> 
comp_a = cards.find('a',class_='subTitle ellipsis fleft')<br /> 
#skills required<br /> 
Skill=[]<br /> 
        sk1 = cards.find(class_='tags has-description')<br /> 
        sk2 = sk1.find_all('li',class_='fleft fs12 grey-text lh16 dot')<br /> 
        for li in sk2 :<br /> 
            li=str(li)<br /> 
            li=cleanhtml(li)<br /> 
            Skill.append(li)<br /> 
### Step 6.Store all the details in a python dataframe.
--------------------------------------------------------
df = pd.DataFrame(columns=['Company','Description','Experience','Location','Salary','Skills'])<br /> 
### Step 7.Store this dataframe in CSV file, say Scrape_Naukri.csv
-------------------------------------------------------------------
df.to_csv("D:/Scrape_Naukri.csv",index=False)<br /> 
### Step 8.flatten dataframe column from 2D to 1D .
---------------------------------------------------
sk_set=df['Skills'].to_list()<br /> 
Skills_1d = reduce(lambda z, y :z + y, sk_set)<br /> 
### Step 9. Convert it from list to dictionary using Zip function.Count the frequency of given skills in 20 pages of job posting.
---------------------------------------------------------------------------------------------------------------------------------
Skill_count = dict(zip(list(Skills_1d),[list(Skills_1d).count(i) for i in list(Skills_1d)]))<br /> 
### Step 10. Storing this dictionary to a new dataframe S_count.
----------------------------------------------------------------
S_count = pd.DataFrame.from_dict(Skill_count , orient ='index')<br /> 
print(S_count) #check this new dataframe to print the skill against its frequency in job postings of 20 pages<br /> 
### Step 11. Store dataframe in a file.
---------------------------------------
S_count.to_csv("D:/Skill_count.csv")<br /> 
