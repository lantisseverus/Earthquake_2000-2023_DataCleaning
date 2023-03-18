Earthquake 2000-2023 Data Cleaning
================

## Motivation

As an East Asian, I can never forget living in an area which
unfortunately on the seismic belt. I cannot resonate more when seeing
the tragedies in Turkey and Syria right now. 3.11 Japan Touhoku
Earthquake just had its 12th anniversary and now the same natural
disaster stroke Turkey. It’s a great opportunity to review the history
that we human beings deal with earthquakes.

Here’s the updates for Turkey Earthquake:
<https://www.theguardian.com/world/turkey-syria-earthquake-2023>

![Photo from
CNN](Earthquake_2000-2023_DataCleaning_files%5CCNN_quake_viz.jpg)
Reference:
<https://www.cnn.com/middleeast/live-news/turkey-earthquake-latest-020623/index.html>

#### My data analysis will dedicate to answering the following questions:

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

``` r
earthquake_df = read_csv("earthquakes.csv")
```

    ## Rows: 6350 Columns: 38
    ## ── Column specification ────────────────────────────────────────────────────────
    ## Delimiter: ","
    ## chr  (1): Location Name
    ## dbl (37): Year, Mo, Dy, Hr, Mn, Sec, Tsu, Vol, Latitude, Longitude, Focal De...
    ## 
    ## ℹ Use `spec()` to retrieve the full column specification for this data.
    ## ℹ Specify the column types or set `show_col_types = FALSE` to quiet this message.

``` r
glimpse(earthquake_df)
```

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

6.  Remove records with `NA` or null value \<\< I did it in version 1
    but then found out we missed too many important data. Therefore, I
    will erase this step.

## Data Processing

We can still rank the top 5 strongest Earthquake from the data.

``` r
filtered_df %>% 
  arrange(-Mag) %>% 
  select(date, location, Mag, Depth_km) %>% 
  head(5) %>% 
  knitr::kable()
```

| date       | location                                 | Mag | Depth_km |
|:-----------|:-----------------------------------------|----:|---------:|
| 2004-12-26 | INDONESIA: SUMATRA: ACEH: OFF WEST COAST | 9.1 |       30 |
| 2011-03-11 | JAPAN: HONSHU                            | 9.1 |       30 |
| 2010-02-27 | CHILE: MAULE, CONCEPCION, TALCAHUANO     | 8.8 |       23 |
| 2005-03-28 | INDONESIA: SUMATERA: SW                  | 8.6 |       30 |
| 2012-04-11 | INDONESIA: N SUMATRA: OFF WEST COAST     | 8.6 |       20 |

We know that the top 5 earthquakes so far took place between 2000 and
2023 are:

1.  2004 Indian/Indonesia Ocean earthquake and tsunami 9.1
2.  Japan Touhoku Earthquake 9.1
3.  Chile Maule, Concepcion, Talcahuano Earthquake 8.8
4.  Indonesia Sumatera 8.6
5.  Indonesia Sumatera 8.6

The focal depth of these top 5 earthquakes resides 10-30 km.

Next, I want to find which country has the most earthquakes between 2000
and 2023. To do this, I need to separate the location name into country
name and location name.

To make the country names and location more readable, I will modify its
font.

``` r
country_cleaned_df = filtered_df %>% separate(location,c("country", "location"), ":") %>% mutate(country = tools::toTitleCase(tolower(country)), 
                                                                                          location = tools::toTitleCase(tolower(location)))
```

``` r
country_cleaned_df %>% group_by(country) %>% summarize(frequency = n()) %>% arrange(-frequency) %>% head(5) %>% knitr::kable()
```

| country   | frequency |
|:----------|----------:|
| China     |       149 |
| Indonesia |       136 |
| Iran      |        83 |
| Japan     |        72 |
| India     |        51 |

From the result, we can see that most of the earthquakes took place in
East and Southeast Asia - including China,Indonesia, Japan, and India. A
few earthquake also took place in Central Asia like Iran.

The top countries will be:

1.  China
2.  Indonesia
3.  Iran
4.  Japan
5.  India

## Dig into the context behind the casulty

Let’s dig into the number behind the casualty. First, I notice that for
each record, there are deaths and total deaths; injuries and total
injuries. These pair-wise variables also lead to the creation of
casualty and total casualty. Not sure how to distinguish these similar
variables.

After simple comparison, most of the data are in alignment with death -
total death, injuries - total injuries, and casualty - total casualty.
Only these 1 record is in difference.

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
| Haiti   |    318268 |       318268 |       43395 |         313395 |       361663 |          631663 |

To define the casualty, I tend to be lenient toward the numbers. Hence,
I decide to use the total casualty as an indicator since I believe it
includes the direct and indirect casualties from the disaster.

##### My original hypothesis is: The bigger the magnitude, the more casualty the disaster caused.

``` r
country_cleaned_df %>% group_by(date, country, Mag) %>% summarize(sum_t_casualty = sum(Total_Casualty)) %>% arrange(-sum_t_casualty) %>% head(5) %>% knitr::kable()
```

| date       | country       | Mag | sum_t\_casualty |
|:-----------|:--------------|----:|----------------:|
| 2010-01-12 | Haiti         | 7.0 |          616000 |
| 2008-05-12 | China         | 7.9 |          461823 |
| 2023-02-06 | Turkey; Syria | 7.8 |          254524 |
| 2005-10-08 | Pakistan      | 7.6 |          222812 |
| 2001-01-26 | India         | 7.6 |          186841 |

However, from the above table, we can see that Japan has the strongest
magnitude earthquake between 2000 to 2023. The casualties are not the
worst. We may infer from the result that magnitude is not the only
factor that caused casualties but infrastructure and familiarity with
massive emergency first aid.

Let’s use a graph to present the hypothesis with the data.

``` r
country_cleaned_df %>% group_by(date, country, Mag) %>% summarize(sum_t_casualty = sum(Total_Casualty)) %>% ggplot(aes(x = Mag, y = sum_t_casualty)) + geom_point() + geom_smooth()+ scale_y_continuous(labels = scales::unit_format(unit = "M", scale = 1e-6)) + labs(title = "Sum of Casualties and Magnitude of Earthquake") +
  xlab("Magnitude") + ylab("Sum of Casualty (Million)")
```

    ## Warning: Removed 883 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 883 rows containing missing values (`geom_point()`).

![](Earthquake_2000-2023_DataCleaning_files/figure-gfm/Casualy%20Viz-1.png)<!-- -->

We can see the scatter plot and the smooth line are almost horizontally
related, which meant they are barely relevant. But, before drawing a
conclusion, let’s test it with a correlation test.

``` r
casualty_df = country_cleaned_df %>% group_by(date, country, Mag) %>% summarize(sum_t_casualty = sum(Total_Casualty)) 
shapiro.test(casualty_df$Mag)
```

    ## 
    ##  Shapiro-Wilk normality test
    ## 
    ## data:  casualty_df$Mag
    ## W = 0.99322, p-value = 1.209e-05

``` r
shapiro.test(casualty_df$sum_t_casualty)
```

    ## 
    ##  Shapiro-Wilk normality test
    ## 
    ## data:  casualty_df$sum_t_casualty
    ## W = 0.099968, p-value < 2.2e-16

From the output, the two p-values are greater than the significance
level 0.05 implying that the distribution of the data are not
significantly different from normal distribution. In other words, we can
assume the normality.

``` r
res = cor.test(casualty_df$Mag, casualty_df$sum_t_casualty, 
                    method = "pearson")
res
```

    ## 
    ##  Pearson's product-moment correlation
    ## 
    ## data:  casualty_df$Mag and casualty_df$sum_t_casualty
    ## t = 3.1411, df = 411, p-value = 0.001805
    ## alternative hypothesis: true correlation is not equal to 0
    ## 95 percent confidence interval:
    ##  0.0574686 0.2459744
    ## sample estimates:
    ##       cor 
    ## 0.1531141

The correlation coefficient: 0.1531141 is close to 0 meaning that there
is a lower degree of a positive association between the two variables
(Magnitude and Sum of Casualites).

## Deep Dive into the Damage and the House Destroyed of each quake

Again, I notice that for each record, there are Damage in Million and
Total Damage in Million; House Damaged and Total House Damaged.Not sure
how to distinguish these similar variables.

After simple comparison, most of the data are in alignment with death -
total death, injuries - total injuries, and casualty - total casualty.
Only these 3 records are in difference.

``` r
country_cleaned_df %>% 
  group_by(country) %>% 
  summarize(sum_damage_mil = sum(Damage_Mil),
            sum_t_damage_mil = sum(Total_Damage_Mil),
            sum_house_damaged = sum(House_Damaged),
            sum_t_house_damaged = sum(Total_House_Damaged)) %>% 
  filter(sum_damage_mil != sum_t_damage_mil | sum_house_damaged != sum_t_house_damaged) %>% knitr::kable()
```

| country                 | sum_damage_mil | sum_t\_damage_mil | sum_house_damaged | sum_t\_house_damaged |
|:------------------------|---------------:|------------------:|------------------:|---------------------:|
| Bulgaria                |             NA |                NA |               100 |                  240 |
| Greece, Turkey          |            450 |              4500 |                NA |                   NA |
| Tajikistan; Afghanistan |             NA |                NA |               160 |                  250 |

To define the Damage, I again incline to be lenient toward the numbers.
Hence, I decide to use the Total Damage in Million and Total House
Damaged as 2 indicators since I believe they include the direct and
indirect damages from the disaster.

##### My original hypothesis is The bigger the magnitude, the more damage the disaster caused and the more houses damaged by the disaster.

``` r
country_cleaned_df %>% group_by(date, country, Mag) %>% summarize(sum_damage = sum(Total_Damage_Mil), 
          sum_house_damage = sum(Total_House_Damaged)) %>% arrange(desc(sum_damage), desc(sum_house_damage)) %>% head(5) %>% knitr::kable()
```

| date       | country | Mag | sum_damage | sum_house_damage |
|:-----------|:--------|----:|-----------:|-----------------:|
| 2011-03-11 | Japan   | 9.1 |   220136.6 |           280920 |
| 2008-05-12 | China   | 7.9 |    86000.0 |         21000000 |
| 2010-02-27 | Chile   | 8.8 |    30000.0 |           500000 |
| 2004-10-23 | Japan   | 6.6 |    28000.0 |               NA |
| 2016-04-15 | Japan   | 7.0 |    20000.0 |               NA |

From the above table, we can see that Japan has the strongest magnitude
earthquake between 2000 to 2023. The damage cost the most but the amount
of the house damaged is not the most. **The damage and house damaged
should be treated separately in this case.**

Since house damage may include other factors such as the building
materials.

##### Let’s visualize the relationship between Total Damage in Million and Magnitude.

``` r
country_cleaned_df %>% group_by(date, country, Mag) %>% summarize(sum_damage = sum(Total_Damage_Mil)) %>% ggplot(aes(x = Mag, y = sum_damage)) + geom_point() + geom_smooth()+ geom_vline(xintercept = 8.1, colour="red", linetype = "longdash") + labs(title = "The Relationship between sum of Damage in Million and Magnitude of Earthquake") +
  xlab("Magnitude") + ylab("Sum of Damage (Million)")
```

    ## Warning: Removed 1117 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 1117 rows containing missing values (`geom_point()`).

![](Earthquake_2000-2023_DataCleaning_files/figure-gfm/Damage%20in%20Mil%20and%20Magnitude%20Viz-1.png)<!-- -->

Interestingly, we found an elbow point in the trend. When the Magnitude
of the quake is greater than or equal to 8.1 the potential damage could
range from 1 to 200,000 Million.

``` r
damage_df = country_cleaned_df %>% group_by(date, country, Mag) %>% summarize(sum_damage = sum(Total_Damage_Mil)) 
shapiro.test(damage_df$Mag)
```

    ## 
    ##  Shapiro-Wilk normality test
    ## 
    ## data:  damage_df$Mag
    ## W = 0.99322, p-value = 1.209e-05

``` r
shapiro.test(damage_df$sum_damage)
```

    ## 
    ##  Shapiro-Wilk normality test
    ## 
    ## data:  damage_df$sum_damage
    ## W = 0.15864, p-value < 2.2e-16

From the output, the two p-values are greater than the significance
level 0.05 implying that the distribution of the data are not
significantly different from normal distribution. In other words, we can
assume the normality.

``` r
res1 = cor.test(damage_df$Mag, damage_df$sum_damage, 
                    method = "pearson")
res1
```

    ## 
    ##  Pearson's product-moment correlation
    ## 
    ## data:  damage_df$Mag and damage_df$sum_damage
    ## t = 3.8501, df = 177, p-value = 0.0001648
    ## alternative hypothesis: true correlation is not equal to 0
    ## 95 percent confidence interval:
    ##  0.1368920 0.4080192
    ## sample estimates:
    ##       cor 
    ## 0.2779829

The correlation coefficient: 0.2779829 is close to 0 meaning that there
is a lower degree of a positive association between the two variables
(Magnitude and Damage in Million).

##### What about House Damaged and the Magnitude ?

``` r
country_cleaned_df %>% group_by(date, country, Mag) %>% summarize(sum_house_damage = sum(Total_House_Damaged)) %>% ggplot(aes(x = Mag, y = sum_house_damage)) + geom_point() +  geom_smooth()+ scale_y_continuous(labels = scales::unit_format(unit = "M", scale = 1e-6)) +
  labs(title = "The Relationship between sum of House damaged and Magnitude of Earthquake") +
  xlab("Magnitude") + ylab("Sum of House damaged(Million)") 
```

    ## Warning: Removed 924 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 924 rows containing missing values (`geom_point()`).

![](Earthquake_2000-2023_DataCleaning_files/figure-gfm/House%20Damaged%20and%20Magnitude%20Viz-1.png)<!-- -->

From the output, we can tell that the numbers of house damaged has
little relation with the magnitude. However, you can see one data point
that is particularly high.

Let’s apply filter and find out this data point.

``` r
highlight_df = country_cleaned_df %>% 
  group_by(date, country, Mag) %>% 
  summarize(sum_house_damage = sum(Total_House_Damaged)) %>% 
  filter(sum_house_damage >= 20000000)
```

Here you go. The potential outlier is from China Sichuan Earthquake with
magnitude at 7.9 and the focal depth was merely 10 km.

``` r
country_cleaned_df %>% group_by(date, country, Mag) %>% summarize(sum_house_damage = sum(Total_House_Damaged)) %>% ggplot(aes(x = Mag, y = sum_house_damage)) + geom_point(alpha = 0.5)  + geom_point(data=highlight_df, 
             aes(x=Mag,y=sum_house_damage), 
             color='red',
             size=3) + geom_smooth()+ scale_y_continuous(labels = scales::unit_format(unit = "M", scale = 1e-6)) +
  labs(title = "The Relationship between sum of House damaged and Magnitude of Earthquake") +
  xlab("Magnitude") + ylab("Sum of House damaged(Million)")
```

    ## Warning: Removed 924 rows containing non-finite values (`stat_smooth()`).

    ## Warning: Removed 924 rows containing missing values (`geom_point()`).

![](Earthquake_2000-2023_DataCleaning_files/figure-gfm/highlighted%20specific%20data%20point-1.png)<!-- -->

``` r
house_damage_df = country_cleaned_df %>% group_by(date, country, Mag) %>% summarize(sum_house_damage = sum(Total_House_Damaged)) 
shapiro.test(house_damage_df$Mag)
```

    ## 
    ##  Shapiro-Wilk normality test
    ## 
    ## data:  house_damage_df$Mag
    ## W = 0.99322, p-value = 1.209e-05

``` r
shapiro.test(house_damage_df$sum_house_damage)
```

    ## 
    ##  Shapiro-Wilk normality test
    ## 
    ## data:  house_damage_df$sum_house_damage
    ## W = 0.033415, p-value < 2.2e-16

From the output, the two p-values are greater than the significance
level 0.05 implying that the distribution of the data are not
significantly different from normal distribution. In other words, we can
assume the normality.

``` r
res2 = cor.test(house_damage_df$Mag,house_damage_df$sum_house_damage, 
                    method = "pearson")
res2
```

    ## 
    ##  Pearson's product-moment correlation
    ## 
    ## data:  house_damage_df$Mag and house_damage_df$sum_house_damage
    ## t = 2.3062, df = 370, p-value = 0.02165
    ## alternative hypothesis: true correlation is not equal to 0
    ## 95 percent confidence interval:
    ##  0.0175740 0.2180798
    ## sample estimates:
    ##       cor 
    ## 0.1190403

The correlation coefficient: 0.1190403is close to 0 meaning that there
is a lower degree of a positive association between the two variables
(Magnitude and Sum of House Damage).

For establishing an interactive data visualization, I will save the
`country_cleaned_df` for Tableau presentation.

``` r
write.csv(country_cleaned_df, "earthquake_by_country_cleaned.csv", row.names=FALSE)
```

## Findings and Conclusion

First of all, I found that most of the earthquakes took place more
frequently in East or Southeast Asia. It has a lot to do with the plate
activity underneath East and Southeast Asia.

Please refer to below for the plates under East and
Southeast![](Earthquake_2000-2023_DataCleaning_files%5Casiaplates.png)

Map of Tectonic Plates in East and Southeast Asia (United States
Geological Survey, Public Domain)

Second, I also found that magnitude is not the fatal factor that led to
massive casualties. Sometimes, the infrastructure, the building
material, the stability of the building, and the emergency first aid can
greatly impact the number of casualties. Besides, Earthquakes can
trigger landslides, tsunamis, fires, and floods. These secondary
disasters can also exacerbate the number of casualties.

Reference:

- 2004 Indian Ocean earthquake and tsunami:
  <https://en.wikipedia.org/wiki/2004_Indian_Ocean_earthquake_and_tsunami>
- 2011 Tōhoku earthquake and
  tsunami:<https://en.wikipedia.org/wiki/2011_T%C5%8Dhoku_earthquake_and_tsunami>

The time that the earthquake took place also played a key role here.
Take Turkey’s 2023 Earthquake for example, the earthquake stroke at
midnight while people are in deep sleep, making people unaware of the
coming of an earthquake and thus missing the time to stay safe, and find
protection.

The other key factor that resulted in massive casualties is architecture
quality. Previously, I have mentioned that the building material, the
stability of the building, and the structure of the architecture have
also dramatically impacted the massive casualties. Take the tragedy that
happened in Turkey in 2023 as an example again. Experts have pointed out
that the “pancake collapse” of the building made the rescue even more
impossible.

A similar situation also occurred in Haiti.

Reference:

- NPR’s article :
  <https://www.npr.org/2010/01/14/122547242/haitis-buildings-werent-fit-to-withstand-quakes>

The finding indirectly answered our next question, the magnitude is not
the only factor that caused house damage. The massive house damage could
be the result of unstable architecture structures, under-qualified
building materials, etc.

Finally, the cost of the damage is not significant if the magnitude is
less than 8. Only a quake whose Magnitude is equal to or greater than 8,
would the cost of damage positively correlate to the magnitude. However,
this is not conclusive. As previously mentioned, other secondary
disasters such as landslides, tsunamis, and floods can also bring up a
longer-term impact compared to earthquakes themselves.

Last but not the least, hopefully, this data analysis can call out some
actions for those governments whose countries are situated somewhere
adjacent to the continental plates to examine the architectural
structure, intensify the emergency first aid, sponsor the rescue teams,
reinforce the earthquake drill and plan, and prepare for supply.

![](Earthquake_2000-2023_DataCleaning_files%5Ctectonics.jpg) Reference
from NOAA: <https://oceanservice.noaa.gov/facts/tectonics.html>
