
**ANALYSIS**

Observed Trend 1:
After review of the dataset, there is a large amount Drivers within the Urban areas. What I found interesting was the Driver Count and Total Rider ratio based on the below table. This could be based on the amount of transportation in the Urban area which would equal less riders or vice versa.


Observed Trend 2:
The scatter plot shows the Total Number of Riders vs. Average Fares by city that is categorized by City Type (Urban (gold), Rural(red) and Suburban(lightblue). The plot sizes are based on Total Drivers. The graph shows an when there is a large amount drivers / riders in the Urban areas, there is an low average fare at a range of $20-30. Whereas, rides that take place in the Rural and Suburban areas have less Total Drivers / Riders but there are increase in the Average Fare. This could be due to a higher rate based on distance of travel.



Observed Trend 3:
The pie chart obersvation shows a breakdown of the Total Fare, Drivers and Riders based the City Type. As you can see, there is a significant increase of the 3 categories based on the Urban areas.




```python
#%matplotlib inline
import matplotlib.pyplot as plt
import numpy as np
import pandas as pd
import csv
from scipy import stats
from scipy.stats import linregress
```


```python
# Read in data
city_data = pd.read_csv("city_data.csv")
ride_data = pd.read_csv("ride_data.csv")

#Create DataFrame for City Data
city_data_df = pd.DataFrame(city_data)
city_data_df.head()

#Create DataFrame for Ride Data
ride_data_df = pd.DataFrame(ride_data)
ride_data_df.head()

#Merge both DataFrame for City and Ride using Groupby by 'City'
combined_data_df = pd.merge(ride_data, city_data, how="left", on=["city", "city"])
combined_data_df.head()

                          
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>city</th>
      <th>date</th>
      <th>fare</th>
      <th>ride_id</th>
      <th>driver_count</th>
      <th>type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Sarabury</td>
      <td>2016-01-16 13:49:27</td>
      <td>38.35</td>
      <td>5403689035038</td>
      <td>46</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>1</th>
      <td>South Roy</td>
      <td>2016-01-02 18:42:34</td>
      <td>17.49</td>
      <td>4036272335942</td>
      <td>35</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Wiseborough</td>
      <td>2016-01-21 17:35:29</td>
      <td>44.18</td>
      <td>3645042422587</td>
      <td>55</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Spencertown</td>
      <td>2016-07-31 14:53:22</td>
      <td>6.87</td>
      <td>2242596575892</td>
      <td>68</td>
      <td>Urban</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Nguyenbury</td>
      <td>2016-07-09 04:42:44</td>
      <td>6.28</td>
      <td>1543057793673</td>
      <td>8</td>
      <td>Urban</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Group DataFrame by City for Average and total calculations
city_group = combined_data_df.groupby(['city'])
city_group

#Average Fare ($) Per City
average_fare = city_group['fare'].mean()

#Total Number of Rides Per City
total_num_rides = city_group['ride_id'].count()


#Total Number of Drivers Per City
total_num_drivers = city_group['driver_count'].mean()


plot_data = pd.DataFrame({"Average Fare": average_fare,
                           "Total Rides": total_num_rides,
                          "Total Drivers": total_num_drivers
                           })

plot_data.head()

```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Average Fare</th>
      <th>Total Drivers</th>
      <th>Total Rides</th>
    </tr>
    <tr>
      <th>city</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Alvarezhaven</th>
      <td>23.928710</td>
      <td>21</td>
      <td>31</td>
    </tr>
    <tr>
      <th>Alyssaberg</th>
      <td>20.609615</td>
      <td>67</td>
      <td>26</td>
    </tr>
    <tr>
      <th>Anitamouth</th>
      <td>37.315556</td>
      <td>16</td>
      <td>9</td>
    </tr>
    <tr>
      <th>Antoniomouth</th>
      <td>23.625000</td>
      <td>21</td>
      <td>22</td>
    </tr>
    <tr>
      <th>Aprilchester</th>
      <td>21.981579</td>
      <td>49</td>
      <td>19</td>
    </tr>
  </tbody>
</table>
</div>




```python
#City Type Data Collection
urban_data = combined_data_df.loc[(combined_data_df["type"] == "Urban")]
rural_data = combined_data_df.loc[(combined_data_df["type"] == "Rural")]
suburban_data = combined_data_df.loc[(combined_data_df["type"] == "Suburban")]
```


```python
# Create a scatter plot which compares Total Rides to Average Fare Per City Type
group = ["Rural", "Urban", "Suburban"]
colors = ["gold","lightcoral","lightskyblue"]
plot_data.plot.scatter(x='Total Rides', y='Average Fare',color=colors, s=plot_data['Total Drivers']*25, alpha=0.75, edgecolor="black", figsize=(10,7))
plt.ylim(15,45)
plt.xlim(0,40)
plt.xlabel("Total Number of Rides (per City)")
plt.ylabel("Average Fare ($)")
plt.title("Pyber Ride Sharing Data (2016)")
plt.grid()
plt.show()
```


![png](output_5_0.png)



```python
#Create Pie Chart based on % Total Fares by City Type
total_fare = combined_data_df.groupby(['type']).sum()['fare']
total_fare


colors = ["gold","lightskyblue","lightcoral"]
explode = (0, 0, 0.1)
total_fare_chart = total_fares.plot(kind='pie', figsize=(5,5), colors=colors, explode=explode, shadow=True, autopct="%1.1f%%", startangle=140)
total_fare_chart.set_title("% of Total Fare by City Type")
plt.axis('tight')
plt.ylabel("")
plt.show()
```


![png](output_6_0.png)



```python
#Create Pie Chart based on % Total Rides by City Type
total_ride_count = combined_data_df.groupby(['type']).count()['ride_id']

#Total Fares per City Type
# total_ride_count = city_type_group['ride_id'].count()

colors = ["gold","lightskyblue","lightcoral"]
explode = (0, 0, 0.1)
total_ride_chart = total_ride_count.plot(kind='pie', figsize=(5,5), colors=colors, explode=explode, shadow=True, autopct="%1.1f%%", startangle=140)
total_ride_chart.set_title("% of Total Rides by City Type")
plt.axis('tight')
plt.ylabel("")
plt.show()
```


![png](output_7_0.png)



```python
#Create Pie Chart based on % Total Driver by City Type
total_driver_count = combined_data_df.groupby(['type']).mean()['driver_count']


colors = ["gold","lightskyblue","lightcoral"]
explode = (0, 0, 0.1)
total_driver_chart = total_driver_count.plot(kind='pie', figsize=(5,5), colors=colors, explode=explode, shadow=True, autopct="%1.1f%%", startangle=140)
total_driver_chart.set_title("% of Total Driver by City Type")
plt.axis('tight')
plt.ylabel("")
plt.show()
```


![png](output_8_0.png)

