# python-html-challenge
Module 11 Challenge - Robertson, J

### Getting started

VSCode with the Jupyter extension (ms-toolsai.jupyter) & Python extension(ms-python.python) was used in conjunction with an anaconda python 3.7 environment has been used for all coding.

The following python libraries were imported as below:

    from splinter import Browser
    from bs4 import BeautifulSoup
    import matplotlib.pyplot as plt
    import pandas as pd

If a Chrome driver error is returned while trying to execuse the functions in the splinter library, install the Selenium library in your dev environment with the following command:

    pip install splinter[selenium]


---
---

### Summary of code
---

#### Activity 1

Build a BeautifulSoup object to get the HTML for the Mars News Website with the following code:

    url = 'https://static.bc-edx.com/data/web/mars_news/index.html'
    browser.visit(url)
    html = browser.html

    soup = BeautifulSoup(html, 'html.parser')

Titles in 'div' elements with class 'content_title':

    title_find = soup.find_all('div', class_ = 'content_title')
    title = [titles.text for titles in title_find]

Previews in 'div' elements with class 'article_teaser_body':

    preview_find = soup.find_all('div', class_ = 'article_teaser_body')
    preview = [preview.text for preview in preview_find]

Store outputs as a list of dictionaries with the 'title' : 'preview' pairing using the comprehension method:

    title_preview = [{'title' : title[index], 'preview' : preview[index]} for index, entry in enumerate(title)]
---

#### Activity 2

Build a BeautifulSoup object to get the HTML for the Mars Facts Website with the following code:

    url = 'https://static.bc-edx.com/data/web/mars_facts/temperature.html'
    browser.visit(url)
    html = browser.html
    soup = BeautifulSoup(html, 'html.parser')

Table columns are stored in the first 'tr' element. Scrape the 'tr' element and add each to a 'columns' dictionary (to be used to define column names for the dataframe):

    columns = []
    columns_raw = soup.find_all('tr')
    columns_text = columns_raw[0].find_all('th')
    for each in columns_text:
        columns.append(each.text)

Table rows are stored in a 'tr' element with class 'data-row'. Scrape the 'tr' element with class_ parameters and add each to a 'mars_df_list' dictionary:

    mars_data = soup.find_all('tr', class_ = 'data-row')

    mars_df_list = []

    for i in range(len(mars_data)):
    check_row = mars_data[i]

    select_data = [row.text for row in check_row.find_all('td')]
    mars_df_list.append(select_data)

Create a Pandas dataframe using the 'columns' & 'mars_df_list' dictionaries:

    mars_df = pd.DataFrame(mars_df_list, columns = columns)

The datatypes scraped from the HTML elements are by default stored as objects. We can't perform operations on objects, so we need to convert each row in the dataframe to the appropriate data type:

    mars_df = mars_df.astype({'id': 'int',
                    'terrestrial_date': 'datetime64', 
                    'sol': 'int', 
                    'ls': 'int', 
                    'month': 'int', 
                    'min_temp': 'float', 
                    'pressure': 'float'})

## Questions

### 1. How many months are there on Mars?

    months_ = mars_df['month'].value_counts().sort_index()

Answer: 12 months on Mars

### 2. How many Martian days' worth of data are there?

    days_ = len(mars_df)

Answer: 1867 days' worth of data

### 3. What is the average low temperature by month?

    avg_low = mars_df.groupby('month')['min_temp'].mean()

### Plot the average temperature by month

    avg_low.plot(kind = 'bar',
             xlabel = 'Month',
             ylabel = 'Temperature (C)',
             title = 'Average Temperatures by Month')

### Identify the coldest and hottest months in Curiosity's location

    avg_low_sorted = avg_low.sort_values(ascending = True)

    avg_low_sorted.plot(kind = 'bar',
                xlabel = 'Month',
                ylabel = 'Temperature (C)',
                title = 'Average Temperatures by Month')

    plt.show()

Answer:   

    Coldest Month: 3
    Hottest Month: 8

### 4. Average pressure by Martian month

    avg_pressure = mars_df.groupby('month')['pressure'].mean()

Answer:

    Atmospheric pressure is, on average, lowest in the sixth month and highest in the ninth.

### Plot the average pressure by month

    avg_pressure.plot(kind = 'bar',
                    xlabel = 'Month',
                    ylabel = 'Pressure',
                    title = 'Average Pressure by Month')

    plt.show()

### 5. How many terrestrial (earth) days are there in a Martian year?

    plot_data = mars_df['min_temp'].reset_index(drop = True)

    plt.plot(plot_data)
    plt.xlabel('Number of terrestrial days')
    plt.ylabel('Temperature (C)')
    plt.show()

Answer:

    The distance from peak to peak is roughly 1425-750, or 675 days. A year on Mars appears to be about 675 days from the plot. Internet search confirms that a Mars year is equivalent to 687 earth days.

--- 
---
