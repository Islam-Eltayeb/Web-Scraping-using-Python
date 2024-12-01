# Web-Scraping-using-Python
Used Python to scrape ebay.com to get data about mobile phones and stored these data in a csv file.

```python
#Importing BeautifulSoup, requests, pandas, and numpy libraries 
from bs4 import BeautifulSoup
import requests
import pandas as pd
import numpy as np
```

```python
# Function to extract Product Title
def get_title(soup):

    try:
        # Outer Tag Object
        title = soup.find('h1', attrs = {'class' : 'x-item-title__mainTitle'})

        # Inner NavigatableString Object
        title_value = title.text

        # Title as a string value
        title_string = title_value.strip()

    except AttributeError:
        title_string = ""

    return title_string

# Function to extract Product Price
def get_price(soup):

    try:
        price = soup.find('div', attrs = {'class' : 'x-price-primary'}).text.strip()

    except AttributeError:
         price = ""

    return price

# Function to extract Product Condition
def get_condition(soup):

    try:
        condition = soup.find('span' , attrs = {'class' : 'ux-icon-text__text'}).find('span' , attrs = {'class' : 'ux-textspans'}).string.strip()
    
    except AttributeError:
        condition = ""	

    return condition

# Function to extract the name of the product's seller
def get_seller(soup):
    try:
        seller = soup.find('div' , attrs = {'class' : 'x-sellercard-atf__info__about-seller'}).find('span' , attrs = {'class' : 'ux-textspans ux-textspans--BOLD'}).string.strip()

    except AttributeError:
        seller = ""	

    return seller

# Function to extract rating of the seller
def get_seller_rating(soup):
    try:
        seller_rating = soup.find('ul' , attrs = {'class' : 'x-sellercard-atf__data-item-wrapper'}).find('span' , attrs = {'class' : "ux-textspans ux-textspans--PSEUDOLINK"}).string.strip()
       

    except AttributeError:
        seller_rating = ""	

    return seller_rating
```

```python
if __name__ == '__main__':

    # add your user agent 
    HEADERS = ({'User-Agent' : 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/131.0.0.0 Safari/537.36' , 'Accept-Language' : 'en-US, en ; q=0.5'})

    # Add The main webpage URL
    URL =  "https://www.ebay.com/sch/i.html?_from=R40&_trksid=p2334524.m570.l1311&_nkw=iphone+16+pro+max&_sacat=0&_odkw=laptop&_osacat=0"

    # HTTP Request
    webpage = requests.get(URL, headers=HEADERS)

    # Soup Object containing all data
    soup = BeautifulSoup(webpage.content, "html.parser")

    # Get the links for the main webpage
    links = soup.find_all('a', attrs = {'class' : 's-item__link'})

    # Store the links in a list
    links_list = []

    # Loop for extracting links from Tag Objects
    for link in links:
            links_list.append(link.get('href'))

    # Creat a dictionary to store the information of each product
    d = {"title":[], "price":[], "condition":[], "seller":[],"seller_rating":[]}
    
    # Loop for extracting product details from each link 
    for link in links_list:
        # The product's link
        new_webpage = requests.get(link, headers=HEADERS)
        # Reading the content of the product's webpage using BeautifulSoup
        new_soup = BeautifulSoup(new_webpage.content, "html.parser")
       
    # Calling functions to display all necessary product's information
        d['title'].append(get_title(new_soup))
        d['price'].append(get_price(new_soup))
        d['condition'].append(get_condition(new_soup))
        d['seller'].append(get_seller(new_soup))
        d['seller_rating'].append(get_seller_rating(new_soup))

    #Creating a data frame using the data in dictionary (d)
    ebay_df = pd.DataFrame.from_dict(d)
    ebay_df['title'].replace('', np.nan, inplace=True)
    ebay_df = ebay_df.dropna(subset=['title'])
    
    # Create a csv file containing the scraped data
    ebay_df.to_csv("ebay_data.csv", header=True, index=False)
```
