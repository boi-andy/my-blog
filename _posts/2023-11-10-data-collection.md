---
layout: post
title:  "Data Collection for Star Wars"
author: Andy Nelson
description: This article explains the route I took when trying to collect useful data for my final project in STAT 386.
image: "/assets/images/Tatooine.webp"
---


## Brainstorming Idea 

Before collecting data, I needed to come up with an idea of what I wanted to research. As summer turned to fall, and fall to winter, I began to end most of my nights off with a movie. I would log my movies on the app LetterBox and read reviews given by other users. I became interested in movie reviews and started to wonder how much each of these movies were making in box office sales. At this moment, I thought I had my project figured out.

I started looking at data on Kaggle and Data.world. As I browsed these websites, I was able to see what others had been able to do with movie data. Many users had used the IMDb API to retrieve their data. I quickly came to the realization that the IMDb API cost money, so I started researching third-party APIs. After a few hours of painful research, I had no success in finding an API that would work for me. This was when a movie title caught my eye - Star Wars.

I returned to the Google search bar and typed "free Star Wars apis". One of the first links took me to swampi.dev, which had loads of data on the entire universe. As a kid, I always wondered how big the planets in Star Wars really were, especially when the Death Star blew up Alderaan. I went through the endpoints of the API and came across /planets. This contained all the information I could have asked for, including diameter, density, gravity and population.

After what ended up being close to a full day's worth of seeking, I had finally found my data source. Now that I have requested the data into VS code, I can finally say: "this is where the fun begins".

I decided that I wanted to know how many people live on each planet in the Star Wars universe. I posed the following questions: <br>
    1. What causes a planet in the Star Wars' Galaxy to have a large population? <br>
    2. Does the terrian of a planet correlate to a population of a planet?


## Where the Data Collection Began

As stated above, I retrieved my data from the [Star Wars' API](https://swapi.dev/). I used the endpoint /planets to retrieve the information about each planet. I then put this information into a dataframe named 'planets_df'. This was done by following this code:

```
# Python packages
import requests
import pandas as pd

# Function to fetch all planet data from SWAPI
def get_all_planet_data():
    all_planets_data = []

    for planet_id in range(1, 62):  # There are 60 planets in the SWAPI
        api_url = f'https://swapi.dev/api/planets/{planet_id}/'
        response = requests.get(api_url)

        if response.status_code == 200:
            planet_data = response.json()
            all_planets_data.append(planet_data)

    return all_planets_data

# Fetch all planet data
all_planets_data = get_all_planet_data()

# Create a DataFrame from the list of planet data
planets_df = pd.DataFrame(all_planets_data)
```

## Data Cleaning
Once I was able to make the dataframe, I cleaned it up by getting rid of all planets that had unknown diameters. I also got rid of all the columns that were irrelevant for my research and made the column 'diameter' from a string to numeric. This was done with the following code:

```
# Remove all planets with a diameter of '0' or 'unknown'
planets_df = planets_df[(planets_df['diameter'] != 0)]
planets_df = planets_df[planets_df['diameter'] != 'unknown']

# Remove columns after "population"
planets_df = planets_df.loc[:, :'population']

# Convert diameter to numeric
planets_df['diameter'] = pd.to_numeric(planets_df['diameter'])
```
This left me with a dataframe that looked like this:

![Figure](https://boi-andy.github.io/my-blog/assets/images/planet_df.png)


## Add Additional Data
I then pulled data from the endpoint /people. This contained information of each main Star Wars character, including height, weight, and where they were born. I thought it would be fun to add a part in my Streamlit app where I could see all the characters from a selected planet. To do this, I used the following code (which has descriptions of cleaning):

```
import requests
import pandas as pd

# Function to fetch data for a specific person
def get_person_data(person_id):
    api_url = f'https://swapi.dev/api/people/{person_id}/'
    response = requests.get(api_url)
    
    if response.status_code == 200:
        return response.json()
    else:
        print(f"Failed to retrieve data for person {person_id}. Status code: {response.status_code}")
        return None

# Get the total number of characters
initial_response = requests.get('https://swapi.dev/api/people/')
total_characters = initial_response.json()['count']

# Create an empty DataFrame to store the data
columns = ['Name', 'Birth Year', 'Gender', 'Height', 'Mass', 'Hair Color', 'Eye Color', 'Skin Color']
characters_df = pd.DataFrame(columns=columns)

# Fetch and append data for each person
for person_id in range(1, total_characters + 1):
    person_data = get_person_data(person_id)
    
    if person_data:
        # Append a new row to the DataFrame
        characters_df = characters_df.append({
            'Name': person_data['name'],
            'Birth Year': person_data['birth_year'],
            'Gender': person_data['gender'],
            'Height': person_data['height'],
            'Mass': person_data['mass'],
            'Hair Color': person_data['hair_color'],
            'Eye Color': person_data['eye_color'],
            'Skin Color': person_data['skin_color'],
        }, ignore_index=True)
```
This generated a dataframe that looked like this:<br>
![Figure](https://boi-andy.github.io/my-blog/assets/images/people_df.png)


## Ethical Considerations
I did not have many ethical concerns while gathering this data as it is all public information. Considering the Star Wars galaxy is made up (at least thats what they want us to think- look at all the data we have!), I don't think people would mind that I am running analysis on its data. I think more than anything, people would be happy that I took intereest in this data and facsinated by my findings. The API allows requests and only asks that users don't abuse or sell the information, which I will not do. 


## Conclusion 
After considering ethical issues, I have finalized a clean dataframe that contained all the planets in the Star Wars' galaxy and the information of main characters that originate from these planets. Between planets and characters, I have a surplus of observations and types of variables. I am officially ready to start my Exploratory Data Analysis and create visuals.

For my full code and to view the data, visit this repo: [Final Project](https://github.com/boi-andy/final_project)

For a description and analysis of each graph, visit my blog post [EDA of Planets and People](https://boi-andy.github.io/my-blog/2023/11/14/EDA.html)

For an interactive app of the EDA of planets and people, visit [Streamlit](https://boi-andy-final-project-streamlitstreamlit-app-pfbhxb.streamlit.app/)
