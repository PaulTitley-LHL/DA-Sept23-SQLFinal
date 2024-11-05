# Final-Project-Transforming-and-Analyzing-Data-with-SQL

## Project/Goals
This project will attempt to answer the questions within, but hopefully with the QA process attached, will shed light on my thought process of how I came to those answers.

Before starting this project I have briefly looked at the CSV files, and became aware how much data is missing. My goal is that even if I'm not able to produce the data that's missing if it's nessecary to answer the questions completely, I can at least prove I tried everything I could to verify my answers.

## Process

Taking queues from my cohorts, usually my process involved attempting to answer the questions first, before cleaning the data. When I inevitably would come across results in my queries that were questionable, that would lead me down a road of doing QA to determine what I was working with was viable, trustable data. This work flow actually led me to create a few of my own questions for the other part of the project first, as I would discover interesting results that didn't have anything to do with the pre-written questions. But when focusing on those pre-written questions, I would find myself spending more time verifying that the answer I got was correct, rather than trying to generate the proper query itself to answer it.

Answering the question was the simple part. Answering it confidently is the hard part.

## Results


Primairly I was able to discover that this is a Google based product database, based on the volume of products in specific Calafornian cities, as well as the large amount of Google branded products within the categories of products available.


## Challenges 

With the pre-written questions focusing on the revenue from the transactions in the tables, the high amount of rows in each table that didn't list the proper amount of data to determine the totals for each transaction, either individually or grouped by city/country, posed the biggest challenge. A large amount of my QA steps involved determining if the available data on quantity sold or price per item could lead me to fill in any blanks that could lead me to more accurate answers. Ultimately though, I had to determine that I didn't have enough available data to make a confident answer for the missing values in the revenue totals, so I had to base my answers on what was available in those rows, outside of the null values.


## Future Goals

If I had more time, I suppose I would have consulted with my classmates to determine if they had noticed points of data that might have led to answers I didn't have. I wanted to challange myself on my first run of this project to see all I could discover and answer in a vacuum. But, ultimately with so much missing data in the provided CSV files, the struggle to determine if missing data or duplicate values were nessecary in some tables led to me second guessing myself quite often. It always felt like I was missing a piece of the puzzle.
