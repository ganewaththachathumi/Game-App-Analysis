# Game App Analysis Project

## Table of Contents
- [Project Overview](#project-overview)
- [Data Sources](#data-sources)
- [Tools](#tools)
- [Data Collection](#data-collection)
- [Data Cleaning](#data-cleaning)
- [Data Analysis](#data-analysis)
- [Chatbot Features](#chatbot-features)
- [Results](#results)
- [Recommendations](#recommendations)
- [Limitations](#limitations)
- [Conclusion](#conclusion)

## Project Overview
This project is designed to assist companies and developers in making informed decisions about launching new game apps. By leveraging Streamlit for interactivity and Power BI for advanced data visualization, the project provides deep insights into game app trends, user preferences, and market opportunities.

## Data Sources
This project uses real-world game app data, including:
- App install counts
- Revenue figures
- User ratings and reviews
- App descriptions and features

## Tools
- **Frontend**: Built using Streamlit for a clean, interactive user experience.
- **Backend**: Data analysis and processing performed using Python.
- **Visualization**: Advanced insights displayed using Power BI with interactive graphs, charts, and slicers.

## Data Collection
The data was collected from app stores, review aggregators, and user feedback platforms. APIs and web scraping techniques were employed to gather relevant data.

## Data Cleaning
The data was preprocessed to remove duplicates, handle missing values, and normalize formats. This ensured accuracy and consistency for analysis.

## Data Analysis
Python was used for data analysis. Below is an example of the code implemented to analyze genre popularity:

```python
from google_play_scraper import app, search
import pandas as pd
def fetch_app_details(app_id):
    app_data = app(app_id)
    return {
        "App Name": app_data["title"],
        "Genre": app_data["genre"],
        "Rating": app_data["score"],
        "Reviews Count": app_data["reviews"],
        "Installs": app_data["installs"],
        "Price": app_data["price"],
        "Revenue": app_data["free"] and app_data["minInstalls"] * 0.1 or app_data["price"],
        "Developer": app_data["developer"],
        "Release Date": app_data.get("released", "Unknown"),
        "Description": app_data["description"],
    }
categories = ["action", "strategy", "board","card","casino","casual","education","music","word","puzzle", "adventure", "racing", "sports", "arcade", "simulation", "role-playing", "trivia"]

all_game_details = []

for category in categories:
    print(f"Fetching {category} games...")
    results = search(f"{category} games", n_hits=100, country="us", lang="en")
    app_ids = [result["appId"] for result in results]
    game_details = [fetch_app_details(app_id) for app_id in app_ids]
    all_game_details.extend(game_details)

# Save all data to CSV
df = pd.DataFrame(all_game_details)
df.to_csv("all_games_data.xlsx", index=False)
print("All game data saved to all_games_data.csv")
```

This analysis helped identify the most popular genres and their corresponding install counts.

## Chatbot Features
An interactive chatbot was developed using Streamlit to simplify querying data and enhance user engagement. The chatbot provides:

- **Genre Popularity**: Identifies the most popular game genres based on total installs.
- **Top-Rated Apps**: Lists top-rated apps along with their genres and ratings.
- **Free vs. Paid Comparison**: Compares free and paid games in terms of installs and revenue.
- **App Descriptions**: Provides genre-specific insights, including app descriptions and features.

### Example Code for Chatbot
Below is an example of how the chatbot was implemented using Streamlit:

```python
import streamlit as st
import pandas as pd


csv_file_path = "C:/Users/Chathumi/OneDrive/Desktop/Project/game/all_games_data.csv"
data = pd.read_csv(csv_file_path)


most_popular_genre = data.groupby("Genre")["Installs"].sum().idxmax()
max_rating = data["Rating"].max()
top_rated_apps = data[data["Rating"] == max_rating]

# Streamlit Chatbot
st.title("Dynamic Game App Analysis Chatbot 🤖")
st.write("Ask me questions about the game app data or request the dashboard.")

# User input
query = st.text_input("Your Question:")

if query:
    # Process the query
    query = query.lower()
    if "most popular genre" in query:
        response = f"The most popular genre is {most_popular_genre}."
    elif "top rated apps" in query:
        top_apps = "\n".join(
            [f"{row['App Name']} ({row['Genre']}): {row['Rating']} stars" for _, row in top_rated_apps.iterrows()]
        )
        response = f"The top-rated apps are:\n{top_apps}"
    elif "free vs paid" in query:
        free_apps = data[data["Price"] == 0]
        paid_apps = data[data["Price"] > 0]
        free_installs = free_apps["Installs"].sum()
        paid_installs = paid_apps["Installs"].sum()
        free_revenue = free_apps["Revenue"].sum()
        paid_revenue = paid_apps["Revenue"].sum()
        response = (
            f"Free Games: {free_installs} installs, generating {free_revenue} revenue.\n"
            f"Paid Games: {paid_installs} installs, generating {paid_revenue} revenue."
        )
    elif "dashboard" in query:
        response = "Here is the dashboard link: [Open Dashboard](https://app.powerbi.com/groups/me/reports/4e497401-4b4c-416d-9a23-e8312f68cced/ReportSection?experience=power-bi)"
    elif "genre description" in query:
        genre = query.split("genre description")[-1].strip()
        genre_apps = data[data["Genre"].str.contains(genre, case=False, na=False)]
        if genre_apps.empty:
            response = f"No data available for genre: {genre}."
        else:
            descriptions = "\n".join(genre_apps["Description"].head(5).values)
            response = f"Top descriptions for {genre} genre:\n{descriptions}"
    else:
        response = "I'm sorry, I don't have an answer for that. Please ask another question."

    # Display the response
    st.write(response)

```

This chatbot enables users to interact with the data dynamically, making it more accessible and user-friendly.

## Results
- **Most Popular Genres**: Identified top genres based on total installs.
- **Revenue Trends**: Analyzed revenue generation across free and paid games.
- **User Ratings**: Highlighted the highest-rated apps and their features.

## Recommendations
- Focus on developing apps in the most popular genres.
- Explore free apps with in-app purchases to maximize revenue.
- Enhance app features that contribute to higher ratings.

## Limitations
- Data is limited to publicly available sources.
- Some data may be outdated due to the fast-paced nature of the gaming industry.

## Conclusion
This project empowers game developers and businesses to make data-driven decisions by analyzing user preferences, market trends, and revenue opportunities. By leveraging the insights provided, companies can optimize their strategies for successful game app launches.
