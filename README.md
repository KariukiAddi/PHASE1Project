# PHASE1Project

Before starting a business, several factors have to be considered:

1. Growth
2. Revenues
3. Competition
4. Market share
5. Customer Satisfaction

From the data located in the data folder, we will seek to seek insights on some of the factors from the data we have.Let us get into the technical stuff.  

We start by importing the libraries we need:  

```
import pandas as pd
import matplotlib.pyplot as plt
import warnings
warnings.filterwarnings("ignore")
import csv
```
We then proceed to read the csv files with the data we need, on the first phase where we will be studying Growth,Revenues,Competition & Market share we will be working with the Movie gross csv file :  

# Phase 1:
#### Growth,Revenues,Competition & Market share

# Opening bom.movie_gross.csv file
df_movie_gross = pd.read_csv("./data/bom.movie_gross.csv")  

Before proceeding let's begin cleaning our data by checking for duplicates in each dataframe afterwhich we will run tests to assert whether there are any underlying duplicate records.

```
#check for duplicates and in all data frames
duplicate_movie_gross = df_movie_gross.columns.duplicated().sum()

#assert duplicates rows for each dataframe is 0
assert duplicate_movie_gross  == 0
```

Next lets begin working with our first dataframe, that contains the revenue figures for each movie produced every year, this variable is called df_movie_gross. Let also check if dataframe colums have the right data types.

```
#check data type for each column to check if its the correct values
print(df_movie_gross.dtypes)
```
The foreign gross column is of object type. It should be an interger or float and run a test on the dataframe to check datatypes.

```
# Convert 'foreign_gross' column to numeric, handling missing values
df_movie_gross['foreign_gross'] = pd.to_numeric(df_movie_gross['foreign_gross'], errors='coerce')

# Convert 'foreign_gross' column to integer, excluding NaN values
df_movie_gross['foreign_gross'] = df_movie_gross['foreign_gross'].astype('Int64')

print(df_movie_gross)
```

I then remove values from the dataframe that are NA:

```
df_movie_gross=df_movie_gross.dropna().reset_index(drop=True)
```
## 1. Growth   
Now let us start making sense of the data. First lets check the number of movies that have been produced each year in a histogram:
```
# Group the data by 'year' and count the number of movies in each year
movies_per_year = df_movie_gross.groupby('year').size()

# Plotting the bar graph
plt.figure(figsize=(10, 6))
movies_per_year.plot(kind='bar')
plt.xlabel('Year')
plt.ylabel('Number of Movies')
plt.title('Movies Produced Each Year')
plt.xticks(rotation=45)
plt.show()
```

## 2. Revenues
Let us check the domestic and foreign  revenues and how they were over the years

```
# Group the data by 'year' and calculate the sum of 'domestic_gross' and 'foreign_gross' for each year
gross_per_year = df_movie_gross.groupby('year')[['domestic_gross', 'foreign_gross']].sum()

print(gross_per_year)
# Plotting the grouped bar chart
plt.figure(figsize=(10, 6))
gross_per_year.plot(kind='line')
plt.xlabel('Year')
plt.ylabel('Gross Earnings')
plt.title('Comparison of Total Domestic and Foreign Gross for Movies Produced Each Year')
plt.xticks(rotation=45)
plt.legend(['Domestic Gross', 'Foreign Gross'], loc='upper left')
plt.show()
```

## 3. Competition

Lets take a look at the highest grossing top 10 studios:

```
# Group the data by 'year' and 'studio' and calculate the sum of 'domestic_gross' and 'foreign_gross' for each year and studio
gross_per_year_studio = df_movie_gross.groupby(['year', 'studio'])[['domestic_gross', 'foreign_gross']].sum().reset_index()

# Get the top 10 studios based on total domestic gross
top_10_studios = gross_per_year_studio.groupby('studio')['domestic_gross'].sum().nlargest(10).index

# Filter the data for the top 30 studios
filtered_data = gross_per_year_studio[gross_per_year_studio['studio'].isin(top_10_studios)]

# Plotting the comparison using seaborn
plt.figure(figsize=(12, 6))
sns.lineplot(data=filtered_data, x='year', y='domestic_gross', hue='studio', ci=None)
sns.lineplot(data=filtered_data, x='year', y='foreign_gross', hue='studio', style=True, ci=None, errorbar=None)
plt.xlabel('Year')
plt.ylabel('Gross Earnings')
plt.title('Comparison of Total Domestic and Foreign Gross for Top 10 Studios Each Year')
plt.xticks(rotation=45)
plt.legend(title='Studio', bbox_to_anchor=(1.05, 1), loc='upper left')
plt.show()
```

## 4.Market Share

Here we will take a look at the market share for the highest grossing studios in both the domestic and foreign markets.Lets start with the:  

#### Domestic Market :
```
# Calculate the total domestic gross for each studio
studio_domestic_gross = df_movie_gross.groupby('studio')['domestic_gross'].sum()

# Get the most dominant studios based on total domestic gross
top_studios = studio_domestic_gross.nlargest(15)  # Adjust the number as per your preference

# Calculate the market share percentage for the most dominant studios
market_share = (top_studios / top_studios.sum()) * 100

# Plotting the pie chart
plt.figure(figsize=(10, 6))
plt.pie(market_share, labels=market_share.index, autopct='%1.1f%%')
plt.title('Market Share for the Most Dominant Studios (Based on Domestic Gross)')
plt.show()

```
#### Foreign Market
```
# Calculate the total foreign gross for each studio
studio_foreign_gross = df_movie_gross.groupby('studio')['foreign_gross'].sum()

# Get the most dominant studios based on total foreign gross
top_studios = studio_foreign_gross.nlargest(15)  # Adjust the number as per your preference

# Calculate the market share percentage for the most dominant studios
market_share = (top_studios / top_studios.sum()) * 100

# Plotting the pie chart
plt.figure(figsize=(10, 6))
plt.pie(market_share, labels=market_share.index, autopct='%1.1f%%')
plt.title('Market Share for the Most Dominant Studios (Based on Foreign Gross)')
plt.show()
```
#### Total Market Share
```
# Group the data by 'studio' and calculate the sum of 'domestic_gross' and 'foreign_gross' for each studio
gross_per_studio = df_movie_gross.groupby('studio')[['domestic_gross', 'foreign_gross']].sum()

# Calculate the total gross for each studio by summing the domestic and foreign gross
gross_per_studio['total_gross'] = gross_per_studio['domestic_gross'] + gross_per_studio['foreign_gross']

# Sort the studios based on total gross in descending order
sorted_studios = gross_per_studio.sort_values(by='total_gross', ascending=False)

# Select the top 15 most dominant studios
top_studios = sorted_studios.head(15)

# Calculate the market share as a percentage
market_share = top_studios['total_gross'] / top_studios['total_gross'].sum() * 100

# Plotting the pie chart
plt.figure(figsize=(8, 8))
plt.pie(market_share, labels=top_studios.index, autopct='%1.1f%%')
plt.title('Market Share of Dominant Studios based on Total Gross')
plt.show()
```

# Phase 2:

In this phase we will look at customer satisfaction,let us start by  loading the data we need, we will be using title basics csv and movie rating csv and combine the date on a common key:

```
#Opening title.basics.csv
df_movie_title_basics = pd.read_csv("./data./title.basics.csv")
#Opening  title.ratings.csv
df_movie_ratings = pd.read_csv("./data./title.ratings.csv")

# Merge the dataframes based on the common 'tconst' column
combined_df = pd.merge(df_movie_title_basics, df_movie_ratings, on='tconst', how='inner')

#clean the data to remove na values
combined_df.dropna()

# Display the combined dataframe
print(combined_df.info())
```
#### What is the most popular genre?
```
# Split the genres column by ',' to create a list of genres
combined_df['genres'] = combined_df['genres'].str.split(',')

# Create a new dataframe to hold the exploded genre values
df_genres = combined_df.explode('genres')

# Group the data by genre and calculate the total number of votes
genre_votes = df_genres.groupby('genres')['numvotes'].sum()

# Find the genre with the highest total number of votes
most_popular_genre = genre_votes.idxmax()

# Display the most popular genre
print("The most popular genre is:", most_popular_genre)
```
#### The top 10 most popular genres are:

```
# Group the data by genre and calculate the sum of 'numvotes' for each genre
popular_genres = df_genres.groupby('genres')['numvotes'].sum().sort_values(ascending=False).head(10)

# Display the 10 most popular genres
print(popular_genres)
```
#### A Pie Chart Showing Most Popular Movie Genre 
```
# Group the data by genre and calculate the count of movies in each genre

genre_counts = df_genres.groupby('genres')['tconst'].count().sort_values(ascending=False).head(10)

# Plotting the pie chart
plt.figure(figsize=(8, 8))
genre_counts.plot(kind='pie', autopct='%1.1f%%')
plt.title('Top 10 Most Popular Genres')
plt.ylabel('')

# Display the pie chart
plt.show()
```
#### Top 20 Highest rated movies
```
# Sort the dataframe by 'averagerating' column in descending order
sorted_df = combined_df.sort_values('averagerating', ascending=False)

# Select the top 20 highest rated primary titles
top_20_titles = sorted_df.head(30)

# Display the top 20 titles
print(top_20_titles)
```
#### A Bar Chart of the top 20 highest rated primary titles
```
# Plot a bar chart of the top 20 highest rated primary titles
plt.figure(figsize=(10, 6))
plt.bar(top_20_titles['primary_title'], top_20_titles['averagerating'])
plt.xlabel('Primary Title')
plt.ylabel('Average Rating')
plt.title('Top 20 Highest Rated Primary Titles')
plt.xticks(rotation=90)
plt.show()
```