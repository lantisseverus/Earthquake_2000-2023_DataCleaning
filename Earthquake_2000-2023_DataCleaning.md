Earthquake 2000-2023 Data Cleaning
================

## Motivation

As an East Asian, I can never forget living in an area which
unfortunately on the seismic belt. I cannot resonate more when seeing
the tragedies in Turkey and Syria right now. 3.11 Japan Touhoku
Earthquake just had its 12th anniversary and now the same natural
disaster stroke Turkey. It’s a great opportunity to review the history
that we human beings deal with earthquakes.

My data analysis will dedicate to answering the following questions:

- Where are earthquakes frequently take place between 2000 and 2023?
- What are the top 5 strong magnitudes and their Focal Depth (km)of the
  earthquake between 2000 and 2023?
- By comparing the death and injured people in each earthquake to
  discuss if any other external factors can mitigate the impact.
- By comparing the Damage to discuss if these damages can be prevented
  by intervening beforehand.

Hope those who are unfortunately deceased can rest in peace and the data
can bring some insights into future prevention and preparation.

## Import Data

This dataset came from Kaggle Dataset: *Earthquakes -2150 BC – 2023 AD
around the world* Link:
<https://www.kaggle.com/datasets/bharathposa/earthquakes-from-2150bc-2023-ad-around-the-world>.

I will use `readr` package (which is a package of `tidyverse`) to read
the csv file. And then use glimpse function to take a quick review of
this data.

    ## Rows: 6350 Columns: 38
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (1): Location Name
    ## dbl (37): Year, Mo, Dy, Hr, Mn, Sec, Tsu, Vol, Latitude, Longitude, Focal De...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

    ## Rows: 6,350
    ## Columns: 38
    ## $ Year                                 <dbl> NA, -2150, -2000, -2000, -1610, -…
    ## $ Mo                                   <dbl> NA, NA, NA, NA, NA, NA, NA, NA, N…
    ## $ Dy                                   <dbl> NA, NA, NA, NA, NA, NA, NA, NA, N…
    ## $ Hr                                   <dbl> NA, NA, NA, NA, NA, NA, NA, NA, N…
    ## $ Mn                                   <dbl> NA, NA, NA, NA, NA, NA, NA, NA, N…
    ## $ Sec                                  <dbl> NA, 0, NA, NA, NA, 0, NA, NA, 0, …
    ## $ Tsu                                  <dbl> NA, NA, 1, NA, 3, NA, NA, 4, NA, …
    ## $ Vol                                  <dbl> NA, NA, NA, NA, 1351, NA, NA, NA,…
    ## $ `Location Name`                      <chr> NA, "JORDAN:  BAB-A-DARAA,AL-KARA…
    ## $ Latitude                             <dbl> NA, 31.100, 35.683, 38.000, 36.40…
    ## $ Longitude                            <dbl> NA, 35.50, 35.80, 58.20, 25.40, 3…
    ## $ `Focal Depth (km)`                   <dbl> NA, NA, NA, 18, NA, NA, NA, NA, N…
    ## $ Mag                                  <dbl> NA, 7.3, NA, 7.1, NA, NA, NA, NA,…
    ## $ `MMI Int`                            <dbl> NA, NA, 10, 10, NA, 10, 10, NA, N…
    ## $ Deaths                               <dbl> NA, NA, NA, 1, NA, NA, NA, NA, NA…
    ## $ `Death Description`                  <dbl> NA, NA, 3, 1, NA, NA, NA, NA, NA,…
    ## $ Missing                              <dbl> NA, NA, NA, NA, NA, NA, NA, NA, N…
    ## $ `Missing Description`                <dbl> NA, NA, NA, NA, NA, NA, NA, NA, N…
    ## $ Injuries                             <dbl> NA, NA, NA, NA, NA, NA, NA, NA, N…
    ## $ `Injuries Description`               <dbl> NA, NA, NA, NA, NA, NA, NA, NA, N…
    ## $ `Damage ($Mil)`                      <dbl> NA, NA, NA, NA, NA, NA, NA, NA, N…
    ## $ `Damage Description`                 <dbl> NA, 3, NA, 1, NA, 3, NA, 3, 3, 3,…
    ## $ `Houses Destroyed`                   <dbl> NA, NA, NA, NA, NA, NA, NA, NA, N…
    ## $ `Houses Destroyed Description`       <dbl> NA, NA, NA, 1, NA, NA, NA, NA, NA…
    ## $ `Houses Damaged`                     <dbl> NA, NA, NA, NA, NA, NA, NA, NA, N…
    ## $ `Houses Damaged Description`         <dbl> NA, NA, NA, NA, NA, NA, NA, NA, N…
    ## $ `Total Deaths`                       <dbl> NA, NA, NA, 1, NA, NA, NA, NA, NA…
    ## $ `Total Death Description`            <dbl> NA, NA, 3, 1, 3, NA, NA, NA, NA, …
    ## $ `Total Missing`                      <dbl> NA, NA, NA, NA, NA, NA, NA, NA, N…
    ## $ `Total Missing Description`          <dbl> NA, NA, NA, NA, NA, NA, NA, NA, N…
    ## $ `Total Injuries`                     <dbl> NA, NA, NA, NA, NA, NA, NA, NA, N…
    ## $ `Total Injuries Description`         <dbl> NA, NA, NA, NA, NA, NA, NA, NA, N…
    ## $ `Total Damage ($Mil)`                <dbl> NA, NA, NA, NA, NA, NA, NA, NA, N…
    ## $ `Total Damage Description`           <dbl> NA, NA, NA, 1, 3, NA, NA, 3, NA, …
    ## $ `Total Houses Destroyed`             <dbl> NA, NA, NA, NA, NA, NA, NA, NA, N…
    ## $ `Total Houses Destroyed Description` <dbl> NA, NA, NA, 1, NA, NA, NA, NA, NA…
    ## $ `Total Houses Damaged`               <dbl> NA, NA, NA, NA, NA, NA, NA, NA, N…
    ## $ `Total Houses Damaged Description`   <dbl> NA, NA, NA, NA, NA, NA, NA, NA, N…

## Data Cleaning

We now understand the data and the data types of each column. Let’s get
our hand dirty with data cleaning! What I am going to do with the data
cleaning:

1.  Concatenate the year, month, and day as a Date column.  
2.  I will apply the filter on the data to subset only data from 2000 to
    2023.
3.  Select only the columns that are relevant to my questions of
    interest.
4.  Rename the variable to make its naming more consistent
5.  Create 2 new variables to estimate the overall impact on human
    beings

``` r
filtered_df = earthquake_df %>% mutate(date = make_date(year = Year, month = Mo, day = Dy)) %>%  filter(date >= "2000-01-01" & date <= "2023-12-31") %>% select(date, `Location Name`, Latitude, Longitude, `Focal Depth (km)`, Mag, Deaths, Injuries, `Damage ($Mil)`,`Houses Damaged`, `Total Deaths`, `Total Injuries`, `Total Damage ($Mil)`, `Total Houses Damaged`) %>% rename(location = `Location Name`, 
           Depth_km = `Focal Depth (km)`,
           Damage_Mil = `Damage ($Mil)`,
           House_Damaged = `Houses Damaged`,
           Total_Death = `Total Deaths`,
           Total_Injuries = `Total Injuries`,
           Total_Damage_Mil = `Total Damage ($Mil)`, 
           Total_House_Damaged = `Total Houses Damaged`
           ) %>% 
  mutate(Casualty = Deaths + Injuries, 
         Total_Casualty = Total_Death + Total_Injuries)
```

From Step 1 to 5, we have 1298 entries of observation, but most of the
data containing `NA` values.

I decide to take further step to remove those incompleted cases.

6.  Remove records with `NA` or null value

``` r
cleaned_earthquake_df = na.omit(filtered_df) 

cleaned_earthquake_df %>% head() %>%  knitr::kable()
```

| date       | location                                             | Latitude | Longitude | Depth_km | Mag | Deaths | Injuries | Damage_Mil | House_Damaged | Total_Death | Total_Injuries | Total_Damage_Mil | Total_House_Damaged | Casualty | Total_Casualty |
|:-----------|:-----------------------------------------------------|---------:|----------:|---------:|----:|-------:|---------:|-----------:|--------------:|------------:|---------------:|-----------------:|--------------------:|---------:|---------------:|
| 2001-01-13 | EL SALVADOR; GUATEMALA                               |   13.049 |   -88.660 |       60 | 7.7 |    844 |     4723 |    753.000 |        169632 |         844 |           4723 |          753.000 |              169632 |     5567 |           5567 |
| 2001-02-13 | EL SALVADOR: SAN JUAN TEPEZONTES-SAN VICENTE-COJUTEP |   13.671 |   -88.938 |       10 | 6.6 |    315 |     3399 |    348.500 |         15706 |         315 |           3399 |          348.500 |               15706 |     3714 |           3714 |
| 2001-03-24 | JAPAN: HIROSHIMA, OKAYAMA, HONSHU, KAGAMA            |   34.083 |   132.526 |       50 | 6.8 |      2 |      161 |    500.000 |          3700 |           2 |            161 |          500.000 |                3700 |      163 |            163 |
| 2002-03-05 | PHILIPPINES: MINDANAO                                |    6.033 |   124.249 |       31 | 7.5 |     15 |      100 |      1.714 |           800 |          15 |            100 |            1.714 |                 800 |      115 |            115 |
| 2003-05-21 | ALGERIA: ALGIERS, BOUMERDES, REGHIA, THENIA          |   36.964 |     3.634 |       12 | 6.8 |   2287 |    11000 |   5000.000 |        163000 |        2287 |          11000 |         5000.000 |              163000 |    13287 |          13287 |
| 2004-08-10 | CHINA: YUNNAN PROVINCE: LUDIAN                       |   27.266 |   103.873 |        6 | 5.4 |      4 |      600 |     50.000 |         65601 |           4 |            600 |           50.000 |               65601 |      604 |            604 |

After filtering and removing the `NA` values from the data, we have only
55 observations.

## Data Processing

We can still rank the top 5 strongest Earthquake from the data.

``` r
cleaned_earthquake_df %>% 
  arrange(-Mag) %>% 
  select(date, location, Mag, Depth_km) %>% 
  head(5) %>% 
  knitr::kable()
```

| date       | location                                    | Mag | Depth_km |
|:-----------|:--------------------------------------------|----:|---------:|
| 2011-03-11 | JAPAN: HONSHU                               | 9.1 |       30 |
| 2010-02-27 | CHILE: MAULE, CONCEPCION, TALCAHUANO        | 8.8 |       23 |
| 2015-09-16 | CHILE: CENTRAL                              | 8.3 |       22 |
| 2017-09-08 | MEXICO: OAXACA, CHIAPAS, TABASCO; GUATEMALA | 8.2 |       46 |
| 2008-05-12 | CHINA: SICHUAN PROVINCE                     | 7.9 |       10 |

We know that the top 5 earthquakes so far took place between 2000 and
2023 are:

1.  Japan Touhoku Earthquake 9.1
2.  Chile Maule, Concepcion, Talcahuano Earthquake 8.8
3.  Chile Central Earthquake 8.3
4.  Mexico: Oaxaca, Chiapas, Tabasco; Guatemala 8.3
5.  China Sichuan Earthquake 7.9

The focal depth of 4 out of 5 earthquakes reside 10-30 km.

Next, I want to find which country has the most earthquakes between 2000
and 2023. To do this, I need to make use of latitude and longitude to
match the country name. We then need to install and load `sp` and
`rworldmap` packages.

``` r
#install.packages("maps")
#library(maps)
```
