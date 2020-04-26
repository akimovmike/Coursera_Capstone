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

## Data handling

The computations were performed using the [IBM Watson ML Studio](https://www.ibm.com/cloud/watson-studio) cloud service. The data were analyzed using Python 3.6. The pandas package was used to manipulate the table data, the geocoder package was used to obtain the geographical coordinates, the statsmodels package was used for linear regression, and the seaborn package was used for visualization.

## Crime cases data preparation

The crime rates data was obtained from the [opendata.dc.gov](https://opendata.dc.gov/datasets/crime-incidents-in-2019) website. The dataset contained 1641 crime records for 9 categories: 
'THEFT/OTHER', 'THEFT F/AUTO', 'BURGLARY', 'MOTOR VEHICLE THEFT', 'ASSAULT W/DANGEROUS WEAPON', 'ROBBERY', 'HOMICIDE', 'SEX ABUSE',      and 'ARSON'. No missing data were found for the Neighborhood cluster and Crime type featues, which were required for the analysis.

## Crime severity scores

In the dataset used in this project, there were much less crime types than there is present in law standards present in the Internet (e.g., 
[Manhattan crimes classification paper](http://cda.psych.uiuc.edu/statistical_learning_course/Allcode_manuals/mac_crime_class.pdf), 
[Aduld sentencing manual](https://sentencing.umn.edu/sites/sentencing.umn.edu/files/adult_sentencing_manual_2001.pdf)). Therefore, the available crime types were manually matched with those from the 
[Crime severity scores statistics](https://www.ons.gov.uk/peoplepopulationandcommunity/crimeandjustice/datasets/crimeseverityscoreexperimentalstatistics).
This source was chosen based on that it a crimes classification list, which was quite similar to other sources, and a numerical crime severity score, and not a judgement advice (compared to a typical sentencing manual). For the most cases, a direct category match was found between the Washington DC dataset and the Crime severity scores data. The only exception was the 'SEX ABUSE' category, for which the most close one in the Crime severity scores data was 'rape'. Based on this correspondence, the Washington crime types were mathced to the crime severity scores.

## Mapping Nighborhood clusters to Neighborhood names and coordinates

The crimes were labeled with Neighborhood clusters (46 in total), and not individual neighborhoods. The project goal was to match  crime severity with various venue counts, and these counts should have been obtained from the Foursquare database. The problem was that for this database, each venue was marked only with its coordinate and address, and not with a neighborhood cluster. A coordinate for the center could be obtained both for a neighborhood and a neighborhood cluster. However, it is logical to assume that a neighborhood would give a much better venue attribution precision due to its smaller size. Therefore, a mapping of individual neighborhoods to neighborhood clusters was performed using another dataset from the [opendata.dc.govm](https://opendata.dc.gov/datasets/neighborhood-clusters) web site. 

For each neighborhood, geographical coordinates were obtained using the geocoder package. As far as the Google Maps API requires a subscription, a free ArcGIS API was used.

## Obtainig venue counts from the Foursquare database

First of all, two lists were obtained from the Foursquare database: a list of all venues for each neighborhood (defined as a circle with the radius 500 around the neighborhood center) and a nested list of venue categories. The key categories were manually selected:
- Culture venues:
    - Art Gallery
    - Concert Hall
    - Museum
- Sports venues:
    - College Stadium
    - Gym / Fitness Center
    - College Gym
- Religious venues:
    - Spiritual Center
    
After that, a function was constructed to recursively store the sub-categories of each of the chosen categories based on the whole category list. In this way, three category id lists were constructed for each of the target venue types.

The next step was to filter the total venue list for each neighborhood to include only those from the target category lists and to calculate the appropriate venue count. For some neighborhoods, there were no venues of the particular categories, so the reseltant NaN values were replaced with zeros. 

The final step was to sum up the neighborhoods venues counts and crime scores within each neighborhood cluster. The crime score was estimated using the following equation:

_score = sum(crime_type_severity_score*crime_type_cases) for all crime types_

## Statistical analysis of correlations

Finally, an ordinary least squares model (OLM) was applied to the constructed data to estimate the influence of each venue type on the crimes severity, and test whether such influence is statistically significant. The model equation was as follows:

_crime_score = W1*culture_venues_count + W2*sports_venues_count + W3*spiritual_venues_count + C_

In addition, the Pearson's r correlation coefficient between the frequencies of particular crime types and the particular venue counts was calculated.


# Results

## The connecion between venue counts and crime severity

The results of the linear regression model fitting to the data are presented in tables 1 and 2 below. 

Table 1. The parameters and fit quality of the model
|Parameter          |Value          |Parameter          |Value  |
|-------------------|---------------|-------------------|-------|
|Dep. Variable:     |	score       |	R-squared:	    | 0.301 |
|Model:	            |OLS	        |Adj.  R-squared:   |0.243  |  
|Method:	        |Least Squares	|F-statistic:	    |5.163  |
|No. Observations:	|40	            |Prob (F-statistic):|0.00452|
|AIC:	            |941.4	        |Log-Likelihood:	|-466.71|	
|Df Residuals:	    |36	            |BIC:	            |948.2  |
|Df Model:	        |3              |                   |       |		
|Covariance Type:	|nonrobust      |                   |	    |	

Table 2. The statistical significance of the model coefficients

|       |	coef	|std err	|_t_    |P>\|_t_\|	|[0.025	    |0.975]  |
|-------|-----------|-----------|-------|-----------|-----------|--------|
|const	|2.662e+04	|5749.002	|4.630	|0.000	    |1.5e+04	|3.83e+04|
|church	|641.5834	|460.168	|1.394	|0.172	    |-291.681	|1574.848|
|culture|-84.6749	|39.947	    |-2.120	|0.041	    |-165.691	|-3.659  |
|sport	|89.6720	|25.484	    |3.519	|0.001	    |37.987	    |141.357 |

First of all, it should be noted that the model has quite low R-squared value, indicating that the count of the selected venues is not enough to explain the crime severities, or that the dependency is non-linear.

The _t_ values for sports and culture venues counts are below 0.05, so their influence is statistically significant. The _t_ value for the spiritauls venues ('church') count, on the other hand, is not, and thus spiritual venues' influence is not a statistically significant one.

The coefficient values indicate that the influence of sports venues counts on crime severity is positive, and culture venues count negative, as was expected. The sports venues have somewhat larger influence (the coefficient absolute value is 3.52 vs. 2.12 for the culture venues).

## Correlation of venue counts with crime types

The Pearson's r correlation coefficients between venue counts and cases counts for various crime types are depicted on Figure 1.

![Figure 1](https://github.com/akimovmike/Coursera_Capstone/blob/master/corrpolot.png)

Figure 1. Correlation of venue counts with crime types

The more culture- and sports-related venues a neighborhood cluster has, the more theft cases would be. On the other hand, more severe crimes have a small negative correlation with such venues.


# Discussion

The hypothesis of this project was that more educated more spiritual people would commence less crimes; therefore, education and spirituality level is linked to the culture, religious, and sports (inversely) venues counts. To test this hypothesis, the correlation of crime rates and crime severity with the amount of culture, sports, and religious venues was estimated for the Washington DC data of 2019.  In this setting, a statistically significant correlation was found for sports and culture venues, but not for the religious ones.

Using a linear regression model, the influence of the counts of sports, religious, and culture venues on the crimes severity rates was estimated. Each neighborhood cluster represented a separate data point. In this setting, for the sports venues, the influence was positive (that is, the more sports venues, the more severe crimes were registered in a given neighborhood), and for the culture venues, it was negative (the more culture venues, the less severe crimes were registered). The influence of the religious venues counts was not statistically significant. These results are, of course, of limited significance. First, the dataset was constructed using a single city for a single year, and thus the discoverend links could be an outlier. Second, The data on population density, occupation, and average income of each neighborhood or neighborhood was not used, and they could in principle play a role in defining the crime types for each area and the relative frequency of the crimes.

Using the Pearson's correlation coefficient, a positive correlation between theft case counts and culture, religious, and sports objects was found. This effect could be attributed to that such venues are the mass people gathering places, in which theft could be easier to conduct. This result also suffers for the limitations of a single city within a single year. Its reliability, on the other hand, looks higher compared to the previous one, as such venues are places of people gahering independend of population density and economical status.

This project could be expanded using the data from at least more cities. The use of another years coud be complicated, as it requires also the data on new venue construction. In addition, more interesting results in the case of religious venues could be obtained using ethnicity or religious beliefs data, as moral standards coud differ from one religion to another.


# Conclusion

Based on Washington DC 2019 data, crime severity rates display a positive correlation with sports venues count and negative correlation with culture venues counts. No statistically significant  correlation was found between crime severity rates and religious venues counts. In the areas with many sports, culture, and religious venues theft rates are much higher than in other areas.
