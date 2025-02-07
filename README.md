# Import libraries
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from datetime import datetime

# Load the dataset
df = pd.read_csv('netflix_titles.csv')

# Display first few rows of the dataset
print(df.head())

# Display basic information about the dataset
print(df.info())

# Display summary statistics
print(df.describe())

# Data Cleaning
# Convert 'date_added' to datetime
df['date_added'] = pd.to_datetime(df['date_added'], errors='coerce')

# Handle missing values
# Show missing values per column
print(df.isnull().sum())

# Remove rows with missing 'title' or 'type' as they are essential for the analysis
df.dropna(subset=['title', 'type'], inplace=True)

# Fill missing values in 'rating' with 'Not Rated'
df['rating'].fillna('Not Rated', inplace=True)

# Fill missing values in 'duration' with 0 (in case of TV Shows)
df['duration'].fillna(0, inplace=True)

# Convert 'duration' to numeric (for Movies)
df['duration'] = df['duration'].apply(lambda x: str(x).replace(' min', '') if 'min' in str(x) else 0)
df['duration'] = pd.to_numeric(df['duration'], errors='coerce')

# Exploratory Data Analysis (EDA)
# Number of Movies vs. TV Shows
type_counts = df['type'].value_counts()
print(type_counts)

# Plot the number of Movies vs TV Shows
sns.countplot(x='type', data=df)
plt.title('Movies vs TV Shows')
plt.show()

# Distribution of content by Country (Top 10 countries)
country_counts = df['country'].value_counts().head(10)
print(country_counts)

# Plot content distribution by country
plt.figure(figsize=(10, 6))
sns.barplot(x=country_counts.index, y=country_counts.values)
plt.title('Top 10 Countries Producing Netflix Content')
plt.xticks(rotation=45)
plt.show()

# Content Added Over the Years
df['year_added'] = df['date_added'].dt.year
yearly_content = df['year_added'].value_counts().sort_index()

# Plot content added over the years
plt.figure(figsize=(10, 6))
yearly_content.plot(kind='line', marker='o')
plt.title('Content Added Over the Years')
plt.xlabel('Year')
plt.ylabel('Number of Titles')
plt.show()

# Most Popular Genres (Top 10)
# Extracting genres from the 'listed_in' column
genres = df['listed_in'].str.split(',').explode().str.strip().value_counts().head(10)
print(genres)

# Plot the most popular genres
plt.figure(figsize=(10, 6))
sns.barplot(x=genres.index, y=genres.values)
plt.title('Top 10 Most Popular Netflix Genres')
plt.xticks(rotation=45)
plt.show()

# Ratings Distribution
rating_counts = df['rating'].value_counts()

# Plot the distribution of ratings
plt.figure(figsize=(10, 6))
sns.barplot(x=rating_counts.index, y=rating_counts.values)
plt.title('Ratings Distribution')
plt.xticks(rotation=45)
plt.show()

# Average Duration of Movies vs. TV Shows
avg_duration = df.groupby('type')['duration'].mean()

# Plot the average duration
avg_duration.plot(kind='bar', color=['#3498db', '#e74c3c'])
plt.title('Average Duration of Movies vs TV Shows')
plt.ylabel('Average Duration (min)')
plt.show()

# Top 10 Directors with Most Titles on Netflix
director_counts = df['director'].dropna().str.split(',').explode().str.strip().value_counts().head(10)
print(director_counts)

# Plot the top 10 directors
plt.figure(figsize=(10, 6))
sns.barplot(x=director_counts.index, y=director_counts.values)
plt.title('Top 10 Directors with Most Titles on Netflix')
plt.xticks(rotation=45)
plt.show()

