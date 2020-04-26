# Introduction/Business Problem

There is a common sense (often depicted in movies) that crime rates are somehow linked to the culture background of the people: the higher the education and culture level, the less crimes would be committed. Some 
[studies](https://criminal-justice.iresearchnet.com/crime/education-and-crime/4/)
show that there indeed is some correlation. At least it should hold true for more basic crimes like theft and murder. The culture level of a given area could be theoretically linked to the quantitiy of active culture objects, like museums, art galleries, and similar. On the contrary, less educated people often spend more time practicing sports or watching them, and therefore there is often a 
[positive correlation of sports with crime rate](https://mpra.ub.uni-muenchen.de/78596/1/MPRA_paper_78596.pdf).
The aim of this project would be to check if there is any significant correlation between crime rates and the amount of culture and sports objects. The resultant model could be useful for adjusting police patroling intervals for particular areas. It is tempting to say that based on such model, a recommendation for building of particular culture places could be put forward, but more studies would be required to establish the causative links.

# Data

As the source of the data, I plan to use the 
[Foursquare](https://foursquare.com/) 
service for the description of culture and sports objects. The field 
['category'](https://developer.foursquare.com/docs/build-with-foursquare/categories) 
there contains various classifications like Church, Museum, Stadum, etc. For example:

Arts & Entertainment

    Amphitheater

    Aquarium

    Arcade

    Art Gallery

    Bowling Alley


Crime rates would be obtained from  the 
[Opendata DC for 2019](https://opendata.dc.gov/datasets/crime-incidents-in-2019) 
service. As the model, I would use the data on Washington DC. The quantity of various culture objects and crime rates would be grouped by the neighborhoods, and then linear regression would be used to estimate, whether the amount of variuos objects significantly influences crime rates. If possible, various crime severity grades would be extracted from the 
[aduld sentencing manual](https://sentencing.umn.edu/sites/sentencing.umn.edu/files/adult_sentencing_manual_2001.pdf) 
to group the crimes by severity levels.


# Methodology

_represents the main component of the report where you discuss and describe any exploratory data analysis that you did, any inferential statistical testing that you performed, if any, and what machine learnings were used and why_




# Results

discuss the results.


# Discussion

In this project, the correlation of crime rates and crime severity with the amount of culture, sports, and religious venues was estimated. For the sake of simplicity, the estimation was performed only for one year (2019) and one city (Washington DC). For this setting, a statistically significant positive correlation was found; for the sports venues, it was positive (that is, the more sports venues, the more severe crimes were registered in a given neighborhood), and for the culture venues, it was negative (the more culture venues, the less severe crimes were registered). 


# Conclusion

Based on Washington DC 2019 data, crime severity rates display a positive correlation with sports venues count and negative correlation with culture venues counts. No statistically significant  correlation was found between crime severity rates and religious venues counts.
