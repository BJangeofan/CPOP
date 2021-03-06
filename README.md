# Chinese Precipitation And Oceanic Processes

Full name **Temporal and Spatial Information Mining and Application of Chinese Precipitation and Ocean Processes Based on Remote Sensing Big Data**

CPOP(Chinese Precipitation and Oceanic Processes) is a scientific research project that analyzes the spatial correlation  between Chinese precipitation and the Pacific Ocean and the Indian Ocean. This project focuses on the impact of ocean processes on precipitation in China.

## Research Background

## Data Description

We have used four different data sets:

1. **Monthly precipitation in China**(中国地面降水月值) You can click on this [link][PREC] for more information.

2. **SST**(Sea surface temperature). NOAA_OI_SST_V2 data provided by the NOAA/OAR/ESRL PSD, Boulder, Colorado, USA, from their Web site at [here][OISST]. OISST 的周SST数据在 1989-12-31 之后直接变为 1990-01-03 不符合一周的定义。所以在处理降水数据时也与之匹配的进行，把前一周降水平均值作为 1990-01-03 的数据。

3. **Wind** CCMP Version-2.0 vector wind analyses are produced by Remote Sensing Systems. Data are available at [here][Wind].

4. **SLA**  [TODO]AVISO

After all of our data is reshaped for the square, (0,0) is in the N-W direction of the map.



## Research Principles and Processes

### Preprocessing
This section preprocesses the raw data. You can see the source code in the `1-Preprocessing\`. The main operations are 
#### Area extraction

We extract the ocean range is 100°E - 290°E & 50°N - 50°S. This range covers the entire Pacific Ocean. For different data sets we use different mask, which are provided along with the raw data. The marine area is shown below
<div align="center">
    <img src="https://github.com/MajorChina/CPOP/blob/master/img/Ocean_Range.png" width="500">
</div>

#### Fineness conversion
We transform the resolution of oceanic data from different resolutions into 1° * 1°. Both to reduce the amount of data and make the data structure of unity. China's land precipitation data only deal with null values. 


### SPI

Calculate the Standardized Precipitation Index.

### EOF

#### SST - Principal components analysis
EOF (or PCA) is used to reduce the ocean data's dimensions. Because of the high correlation between adjacent locations of oceanic data, only the most significant changes need to be extracted to characterize the impact on land. For SST data 99.999% of variance is retained. This means that our data dimension extends from the more than 10,000 dimensions to 402 dimensions.
The first 4 features after dimension reduction are as follows
<div align="center">
    <img src="https://github.com/MajorChina/CPOP/blob/master/img/SST_Principal_component_1-4.png">
</div>

The first 4 spatial modes after dimension reduction are as follows
<div align="center">
    <img src="https://github.com/MajorChina/CPOP/blob/master/img/SST_spatial_modes_1-4.png">
</div>

### Clustering

#### Akaike information criterion

AIC(Akaike information criterion) is a measure of the relative quality of statistical models for a given set of data(From [wiki][AIC_wiki]). This is an effective means to determine the optimal number of clusters. You can read the [original paper][AIC article] for more information. Using this algorithm for China's land precipitation data, the results are as follows
<div align="center">
    <img src="https://github.com/MajorChina/CPOP/blob/master/img/Clusters_AIC_200_Optimal_23.png" width="500">
</div>
We can see that. As the number of clusters increases, AIC value decreases first and then increases. When AIC value is minimum, the number of clusters is 23.

#### Hierarchical Clustering
Hierarchical clustering (also called hierarchical cluster analysis or HCA) is a method of cluster analysis which seeks to build a hierarchy of clusters(From [wiki][Hierarchical Clustering wiki]). The biggest advantage of this clustering algorithm is that it does not need to set the number of clusters at the beginning. The following figure is the result of clustering using this algorithm.
<div align="center">
    <img src="https://github.com/MajorChina/CPOP/blob/master/img/Clusters_Hierarchical_Clustering.png" width="500">
</div>
But we still need to choose a height to determine the original data of a division.

#### Silhouette value
We also try to use **Silhouette Value** to determine the optimal number of clusters. 
<div align="center">
    <img src="https://github.com/MajorChina/CPOP/blob/master/img/Silhouette_value_2~100.png" width="500">
</div>
With the increase of the number of clusters, the overall Silhouette Value also gradually increased, but the rate of increase gradually slowed down. Therefore, the significance of Silhouette Value in selecting the number of clusters is not significant.

#### The final selected clustering method
Considering the actual precipitation situation and clustering algorithm, we finally selected the **k-means algorithm** to cluster the land precipitation in China. If the number of clusters is too large, the effect of ocean on land precipitation will be dispersed, so we choose 9 as the number of clusters. Because the algorithm is stochastic, the algorithm is no longer running. Directly using the previously processed results.
<div align="center">
    <img src="https://github.com/MajorChina/CPOP/blob/master/img/K-means_Clustering_Results.png" width="670">
</div>

### TDNN

### NARX+TDNN

### Deep Learning


## Reference

[1] Akaike H. Information Theory and an Extension of the Maximum Likelihood Principle[J]. Inter.symp.on Information Theory, 1973, 1:610-624.

## License

The MIT License (MIT)

Copyright (c) 2016 Mei Jie

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.


[PREC]:http://data.cma.cn/data/detail/dataCode/SURF_CLI_CHN_PRE_MON_GRID_0.5.html
[OISST]:http://www.esrl.noaa.gov/psd/
[Wind]:http://www.remss.com/measurements/ccmp
[AIC_wiki]:https://en.wikipedia.org/wiki/Akaike_information_criterion
[AIC article]:http://link.springer.com/chapter/10.1007/978-1-4612-1694-0_15
[Hierarchical Clustering wiki]:https://en.wikipedia.org/wiki/Hierarchical_clustering 