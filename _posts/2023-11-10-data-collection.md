---
layout: post
title:  "Data Collection for Star Wars"
author: Andy Nelson
description: This article explains the route I took when trying to collect useful data for my final project in STAT 386.
image: "/assets/images/Tatooine.webp"
---


## "Hello There"

Before collecting data, I needed to come up with an idea of what I wanted to research. As summer turned to fall, and fall to winter, I began to end most of my nights off with a movie. I would log my movies on the app LetterBox and read reviews given by other users. I became interested in movie reviews and started to wonder how much each of these movies were making in box office sales. At this moment, I thought I had my project figured out.

I started looking at data on Kaggle and Data.world. As I browsed these websites, I was able to see what others had been able to do with movie data. Many users had used the IMDb API to retrive their data. I quickly came to the realization that the IMDb API cost money, so I started researching third-party APIs. After a few hours of painful research, I had no success in finding an API that would work for me. This was when a movie title caught my eye - Star Wars.

I returned to the Google search bar and typed "free Star Wars apis". One of the first links took me to swampi.dev, which had loads of data on the entire universe. As a kid, I always wondered how big the planets in Star Wars really were, especially when the Death Star blew up Alderaan. I went through the endpoints of the API and came across /planets. This contained all the information I could have asked for, including diameter, density, gravity and population.

After what ended up being close to a full day's worth of seeking, I had finally found my data source. Now that I have requested the data into VS code, I can finally say: "this is where the fun begins".

I decided that I wanted to know how many people live on each planet in tthe Star Wars universe. I posed the question: What causes a planet in the Star Wars' Galaxy to have a large population? 

## Where the Research Began

As stated above, I retrieved my data from the [Star Wars' API](https://swapi.dev/). I used the endpoint /planets to retrieve the information about each planet. I then put this information into a dataframe named 'planets_df". This was done by following this code:

```
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