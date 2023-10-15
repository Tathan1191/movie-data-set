# movie-data-set
EDA and analisys movie data 



Open In Colab

Descriptive Study and EDA of the Movie Database

To perform a descriptive study and EDA of the movie database, I would use the following steps:

    Import the necessary libraries.


from google.colab import files
uploaded = files.upload()


     
Upload widget is only available when the cell has been executed in the current browser session. Please rerun this cell to enable.

# @title Default title text

import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
     

    Load the dataset.



Movie_Data_df = pd.read_excel('Movie_Data.xlsx')
df = pd.read_excel('Movie_Data.xlsx')
     

    Check the data types and identify any missing values.


df.dtypes
df.isna().sum()
     

Movie Title                 0
Release Date                0
Wikipedia URL               0
Genre (1)                   0
Genre (2)                 246
Director (1)                0
Director (2)              467
Cast (1)                    0
Cast (2)                    5
Cast (3)                   23
Cast (4)                   56
Cast (5)                  119
Budget ($)                  0
Box Office Revenue ($)      0
dtype: int64


print(df.columns)
     

Index(['Movie Title', 'Release Date', 'Wikipedia URL', 'Genre (1)',
       'Genre (2)', 'Director (1)', 'Director (2)', 'Cast (1)', 'Cast (2)',
       'Cast (3)', 'Cast (4)', 'Cast (5)', 'Budget ($)',
       'Box Office Revenue ($)'],
      dtype='object')

    Clean and prepare the data.


# Remove any duplicate rows.
df = df.drop_duplicates()

# Convert the `Release Date` column to a datetime type.
df['Release Date'] = pd.to_datetime(df['Release Date'])

# Fill in any missing values in the `Release Date` column with the median value.
df['Release Date'].fillna(df['Release Date'].median(), inplace=True)

# Create a new column called `Release Year` that extracts the year from the `Release Date` column.
df['Release Year'] = df['Release Date'].dt.year

# Create a new column called `Budget in Millions` that converts the `Budget ($)`` column from dollars to millions.
df['Budget in Millions'] = df['Budget ($)'] / 1000000

# Drop the original `Budget ($)` column.
df.drop('Budget ($)', axis=1, inplace=True)

# Display the DataFrame to verify the changes
print(df.head())
     

                                 Movie Title Release Date  \
0                        10 Cloverfield Lane   2016-03-08   
1  13 Hours: The Secret Soldiers of Benghazi   2016-01-15   
2                                     2 Guns   2013-08-02   
3                             21 Jump Street   2012-03-16   
4                             22 Jump Street   2014-06-04   

                                       Wikipedia URL Genre (1) Genre (2)  \
0  https://en.wikipedia.org/wiki/10_Cloverfield_Lane  Thriller    Horror   
1  https://en.wikipedia.org/wiki/13_Hours:_The_Se...    Action  Thriller   
2               https://en.wikipedia.org/wiki/2_Guns    Action     Crime   
3  https://en.wikipedia.org/wiki/21_Jump_Street_(...    Comedy    Action   
4       https://en.wikipedia.org/wiki/22_Jump_Street    Action    Comedy   

        Director (1)  Director (2)                 Cast (1)  \
0   Dan Trachtenberg           NaN  Mary Elizabeth Winstead   
1        Michael Bay           NaN         James Badge Dale   
2  Baltasar Kormákur           NaN            Mark Wahlberg   
3          Phil Lord  Chris Miller               Jonah Hill   
4          Phil Lord  Chris Miller           Channing Tatum   

            Cast (2)        Cast (3)         Cast (4)            Cast (5)  \
0       John Goodman  John Gallagher              NaN                 NaN   
1     John Krasinski   Toby Stephens  Pablo Schreiber         Max Martini   
2  Denzel Washington    Paula Patton      Bill Paxton  Edward James Olmos   
3     Channing Tatum        Ice Cube      Brie Larson          Rob Riggle   
4         Jonah Hill        Ice Cube              NaN                 NaN   

   Box Office Revenue ($)  Release Year  Budget in Millions  
0             108300000.0          2016                15.0  
1              69400000.0          2016                45.0  
2             131900000.0          2013                61.0  
3             201500000.0          2012                55.0  
4             331300000.0          2014                84.5  

    Perform descriptive analysis.


df.describe()
     
	Box Office Revenue ($) 	Release Year 	Budget in Millions
count 	5.080000e+02 	508.000000 	508.000000
mean 	1.519832e+08 	2013.950787 	48.871398
std 	1.832555e+08 	1.352746 	49.198592
min 	1.000000e+06 	2012.000000 	1.000000
25% 	3.110000e+07 	2013.000000 	14.000000
50% 	7.935000e+07 	2014.000000 	30.000000
75% 	2.037250e+08 	2015.000000 	65.000000
max 	9.708000e+08 	2016.000000 	250.000000
Distributions
2-d distributions
Values

    Create visualizations to explore the data.


plt.figure(figsize=(10, 6))
sns.barplot(x='Release Year', y='Box Office Revenue ($)', data=df)
plt.xlabel('Release Year')
plt.ylabel('Box Office Revenue ($)')
plt.title('Box Office Revenue by Release Year')
plt.show()
     

plt.figure(figsize=(13, 12))
plt.pie(df['Genre (1)'].value_counts(), labels=df['Genre (1)'].unique(), autopct="%1.1f%%")
plt.title('Movie Genres Distribution')
plt.show()
     


# Assuming cast_matrix is the correlation matrix for relevant columns
# Extracting relevant columns for correlation matrix
correlation_cols = ['Box Office Revenue ($)', 'Release Year', 'Budget in Millions']
cast_matrix = df[correlation_cols].corr()

# Plotting the heatmap
plt.figure(figsize=(10, 6))
sns.heatmap(cast_matrix, cmap='coolwarm', annot=True, fmt='.2f')
plt.title('Correlation Matrix')
plt.show()
     

actor_revenue = df.groupby('Cast (1)')['Box Office Revenue ($)'].sum().sort_values(ascending=False)

# Taking the top 5 best-paid actors for the pie chart
top_actors = actor_revenue.head(5)

# Creating the pie chart
plt.figure(figsize=(10, 6))
plt.pie(top_actors, labels=top_actors.index, autopct='%1.1f%%', startangle=140)
plt.title('Top 5 Best-Paid Actors (Box Office Revenue)')
plt.axis('equal')  # Equal aspect ratio ensures that pie is drawn as a circle.
plt.show()
     

# Calcular la diferencia entre inversión y ganancias
df['Profit/Loss'] = df['Box Office Revenue ($)'] - df['Budget in Millions']

# Seleccionar las películas con mayor inversión y menores ganancias
movies_with_least_profit = df.nsmallest(5, 'Profit/Loss')[['Movie Title', 'Budget in Millions', 'Box Office Revenue ($)', 'Profit/Loss']]

# Crear el gráfico de barras
plt.figure(figsize=(12, 8))
plt.bar(movies_with_least_profit['Movie Title'], movies_with_least_profit['Profit/Loss'], color='red')
plt.xlabel('Película')
plt.ylabel('Differennce between profit loss (USD Millions)')
plt.title('Most investment and least profit')
plt.xticks(rotation=45)
plt.tight_layout()
plt.show()
     


# Datos de las películas con mayor inversión y pocas ganancias
movie_titles = ['10 Cloverfield Lane', '13 Hours: The Secret Soldiers of Benghazi', '2 Guns', '21 Jump Street', '22 Jump Street']
budget_millions = [15.0, 45.0, 61.0, 55.0, 84.5]
revenue_millions = [108300000.0, 69400000.0, 131900000.0, 201500000.0, 331300000.0]

# Crear el gráfico de dispersión
plt.figure(figsize=(12, 8))
plt.scatter(budget_millions, revenue_millions, color='red')

# Anotar los nombres de las películas en el gráfico
for i, title in enumerate(movie_titles):
    plt.annotate(title, (budget_millions[i], revenue_millions[i]), textcoords="offset points", xytext=(0,10), ha='center')

plt.xlabel('Presupuesto (Millones de $)')
plt.ylabel('Ganancias en Taquilla (Millones de $)')
plt.title('Películas con Mayor Inversión y Pocas Ganancias')
plt.grid(True)
plt.show()
     


     
