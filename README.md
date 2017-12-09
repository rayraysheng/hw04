
# Heroes Of Pymoli Data Analysis

* Heroes Of Pymoli players are overwhelmingly male
* Paid items are mostly bought by players from 20 to 24 years old, but they spend less money per person than the other age groups
* Female players tend to spend less money per item and less money per player than male players


```python
# Imports
import pandas as pd
import numpy as np
import os
```


```python
# Load file
# Because the file name can be anything, I don't want to hard code it
# I've set up a directory to load the input file into
# The code will read the first file in that directory
# So whoever runs the code will have to make sure there's only one file in there
# For this assignment, we'll use purchase_data.json
file_df = pd.read_json("input_file/" + os.listdir("input_file")[0])
```

### Player Count


```python
# Player Count

# -----------------------------------------
# Total Number of Players is the number of unique SNs
tot_players = file_df["SN"].nunique()

# Show it
player_count_df = pd.DataFrame({"Total Players": [tot_players]})
player_count_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Players</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>573</td>
    </tr>
  </tbody>
</table>
</div>



### Purchasing Analysis (Total)


```python
# Purchasing Analysis (Total)

# -----------------------------------------
# Group the data by Item IDs
by_id = file_df.groupby("Item ID")

# Number of Unique Items
uniq_items = len(by_id)

# -----------------------------------------
# Calculate Average Purchase Price
avg_price = sum(file_df["Price"]) / len(file_df)

# -----------------------------------------
# Total Number of Purchases is the number of total rows
tot_purchases = len(file_df)

# -----------------------------------------
# Total Revenue is the sum of all items in the Price column
tot_revenue = sum(file_df["Price"])

# -----------------------------------------
# Set the table
analysis_total = pd.DataFrame({
    "Number of Unique Items": [uniq_items],
    "Average Price": [avg_price],
    "Number of Purchases": [tot_purchases],
    "Total Revenue": [tot_revenue]
})
analysis_total = analysis_total[[
    "Number of Unique Items",
    "Average Price",
    "Number of Purchases",
    "Total Revenue"
]]

# Change the number format for dollar amounts
# Function to convert format
def dollarize(num):
    round_out = round(num, 2)
    string_out = "$" + str(round_out)
    return string_out

# Map the dollar amount
analysis_total["Total Revenue"] = analysis_total["Total Revenue"].map(dollarize)
analysis_total["Average Price"] = analysis_total["Average Price"].map(dollarize)

# Show it
analysis_total
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Number of Unique Items</th>
      <th>Average Price</th>
      <th>Number of Purchases</th>
      <th>Total Revenue</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>183</td>
      <td>$2.93</td>
      <td>780</td>
      <td>$2286.33</td>
    </tr>
  </tbody>
</table>
</div>



### Gender Demographics


```python
# Gender Demographics

by_gender = file_df.groupby("Gender")
gender_count = by_gender["SN"].nunique()

# Build the dataframe to show it
gender_demo_df = pd.DataFrame({
    "": ["Male", "Female", "Other / Non-Disclosed"],
    "Total Count":[
        gender_count["Male"],
        gender_count["Female"],
        gender_count["Other / Non-Disclosed"]
    ]
})

# Calculate the percentages and add them to new column
percent_lst = [count / tot_players for count in gender_demo_df["Total Count"]]
percent_lst = [round(percent * 100, 2) for percent in percent_lst]
gender_demo_df["Percentage of Players"] = percent_lst

# Show it
gender_demo_df.set_index("", inplace=True)
gender_demo_df
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Total Count</th>
      <th>Percentage of Players</th>
    </tr>
    <tr>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>Male</th>
      <td>465</td>
      <td>81.15</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>100</td>
      <td>17.45</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>8</td>
      <td>1.40</td>
    </tr>
  </tbody>
</table>
</div>



### Purchasing Analysis (Gender)


```python
# Purchasing Analysis (Gender)

# -----------------------------------------
# Purchasing Count
male_count = by_gender["Price"].count()["Male"]
female_count = by_gender["Price"].count()["Female"]
other_count = by_gender["Price"].count()["Other / Non-Disclosed"]
gender_purch_lst = [male_count, female_count, other_count]

# -----------------------------------------
# Average Purchase Price
male_avg = by_gender["Price"].mean()["Male"]
female_avg = by_gender["Price"].mean()["Female"]
other_avg = by_gender["Price"].mean()["Other / Non-Disclosed"]
gender_avg_lst = [male_avg, female_avg, other_avg]

# -----------------------------------------
# Total Purchase Value
male_revenue = by_gender["Price"].sum()["Male"]
female_revenue = by_gender["Price"].sum()["Female"]
other_revenue = by_gender["Price"].sum()["Other / Non-Disclosed"]
gender_revenue_lst = [male_revenue, female_revenue, other_revenue]

# -----------------------------------------
# Normalized Totals
male_norm = male_revenue / by_gender["SN"].nunique()["Male"]
female_norm = female_revenue / by_gender["SN"].nunique()["Female"]
other_norm = other_revenue / by_gender["SN"].nunique()["Other / Non-Disclosed"]
gender_norm_lst = [male_norm, female_norm, other_norm]

# -----------------------------------------
# Set up the DataFrame
analysis_gender = pd.DataFrame({
    "Gender": ["Male", "Female", "Other / Non-Disclosed"],
    "Purchase Count":gender_purch_lst,
    "Average Purchase Price": gender_avg_lst,
    "Total Purchase Value": gender_revenue_lst,
    "Normalized Totals": gender_norm_lst
})

# Map the columns
analysis_gender["Average Purchase Price"] = analysis_gender["Average Purchase Price"].map(dollarize)
analysis_gender["Normalized Totals"] = analysis_gender["Normalized Totals"].map(dollarize)
analysis_gender["Total Purchase Value"] = analysis_gender["Total Purchase Value"].map(dollarize)

# Set Gender to index
analysis_gender.set_index("Gender", inplace=True)

# Rearrange columns
analysis_gender = analysis_gender[[
    "Purchase Count",
    "Average Purchase Price",
    "Total Purchase Value",
    "Normalized Totals"
]]

# Show it
analysis_gender
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
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
      <th>Male</th>
      <td>633</td>
      <td>$2.95</td>
      <td>$1867.68</td>
      <td>$4.02</td>
    </tr>
    <tr>
      <th>Female</th>
      <td>136</td>
      <td>$2.82</td>
      <td>$382.91</td>
      <td>$3.83</td>
    </tr>
    <tr>
      <th>Other / Non-Disclosed</th>
      <td>11</td>
      <td>$3.25</td>
      <td>$35.74</td>
      <td>$4.47</td>
    </tr>
  </tbody>
</table>
</div>



### Age Demographics


```python
# Age Demographics

# -----------------------------------------
# Make bins and group names
bins = [0, 9, 14, 19, 24, 29, 34, 39, 999]
age_groups = ["<10", "10-14", "15-19", "20-24", 
               "25-29", "30-34", "35-39", "40+"]

# Cut it up and apply Age Group to each row
file_df["Age Group"] = pd.cut(file_df["Age"], bins, 
                              labels=age_groups)

# Group data by Age Group
by_age = file_df.groupby("Age Group")

# -----------------------------------------
# Purchase Count
age_purch_lst = [
    by_age["Price"].count()["<10"],
    by_age["Price"].count()["10-14"],
    by_age["Price"].count()["15-19"],
    by_age["Price"].count()["20-24"],
    by_age["Price"].count()["25-29"],
    by_age["Price"].count()["30-34"],
    by_age["Price"].count()["35-39"],
    by_age["Price"].count()["40+"]
]

# -----------------------------------------
# Average Purchase Price
age_avg_lst = [
    by_age["Price"].mean()["<10"],
    by_age["Price"].mean()["10-14"],
    by_age["Price"].mean()["15-19"],
    by_age["Price"].mean()["20-24"],
    by_age["Price"].mean()["25-29"],
    by_age["Price"].mean()["30-34"],
    by_age["Price"].mean()["35-39"],
    by_age["Price"].mean()["40+"]
]

# -----------------------------------------
# Total Purchase Value
age_revenue_lst = [
    by_age["Price"].sum()["<10"],
    by_age["Price"].sum()["10-14"],
    by_age["Price"].sum()["15-19"],
    by_age["Price"].sum()["20-24"],
    by_age["Price"].sum()["25-29"],
    by_age["Price"].sum()["30-34"],
    by_age["Price"].sum()["35-39"],
    by_age["Price"].sum()["40+"]
]

# -----------------------------------------
# Normalized Totals
age_norm_lst = [
    by_age["Price"].sum()["<10"] / by_age["SN"].nunique()["<10"],
    by_age["Price"].sum()["10-14"] / by_age["SN"].nunique()["10-14"],
    by_age["Price"].sum()["15-19"] / by_age["SN"].nunique()["15-19"],
    by_age["Price"].sum()["20-24"] / by_age["SN"].nunique()["20-24"],
    by_age["Price"].sum()["25-29"] / by_age["SN"].nunique()["25-29"],
    by_age["Price"].sum()["30-34"] / by_age["SN"].nunique()["30-34"],
    by_age["Price"].sum()["35-39"] / by_age["SN"].nunique()["35-39"],
    by_age["Price"].sum()["40+"] / by_age["SN"].nunique()["40+"]
]

# -----------------------------------------
# Set up the DataFrame
age_demo = pd.DataFrame({
    "": age_groups,
    "Purchase Count":age_purch_lst,
    "Average Purchase Price": age_avg_lst,
    "Total Purchase Value": age_revenue_lst,
    "Normalized Totals": age_norm_lst
})

# Map the columns
age_demo["Average Purchase Price"] = age_demo["Average Purchase Price"].map(dollarize)
age_demo["Normalized Totals"] = age_demo["Normalized Totals"].map(dollarize)
age_demo["Total Purchase Value"] = age_demo["Total Purchase Value"].map(dollarize)

# Set Gender to index
age_demo.set_index("", inplace=True)

# Rearrange columns
age_demo = age_demo[[
    "Purchase Count",
    "Average Purchase Price",
    "Total Purchase Value",
    "Normalized Totals"
]]

# Show it
age_demo

```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
      <th>Normalized Totals</th>
    </tr>
    <tr>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>&lt;10</th>
      <td>28</td>
      <td>$2.98</td>
      <td>$83.46</td>
      <td>$4.39</td>
    </tr>
    <tr>
      <th>10-14</th>
      <td>35</td>
      <td>$2.77</td>
      <td>$96.95</td>
      <td>$4.22</td>
    </tr>
    <tr>
      <th>15-19</th>
      <td>133</td>
      <td>$2.91</td>
      <td>$386.42</td>
      <td>$3.86</td>
    </tr>
    <tr>
      <th>20-24</th>
      <td>336</td>
      <td>$2.91</td>
      <td>$978.77</td>
      <td>$3.78</td>
    </tr>
    <tr>
      <th>25-29</th>
      <td>125</td>
      <td>$2.96</td>
      <td>$370.33</td>
      <td>$4.26</td>
    </tr>
    <tr>
      <th>30-34</th>
      <td>64</td>
      <td>$3.08</td>
      <td>$197.25</td>
      <td>$4.2</td>
    </tr>
    <tr>
      <th>35-39</th>
      <td>42</td>
      <td>$2.84</td>
      <td>$119.4</td>
      <td>$4.42</td>
    </tr>
    <tr>
      <th>40+</th>
      <td>17</td>
      <td>$3.16</td>
      <td>$53.75</td>
      <td>$4.89</td>
    </tr>
  </tbody>
</table>
</div>



### Top Spenders


```python
# Top 5 spenders
# Group the data by Screen Names
by_sn = file_df.groupby("SN")

# -----------------------------------------
# Purchase Count
ballers_purc_lst = by_sn["Price"].count()

# -----------------------------------------
# Average Purchase Value
ballers_avg_lst = by_sn["Price"].mean()

# -----------------------------------------
# Total Purchase Value
ballers_revenue_lst = by_sn["Price"].sum()

# -----------------------------------------
# Build the data frame
top_spenders = pd.DataFrame({
    "Purchase Count": ballers_purc_lst,
    "Average Purchase Price": ballers_avg_lst,
    "Total Purchase Value": ballers_revenue_lst
})

# Sort it by Total Purchase Value and only keep the top 5
top_spenders.sort_values(by="Total Purchase Value", 
                         inplace=True, ascending=False)
top_spenders = top_spenders[0:5]

# Dollarize the dollar amounts
top_spenders["Average Purchase Price"] = top_spenders["Average Purchase Price"].map(dollarize)
top_spenders["Total Purchase Value"] = top_spenders["Total Purchase Value"].map(dollarize)

# Reorder columns
top_spenders = top_spenders[["Purchase Count", "Average Purchase Price", "Total Purchase Value"]]

# Show it
top_spenders
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Purchase Count</th>
      <th>Average Purchase Price</th>
      <th>Total Purchase Value</th>
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
      <td>5</td>
      <td>$3.41</td>
      <td>$17.06</td>
    </tr>
    <tr>
      <th>Saedue76</th>
      <td>4</td>
      <td>$3.39</td>
      <td>$13.56</td>
    </tr>
    <tr>
      <th>Mindimnya67</th>
      <td>4</td>
      <td>$3.18</td>
      <td>$12.74</td>
    </tr>
    <tr>
      <th>Haellysu29</th>
      <td>3</td>
      <td>$4.24</td>
      <td>$12.73</td>
    </tr>
    <tr>
      <th>Eoda93</th>
      <td>3</td>
      <td>$3.86</td>
      <td>$11.58</td>
    </tr>
  </tbody>
</table>
</div>



### Most Popular Items


```python
# Most Popular Items

# -----------------------------------------
# Group by Item ID and Item Name
by_item = file_df.groupby(["Item ID", "Item Name"])

# -----------------------------------------
# Purchase Count
# Not really necessary because of the way I'm making the df
# item_purc_lst = by_item["Price"].count()

# -----------------------------------------
# Item Price
item_price_lst = by_item["Price"].mean()

# -----------------------------------------
# Total Purchase Value
item_revenue_lst = by_item["Price"].sum()

# -----------------------------------------
# Make the base data frame, which will be used in the next part too
items_df = pd.DataFrame(by_item["Price"].count())

# Change Price column name to Purchase Count
items_df.rename(columns={"Price": "Purchase Count"}, inplace=True)

# Add the other columns
items_df["Item Price"] = item_price_lst
items_df["Total Purchase Value"] = item_revenue_lst

# -----------------------------------------
# Make new data frame sorted by Purchase Count
# Secondary sort by Total Purchase Value to break ties
most_popular_items = items_df.sort_values(["Purchase Count", "Total Purchase Value"], ascending=False)
most_popular_items = most_popular_items[0:5]

# Dollarize the dollar amounts
most_popular_items["Item Price"] = most_popular_items["Item Price"].map(dollarize)
most_popular_items["Total Purchase Value"] = most_popular_items["Total Purchase Value"].map(dollarize)

# Show it
most_popular_items
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>39</th>
      <th>Betrayal, Whisper of Grieving Widows</th>
      <td>11</td>
      <td>$2.35</td>
      <td>$25.85</td>
    </tr>
    <tr>
      <th>84</th>
      <th>Arcane Gem</th>
      <td>11</td>
      <td>$2.23</td>
      <td>$24.53</td>
    </tr>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <td>9</td>
      <td>$4.14</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>31</th>
      <th>Trickster</th>
      <td>9</td>
      <td>$2.07</td>
      <td>$18.63</td>
    </tr>
    <tr>
      <th>13</th>
      <th>Serenity</th>
      <td>9</td>
      <td>$1.49</td>
      <td>$13.41</td>
    </tr>
  </tbody>
</table>
</div>



### Most Profitable Items


```python
# Most Profitable Items
# -----------------------------------------
# Make new data frame sorted by Total Purchase Value
most_profitable_items = items_df.sort_values("Total Purchase Value", ascending=False)
most_profitable_items = most_profitable_items[0:5]

# Dollarize the dollar amounts
most_profitable_items["Item Price"] = most_profitable_items["Item Price"].map(dollarize)
most_profitable_items["Total Purchase Value"] = most_profitable_items["Total Purchase Value"].map(dollarize)

# Show it
most_profitable_items
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th></th>
      <th>Purchase Count</th>
      <th>Item Price</th>
      <th>Total Purchase Value</th>
    </tr>
    <tr>
      <th>Item ID</th>
      <th>Item Name</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>34</th>
      <th>Retribution Axe</th>
      <td>9</td>
      <td>$4.14</td>
      <td>$37.26</td>
    </tr>
    <tr>
      <th>115</th>
      <th>Spectral Diamond Doomblade</th>
      <td>7</td>
      <td>$4.25</td>
      <td>$29.75</td>
    </tr>
    <tr>
      <th>32</th>
      <th>Orenmir</th>
      <td>6</td>
      <td>$4.95</td>
      <td>$29.7</td>
    </tr>
    <tr>
      <th>103</th>
      <th>Singed Scalpel</th>
      <td>6</td>
      <td>$4.87</td>
      <td>$29.22</td>
    </tr>
    <tr>
      <th>107</th>
      <th>Splitter, Foe Of Subtlety</th>
      <td>8</td>
      <td>$3.61</td>
      <td>$28.88</td>
    </tr>
  </tbody>
</table>
</div>


