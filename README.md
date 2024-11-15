# Athletic Sales Analysis

## 1. Combine and Clean the Data

Import CSVs

```python
# Read the CSV files into DataFrames.
sales_2020_df = pd.read_csv("Resources/athletic_sales_2020.csv")
sales_2021_df = pd.read_csv("Resources/athletic_sales_2021.csv")
```

```python
# Display the 2020 sales DataFrame
sales_2020_df
```

<figure>
    <img src="images\img1.png">
</figure>


```python
# Display the 2021 sales DataFrame
sales_2021_df
```

<figure>
    <img src="images\img2.png">
</figure>

### Check the data types of each DataFrame

```python
# Check the 2020 sales data types.
sales_2020_df.info()
```

<figure>
    <img src="images\img3.png">
</figure>

```python
# Check the 2021 sales data types.
sales_2021_df.info()
```

<figure>
    <img src="images\img4.png">
</figure>

### Combine the sales data by rows.

```python
# Combine the 2020 and 2021 sales DataFrames on the rows and reset the index.
sales_20_21_df = pd.concat([sales_2020_df, sales_2021_df], ignore_index=True).sort_index()
sales_20_21_df
```

<figure>
    <img src="images\img5.png">
</figure>

```python
# Check if any values are null.
sales_20_21_df.isna().sum()
```

<figure>
    <img src="images\img6.png">
</figure>

```python
# Check the data type of each column
sales_20_21_df.info()
```

<figure>
    <img src="images\img7.png">
</figure>

```python
# Convert the "invoice_date" to a datetime datatype
sales_20_21_df["invoice_date"] = sales_20_21_df["invoice_date"].astype("datetime64[ns]")
sales_20_21_df
```

<figure>
    <img src="images\img8.png">
</figure>

```python
# Confirm that the "invoice_date" data type has been changed.
sales_20_21_df.info()
```

<figure>
    <img src="images\img9.png">
</figure>

## 2. Determine which Region Sold the Most Products

### Using groupby

```python
# Show the number products sold for region, state, and city.
# Rename the sum to "Total_Products_Sold".
most_prd_sold = (
    sales_20_21_df.groupby(["region", "state", "city"])
    .apply(
        lambda x: pd.Series({"Total_Products_Sold": x["units_sold"].sum()}),
        include_groups=False,
    )
    .sort_values("Total_Products_Sold", ascending=False)
)

# Show the top 5 results.
most_prd_sold.head()
```

<figure>
    <img src="images\img10.png">
</figure>

### Using pivot_table


```python
# Show the number products sold for region, state, and city.

# Rename the "units_sold" column to "Total_Products_Sold"

pivot_total_sales = pd.pivot_table(
    sales_20_21_df,
    index=["region", "state", "city"],
    values="units_sold",
    aggfunc="sum",
).sort_values("units_sold", ascending=False)


# Show the top 5 results.
pivot_total_sales.rename(columns={"units_sold": "Total_Products_Sold"}).head(5)
```

<figure>
    <img src="images\img11.png">
</figure>

## 3. Determine which Region had the Most Sales

### Using groupby

```python
# Show the total sales for the products sold for each region, state, and city.
# Rename the "total_sales" column to "Total Sales"
total_sales = (
    sales_20_21_df.groupby(["region", "state", "city"])
    .apply(
        lambda x: pd.Series({"Total_Sales": x["total_sales"].sum()}),
        include_groups=False,
    )
    .sort_values("Total_Sales", ascending=False)
)


# Show the top 5 results.
total_sales.head()
```

<figure>
    <img src="images\img12.png">
</figure>

### Using pivot_table


```python
# Show the total sales for the products sold for each region, state, and city.

# Optional: Rename the "total_sales" column to "Total Sales"
pivot_total_sales = pd.pivot_table(
    sales_20_21_df,
    index=["region", "state", "city"],
    values="total_sales",
    aggfunc="sum",
).sort_values("total_sales", ascending=False)


# Show the top 5 results.
pivot_total_sales.rename(columns={"total_sales": "Total_Sales"}).head(5)    
```

<figure>
    <img src="images\img12.png">
</figure>

## 4. Determine which Retailer had the Most Sales

### Using groupby

```python
# Show the total sales for the products sold for each retailer, region, state, and city.
# Rename the "total_sales" column to "Total Sales"
total_retailer_sales = (
    sales_20_21_df.groupby(["retailer", "region", "state", "city"])
    .apply(
        lambda x: pd.Series({"Total_Sales": x["total_sales"].sum()}),
        include_groups=False,
    )
    .sort_values("Total_Sales", ascending=False)
)


# Show the top 5 results.
total_retailer_sales.head()
```

<figure>
    <img src="images\img13.png">
</figure>

### Using pivot_table


```python
# Show the total sales for the products sold for each retailer, region, state, and city.

# Optional: Rename the "total_sales" column to "Total Sales"

pivot_total_sales = pd.pivot_table(
    sales_20_21_df,
    index=["retailer", "region", "state", "city"],
    values="total_sales",
    aggfunc=("sum"),
).sort_values("total_sales", ascending=False)


# Show the top 5 results.
pivot_total_sales.rename(columns={"total_sales": "Total_Sales"}).head(5)
```

<figure>
    <img src="images\img13.png">
</figure>


## 5. Determine which Retailer Sold the Most Women's Athletic Footwear


```python
# Filter the sales data to get the women's athletic footwear sales data.

# Results can be obtained either way
# womans_athletic_fw_sales = sales_20_21.loc[(sales_20_21["product"] == "Women's Athletic Footwear")]
# or below
womans_athletic_fw_sales = sales_20_21_df.groupby("product").get_group(
    "Women's Athletic Footwear"
)
womans_athletic_fw_sales
```

<figure>
    <img src="images\img14.png">
</figure>

### Using groupby


```python
# Show the total number of women's athletic footwear sold for each retailer, region, state, and city.
# Rename the "units_sold" column to "Womens_Footwear_Units_Sold"

womans_athletic_fw_sales = sales_20_21_df.loc[
    (sales_20_21_df["product"] == "Women's Athletic Footwear")
]

womans_athletic_sold = (
    womans_athletic_fw_sales.groupby(["retailer", "region", "state", "city"]).apply(
        lambda x: pd.Series({"Womens_Footwear_Units_Sold": x["units_sold"].sum()}),
        include_groups=False,
    )
).sort_values(by="Womens_Footwear_Units_Sold", ascending=False)


# Show the top 5 results.
womans_athletic_sold.head()
```

<figure>
    <img src="images\img15.png">
</figure>

### Using pivot_table


```python
# Show the total number of women's athletic footwear sold for each retailer, region, state, and city.
# Rename the "units_sold" column to "Womens_Footwear_Units_Sold"

womans_athletic_fw_sales = sales_20_21_df.loc[
    (sales_20_21_df["product"] == "Women's Athletic Footwear")
]

pivot_woman_units_sold = pd.pivot_table(
    womans_athletic_fw_sales,
    index=["retailer", "region", "state", "city"],
    values="units_sold",
    aggfunc="sum",
).sort_values("units_sold", ascending=False)


# Show the top 5 results.
pivot_woman_units_sold.rename(
    columns={"units_sold": "Womens_Footwear_Units_Sold"}
).head(5)
```

<figure>
    <img src="images\img15.png">
</figure>

## 6. Determine the Day with the Most Women's Athletic Footwear Sales


```python
# Create a pivot table with the 'invoice_date' column is the index, and the "total_sales" as the values.
pivot_tbl = (
    pd.pivot_table(
        womans_athletic_fw_sales,
        index=["invoice_date"],
        values=["total_sales"],
        aggfunc="sum",
    )
    .sort_values("invoice_date")
    .rename(columns={"total_sales": "Total_Sales"})
)


# Show the table.
pivot_tbl.head(10)
```

<figure>
    <img src="images\img16.png">
</figure>


```python
# Resample the pivot table into daily bins, and get the total sales for each day.
resample_tot_sales = pivot_tbl.resample("d").sum()

# Sort the resampled pivot table in descending order on "Total Sales".
resample_tot_sales.sort_values("Total_Sales", ascending=False).head(10)
```

<figure>
    <img src="images\img17.png">
</figure>

## 7. Determine the Week with the Most Women's Athletic Footwear Sales


```python
# Resample the pivot table into weekly bins, and get the total sales for each week.
resample_tot_sales = pivot_tbl.resample("W").sum()


# Sort the resampled pivot table in descending order on "Total Sales".
resample_tot_sales.sort_values("Total_Sales", ascending=False).head(10)
```

<figure>
    <img src="images\img18.png">
</figure>