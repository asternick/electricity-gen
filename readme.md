# US States Electricity Generation: 
## Renewable Transition and Retail Rates
---
Author: Andrew Sternick

### Table of Contents 

* [Problem Statement](##Problem-Statement)
* [Executive Summary](##Executive-Summary)
* [Analysis](##Analysis)
* [Conclusions](##Conclusions)
* [Limitations and Next Steps](##Limitations-and-Next-Steps)

### Links
* [Images](./images/)
* [Presentation](./presentation.pdf)

### Code
* [EDA and Preprocessing](./code/eda-preprocessing.ipynb)
* [Modeling](./code/modeling.ipynb)

## Problem Statement

In a time of overt hostility to climate goals at the federal level, state regulation is critical to United States's capacity to confront the climate crisis. Electricity generation produces 28% of overall US greenhouse gas emissions, and the need to transition to zero-emissions generation is manifest. Fortunately, energy generation is regulated at the state rather than federal level. Numerous states have established mandates for zero-emissions energy generation, but there is resistance to such targets in some states, due in part to a perceived high cost of low emissions. In this project I will explore state electricity generation profiles over the period of 1990 - 2018, during which wind and solar power emerged from niche technologies to mainstream, cost-effective forms of electricity generation. I will use unsupervised clustering algorithms to gain insight into how geography, state policy, and the cost of fuels influence the states' journey toward a sustainable electricity grid, and how that journey has impacted retail electricity rates over time.

## Executive Summary

Renewable generation in the US consists of hydroelectric, geothermal, biomass, solar, and wind. The Energy Information Administration provides annual reporting of how electricity consumed at the state level is generated (including Washington, DC), as well as average electricity rates data. I use this data to visualize each state's electricity generation and retail rates profile over the entire 1990 - 2018 reporting period. K-Means and DCSCAN clustering algorithms are both considered as tools to gain insight into how state geography and policy have influenced generation profile. 

### Data Sources

Detailed [state generation profile data](https://www.eia.gov/electricity/data/state/) used in this project is from the Energy Information Administration. [Solar](https://www.nrel.gov/gis/solar.html) and [wind](https://www.nrel.gov/gis/wind.html) resource data is from the National Renewable Energy Laboratory.

### Data Cleaning and Preprocessing

EIA generation data is detailed with total MWh generated and numerous source categories. Rates data includes economic sectors as well as totals. For the purpose of this project, the data was condensed to relative generation by source, with each generation source expressed as a fraction of 100%. One generation source I remove is pumped hydro, as it is a storage technology rather than pure generation, and energy storage is not considered in our modeling. 

Electricity rates are considered in total by state, which is provided by EIA, whereas residential, industrial, commericial, and transportation rates are dropped. EIA rates data is not indexed for inflation. Therefore, I use Los Angeles Times Data and Graphics Department's [CPI](https://github.com/datadesk/cpi) python library to adjust rates data to 2005 dollars. 

### Visualizations

Three different visualizations are available via functions in the code notebook, and numerous examples are featured in the [images folders](./images/). All visualizations can be created either with renewables expressed as one category, or 5 individual generation sources (hydroelectric, geothermal, biomass, solar, wind): 

* [Bar plots](./images/Barplot/barplot-Idaho-2018.png) are useful for understanding one state's generation profile at a given point in time.
* [Stackplots](./images/Stackplots/stackplot-example-CA.png) express both the generation mix and its change over the entire 29 year period of analysis. The stack order is meaningful. At the bottom, non-dispatchable generation sources (which pair poorly with intermittent renewables such as solar and wind) are displayed in dark colors (black and purple). In the middle in white to gray shades are dispatchable fossil fuels, still polluting, but which pair well with intermittent renewables. Renewables themselves are color coded intuitively in a water/earth/plant/sun/sky palette, corresponding to hydro/geothermal/biomass/solar/wind. 
* [Stackrate](./images/Stackrate/Stackrate-IA.png) plots combine the features of the stackplot with an indicator of relative change in electricity rates over time. The straight horizontal line represents the average electricity retail rate for that state over the entire 29 year period. This is not a specific price but rather a representation of the average for that state. Keeping this a relative measure eliminates any confusion caused by inherent electricity price differences between states founded upon geography or past capitalized investments in infrastructure. The thicker, meandering line indicates the difference at any given time between the state's retail electricity price and the overall average for that state. 

### Clustering Algorithms

Both K-Means and DBSCAN are used to cluster the states by generation profile each year. This takes maximum advantage of the algorithms, allowing them to identify patterns over time. K-Means was instantiated for each year's data with 3 through 7 centroids, and the cluster count that produced the highest silhouette score was retained. Since each year was evaluated separately, cluster count changes, but nonetheless a pattern is [evident over time](./images/cluster-count-km.png). DBSCAN was also instantiated for each year, and 100 epsilons of 0.01 - 1.00 were evaluated. A minimum [cluster count](./images/cluster-count-dbscan.png) of 3 was required, excluding [outliers](./images/cluster-outliers-db.png). 

For the purposes of providing insight, K-Means outperformed DBSCAN. The property of forcing all data points into a cluster was useful for our data set, with only 51 data points per year. For example, K-Means identified states which used majority petroleum for generation as a cluster, even when that cluster included only one state (Hawaii) in the later years of our analysis. DBSCAN considered these states as outliers for all years. This "petroleum" cluster has explanatory power and is useful for analysis. DBSCAN clusters, no matter what the minimum required count, did not map to generation trends closely, while K-Means clustering did. All analysis and conclusions are based on the K-Means clusters. 

## Analysis

K-Means clustering identifies seven clusters almost every year from 1990 to 2006. In 1990, these constitute:

* [Coal States (14)](./images/KMeans/km-1990-0-coal.png)
* [Coal and Nuclear States (16)](./images/KMeans/km-1990-1-coal-nuke.png)
* [Renewable States (4)](./images/KMeans/km-1990-2-ren.png)
* [Mixed Generation States (4)](./images/KMeans/km-1990-3-mix.png)
* [Natual Gas States (6)](./images/KMeans/km-1990-4-gas-mix.png)
* [Petroleum States (2)](./images/KMeans/km-1990-5-petroleum.png)
* [Nuclear States (5)](./images/KMeans/km-1990-6-nuke.png)

By 2006, the clusters have changed somewhat:

* [Coal States (12)](./images/KMeans/km-2006-1-coal.png)
* [Coal and Nuclear States (15)](./images/KMeans/km-2006-0-coal-nuke.png)
* [Renewable States (4)](./images/KMeans/km-2006-2-ren.png)
* [Mixed Generation States (2)](./images/KMeans/km-2006-5-coal-ren.png)
* [Natual Gas States (11)](./images/KMeans/km-2006-3-gas-mix.png)
* [Petroleum States (2)](./images/KMeans/km-2006-4-petroleum.png)
* [Nuclear States (5)](./images/KMeans/km-2006-6-nuke.png)

Membership in clusters changes somewhat over the course of 1990 - 2006, but the overal breakdown is remarkably stable. Coal and nuclear remain dominant, and renewables are restricted to states with favorable geography in damnable rivers, mostly concentrated in the Pacific Northwest. 

Between 2006 an 2018, the cluster count declines to 5 and the content of the clusters changes. This expresses very closely the actual trends in electricity generation of this time period: the decline of coal, the rise of natural gas, the diversification of generation within states, the transition away from petroleum, and the rise of wind and solar. By 2018, K-Means identifies only 5 clusters:

* [Natural Gas States (11)](./images/KMeans/km-2018-0-gas.png)
* [Coal States (17)](./images/KMeans/km-2018-1-coal.png)
* [Renewable States (7)](./images/KMeans/km-2018-2-ren.png)
* [Mixed Generation States (15)](./images/KMeans/km-2018-3-mix.png)
* [Petroleum State (1)](./images/KMeans/km-2018-4-petroleum.png)

Of the 11 natural gas dominant states, 4 have have renewable generation over 20%. Of the 17 coal-dominant states, 7 have renewable generation in excess of 20%, with some near 50%. Washington DC, South Dakota, and Vermont have transitioned to join the original 4 renewable states, and all renewable states are at or over 70% renewable. Of the states with mixed generation, natural gas, coal, nuclear, and renewables are all well represented. Only one petroleum state remains, Hawaii.

The association between renewables and electricity rates is unclear. Some states which saw a big increase in renewable generation also saw rate increases, such as [Kansas](./images/Stackrate/Stackrate-KS.png) and [South Dakota](./images/Stackrate/Stackrate-SD.png). Others saw a decrease in rates, notably [Texas](./images/Stackrate/Stackrate-TX.png). Others saw no signififcant change even in the face of a rapid and far-reaching transformation in generation profile, such as [Iowa](./images/Stackrate/Stackrate-IA.png) and [Vermont](./images/Stackrate/Stackrate-VT.png).

## Conclusions

The electrical grid has been called the most complicated machine ever built. To model it is far beyond the scope of this project. However the insights gained from the data presented are meaningful. Renewable generation deployment is driven partly by market forces and partly by policy, and there is an interaction between these forces. 

Consider solar energy and its growth over the past decade. There are [nine states](./images/NREL/solar_ghi_2018_usa_scale_01.jpg) with exceptional solar potential in the US, seven in the southwest, plus Florida and Hawaii. Yet only [California](./images/Stackplots/stackplots-solar-potential.png) can be said to have "taken off" where solar generation is considered. This is entirely due to California's early regulatory support for solar energy. Other states have since put the requisite structures in place, such as Hawaii, and are poised for such a takeoff. In Texas the overall environment for renewables is excellent, but several more doublings of solar generation are necessary before the geometric growth seen in California is visible relative to other generation sources. Other states, such as Arizona and Nevada, have suffered from an inconsistent regulatory environment, where "poison pill" bills have been put into effect at the behest of fossil fuel funded lobbying groups, derailing solar's momentum. In Florida, yet another dynamic is at work, where rooftop solar is discouraged, but vast, centralized, inexpensive generation is poised to explode over the coming decade, with strong regulatory support. 
 
As an industry scales, costs decline, and renewables are no exception. The decline in cost of renewables has in fact been widely understood to be breathtaking over the past decade, such that they will soon out-complete all other forms of electricity generation in numeorus markets. This of course also depends on market structures and geography. Hawaii, which imports petroleum for most of its generation by boat, suffers from predictably high electricity rates, and renewables will almost certainly provide  cleaner air, zero emissions, and lower consumer costs. However there are states which consume locally-mined coal in generation facilities that are fully depreciated, where rates are low and renewables will require favorable regulatory environments to gain traction. Elsewhere, natural gas infrastructure investments have been made recently, and even continue to be made, which many industry analysts regard as likely to become stranded assets in the low-cost renewable world of the near future. 

## Limitations and Next Steps

The model represents a simplification of the electricity generation landscape. There are many features that could be added to the model which would allow deeper insights to be delivered by the clustering algorithms:

* Electricity is traded between states; some states import some or even all their generation (DC, for example) whereas many produce virtually all the electricity they consume. This may impact retail electricity rates. 
* Energy storage, presently overwhelmingly pumped hydro, supports the integration of intermittent renewables and can have a favorable impact on retail rates. Short-duration lithium-ion storage, while still small, is scaling at a pace that rivals wind and solar growth over the past decade, with aggressive policy support in many states, and is a recognized driver of solar integration in particular. 
* The presence, or non-presence, of a state renewable portfolio standard (RPS) is widely recognized as a primary driver of renewable investment. 

This project is focused on clustering, but a predictive model for electricity rates may be possible. However, machine learning algorithms typically require a significant quantity of training data to build useful models, and we have only 29 rows for each state. An ensemble model which aggregates models of all the states' profiles may be practical, and is a logical next step in the analysis.
