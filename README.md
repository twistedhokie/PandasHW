
# Heroes of Pymolii

Observations
- A remarkably higher number of men play the game than women
- There were some items with differing Item IDs and prices, which may have altered some calculations
- 75% of players are over the age of 20


```python
#import dependencies
import pandas as pd
import json
from pandas.io.json import json_normalize
from sklearn import preprocessing
```


```python
#read json file
data = pd.read_json("purchase_data.json")
#data["Price"] = data["Price"].map("${:.2f}".format)
data.head()
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
      <th>Age</th>
      <th>Gender</th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>SN</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>38</td>
      <td>Male</td>
      <td>165</td>
      <td>Bone Crushing Silver Skewer</td>
      <td>3.37</td>
      <td>Aelalis34</td>
    </tr>
    <tr>
      <th>1</th>
      <td>21</td>
      <td>Male</td>
      <td>119</td>
      <td>Stormbringer, Dark Blade of Ending Misery</td>
      <td>2.32</td>
      <td>Eolo46</td>
    </tr>
    <tr>
      <th>2</th>
      <td>34</td>
      <td>Male</td>
      <td>174</td>
      <td>Primitive Blade</td>
      <td>2.46</td>
      <td>Assastnya25</td>
    </tr>
    <tr>
      <th>3</th>
      <td>21</td>
      <td>Male</td>
      <td>92</td>
      <td>Final Critic</td>
      <td>1.36</td>
      <td>Pheusrical25</td>
    </tr>
    <tr>
      <th>4</th>
      <td>23</td>
      <td>Male</td>
      <td>63</td>
      <td>Stormfury Mace</td>
      <td>1.27</td>
      <td>Aela59</td>
    </tr>
  </tbody>
</table>
</div>




```python
#calculate total players
Total_Players = data["SN"].nunique()
Total_Players
```




    573




```python
#Calculate number of items
Unique_Items = data["Item Name"].nunique()
Unique_Items
```




    179




```python
#Calculate average price
Average_Price = data["Price"].mean()
Average_Price
```




    2.931192307692303




```python
#Calculate total number of purchases
Total_Purch = data["SN"].count()
Total_Purch
```




    780




```python
#Calculate total revenue
Total_Rev = data["Price"].sum()
Total_Rev
```




    2286.33




```python
#Create overall summary table
summary_table1 = pd.DataFrame({"Total Players":[Total_Players],
                             "Number of Unique Items":[Unique_Items],
                             "Average Purchase Price":[Average_Price],
                             "Total Number of Purchases":[Total_Purch],
                             "Total Revenue":[Total_Rev]})

```


```python
#Format summary table $$ columns
summary_table1["Average Purchase Price"] = summary_table1["Average Purchase Price"].map("${:.2f}".format)
summary_table1["Total Revenue"] = summary_table1["Total Revenue"].map("${:.2f}".format)

summary_table1
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
      <th>Average Purchase Price</th>
      <th>Number of Unique Items</th>
      <th>Total Number of Purchases</th>
      <th>Total Players</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>$2.93</td>
      <td>179</td>
      <td>780</td>
      <td>573</td>
      <td>$2286.33</td>
    </tr>
  </tbody>
</table>
</div>




```python
#rearrange columns
summary_table1 = summary_table1[["Total Players", "Total Revenue", "Total Number of Purchases", 
                                "Number of Unique Items", "Average Purchase Price"]]
summary_table1
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
      <th>Total Players</th>
      <th>Total Revenue</th>
      <th>Total Number of Purchases</th>
      <th>Number of Unique Items</th>
      <th>Average Purchase Price</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>573</td>
      <td>$2286.33</td>
      <td>780</td>
      <td>179</td>
      <td>$2.93</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Calculate total of each gender
GenSum = data.groupby(["Gender"])["SN"].nunique()
GenSum
```




    Gender
    Female                   100
    Male                     465
    Other / Non-Disclosed      8
    Name: SN, dtype: int64




```python
#Calculate gender percentages
PercentGen = (GenSum/Total_Players)*100
PercentGen
```




    Gender
    Female                   17.452007
    Male                     81.151832
    Other / Non-Disclosed     1.396161
    Name: SN, dtype: float64




```python
#Create gender summary table
Gender_summary = pd.DataFrame({"Percent of Total" :
                  PercentGen, "Total Number of Players" :
                   GenSum})

Gender_summary
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
      <th>Percent of Total</th>
      <th>Total Number of Players</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>17.452007</td>
      <td>100</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>81.151832</td>
      <td>465</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>1.396161</td>
      <td>8</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Calculate gender purchase data
Total_GenPurch = data.groupby(["Gender"])["Item ID"].count()
Total_GenPurch
```




    Gender
    Female                   136
    Male                     633
    Other / Non-Disclosed     11
    Name: Item ID, dtype: int64




```python
Avg_GenPurch = data.groupby(["Gender"])["Price"].mean()
Avg_GenPurch
```




    Gender
    Female                   2.815515
    Male                     2.950521
    Other / Non-Disclosed    3.249091
    Name: Price, dtype: float64




```python
Total_GenRev = data.groupby(["Gender"])["Price"].sum()
Total_GenRev
```




    Gender
    Female                    382.91
    Male                     1867.68
    Other / Non-Disclosed      35.74
    Name: Price, dtype: float64




```python
#Create gender purchase summary table
GenPurch_Summary = pd.DataFrame({"Total Purchase Count" : Total_GenPurch, "Average Purchase Price" : Avg_GenPurch, 
                            "Total Purchase Value" : Total_GenRev})
GenPurch_Summary
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
      <th>Average Purchase Price</th>
      <th>Total Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>2.815515</td>
      <td>136</td>
      <td>382.91</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>2.950521</td>
      <td>633</td>
      <td>1867.68</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>3.249091</td>
      <td>11</td>
      <td>35.74</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Normalize Data
# Create x, where x the 'scores' column's values as floats
x = GenPurch_Summary[['Total Purchase Value']].values.astype(float)
```


```python
# Create a minimum and maximum processor object
min_max_scaler = preprocessing.MinMaxScaler()

# Create an object to transform the data to fit minmax processor
x_scaled = min_max_scaler.fit_transform(x)

# Run the normalizer on the dataframe
Gen_normalized = (x_scaled)
```


```python
#Add Normalization column to table
GenPurch_Summary["Normalized Totals"] = Gen_normalized
GenPurch_Summary
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
      <th>Average Purchase Price</th>
      <th>Total Purchase Count</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Gender</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Female</th>
      <td>2.815515</td>
      <td>136</td>
      <td>382.91</td>
      <td>0.189509</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>2.950521</td>
      <td>633</td>
      <td>1867.68</td>
      <td>1.000000</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>3.249091</td>
      <td>11</td>
      <td>35.74</td>
      <td>0.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
#create age bins
max_age = data["Age"].max()
# Create the bins in which Data will be held
bins = [0, 9, 14, 19, max_age]

# Create the names for the four bins
group_names = ['Under 10', '10-14', '15-19', '20+']
pd.cut(data["Age"], bins, labels=group_names)
```




    0        20+
    1        20+
    2        20+
    3        20+
    4        20+
    5        20+
    6        20+
    7        20+
    8        20+
    9        20+
    10       20+
    11       20+
    12       20+
    13       20+
    14       20+
    15       20+
    16       20+
    17       20+
    18       20+
    19       20+
    20       20+
    21     15-19
    22     10-14
    23     15-19
    24     10-14
    25       20+
    26       20+
    27       20+
    28     15-19
    29     15-19
           ...  
    750      20+
    751      20+
    752    15-19
    753      20+
    754      20+
    755      20+
    756      20+
    757      20+
    758      20+
    759    15-19
    760      20+
    761      20+
    762      20+
    763      20+
    764      20+
    765    15-19
    766      20+
    767      20+
    768      20+
    769      20+
    770      20+
    771      20+
    772    15-19
    773      20+
    774      20+
    775      20+
    776    10-14
    777      20+
    778      20+
    779      20+
    Name: Age, Length: 780, dtype: category
    Categories (4, object): [Under 10 < 10-14 < 15-19 < 20+]




```python
#Add bins to dataframe
data["Age Summary"] = pd.cut(data["Age"], bins, labels=group_names)
data.head()
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
      <th>Age</th>
      <th>Gender</th>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th>SN</th>
      <th>Age Summary</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>38</td>
      <td>Male</td>
      <td>165</td>
      <td>Bone Crushing Silver Skewer</td>
      <td>3.37</td>
      <td>Aelalis34</td>
      <td>20+</td>
    </tr>
    <tr>
      <th>1</th>
      <td>21</td>
      <td>Male</td>
      <td>119</td>
      <td>Stormbringer, Dark Blade of Ending Misery</td>
      <td>2.32</td>
      <td>Eolo46</td>
      <td>20+</td>
    </tr>
    <tr>
      <th>2</th>
      <td>34</td>
      <td>Male</td>
      <td>174</td>
      <td>Primitive Blade</td>
      <td>2.46</td>
      <td>Assastnya25</td>
      <td>20+</td>
    </tr>
    <tr>
      <th>3</th>
      <td>21</td>
      <td>Male</td>
      <td>92</td>
      <td>Final Critic</td>
      <td>1.36</td>
      <td>Pheusrical25</td>
      <td>20+</td>
    </tr>
    <tr>
      <th>4</th>
      <td>23</td>
      <td>Male</td>
      <td>63</td>
      <td>Stormfury Mace</td>
      <td>1.27</td>
      <td>Aela59</td>
      <td>20+</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Calculate age demographic data
AgeSum = data.groupby(["Age Summary"])["SN"].nunique()
AgeSum
```




    Age Summary
    Under 10     19
    10-14        23
    15-19       100
    20+         431
    Name: SN, dtype: int64




```python
Percent_Age = (AgeSum / Total_Players)*100
Percent_Age
```




    Age Summary
    Under 10     3.315881
    10-14        4.013962
    15-19       17.452007
    20+         75.218150
    Name: SN, dtype: float64




```python
#Create Age Demographic data summary
Age_Data = pd.DataFrame({"Percent of Total" :
                  Percent_Age, "Total Number of Players" :
                   AgeSum})
Age_Data
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
      <th>Percent of Total</th>
      <th>Total Number of Players</th>
    </tr>
    <tr>
      <th>Age Summary</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Under 10</th>
      <td>3.315881</td>
      <td>19</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>4.013962</td>
      <td>23</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>17.452007</td>
      <td>100</td>
    </tr>
    <tr>
      <th>20+</th>
      <td>75.218150</td>
      <td>431</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Calculate age demographic purchase data
Purchase_Count = data.groupby(['Age Summary'])['Item ID'].count()
Purchase_Count
```




    Age Summary
    Under 10     28
    10-14        35
    15-19       133
    20+         584
    Name: Item ID, dtype: int64




```python
Total_Purch_Age = data.groupby(['Age Summary'])["Price"].sum()
Total_Purch_Age
```




    Age Summary
    Under 10      83.46
    10-14         96.95
    15-19        386.42
    20+         1719.50
    Name: Price, dtype: float64




```python
Avg_Purch_Age = data.groupby(['Age Summary'])['Price'].mean()
Avg_Purch_Age
```




    Age Summary
    Under 10    2.980714
    10-14       2.770000
    15-19       2.905414
    20+         2.944349
    Name: Price, dtype: float64




```python
#Create age purchase summary table
Age_Summary = pd.DataFrame({"Total Purchase Count" : Purchase_Count, "Average Purchase Price" : Avg_Purch_Age, 
                            "Total Purchase Value" : Total_Purch_Age})
Age_Summary
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
      <th>Average Purchase Price</th>
      <th>Total Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Age Summary</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Under 10</th>
      <td>2.980714</td>
      <td>28</td>
      <td>83.46</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>2.770000</td>
      <td>35</td>
      <td>96.95</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>2.905414</td>
      <td>133</td>
      <td>386.42</td>
    </tr>
    <tr>
      <th>20+</th>
      <td>2.944349</td>
      <td>584</td>
      <td>1719.50</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Normalize data
# Create x, where x stores the 'Total Purchase Value' column's values as floats
y = Age_Summary[['Total Purchase Value']].values.astype(float)
```


```python
# Create a minimum and maximum processor object
min_max_scaler = preprocessing.MinMaxScaler()

# Create an object to transform the data to fit minmax processor
y_scaled = min_max_scaler.fit_transform(y)

# Run the normalizer on the dataframe
Age_normalized = (y_scaled)
```


```python
#add normalized column to table
Age_Summary["Normalized Totals"] = Age_normalized
Age_Summary
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
      <th>Average Purchase Price</th>
      <th>Total Purchase Count</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th>Age Summary</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Under 10</th>
      <td>2.980714</td>
      <td>28</td>
      <td>83.46</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>2.770000</td>
      <td>35</td>
      <td>96.95</td>
      <td>0.008246</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>2.905414</td>
      <td>133</td>
      <td>386.42</td>
      <td>0.185179</td>
    </tr>
    <tr>
      <th>20+</th>
      <td>2.944349</td>
      <td>584</td>
      <td>1719.50</td>
      <td>1.000000</td>
    </tr>
  </tbody>
</table>
</div>




```python
#calculate top spender data
top_spend = data.groupby(["SN"])["Price"].sum()
Avg_top5 = data.groupby(["SN"])["Price"].mean()
total_top5 = data.groupby(["SN"])["Item ID"].count()

top_5 = pd.DataFrame({"Total Items Purchased" : total_top5, 
                      "Average Purchase Price" : Avg_top5, 
                      "Total Spent" : top_spend})
top_5data = top_5.nlargest(columns="Total Spent", n=5)
top_5data
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
      <th>Average Purchase Price</th>
      <th>Total Items Purchased</th>
      <th>Total Spent</th>
    </tr>
    <tr>
      <th>SN</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Undirrala66</th>
      <td>3.412000</td>
      <td>5</td>
      <td>17.06</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>3.390000</td>
      <td>4</td>
      <td>13.56</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>3.185000</td>
      <td>4</td>
      <td>12.74</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>4.243333</td>
      <td>3</td>
      <td>12.73</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>3.860000</td>
      <td>3</td>
      <td>11.58</td>
    </tr>
  </tbody>
</table>
</div>




```python
#calculate popular item data
purch_counts = data.groupby(["Item ID", "Item Name", "Price"])["Item ID"].count()
purch_value = data.groupby(["Item ID", "Item Name", "Price"])["Price"].sum()

top_pop = pd.DataFrame({"Purchase Count" : purch_counts, 
                        "Total Purchase Value" : purch_value})
top_pop_data = top_pop.nlargest(columns="Purchase Count", n=5)
top_pop_data
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
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <th>Betrayal, Whisper of Grieving Widows</th>
      <th>2.35</th>
      <td>11</td>
      <td>25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <th>Arcane Gem</th>
      <th>2.23</th>
      <td>11</td>
      <td>24.53</td>
    </tr>
    <tr>
      <th>13</th>
      <th>Serenity</th>
      <th>1.49</th>
      <td>9</td>
      <td>13.41</td>
    </tr>
    <tr>
      <th>31</th>
      <th>Trickster</th>
      <th>2.07</th>
      <td>9</td>
      <td>18.63</td>
    </tr>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <th>4.14</th>
      <td>9</td>
      <td>37.26</td>
    </tr>
  </tbody>
</table>
</div>




```python
#Calculate top revenue data
prof_counts = data.groupby(["Item ID", "Item Name", "Price"])["Item ID"].count()
prof_value = data.groupby(["Item ID", "Item Name", "Price"])["Price"].sum()

top_prof = pd.DataFrame({"Purchase Count" : prof_counts, 
                        "Total Purchase Value" : prof_value})
top_prof_data = top_prof.nlargest(columns="Total Purchase Value", n=5)
top_prof_data
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
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th>Price</th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <th>4.14</th>
      <td>9</td>
      <td>37.26</td>
    </tr>
    <tr>
      <th>115</th>
      <th>Spectral Diamond Doomblade</th>
      <th>4.25</th>
      <td>7</td>
      <td>29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <th>Orenmir</th>
      <th>4.95</th>
      <td>6</td>
      <td>29.70</td>
    </tr>
    <tr>
      <th>103</th>
      <th>Singed Scalpel</th>
      <th>4.87</th>
      <td>6</td>
      <td>29.22</td>
    </tr>
    <tr>
      <th>107</th>
      <th>Splitter, Foe Of Subtlety</th>
      <th>3.61</th>
      <td>8</td>
      <td>28.88</td>
    </tr>
  </tbody>
</table>
</div>


