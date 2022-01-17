# Challenge_6

The goal of this project is to create an analysis of the housing rental market data for San Francisco. The analysis will be complete with professionally styled and formatted interactive visualizations.


## Technology

~~~
import pandas as pd
import hvplot.pandas
from pathlib import Path
~~~
**Note(see above):** After importing these required technologies and following the steps below, the program should be capable of functioning properly.

## Installation Guide

Use the read_csv function and Path module to import the data and create a dataframe.
~~~
csvpath = Path("Resources/sfo_neighborhoods_census_data.csv")
sfo_data_df = pd.read_csv(csvpath)
~~~

## Usage

### Calculate and Plot the Housing Units per Year

1. Use the groupby function to group the data by year. Aggregate the results by the mean of the groups.
~~~
housing_units_by_year = sfo_data_df.groupby('year').mean()
~~~

2. Use the hvplot function to plot the housing_units_by_year DataFrame as a bar chart. Make the x-axis represent the year and the y-axis represent the housing_units.
~~~
housing_units_by_year.hvplot.bar(ylim = [370000, 390000], legend = True, figsize=(20, 15), title="Housing Units in San Fran from 2010 to 2016", x='year', y='housing_units')
~~~
<img width="658" alt="Plot_1" src="https://user-images.githubusercontent.com/94569323/149244136-f22662c5-4dc6-4294-a87e-79acd0902dfa.png">

3. Answer the following question:
**Question:** What is the overall trend in housing_units over the period being analyzed?
**Answer:** The visual aggregation highlights a trend in which the number of housing units increases on an annual basis.

### Calculate and Plot the Average Sale Prices per Square Foot

1. Group the data by year, and then average the results.
~~~
prices_square_foot_by_year = sfo_data_df.groupby('year').mean()
~~~

2. Create a new DataFrame named prices_square_foot_by_year by filtering out the “housing_units” column. The new DataFrame should include the averages per year for only the sale price per square foot and the gross rent.
~~~
prices_square_foot_by_year = prices_square_foot_by_year.drop("housing_units", axis=1)
~~~

3. Use hvPlot to plot the prices_square_foot_by_year DataFrame as a line plot.
~~~
prices_square_foot_by_year.hvplot(legend = True, figsize=(20, 15), title="Sale Price Per Square Foot and Average Gross Rent in San Fran from 2010 to 2016", x='year', y=('sale_price_sqr_foot', 'gross_rent'), ylabel = 'Gross Rent/Sale Price Per Square Foot')
~~~
<img width="666" alt="Plot_2" src="https://user-images.githubusercontent.com/94569323/149244185-19cdc0f5-5ecc-4b14-9f88-d711e2ebfd6b.png">

4. Use both the prices_square_foot_by_year DataFrame and interactive plots to answer the following questions:
**Question:** Did any year experience a drop in the average sale price per square foot compared to the previous year?
**Answer:** In 2011 the average sale price per square foot (341.9) was lower than that of the previous year (369.3).

**Question:** If so, did the gross rent increase or decrease during that year?
**Answer:** However, it can be noted that the gross rent increased from 1239 to 1530 during that year.

### Compare the Average Sale Prices by Neighborhood.

1. Create a new DataFrame that groups the original DataFrame by year and neighborhood. Aggregate the results by the mean of the groups.
~~~
prices_by_year_by_neighborhood = sfo_data_df.groupby(['year','neighborhood']).mean()
~~~

2. Filter out the “housing_units” column to create a DataFrame that includes only the sale_price_sqr_foot and gross_rent averages per year.
~~~
prices_by_year_by_neighborhood = prices_by_year_by_neighborhood.drop("housing_units", axis=1)
~~~

3. Create an interactive line plot with hvPlot that visualizes both sale_price_sqr_foot and gross_rent. Set the x-axis parameter to the year (x="year"). Use the groupby parameter to create an interactive widget for neighborhood.
~~~
prices_by_year_by_neighborhood.hvplot(legend = True, figsize=(20, 15), title="Sale Price Per Square Foot and Average Gross Rent in San Fran from 2010 to 2016 by Neighborhood", x='year', y=('sale_price_sqr_foot', 'gross_rent'), ylabel = 'Gross Rent/Sale Price Per Square Foot', groupby = 'neighborhood')
~~~
<img width="662" alt="Plot_3" src="https://user-images.githubusercontent.com/94569323/149244208-e2b0e9d7-5d2c-470f-b6f0-4bf8961f9f78.png">

4. Use the interactive visualization to answer the following question:
**Question:** For the Anza Vista neighborhood, is the average sale price per square foot for 2016 more or less than the price that’s listed for 2012? 
**Answer:** According to the visualization, the average sale price per square foor for 2016 was higher than that for 2012.

### Build an Interactive Neighborhood Map.

1. Read the neighborhood_coordinates.csv file from the Resources folder into the notebook, and create a DataFrame named neighborhood_locations_df. Be sure to set the index_col of the DataFrame as “Neighborhood”.
~~~
csvpath_2 = Path("Resources/neighborhoods_coordinates.csv")
neighborhood_locations_df = pd.read_csv((csvpath_2),
                                        index_col = 'Neighborhood')
~~~

2. Using the original sfo_data_df Dataframe, create a DataFrame named all_neighborhood_info_df that groups the data by neighborhood. Aggregate the results by the mean of the group.
~~~
all_neighborhood_info_df = sfo_data_df.groupby('neighborhood').mean()
~~~

3. Review the two code cells that concatenate the neighborhood_locations_df DataFrame with the all_neighborhood_info_df DataFrame.
~~~
all_neighborhoods_df = pd.concat(
    [neighborhood_locations_df, all_neighborhood_info_df], 
    axis="columns",
    sort=False
)
~~~
~~~
all_neighborhoods_df = all_neighborhoods_df.reset_index().dropna()
all_neighborhoods_df = all_neighborhoods_df.rename(columns={"index": "Neighborhood"})
~~~

4. Using hvPlot with GeoViews enabled, create a points plot for the all_neighborhoods_df DataFrame.
~~~
all_neighborhoods_df.hvplot.points(
    'Lon',
    'Lat',
    geo=True,
    size='sale_price_sqr_foot',
    color='gross_rent',
    frame_width=700,
    frame_height=500,
    tiles = 'OSM',
    title = 'Neighborhood Map')
~~~
<img width="815" alt="Plot_4" src="https://user-images.githubusercontent.com/94569323/149244234-51ac384f-7dc9-4728-8edd-7833d0b6500a.png">

5. Use the interactive map to answer the following question:
**Question:** Which neighborhood has the highest gross rent, and which has the highest sale price per square foot?
**Answer:** Westwood Park has the highest gross rent, and Union Square District has the highest sale price per square foot.

### Compose Your Data Story.

**Question:**  How does the trend in rental income growth compare to the trend in sales prices? Does this same trend hold true for all the neighborhoods across San Francisco?
**Answer:** While the visualizations highlight a trend in which the rental income increases on an annual basis, they also depict a trend in which the sales prices are somewhat stagnant. This same trend holds true for all of the neighborhoods across San Francisco.

**Question:** What insights can you share with your company about the potential one-click, buy-and-rent strategy that they're pursuing? Do neighborhoods exist that you would suggest for investment, and why?
**Answer:** According to the visualizations it can be inferred that the potential one-click, buy-and-rent strategy has the potential to be profitable. Given that a trend exists in which the rental income increases on an annual basis, the company can expect to make a return on investment over an extended period of time.

## Contributors

Chancie Altham: Developer

## License

MIT License has been added to the project.