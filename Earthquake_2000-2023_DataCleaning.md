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
- By comparing the casualties and magnitude of each earthquake to
  discuss if any other external factors can mitigate the impact.
- By comparing the Damage and House Destroyed with the magnitude of each
  quake to discuss if these damages can be prevented by intervening
  beforehand.

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
and 2023. To do this, I need to separate the location name into country
name and location name.

To make the country names and location more readable, I will modify its
font.

``` r
country_cleaned_df = cleaned_earthquake_df %>% separate(location,c("country", "location"), ":") %>% mutate(country = tools::toTitleCase(tolower(country)), 
                                                                                          location = tools::toTitleCase(tolower(location)))
```

``` r
country_cleaned_df %>% group_by(country) %>% summarize(frequency = n()) %>% arrange(-frequency) %>% head(5) %>% knitr::kable()
```

| country     | frequency |
|:------------|----------:|
| China       |         8 |
| Japan       |         7 |
| Philippines |         7 |
| Indonesia   |         5 |
| Balkans Nw  |         2 |

From the result, we can see that most of the earthquakes took place in
East and Southeast Asia - including China, Japan, Philippines,
Indonesia, India. A few earthquake also took place in Central and South
America, such as Chile, Haiti, and Mexico.

The top 4 countries\* will be:

1.  China
2.  Japan
3.  Philippines
4.  Indonesia

\*NOTE: Choosing top 4 instead of top 5 because the fifth place has the
same frequency as the rest (i.e., 6th to 10th place).

## Dig into the context behind the casulty

Let’s dig into the number behind the casualty. First, I notice that for
each record, there are deaths and total deaths; injuries and total
injuries. These pair-wise variables also lead to the creation of
casualty and total casualty. Not sure how to distinguish these similar
variables.

After simple comparison, most of the data are in alignment with death -
total death, injuries - total injuries, and casualty - total casualty.
Only these 3 records are in difference.

``` r
country_cleaned_df %>% 
  group_by(country) %>% 
  summarize(sum_death = sum(Deaths),
            sum_t_death = sum(Total_Death),
            sum_injured = sum(Injuries),
            sum_t_injured = sum(Total_Injuries), 
            sum_casualty = sum(Casualty), 
            sum_t_casualty = sum(Total_Casualty)) %>% 
  filter(sum_death != sum_t_death | sum_injured != sum_t_injured | sum_casualty != sum_t_casualty) %>% knitr::kable()
```

| country | sum_death | sum_t\_death | sum_injured | sum_t\_injured | sum_casualty | sum_t\_casualty |
|:--------|----------:|-------------:|------------:|---------------:|-------------:|----------------:|
| Chile   |       409 |          573 |       12014 |          12014 |        12423 |           12587 |
| Haiti   |    318248 |       318248 |       42763 |         312763 |       361011 |          631011 |
| Japan   |      1540 |        18493 |        8916 |           8926 |        10456 |           27419 |

To define the casualty, I tend to be lenient toward the numbers. Hence,
I decide to use the total casualty as an indicator since I believe it
includes the direct and indirect casualties from the disaster.

##### My original hypothesis is: The bigger the magnitude, the more casualty the disaster caused.

``` r
country_cleaned_df %>% group_by(date, country, Mag) %>% summarize(Max_casualty = max(Total_Casualty)) %>% arrange(-Max_casualty) %>% head(5)
```

    ## `summarise()` has grouped output by 'date', 'country'. You can override using
    ## the `.groups` argument.

    ## # A tibble: 5 × 4
    ## # Groups:   date, country [5]
    ##   date       country     Mag Max_casualty
    ##   <date>     <chr>     <dbl>        <dbl>
    ## 1 2010-01-12 Haiti       7         616000
    ## 2 2008-05-12 China       7.9       461823
    ## 3 2015-04-25 Nepal       7.8        32957
    ## 4 2011-03-11 Japan       9.1        24595
    ## 5 2018-09-28 Indonesia   7.5        15019

However, from the above table, we can see that Japan has the strongest
magnitude earthquake between 2000 to 2023. The casualties are not the
worst. We may infer from the result that magnitude is not the only
factor that caused casualties but infrastructure and familiarity with
massive emergency first aid.

Let’s use a graph to present the hypothesis with the data.

``` r
country_cleaned_df %>% group_by(date, country, Mag) %>% summarize(Max_casualty = max(Total_Casualty)) %>% ggplot(aes(x = Mag, y = Max_casualty)) + geom_point() + geom_smooth()+ scale_y_continuous(labels = scales::unit_format(unit = "M", scale = 1e-6)) + labs(title = "Max Casualty and Magnitude of Earthquake") +
  xlab("Magnitude") + ylab("Max Casualty (Million)")
```

    ## `summarise()` has grouped output by 'date', 'country'. You can override using
    ## the `.groups` argument.
    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

![](Earthquake_2000-2023_DataCleaning_files/figure-gfm/Casualy%20Viz-1.png)<!-- -->
We can see the scatter plot and the smooth line are almost horizontally
related, which meant they are barely relevant. But, before drawing a
conclusion, let’s test it with a correlation test.

``` r
casualty_df = country_cleaned_df %>% group_by(date, country, Mag) %>% summarize(Max_casualty = max(Total_Casualty)) 
```

    ## `summarise()` has grouped output by 'date', 'country'. You can override using
    ## the `.groups` argument.

``` r
shapiro.test(casualty_df$Mag)
```

    ## 
    ##  Shapiro-Wilk normality test
    ## 
    ## data:  casualty_df$Mag
    ## W = 0.98349, p-value = 0.6484

``` r
shapiro.test(casualty_df$Max_casualty)
```

    ## 
    ##  Shapiro-Wilk normality test
    ## 
    ## data:  casualty_df$Max_casualty
    ## W = 0.22504, p-value = 1.727e-15

From the output, the two p-values are greater than the significance
level 0.05 implying that the distribution of the data are not
significantly different from normal distribution. In other words, we can
assume the normality.

``` r
res = cor.test(casualty_df$Mag, casualty_df$Max_casualty, 
                    method = "pearson")
res
```

    ## 
    ##  Pearson's product-moment correlation
    ## 
    ## data:  casualty_df$Mag and casualty_df$Max_casualty
    ## t = 1.3051, df = 53, p-value = 0.1975
    ## alternative hypothesis: true correlation is not equal to 0
    ## 95 percent confidence interval:
    ##  -0.09320136  0.42200057
    ## sample estimates:
    ##       cor 
    ## 0.1764589

The correlation coefficient: 0.1764589 is close to 0 meaning that there
is no association between the two variables (Magnitude and Casualty).

## Deep Dive into the Damage and the House Destroyed of each quake

Again, I notice that for each record, there are Damage in Million and
Total Damage in Million; House Damaged and Total House Damaged.Not sure
how to distinguish these similar variables.

After simple comparison, most of the data are in alignment with death -
total death, injuries - total injuries, and casualty - total casualty.
Only these 2 records are in difference.

``` r
country_cleaned_df %>% 
  group_by(country) %>% 
  summarize(sum_damage_mil = sum(Damage_Mil),
            sum_t_damage_mil = sum(Total_Damage_Mil),
            sum_house_damaged = sum(House_Damaged),
            sum_t_house_damaged = sum(Total_House_Damaged)) %>% 
  filter(sum_damage_mil != sum_t_damage_mil | sum_house_damaged != sum_t_house_damaged) %>% knitr::kable()
```

| country | sum_damage_mil | sum_t\_damage_mil | sum_house_damaged | sum_t\_house_damaged |
|:--------|---------------:|------------------:|------------------:|---------------------:|
| China   |       89568.00 |           89568.0 |           5684498 |             21324498 |
| Japan   |       42901.71 |          258636.6 |            108435 |               383737 |

To define the Damage, I again incline to be lenient toward the numbers.
Hence, I decide to use the Total Damage in Million and Total House
Damaged as 2 indicators since I believe they include the direct and
indirect damages from the disaster.

##### My original hypothesis is The bigger the magnitude, the more damage the disaster caused and the more houses damaged by the disaster.

``` r
country_cleaned_df %>% group_by(date, country, Mag) %>% summarize(sum_damage = sum(Total_Damage_Mil), 
          sum_house_damage = sum(Total_House_Damaged)) %>% arrange(desc(sum_damage), desc(sum_house_damage)) %>% head(5) %>% knitr::kable()
```

    ## `summarise()` has grouped output by 'date', 'country'. You can override using
    ## the `.groups` argument.

| date       | country | Mag | sum_damage | sum_house_damage |
|:-----------|:--------|----:|-----------:|-----------------:|
| 2011-03-11 | Japan   | 9.1 |   220136.6 |           280920 |
| 2008-05-12 | China   | 7.9 |    86000.0 |         21000000 |
| 2010-02-27 | Chile   | 8.8 |    30000.0 |           500000 |
| 2007-07-16 | Japan   | 6.6 |    12500.0 |              875 |
| 2022-03-16 | Japan   | 7.3 |     8800.0 |            56718 |

From the above table, we can see that Japan has the strongest magnitude
earthquake between 2000 to 2023. The damage cost the most but the amount
of the house damaged is not the most. The damage and house damaged
should be treated separately in this case.

Since house damage may include other factors such as the building
materials.

Let’s visualize the relationship between Total Damage in Million and
Magnitude.

``` r
country_cleaned_df %>% group_by(date, country, Mag) %>% summarize(sum_damage = sum(Total_Damage_Mil)) %>% ggplot(aes(x = Mag, y = sum_damage)) + geom_point() + geom_smooth()+ geom_vline(xintercept = 8, colour="red", linetype = "longdash") + labs(title = "The Relationship between sum of Damage in Million and Magnitude of Earthquake") +
  xlab("Magnitude") + ylab("Sum of Damage (Million)")
```

    ## `summarise()` has grouped output by 'date', 'country'. You can override using
    ## the `.groups` argument.
    ## `geom_smooth()` using method = 'loess' and formula = 'y ~ x'

![](Earthquake_2000-2023_DataCleaning_files/figure-gfm/Damage%20in%20Mil%20and%20Magnitude%20Viz-1.png)<!-- -->
Interestingly, we found an elbow point in the trend. When the Magnitude
of the quake is greater than or equal to 8 the potential damage could
range from 1 to 200,000 Million.
