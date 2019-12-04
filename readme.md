# US States Power Generation: 
Renewable Transition and Retail Rates
---
Author: Andrew Sternick

### Table of Contents 

* [Problem Statement](#Problem-Statement)
* [Executive Summary](#Executive-Summary)
* [Data Sources](#Data Sources) 
* [Data Cleaning and Preprocessing](#Data Cleaning and Preprocessing)
* [Clustering Algorithms](# Clustering Algorithms)

* [US Solar Resource Map](#US-Solar-Resource-Map)
* [Conclusion](#Conclusion)
* [Next Steps](#Next-Steps)

### Links
* [Images](./images/)
* [Presentation](./presentation.pdf)

## Problem Statement

In a time of overt hostility to climate goals at the federal level, state regulation is critical to confronting the climate crisis in the US. Electricity generation produces 28% of overall US greenhouse gas emissions and the need to transition to zero-emissions generation is manifest. Fortunately, energy generation is regulated at the state rather than federal level. Numerous states have established mandates for zero-emission energy generation, but there is resistance to such targets in many states, due in part to a perceived high cost of low emissions. In this project I will explore state electricity generation profiles over the period of 1990 - 2018, during which wind and solar power emerged from niche technologies to mainstream, cost-effective forms of electricity generation. I will use unsupervised clustering algorithms to gain insight into how geography, state policy, and the cost of fuels influence the states' journey toward a sustainable electricity grid, and how that journey has impacted retail electricity rates over time.

## Executive Summary

Renewable generation in the US consists of hydroelectric, wind, solar, biomass, and geothermal. The Energy Information Administration provides annual reporting of how electricity consumed at the state level (including Washington, DC) is generated, as well as average electricity rates data. We use this data to visualize each state's electricity generation and pricing profile over the entire 1990 - 2018 reporting period. Unsupervised clustering algorithms are used to gain insight into how state geography and policy have influenced generation profile. 

### Data Sources

Detailed [state generation profile data](https://www.eia.gov/electricity/data/state/) used in this project is from the Energy Information Administration. [Solar](https://www.nrel.gov/gis/solar.html) and [wind](https://www.nrel.gov/gis/wind.html) resource data is from the National Renewable Energy Laboratory.

### Data Cleaning and Preprocessing

EIA generation data is detailed with total MWh generated and numerous source categories. Rates data includes economic sectors as well as totals. For the purposes of this project, the data was condensed to relative generation by source, with each state's generation expressed as a fraction of 100%. One generation source we remove is pumped hydro, as it is a storage technology rather than pure generation, and energy storage is not considered in our modeling. 

Electricity rates are considered in total by state, which is provided by EIA, whereas residential, industrial, commericial, and transportation rates are dropped. EIA rates data is not indexed for inflation. We use Los Angeles Times Data and Graphics Department's [CPI](https://github.com/datadesk/cpi) python library to adjust rates data to 2005 dollars. 

### Clustering Algorithms

Both K-Means and DBSCAN are used to cluster the states by generation profile each year. This takes maximum advantage of the algorithms, allowing them to identify patterns over time. K-Means was instantiated for each year's data with 3 - 7 centroids, and the cluster count that produced the highest silhouette score was retained. Since each year was evaluated separately, cluster count changes, but nonetheless a pattern is [evident over time](./images/cluster-count-km.png). DBSCAN was also instantiated for each year, and 100 epsilons of 0.01 - 1.00 were evaluated. A minimum [cluster count](./images/cluster-count-dbscan.png) of 3 was required, excluding [outliers](./images/cluster-outliers-db.png). 

For the purposes of providing insight, K-Means outperformed DBSCAN. The property of forcing all data points into a cluster was useful for our data set, with only 51 data points per year. For example, K-Means identified states which used majority petroleum for generation as a cluster, even when that cluster included only one state (Hawaii) in the later years of our analysis. DBSCAN considered these states as outliers for all years. This cluster has explanatory power and is useful for analysis. DBSCAN clusters, no matter what the minimum required count, did not map to generation trends closely, while K-Means clustering did. All analysis and conclusions are based on the K-Means clusters. 

## Analysis

K-Means clustering identifies seven clusters almost every year from 1990 to 2006. In 1990, these constitute:

* [Coal States (14)](./images/KMeans/km-1990-0-coal.png)
* [Coal and Nuclear States (16)](./images/KMeans/km-1990-1-coal-nuke.png)
* [Renewable States (4)](./images/KMeans/km-1990-2-ren.png)
* [Mixed Generation States (4)](./images/KMeans/km-1990-3-mix.png)
* [Natual Gas States (6)](./images/KMeans/km-1990-4-gas.png)
* [Petroleum States (2)](./images/KMeans/km-1990-5-petroleum.png)
* [Nuclear States (5)](./images/KMeans/km-1990-6-nuke.png)

By 2006, the clusters have changed somewhat:

* [Coal States (12)](./images/KMeans/km-2006-1-coal.png)
* [Coal and Nuclear States (15)](./images/KMeans/km-2006-0-coal-nuke.png)
* [Renewable States (4)](./images/KMeans/km-2006-2-ren.png)
* [Mixed Generation States (2)](./images/KMeans/km-2006-5-mix.png)
* [Natual Gas States (11)](./images/KMeans/km-2006-3-gas.png)
* [Petroleum States (2)](./images/KMeans/km-2006-4-petroleum.png)
* [Nuclear States (5)](./images/KMeans/km-2006-6-nuke.png)

Membership in clusters changes somewhat over the course of 1990 - 2016, but the overal breakdown is remarkably stable. Coal and nuclear remain dominant, and renewables are restricted to states with favorable geography in damnable rivers, mostly concentrated in the Pacific Northwest. 

Between 2006 an 2018, the cluster count declines to 5 and the content of the clusters changes. This expresses the decline of coal, the rise of natural gas, the diversification of generation within states, the transition away from petroleum, and the rise of wind and solar. 
