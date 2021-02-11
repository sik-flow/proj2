# The Objective: 
### A Multiple Linear Regression Model to Demonstrate Houing Prices in King County 

# The Motive: 

It is no secret that King County, WA yeilds high incomes ranking 47th in the country for household medians. Notably, King County's median cost of living comes in [60% higher than the national average, while the cost of housing is over 160% of the national average.](https://www.bestplaces.net/cost_of_living/county/washington/king) 

### Living in King County most certainly has it's perks. 
For example: the Pacific Northwest offers a scenic and variant landscape and Seattle itself is [home to massive headquarters such as Microsoft and Starbucks.](https://www.usatoday.com/story/money/2019/01/24/richest-counties-us-median-household-income/38870227/) With this in mind, it is no surprise that the Greater King County is considered a desireable place to live.


### The downside? 
King County's highly inflated property values can make the housing market extremely difficult to naviage- especially for first-time or amateur home buyers or investors. 

### The model, for this reason, has many uses including: 

- Understanding market value of your own home

- Maximizing property value of your own home

- Identifying underpriced properties

- Identifying overpriced properties

- Identifying geographic influences of property values 

- Understanding locational desires of buyers

# The Process [(OSEMN)](https://towardsdatascience.com/5-steps-of-a-data-science-project-lifecycle-26c50372b492)

### 1. Obtain: Gathering the Data from Relevant Sources. 

I used the data provided via CSV. The King County housing data provided can be found [here](https://github.com/mollytjbrown/Project-2-DS/blob/main/kc_house_data.csv)

### 2. Scrub: Cleaning the Data for Use. 

The data had null values in the waterfront, yr_renovated, and view categories. I felt confident replacing the null values with 0 in all of these postions. The sqft_basement column contained question marks which also needed to be eliminated. I replaced these values with zero also, using the following function: 

```def remove_mark(q):
    if q == '?':
        return 0  
    else:
        return q
    
housing['sqft_basement'] = housing.sqft_basement.map(lambda x: remove_mark(x))
```
After this step, all null values were addressed and I felt that the data needed to be explored before cleaning further. 

### 3. Explore: Finding Significant Patterns and Trends.

I first checked for co-linearity and found the following: 

- sqft_living correlates with sqft_above, grade, sqft_living15, and bathrooms

- sqft_above correlates with sqft_living, and grade

- grade correlates with sqft_living and sqft_above

- bathrooms correlates with sqft_living


I then created a heat map to indicate which values most heavily correlated with price: 

![alt text](https://github.com/mollytjbrown/Project-2-DS/blob/main/price%20correlation%20heatmap.png?raw=true)

From here, I established a new Pandas DataFrame which eliminated colinear values. 

Next, I separated basement values into two categories: basement (1), and no basement (0), and stacked them categorically in a new column called "basement." 

Similarly, I created dummy variables for condition, floors, view, and waterfront: 

```condition_dummies = pd.get_dummies(housing2['condition'], prefix='cond', drop_first=True)
floors_dummies = pd.get_dummies(housing2['floors'], prefix='floors', drop_first=True)
view_dummies = pd.get_dummies(housing2['view'], prefix='view', drop_first=True)
waterfront_dummies = pd.get_dummies(housing2['waterfront'], prefix='wf', drop_first=True)
```

After connecting the new categorical variables with the existing dataframe using pd.concat, I transformed the latitude and longitude variables into a new column called "distances," which served as a measure of distance in miles between the property and downtown Seattle using the following function: 

```import geopy.distance
def calculate_distance_from_dt(lat, long):
    coords_1 = (lat, long)
    coords_2 = (47.6050, -122.3344)
    return geopy.distance.geodesic(coords_1, coords_2).miles
    distances = []

for latitude, longitude in zip(housing2['lat'], housing2['long']):
    my_distance = calculate_distance_from_dt(latitude, longitude)
    distances.append(my_distance)
```

Last, I eliminated outliers using boxplots and the describe function, and then moved on to creating the model. 

### 4. Model: Constructing a Model to Predict and Forecast 

Based on the heatmap indicated above, I selected variables that were most highly correlated to the price. The variables I ended up using finally were bedrooms, sqft_living, sqft_lot, basement, waterfront, and distances. 

### 5. Interpret: Putting the Results to Good Use

After selecting these features and creating an OLS model, I demonstrated the significance and uses [here.](https://github.com/mollytjbrown/Project-2-DS/blob/main/King%20County%20Housing%20Price%20Model.pdf)


   
