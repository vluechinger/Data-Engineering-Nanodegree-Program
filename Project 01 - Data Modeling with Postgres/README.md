# Documentation Project ETL Pipeline for Sparkify

## Purpose
This project has been initiated by a fictional startup called Sparkify. They developed a music streaming app and started to collect data from their users regarding their listening habits for various artists. The purpose of this project is to combine this data with the information about the songs and the respective artists to create a database in Postgres.
In the end, a functional ETL pipeline is developed in order to give Sparkify the ability to get valuable information out of their music streaming data.
They could apply the data accumulated in this database for example to build recommending models for their users or to analyze at which peak times their app gets used most in order to ensure the stability of the app.


## Project Design

### Data

The data gives information about the type of songs played, the users, the play time, and the song artists. Hereby, the dataset contains entries for the month of November in 2018 and is stored in json files.

### Database Schema

The database is structured in a star schema consisting of 5 tables. In the middle stands the fact table *songplays*, combining the metrics of the database. Around it are the 4 dimension tables *users*, *songs*, *artists* and *time*, giving more information about their respective field.


### ETL Pipeline

The data used in this repository is stored in the data folde with its subsequent folders log_data and song_data.

Codewise, there are four files of importance in the repository. 
The *create_tables.py* file should be run first and hereby creates (and drops beforehand) all necessary tables for this ETL by using the queries defined in *sql_queries.py*.
The file *etl.py* is then used to fill those tables with the data extracted from the data folder and its containing json files.
In the Jupyter Notebook *test.ipynb*, the tables are tested and some example queries are carried out and analysed.

Note: The *etl.ipynb* file has been used to test the respective Python script and can be ignored.



## Example Queries

When the database is all set up, we can run a couple of queries to analyse its contents. The results for these queries can be seen in *test.ipynb*.

For example, we can examine the number of users by their subscription level as well as the number of songs these two groups listened to and the number of sessions:

    sql SELECT level, COUNT(DISTINCT user_id) AS num_users, COUNT(songplay_id) AS num_played_songs, COUNT(songplay_id)/COUNT(DISTINCT user_id) AS avg_num_songs_per_user,  COUNT(DISTINCT session_id) AS num_sessions FROM songplays GROUP BY level
    
This query shows us that although there are 82 free users and only 22 paying users in the dataset, the paying users on average play nearly 17 times more songs in about the same number of sessions.

We can also examine the operating systems chosen by the users. However, looking for Windows and Mac in the column *user_agent* shows that the OS' are pretty balanced with 18 Mac and 19 Windows users.

    SELECT COUNT(DISTINCT user_agent) FROM songplays WHERE user_agent LIKE '%Mac%'
    SELECT COUNT(DISTINCT user_agent) FROM songplays WHERE user_agent LIKE '%Windows%'

Additionally, we can have a look at the time of day when the most songs are played with this query:

    SELECT time.hour, COUNT(songplay_id) FROM songplays AS s JOIN time ON time.start_time=s.start_time GROUP BY time.hour ORDER BY time.hour
    
This gives us an idea when the peak usage times could be. Although, these values could vary depending on the time of year. (Reminder: this data shows only the month of November in 2018)
