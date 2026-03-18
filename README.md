# SQL Summative Lab

This repository contains `SQLSummativeLab.ipynb`, a two-part SQL analysis project.

- Part 1 solves guided business questions against the `data.sqlite` customer, orders, products, payments, employees, and offices database.
- Part 2 performs an open-ended exploratory analysis on the IMDb movie database extracted from `im.db.zip`.

## Project Structure

- `SQLSummativeLab.ipynb` - complete notebook with SQL queries, outputs, reflections, and visuals
- `data.sqlite` - wholesale miniature models business database used in Part 1
- `im.db.zip` / `im.db` - movie database used in Part 2
- `images/ERD.png` - ERD for the customer/product database
- `images/movie_data_erd.jpeg` - ERD for the movie database

## Tools and Libraries

The notebook uses:

- `sqlite3` for database connections
- `pandas` for running SQL and storing results in DataFrames
- `seaborn` and `matplotlib` for visualization
- `numpy` and `pandasql` during setup

## Part 1: Guided SQL Queries

Part 1 translates business requests into SQL queries and basic visuals.

### Step 1: Connect to the Database

- Imported the required Python libraries
- Created a SQLite connection to `data.sqlite`

### Step 2: Limited Edition California Product

Goal: identify California customers with a high credit limit for a limited-edition promotion.

Query logic:

- Filtered to `country = 'USA'`
- Filtered to `state = 'CA'`
- Filtered to `creditLimit > 25000`
- Cast `creditLimit` to numeric for correct comparison and sorting

Result summary:

- Returned 10 California customers with credit limits above 25,000
- Highest-credit customer: `Mini Gifts Distributors Ltd.` at `210500.00`

### Step 3: International Collectable Campaign

Goal: find non-U.S. customers whose names contain `Collect` for an international campaign.

Query logic:

- Filtered to `country != 'USA'`
- Filtered to `customerName LIKE '%Collect%'`
- Sorted alphabetically by customer name

Result summary:

- Returned 15 international customers
- Included customers from Australia, Switzerland, Germany, Ireland, Denmark, Hong Kong, Russia, France, Canada, Austria, Japan, and the UK

Reflection summary:

- Explained how the `WHERE` clause combines both conditions with `AND` so only non-U.S. customers whose names match the campaign theme are returned

### Step 4: USA Credit and Inventory Policy

Goal: compare average customer credit limits by U.S. state.

Query logic:

- Filtered to U.S. customers with non-null states
- Grouped by `state`
- Calculated `AVG(creditLimit)`
- Sorted descending by average credit limit

Result summary:

- Highest average credit limit: `NH` at `114200.00`
- Lowest average credit limit: `NJ` at `43000.00`
- `CA` ranked fourth with `83854.55`

Visual:

- Bar chart of average credit limit by state

### Step 5: Top Customers

Goal: find the top 10 customers by total payment amount.

Query logic:

- Joined `customers` and `payments`
- Grouped by customer
- Summed payment amounts
- Sorted descending and limited to 10 rows

Result summary:

- Top customer: `Euro+ Shopping Channel` with `715738.98`
- Second: `Mini Gifts Distributors Ltd.` with `584188.24`
- Remaining top customers included `Australian Collectors, Co.`, `Muscle Machine Inc`, and `Dragon Souveniers, Ltd.`

Visual:

- Bar chart of the top 10 customers by total payments

### Step 6: Customer Product Quantities

Goal: list, for each customer, any product they purchased in quantities of 10 or more.

Query logic:

- Joined `customers`, `orders`, `orderdetails`, and `products`
- Grouped by customer and product
- Summed `quantityOrdered`
- Used `HAVING` to keep totals of at least 10
- Sorted ascending by total quantity purchased

Result summary:

- Returned `2531` qualifying customer-product pairs
- Smallest qualifying totals began at `10`
- Largest totals were concentrated with `Euro+ Shopping Channel`, including `1992 Ferrari 360 Spider red` at `308`

### Step 7: Product Analysis

Goal: compare product lines by number of products and total quantity ordered.

Query logic:

- Joined `products` to `orderdetails`
- Counted distinct products in each `productLine`
- Summed total quantity ordered per product line
- Used `LEFT JOIN` so product lines with unmatched order records would still be included

Result summary:

- `Classic Cars` led with `38` products and `35582` total units ordered
- `Vintage Cars` followed with `24` products and `22933` units
- `Trains` had the smallest footprint with `3` products and `2818` units

Visual:

- Scatter plot of `total_products` vs `total_quantity_ordered`
- Each point was labeled by product line to show whether larger product catalogs tend to align with higher order volume

Reflection summary:

- Explained why a scatter plot is appropriate for showing the relationship between two numeric measures

### Step 8: Remote Offices

Goal: identify employees who work in offices with fewer than 5 total employees, including their jobs and supervisors.

Query logic:

- Used a subquery to find `officeCode` values where `COUNT(*) < 5`
- Joined `employees` to `offices`
- Self-joined `employees` to retrieve each employee's supervisor

Result summary:

- Returned `12` employees across the small offices in `Boston`, `NYC`, `Tokyo`, `Sydney`, and `London`
- Included both employee titles and supervisor titles to support remote-work planning

Reflection summary:

- Explained that the subquery isolates the office-level employee count logic, while the outer query handles the employee-level detail retrieval

### Step 9: Close the Connection

- Closed the SQLite connection with `conn.close()` after completing Part 1

## Part 1 Takeaways

Part 1 demonstrates practical use of:

- `WHERE` filtering
- `JOIN`
- `GROUP BY`
- `HAVING`
- `ORDER BY`
- Aggregations such as `SUM()` and `AVG()`
- Business-oriented chart selection and labeling

## Part 2: Exploratory Analysis with SQL

Part 2 explores a movie database and frames the work around a business question.

### Dataset Scope

- Source: IMDB movie data
- Coverage: movies released from `2010` to `2019`
- Required constraint: exclude titles with `start_year > 2019`

### Available Tables

The extracted movie database includes:

- `movie_basics`
- `movie_ratings`
- `movie_akas`
- `persons`
- `principals`
- `directors`
- `writers`
- `known_for`

### Initial Exploration

The notebook begins by inspecting schema and running broad exploratory queries.

Key findings:

- Movie counts rose from `11849` in `2010` to `17504` in `2017`
- `2018` remained high at `16849`
- `2019` dropped to `8379`, suggesting the year is less complete than the prior years
- The most common genre label was `Documentary` with `32106` titles

Null-value checks:

- `genres` nulls: `5359`
- `runtime_minutes` nulls: `30765`

These early findings supported a focus on audience reception by genre and highlighted data quality issues that needed to be acknowledged.

### Business Question

The exploratory analysis focused on:

`Which genres should a streaming platform prioritize if it wants stronger audience reception?`

### Methodology for the Deeper Analysis

To answer the question, the notebook:

- Joined `movie_basics` with `movie_ratings`
- Excluded rows where `genres` was null
- Limited titles to `start_year <= 2019`
- Required `numvotes >= 1000` to reduce noise from titles with very small vote counts
- Derived a `primary_genre` by taking the first genre listed in the comma-separated `genres` field
- Kept only genres with at least `50` qualifying titles

The final analysis compared:

- `movie_count`
- `avg_rating`
- `avg_votes`

### Genre Reception Results

Top genres by average rating:

- `Documentary` - `484` movies, `7.20` average rating, `4308` average votes
- `Biography` - `616` movies, `6.96` average rating, `28428` average votes
- `Animation` - `102` movies, `6.84` average rating, `18179` average votes
- `Drama` - `2153` movies, `6.49` average rating, `17990` average votes
- `Adventure` - `490` movies, `6.40` average rating, `54649` average votes

Lower-performing genres in this filtered set:

- `Action` - `6.01` average rating despite very high engagement
- `Thriller` - `5.84` average rating
- `Horror` - `5.10` average rating

Interpretation:

- `Documentary` delivered the strongest average audience ratings, but with lower average vote counts than several mainstream genres
- `Biography` and `Animation` combined strong ratings with stronger engagement than documentaries
- `Drama` offered the best balance of scale and reception because it had the largest sample among the higher-rated genres
- `Adventure` and `Action` showed especially high audience engagement, but not the strongest average ratings

### Part 2 Visuals

Two visuals were created from the `genre_reception` analysis:

- Scatter plot of `avg_votes` vs `avg_rating`, with point size representing `movie_count`
- Bar chart of average IMDB rating by primary genre

Together, these visuals show both quality and engagement, which helps frame a streaming content prioritization decision.

### Data Cleaning Tasks Identified

The notebook surfaces several likely cleaning and modeling tasks for a fuller production analysis:

- Exclude rows with null `genres` when genre-level analysis is required
- Exclude rows with null `runtime_minutes` for runtime-based analysis
- Remove titles after `2019`
- Standardize or normalize the comma-separated `genres` field instead of relying on the first listed genre only
- Recognize that not every movie has ratings, so joins reduce the usable dataset
- Use vote thresholds to avoid unstable averages from low-engagement titles
- Treat `2019` carefully because it appears less complete than other years in the range

## Overall Summary

`SQLSummativeLab.ipynb` demonstrates end-to-end analytical workflow:

- interpreting business questions
- translating them into SQL
- joining and aggregating across related tables
- using reflections to explain query logic in plain language
- choosing chart types that match the question being asked
- performing a more open-ended exploratory analysis and turning it into a business recommendation
