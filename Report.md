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
'THEFT/OTHER', 'THEFT F/AUTO', 'BURGLARY', 'MOTOR VEHICLE THEFT', 'ASSAULT W/DANGEROUS WEAPON', 'ROBBERY', 'HOMICIDE', 'SEX ABUSE',      and 'ARSON'. 
The crime counts for different crime types were (not) evenly distributed (Figure 1).


Figure 1. The counts of individual crime types in Washington DC.

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

To this end, we have a list of Washington DC Neighborhoods, and now we want to obtain a count of culture, sports, and spiritual objects first for each neighborhood, and then for the neighborhood clusters. For this task, we will use the Foursquare api.


The function below will obtain a list of all venues near approximately belonging to the neighborhood. In addition, it will store the venue category id to filter the required venue types later.

Now we will obtain the list of all available categories from the Fourquare. This is required, as the categories could be nested, and we also need the subcategories for the filter to work correctly. Now we define a function to recursively scan the obtained categories and save only those belonging to the supplied category id list or being the appropriate subcategories

Now we define the base categories list and perform the filtering

Now we convert the category ids of the obtained venues and remove those venues, which do not have an assigned category from our lists

Now we calculate the venue count for each venue type (church, sports and culture) for each neighborhood

The next step is to merge the crimes dataset and the venue count dataset. For some neighborhoods, there are no venues of the required category, so we'll replace the missing data with zeros. Now we sum up the neighborhoods venues counts and crime scores within each cluster. 

## Statistical analysis of correlations

Finally, we apply the ordinary least squares model (OLM) to the constructed data to estimate the influence of each venue type on the crimes severity, and test whether such influence is statistically significant

In addition, let's check, whether the frequency of particular crime types correlate with particular venue counts form our lists




# Results

## The connecion between venue counts and crime severity

Table 1.
|Parameter          |Value          |Parameter          |Value  |
|-------------------|---------------|-------------------|-------|
|Dep. Variable:     |	score       |	R-squared:	    | 0.301 |
|Model:	            |OLS	Adj.    | R-squared:        |0.243  |  
|Method:	        |Least Squares	|F-statistic:	    |5.163  |
|No. Observations:	|40	            |Prob (F-statistic):|0.00452|
|AIC:	            |941.4	        |Log-Likelihood:	|-466.71|	
|Df Residuals:	    |36	            |BIC:	            |948.2  |
|Df Model:	        |3              |                   |       |		
|Covariance Type:	|nonrobust      |                   |	    |	

Table 2.

|       |	coef	|std err	|t	    |P>abs(t)	|[0.025	    |0.975]  |
|-------|-----------|-----------|-------|-----------|-----------|--------|
|const	|2.662e+04	|5749.002	|4.630	|0.000	    |1.5e+04	|3.83e+04|
|church	|641.5834	|460.168	|1.394	|0.172	    |-291.681	|1574.848|
|culture|-84.6749	|39.947	    |-2.120	|0.041	    |-165.691	|-3.659  |
|sport	|89.6720	|25.484	    |3.519	|0.001	    |37.987	    |141.357 |

First of all, we note that the model has quite low R-squared value, indicating that the count of the selected venues is not enough to explain the crime severities, or that the dependency is non-linear.

The t values for sports and culture object counts are below 0.05, so their influence is statistically significant. The t value for the churches count, on the other hand, is not, and thus churches' influence is not a statistically significant one.

The coefficient values indicate that the influence of sports venues on crime severity is positive, and culture objects count negative, as was expected. The sports objects have somewhat larger influence (the coefficient absolute value is 3.52 vs. 2.12 for the culture venues).

## Correlation of venue counts with crime types

![Figure 2](https://github.com/akimovmike/Coursera_Capstone/blob/master/corrpolot.png)

Figure 2.

From here, we note that the more culture- and sports-related venues we have, the more theft cases would be. This could be attributed to the mass people gathering places, in which theft could be easier to conduct. On the other hand, more severe crimes have a small negative correlation with such venues.


# Discussion

In this project, the correlation of crime rates and crime severity with the amount of culture, sports, and religious venues was estimated. For the sake of simplicity, the estimation was performed only for one year (2019) and one city (Washington DC). For this setting, a statistically significant positive correlation was found; for the sports venues, it was positive (that is, the more sports venues, the more severe crimes were registered in a given neighborhood), and for the culture venues, it was negative (the more culture venues, the less severe crimes were registered). 


# Conclusion

Based on Washington DC 2019 data, crime severity rates display a positive correlation with sports venues count and negative correlation with culture venues counts. No statistically significant  correlation was found between crime severity rates and religious venues counts.
