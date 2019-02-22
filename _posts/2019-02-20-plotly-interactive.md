---
title: ' Interactive plot in R '
date: 2019-02-20
tags:
  - Data analysis
  - Data visualization
  - Rstat
---


======

The plotly package allows you to create plotly interactive graphs from within R. 

In addition, any ggplot2 graph can be turned into a plotly graph.

Using the Fuel Economy data, we’ll create an interactive graph displaying highway mileage vs. engine displace by car class.


# create plotly graph.
library(ggplot2)
library(plotly)

p <- ggplot(mpg, aes(x=displ, 
                     y=hwy, 
                     color=class)) +
  geom_point(size=3) +
  labs(x = "Engine displacement",
       y = "Highway Mileage",
       color = "Car Class") +
  theme_bw()

ggplotly(p)



There are several sources of good information on plotly. 



See the plotly [R pages](https://plot.ly/r/) and the online plotly for R book. 

Additionally, DataCamp offers a free [interactive tutorial](https://www.datacamp.com/community/blog/a-free-interactive-plotly-r-tutorial)
