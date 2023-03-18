# Earthquake_2000-2023_DataCleaning

## Motivation

As an East Asian, I can never forget living in an area which unfortunately on the seismic belt. I cannot resonate more when seeing the tragedies in Turkey and Syria right now. 3.11 Japan Touhoku Earthquake just had its 12th anniversary and now the same natural disaster stroke Turkey. It's a great opportunity to review the history that we human beings deal with earthquakes.

#### My data analysis will dedicate to answering the following questions:

-   Where are earthquakes frequently take place between 2000 and 2023?
-   What are the top 5 strong magnitudes and their Focal Depth (km)of the earthquake between 2000 and 2023?
-   By comparing the casualties and magnitude of each earthquake to discuss if any other external factors can mitigate the impact.
-   By comparing the Damage and House Destroyed with the magnitude of each quake to discuss if these damages can be prevented by intervening beforehand.

Hope those who are unfortunately deceased can rest in peace and the data can bring some insights into future prevention and preparation.

## Import Data

This dataset came from Kaggle Dataset: *Earthquakes -2150 BC -- 2023 AD around the world* Link: <https://www.kaggle.com/datasets/bharathposa/earthquakes-from-2150bc-2023-ad-around-the-world>.

I will use `readr` package (which is a package of `tidyverse`) to read the csv file. And then use glimpse function to take a quick review of this data.


## Findings and Conclusion

First of all, I found that most of the earthquakes took place more frequently in East or Southeast Asia. It has a lot to do with the plate activity underneath East and Southeast Asia.

Second, I also found that magnitude is not the fatal factor that led to massive casualties. Sometimes, the infrastructure, the building material, the stability of the building, and the emergency first aid can greatly impact the number of casualties. Besides, Earthquakes can trigger landslides, tsunamis, fires, and floods. These secondary disasters can also exacerbate the number of casualties.

Reference:

-   2004 Indian Ocean earthquake and tsunami: <https://en.wikipedia.org/wiki/2004_Indian_Ocean_earthquake_and_tsunami>
-   2011 Tōhoku earthquake and tsunami:<https://en.wikipedia.org/wiki/2011_T%C5%8Dhoku_earthquake_and_tsunami>

The time that the earthquake took place also played a key role here. Take Turkey's 2023 Earthquake for example, the earthquake stroke at midnight while people are in deep sleep, making people unaware of the coming of an earthquake and thus missing the time to stay safe, and find protection.

The other key factor that resulted in massive casualties is architecture quality. Previously, I have mentioned that the building material, the stability of the building, and the structure of the architecture have also dramatically impacted the massive casualties. Take the tragedy that happened in Turkey in 2023 as an example again. Experts have pointed out that the "pancake collapse" of the building made the rescue even more impossible.

A similar situation also occurred in Haiti.

Reference:

-   NPR's article : <https://www.npr.org/2010/01/14/122547242/haitis-buildings-werent-fit-to-withstand-quakes>

The finding indirectly answered our next question, the magnitude is not the only factor that caused house damage. The massive house damage could be the result of unstable architecture structures, under-qualified building materials, etc.

Finally, the cost of the damage is not significant if the magnitude is less than 8. Only a quake whose Magnitude is equal to or greater than 8, would the cost of damage positively correlate to the magnitude. However, this is not conclusive. As previously mentioned, other secondary disasters such as landslides, tsunamis, and floods can also bring up a longer-term impact compared to earthquakes themselves.

Last but not the least, hopefully, this data analysis can call out some actions for those governments whose countries are situated somewhere adjacent to the continental plates to examine the architectural structure, intensify the emergency first aid, sponsor the rescue teams, reinforce the earthquake drill and plan, and prepare for supply.
