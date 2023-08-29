# data-collection-challenge

In this project we will work on a full web-scraping and data analysis.  

In this project we will use our skills to collect data, organize and store data, analyze data, and  visually communicate insights.

We will work on two parts:

1. Part 1: Scrape titles and preview text from Mars news articles

2. Part 2: Scrape and analyze Mars weather data, which exists in a table

## What we will learn from this project:

- How to identify HTML elements on a page
  
- How to identify their id and class attributes

- How to extract information via both automated browsing with Splinter and HTML parsing with Beautiful Soup

- How to scrape various types of information: HTML tables and recurring elements


## Instructions:

Part1:

- Use automated browsing to visit the Mars news site: 'https://static.bc-edx.com/data/web/mars_news/index.html'.

- Create a Beautiful Soup object and use it to extract text elements from the website.

- Extract the titles and preview text of the news articles that you scraped. Store the scraping results in Python data structures.

Part2:

- Use automated browsing to visit the Mars Temperature Data site: 'https://static.bc-edx.com/data/web/mars_facts/temperature.html'.
  
- Create a Beautiful Soup object and use it to scrape the data in the HTML table.

- Assemble the scraped data into a Pandas DataFrame.

- Examine the data types that are currently associated with each column.

- Analyze the dataset by using Pandas functions.

## Program:

### Tools:



- Splinter: is a Python library that provides a higher-level interface to automate web browsing tasks and interactions with web pages.
  
- BeautifulSoup: is a Python library used for parsing and navigating HTML and XML documents.
  
- Pandas: is a Python library designed for data manipulation and analysis. 

- matplotlib: is a Python library used for creating visualizations and plots.


### Code:

#### Part 1: Scrape Titles and Preview Text from Mars News

##### Step 1: Visit the Website
```
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Visit the website
# https://static.bc-edx.com/data/web/mars_facts/temperature.html
url = "https://static.bc-edx.com/data/web/mars_facts/temperature.html"
browser.visit(url)
html = browser.html
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```
##### Step 2: Scrape the Website
```
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Create a Beautiful Soup object
html = browser.html
soup = soup(html, 'html.parser')
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Extract all the text elements
text_elements= soup.find_all('div', class_='list_text')
text_elements
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```
##### Step 3: Store the Results
```
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Create an empty list to store the dictionaries
article_list = []
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Loop through the text elements
for elt in text_elements:
    # Extract the title and preview text from the elements
    title = elt.find('div', class_='content_title').text.strip()
    preview = elt.find('div', class_='article_teaser_body').text.strip()
    
    # Save title and preview  in a dictionary
    dict_elt = {'title': title, 'preview': preview}
    
    # Add the dictionary to the list
    article_list.append(dict_elt)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Create an empty list to store the dictionaries
article_list = []
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Print the list
for elt in  article_list:
    print("Title:", elt['title'])
    print("Preview:", elt['preview'])
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Close the browser
browser.quit()
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```

#### Part 2: Scrape and Analyze Mars Weather Data

##### Step 1: Visit the Website

```
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Visit the website
# https://static.bc-edx.com/data/web/mars_facts/temperature.html
url = "https://static.bc-edx.com/data/web/mars_facts/temperature.html"
# Open a Chrome window using Splinter
browser = Browser('chrome')
browser.visit(url)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```
##### Step 2: Scrape the Table

```
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Create a Beautiful Soup Object
html = browser.html
soup = soup(html, 'html.parser')
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Extract all rows of data
# Extract the table
table = soup.find('table')    
# Extract the rows 
rows = table.find_all('tr')
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------

```
##### Step 3: Store the Data

```
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Create an empty list for the rows
list_rows = []

# Create an empty list for the name of columns
list_column = []    

# Loop through the scraped data to create a list of rows
for r in rows:
    # Look for the list of cells
    cel = r.find_all('td')
    # Look for the name of columns
    column_names = r.find_all('th')
        
    # Extract the names and add them to the list_column
    for column_name in column_names:
        list_column.append(column_name.get_text(strip=True))
        
    # Extract the values of cells and add them to the list_rows
    cell_values = [cell.get_text() for cell in cel]
    list_rows.append(cell_values)

# Print the list of columns names and the content of each row
print("Column Names:", list_column)
print("Data Rows:", list_rows)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Create a Pandas DataFrame by using the list of rows and a list of the column names
df = pd.DataFrame(list_rows, columns=list_column)
df=df.dropna()
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```
##### Step 4: Prepare Data for Analysis
```
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Examine data type of each column
print(df.dtypes)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Change data types for data analysis
df = df.astype({'sol': int, 'ls': int, 'month': int, 'min_temp': float,
                'pressure': float})
df['terrestrial_date']= pd.to_datetime(df['terrestrial_date'])
df.head()
```
##### Step 5: Analyze the Data
```
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Number of months on Mars
nbr_month= df['month'].nunique()

# Occurrences of each month in order by month
occ_month = df['month'].value_counts()
sorted_occ_month = occ_month.sort_index()

# Display the result
print(sorted_occ_month)
print('The number of months in Mars is',nbr_month)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Number of days' worth of data
nbr_day = df['id'].nunique()
nbr_day
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Average low temperature by month
avg_temp = df.groupby('month')['min_temp'].mean()

# Sort the result by month
sorted_avg_temp= avg_temp.sort_index()

# Display the result
print(sorted_avg_temp)
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Identify the coldest and hottest months in Curiosity's location

# Sort the DataFrame
sorted_avg_temp_df = avg_temp.sort_values(by='min_temp')

# Plot the average temperature by month in order to look for the Minimum and Maximum
# Create a bar plot using the sorted DataFrame
sorted_avg_temp_df.plot(kind='bar', legend=None)
plt.xlabel('Month')
plt.ylabel('Average Temperature in Celsius')
plt.savefig('temp.png')
plt.show()

# Look for the coldest and the hottest months with their temperature
min_avg_temp = sorted_avg_temp.min()
max_avg_temp = sorted_avg_temp.max()
month_min_temp = sorted_avg_temp.idxmin()
month_max_temp = sorted_avg_temp.idxmax()

# Display results
print('The coldest month is ', month_min_temp, 'and the temperature is equal to ',min_avg_temp)
print('The hottest month is ', month_max_temp, 'and the temperature is equal to ',max_avg_temp)

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```
###### Analyse: The chart, as well as the calcul results, show that on average, the third month has the coldest minimum temperature on Mars, and the eighth month is the warmest. But it is always very cold there in human terms!
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```
# Identify the lowest and heighest pressure

# Create and sort the dataframe
avg_pressure_df = pd.DataFrame(avg_pressure)
sorted_avg_pressure = avg_pressure_df.sort_values(by='pressure')

# Plot the average pressure by month in order to look for the Minimum and Maximum
# Create a bar plot using the sorted DataFrame
sorted_avg_pressure.plot(kind = 'bar',legend=None)
plt.xlabel('month')
plt.ylabel('Atmospheric Pressure')
plt.savefig('pressure.png')
plt.show()

# Look for the Minimum  and Maximum pressure with their months
avg_pressure_df = avg_pressure.reset_index().rename(columns={'pressure': 'mean_pressure'})
min_pressure = avg_pressure_df ['mean_pressure'].min()
max_pressure = avg_pressure_df ['mean_pressure'].max()
min_month = avg_pressure_df [avg_pressure_df ['mean_pressure'] == min_pressure]['month']
max_month = avg_pressure_df [avg_pressure_df ['mean_pressure'] == max_pressure]['month']

# Display the results
print('Minimum pressure: ', min_pressure, ' Month associated: ', min_month.tolist())
print('Maximum pressure: ', max_pressure," Month associated", max_month.tolist())
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```
###### Analyse: The chart, as well as the calcul results, show that the atmospheric pressure is, on average, lowest in the sixth month and highest in the ninth.
# Number of days in one year on Mars
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```
# Plot to look for the distance between two peaks of the temperature
df.plot(y = 'min_temp', legend=None)
plt.xlabel('Number of Terrestrial Days')
plt.ylabel('Minimum Temperature')
plt.savefig('nbr_j.png')
plt.show()

# One year in Mars means when the ls (Solar Longitude) is the same between two dates
 
# Save the first value from the column ls
first_value = df['ls'].iloc[0]
# Save the date of the first value from the column terrestrial_date
date1 = df['terrestrial_date'].iloc[0]

# Look for the index of the matching value of the saved one(1 year)
matching_index = df[df['ls'] == first_value].index
# Extract the first matching index from the list 
first_matching_index = matching_index[1]
# Look for the date of the matching index
date2 = df['terrestrial_date'].iloc[first_matching_index]

# Look for the number of days between the two dates
nbr_j=date2-date1
# Exctract the number of days
nbr_j=nbr_j.days

# Display the result
print('The number of days in one year in Mars is ',nbr_j)

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
```
###### Analyse: The distance from peak to peak is roughly 1425-750, or 675 days. A year on Mars appears to be about 675 days from the plot. The calcul results is equivalent to 687 earth days.


##### Supports: Slack AskBCS Learning Assistant
