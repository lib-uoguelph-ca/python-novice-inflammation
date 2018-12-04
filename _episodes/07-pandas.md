---
title: "Working with DataFrames"
teaching: 30
exercises: 25
questions:
- "How can I do statistical analysis of tabular data?"
objectives:
- "Import the Pandas library."
- "Use Pandas to load a simple CSV data set."
- "Select individual values from a Pandas dataframe."
- "Select entire rows or entire columns from a dataframe."
- "Select a subset of rows and columns from a dataframe"
keypoints:
- "Use `DataFrame.describe` to get summary statistics about data."
- "Use `DataFrame.iloc[..., ...]` to select values by integer location."
- "Use `:` on its own to mean all columns or all rows."
- "Select multiple columns or rows using `DataFrame.loc` and a named slice."
- "Result of slicing can be used in further operations."
- "Use comparisons to select data based on value."
- "Select values or NaN using a Boolean mask."

---

## Analyzing Tabular Data With Pandas

Pandas is a widely-used Python library for statistics, particularly on tabular data. If you're familiar with R, you'll notice many similarities, since Pandas follows the same patterns.
The star of the Pandas library is the DataFrame type. A DataFrame represents a 2-dimensional table with named columns and rows. A big advantage of DataFrames over numpy array is that a DataFrame can have have different data types for each column.
On top of that, Pandas DataFrames provide a lot of extra functionality to make manipulating and working with your data easier.

~~~
import pandas

data = pandas.read_csv('data/gapminder_gdp_oceania.csv')
print(data)
~~~
{: .python}
~~~
       country  gdpPercap_1952  gdpPercap_1957  gdpPercap_1962  \
0    Australia     10039.59564     10949.64959     12217.22686
1  New Zealand     10556.57566     12247.39532     13175.67800

   gdpPercap_1967  gdpPercap_1972  gdpPercap_1977  gdpPercap_1982  \
0     14526.12465     16788.62948     18334.19751     19477.00928
1     14463.91893     16046.03728     16233.71770     17632.41040

   gdpPercap_1987  gdpPercap_1992  gdpPercap_1997  gdpPercap_2002  \
0     21888.88903     23424.76683     26997.93657     30687.75473
1     19007.19129     18363.32494     21050.41377     23189.80135

   gdpPercap_2007
0     34435.36744
1     25185.00911
~~~
{: .output}

Here we use the read_csv function to read in a file from our data directory. If we print our DataFrame we can see that the columns in a DataFrame contain the observed variables, and the rows contain the observations.
Note that since our dataset has so many columns, Pandas will use a backslash `\` to show that it has wrapped lines when output is too wide to fit the screen.

## Named Collumns and Rows

One of the great benefits of using pandas DataFrames is that DataFrames have support for row and column names.

In our data above, we can see that the columns include the name of the variable. Similarly, we can see that the rows have been indexed with integers. If we prefer, we can provide another column to use as the row names:

~~~
data = pandas.read_csv('data/gapminder_gdp_oceania.csv', index_col='country')
print(data)
~~~
{: .python}
~~~
             gdpPercap_1952  gdpPercap_1957  gdpPercap_1962  gdpPercap_1967  \
country
Australia       10039.59564     10949.64959     12217.22686     14526.12465
New Zealand     10556.57566     12247.39532     13175.67800     14463.91893

             gdpPercap_1972  gdpPercap_1977  gdpPercap_1982  gdpPercap_1987  \
country
Australia       16788.62948     18334.19751     19477.00928     21888.88903
New Zealand     16046.03728     16233.71770     17632.41040     19007.19129

             gdpPercap_1992  gdpPercap_1997  gdpPercap_2002  gdpPercap_2007
country
Australia       23424.76683     26997.93657     30687.75473     34435.36744
New Zealand     18363.32494     21050.41377     23189.80135     25185.00911
~~~
{: .output}


## Use `DataFrame.info` to find out more about a DataFrame.

~~~
data.info()
~~~
{: .python}
~~~
<class 'pandas.core.frame.DataFrame'>
Index: 2 entries, Australia to New Zealand
Data columns (total 12 columns):
gdpPercap_1952    2 non-null float64
gdpPercap_1957    2 non-null float64
gdpPercap_1962    2 non-null float64
gdpPercap_1967    2 non-null float64
gdpPercap_1972    2 non-null float64
gdpPercap_1977    2 non-null float64
gdpPercap_1982    2 non-null float64
gdpPercap_1987    2 non-null float64
gdpPercap_1992    2 non-null float64
gdpPercap_1997    2 non-null float64
gdpPercap_2002    2 non-null float64
gdpPercap_2007    2 non-null float64
dtypes: float64(12)
memory usage: 208.0+ bytes
~~~
{: .output}

Here we can see a variety of information about our dataset:
*   This is a `DataFrame`
*   It contains two rows named `'Australia'` and `'New Zealand'`
*   It has twelve columns, each of which has two actual 64-bit floating point values.
    *   We will talk later about null values, which are used to represent missing observations.
*   Uses 208 bytes of memory.

## Transposing a DataFrame

Occasionally, it's useful to treat columns as rows and vice versa. This is done so commonly that the DataFrame comes with a function to do just that:

~~~
print(data.transpose())
~~~
{: .python}
~~~
country           Australia  New Zealand
gdpPercap_1952  10039.59564  10556.57566
gdpPercap_1957  10949.64959  12247.39532
gdpPercap_1962  12217.22686  13175.67800
gdpPercap_1967  14526.12465  14463.91893
gdpPercap_1972  16788.62948  16046.03728
gdpPercap_1977  18334.19751  16233.71770
gdpPercap_1982  19477.00928  17632.41040
gdpPercap_1987  21888.88903  19007.19129
gdpPercap_1992  23424.76683  18363.32494
gdpPercap_1997  26997.93657  21050.41377
gdpPercap_2002  30687.75473  23189.80135
gdpPercap_2007  34435.36744  25185.00911
~~~
{: .output}

There's another shorthand to do this, which is exposed as the T attribute: `data.T`. I prefer to call the `transpose()` function directly because it's more meaningful to the reader.

## Generating Summary Statistics

Another common operation for DataFrames is to generate summary statistics for each column. The `DataFrame.describe()` function gets the summary statistics of the columns that have numerical data.
All other columns are ignored, unless you use the argument `include='all'`.
~~~
print(data.describe())
~~~
{: .python}
~~~
       gdpPercap_1952  gdpPercap_1957  gdpPercap_1962  gdpPercap_1967  \
count        2.000000        2.000000        2.000000        2.000000
mean     10298.085650    11598.522455    12696.452430    14495.021790
std        365.560078      917.644806      677.727301       43.986086
min      10039.595640    10949.649590    12217.226860    14463.918930
25%      10168.840645    11274.086022    12456.839645    14479.470360
50%      10298.085650    11598.522455    12696.452430    14495.021790
75%      10427.330655    11922.958888    12936.065215    14510.573220
max      10556.575660    12247.395320    13175.678000    14526.124650

       gdpPercap_1972  gdpPercap_1977  gdpPercap_1982  gdpPercap_1987  \
count         2.00000        2.000000        2.000000        2.000000
mean      16417.33338    17283.957605    18554.709840    20448.040160
std         525.09198     1485.263517     1304.328377     2037.668013
min       16046.03728    16233.717700    17632.410400    19007.191290
25%       16231.68533    16758.837652    18093.560120    19727.615725
50%       16417.33338    17283.957605    18554.709840    20448.040160
75%       16602.98143    17809.077557    19015.859560    21168.464595
max       16788.62948    18334.197510    19477.009280    21888.889030

       gdpPercap_1992  gdpPercap_1997  gdpPercap_2002  gdpPercap_2007
count        2.000000        2.000000        2.000000        2.000000
mean     20894.045885    24024.175170    26938.778040    29810.188275
std       3578.979883     4205.533703     5301.853680     6540.991104
min      18363.324940    21050.413770    23189.801350    25185.009110
25%      19628.685413    22537.294470    25064.289695    27497.598692
50%      20894.045885    24024.175170    26938.778040    29810.188275
75%      22159.406358    25511.055870    28813.266385    32122.777857
max      23424.766830    26997.936570    30687.754730    34435.367440
~~~
{: .output}

> ## Reading Other Data
>
> Read the data in `gapminder_gdp_americas.csv`
> (which should be in the same directory as `gapminder_gdp_oceania.csv`)
> into a variable called `americas`
> and display its summary statistics.
>
> > ## Solution
> > To read in a CSV, we use `pandas.read_csv` and pass the filename 'data/gapminder_gdp_americas.csv' to it. We also once again pass the
> > column name 'country' to the parameter `index_col` in order to index by country:
> > ~~~
> > americas = pandas.read_csv('data/gapminder_gdp_americas.csv', index_col='country')
> > ~~~
> >{: .python}
> {: .solution}
{: .challenge}

> ## Inspecting Data
>
> After reading the data for the Americas,
> use `help(americas.head)` and `help(americas.tail)`
> to find out what `DataFrame.head` and `DataFrame.tail` do.
>
> 1.  What method call will display the first three rows of this data?
> 2.  What method call will display the last three columns of this data?
>     (Hint: you may need to change your view of the data.)
>
> > ## Solution
> > 1. We can check out the first five rows of `americas` by executing `americas.head()` (allowing us to view the head
> > of the DataFrame). We can specify the number of rows we wish to see by specifying the parameter `n` in our call
> > to `americas.head()`. To view the first three rows, execute:
> >
> > ~~~
> > americas.head(n=3)
> > ~~~
> >{: .python}
> >
> > The output is then
> > ~~~
> >          continent  gdpPercap_1952  gdpPercap_1957  gdpPercap_1962  \
> >country
> >Argentina  Americas     5911.315053     6856.856212     7133.166023
> >Bolivia    Americas     2677.326347     2127.686326     2180.972546
> >Brazil     Americas     2108.944355     2487.365989     3336.585802
> >
> >           gdpPercap_1967  gdpPercap_1972  gdpPercap_1977  gdpPercap_1982  \
> >country
> >Argentina     8052.953021     9443.038526    10079.026740     8997.897412
> >Bolivia       2586.886053     2980.331339     3548.097832     3156.510452
> >Brazil        3429.864357     4985.711467     6660.118654     7030.835878
> >
> >           gdpPercap_1987  gdpPercap_1992  gdpPercap_1997  gdpPercap_2002  \
> >country
> >Argentina     9139.671389     9308.418710    10967.281950     8797.640716
> >Bolivia       2753.691490     2961.699694     3326.143191     3413.262690
> >Brazil        7807.095818     6950.283021     7957.980824     8131.212843
> >
> >           gdpPercap_2007
> >country
> >Argentina    12779.379640
> >Bolivia       3822.137084
> >Brazil        9065.800825
> > ~~~
> >{: .output}
> > 2. To check out the last three rows of `americas`, we would use the command, `americas.tail(n=3)`,
> > analogous to `head()` used above. However, here we want to look at the last three columns so we need
> > to change our view and then use `tail()`. To do so, we create a new DataFrame in which rows and
> > columns are switched
> >
> > ~~~
> > americas_flipped = americas.transpose()
> > ~~~
> >{: .python}
> >
> > We can then view the last three columns of `americas` by viewing the last three rows of `americas_flipped`:
> > ~~~
> > americas_flipped.tail(n=3)
> > ~~~
> >{: .python}
> > The output is then
> > ~~~
> > country        Argentina  Bolivia   Brazil   Canada    Chile Colombia  \
> > gdpPercap_1997   10967.3  3326.14  7957.98  28954.9  10118.1  6117.36
> > gdpPercap_2002   8797.64  3413.26  8131.21    33329  10778.8  5755.26
> > gdpPercap_2007   12779.4  3822.14   9065.8  36319.2  13171.6  7006.58
> >
> > country        Costa Rica     Cuba Dominican Republic  Ecuador    ...     \
> > gdpPercap_1997    6677.05  5431.99             3614.1  7429.46    ...
> > gdpPercap_2002    7723.45  6340.65            4563.81  5773.04    ...
> > gdpPercap_2007    9645.06   8948.1            6025.37  6873.26    ...
> >
> > country          Mexico Nicaragua   Panama Paraguay     Peru Puerto Rico  \
> > gdpPercap_1997   9767.3   2253.02  7113.69   4247.4  5838.35     16999.4
> > gdpPercap_2002  10742.4   2474.55  7356.03  3783.67  5909.02     18855.6
> > gdpPercap_2007  11977.6   2749.32  9809.19  4172.84  7408.91     19328.7
> >
> > country        Trinidad and Tobago United States  Uruguay Venezuela
> > gdpPercap_1997             8792.57       35767.4  9230.24   10165.5
> > gdpPercap_2002             11460.6       39097.1     7727   8605.05
> > gdpPercap_2007             18008.5       42951.7  10611.5   11415.8
> > ~~~
> >{: .output}
> > Note: we could have done the above in a single line of code by 'chaining' the commands:
> > ~~~
> > americas.transpose().tail(n=3)
> > ~~~
> >{: .python}
> {: .solution}
{: .challenge}

> ## Writing Data
>
> As well as the `read_csv` function for reading data from a file,
> Pandas provides a `to_csv` function to write dataframes to files.
> Applying what you've learned about reading from files,
> write one of your dataframes to a file called `processed.csv`.
> You can use `help` to get information on how to use `to_csv`.
> > ## Solution
> > In order to write the DataFrame `americas` to a file called `processed.csv`, execute the following command:
> > ~~~
> > americas.to_csv('processed.csv')
> > ~~~
> >{: .python}
> > For help on `to_csv`, you could execute, for example,
> > ~~~
> > help(americas.to_csv)
> > ~~~
> >{: .python}
> > Note that `help(to_csv)` throws an error! This is a subtlety and is due to the fact that `to_csv` is NOT a function in
> > and of itself and the actual call is `americas.to_csv`.
> >
> {: .solution}
{: .challenge}

## Note about Pandas DataFrames/Series

A [DataFrame][pandas-dataframe] is a collection of [Series][pandas-series];
The DataFrame is the way Pandas represents a table, and Series is the data-structure
Pandas use to represent a column.

Pandas is built on top of the [Numpy][numpy] library, which in practice means that
most of the methods defined for Numpy Arrays apply to Pandas Series/DataFrames.

What makes Pandas so attractive is the powerful interface to access individual records
of the table, proper handling of missing values, and relational-databases operations
between DataFrames.

## Selecting values

To access a value at the position `[i,j]` of a DataFrame, we have two options, depending on
what whether we want to find a value based on its labels or its numerical index.

The term index here gets a bit overloaded because we have the concept of array indexes from vanilla python arrays (and numpy arrays) as well as the concept of the row index from the pandas DataFrame.
To be clear, in this section I'll refer to the prior as `numerical index` and the latter as `row index`.

## Selecting Values Using Their Numerical Index

We can use `DataFrame.iloc[i,j]` to specify location by numerical index the same way we would with a numpy array:

~~~
import pandas
data = pandas.read_csv('data/gapminder_gdp_europe.csv', index_col='country')
print(data.iloc[0, 0])
~~~
{: .language-python}
~~~
1601.056136
~~~
{: .output}

## Selecting Values Using Their Label

If you'd rather select values by their label (and row index), you can do DataFrame.loc[i, j].

~~~
print(data.loc["Albania", "gdpPercap_1952"])
~~~
{: .language-python}
~~~
1601.056136
~~~
{: .output}

## Selecting Rows and Columns

If you'd like to select full rows or columns from your DataFrame, you can use the same notation `:` that we used with Numpy arrays:

~~~
print(data.loc["Albania", :])
~~~
{: .language-python}
~~~
gdpPercap_1952    1601.056136
gdpPercap_1957    1942.284244
gdpPercap_1962    2312.888958
gdpPercap_1967    2760.196931
gdpPercap_1972    3313.422188
gdpPercap_1977    3533.003910
gdpPercap_1982    3630.880722
gdpPercap_1987    3738.932735
gdpPercap_1992    2497.437901
gdpPercap_1997    3193.054604
gdpPercap_2002    4604.211737
gdpPercap_2007    5937.029526
Name: Albania, dtype: float64
~~~
{: .output}

You would get the same result printing `data.loc["Albania"]` (without a second index).

~~~
print(data.loc[:, "gdpPercap_1952"])
~~~
{: .language-python}
~~~
country
Albania                    1601.056136
Austria                    6137.076492
Belgium                    8343.105127
⋮ ⋮ ⋮
Switzerland               14734.232750
Turkey                     1969.100980
United Kingdom             9979.508487
Name: gdpPercap_1952, dtype: float64
~~~
{: .output}

Since we work with columns so often in this kind of work you can also take a short cut to refer to a column by its label.
~~~
print(data["gdpPercap_1952"])
~~~
{: .language-python}
And if your column label doesn't have any spaces or special characters you can even get result printing `data.gdpPercap_1952` (since it's a column name)
~~~
print(data.gdpPercap_1952)
~~~
{: .language-python}

## Selecting Multiple Columns

We can use the same slicing notation that we used earlier:

~~~
print(data.loc['Italy':'Poland', 'gdpPercap_1962':'gdpPercap_1972'])
~~~
{: .language-python}
~~~
             gdpPercap_1962  gdpPercap_1967  gdpPercap_1972
country
Italy           8243.582340    10022.401310    12269.273780
Montenegro      4649.593785     5907.850937     7778.414017
Netherlands    12790.849560    15363.251360    18794.745670
Norway         13450.401510    16361.876470    18965.055510
Poland          5338.752143     6557.152776     8006.506993
~~~
{: .output}

In the above code, we discover that **slicing using `loc` is inclusive at both
ends**, which differs from **slicing using `iloc`**, where slicing indicates
everything up to but not including the final index.

We can also select individual columns, even if they're not arranged side by side:

~~~
print(data.loc[['Italy','Albania'], ['gdpPercap_1952', 'gdpPercap_1972']])
~~~
{: .language-python}
~~~
         gdpPercap_1952  gdpPercap_1972
country
Italy       4931.404155    12269.273780
Albania     1601.056136     3313.422188
~~~
{: .output}

## Pandas and Numpy

Usually we won't just print a slice. Instead, we'll use it as the basis to do some other form of computation. Fortunately, numpy and pandas are designed to work together, so we can use the same numpy methods with our Dataframes:

~~~
print(numpy.min(data.loc['Italy':'Poland', 'gdpPercap_1962':'gdpPercap_1972']))
~~~
{: .language-python}
~~~
gdpPercap_1962    13450.40151
gdpPercap_1967    16361.87647
gdpPercap_1972    18965.05551
dtype: float64
~~~
{: .output}

## Select Data Based on Value
Often, it's useful to be able to subset our data based on some condition. For example, how would I generate a subset of the data where the GDP is greater than some value?
If you do a conditional statement with a pandas DataFrame, the result will be a DataFrame where each element is the result of that conditional statement.

~~~
# Let's use a subset of data to keep output readable.
subset = data.loc['Italy':'Poland', 'gdpPercap_1962':'gdpPercap_1972']
print('Subset of data:\n', subset)

# Which values were greater than 10000 ?
print('\nWhere are values large?\n', subset > 10000)
~~~
{: .language-python}
~~~
Subset of data:
             gdpPercap_1962  gdpPercap_1967  gdpPercap_1972
country
Italy           8243.582340    10022.401310    12269.273780
Montenegro      4649.593785     5907.850937     7778.414017
Netherlands    12790.849560    15363.251360    18794.745670
Norway         13450.401510    16361.876470    18965.055510
Poland          5338.752143     6557.152776     8006.506993

Where are values large?
            gdpPercap_1962 gdpPercap_1967 gdpPercap_1972
country
Italy                False           True           True
Montenegro           False          False          False
Netherlands           True           True           True
Norway                True           True           True
Poland               False          False          False
~~~
{: .output}

## Select values or NaN using a Boolean mask.

This is neat, but it would really be useful if we could actually subset our data with it. Fortunately, we can! A frame full of Booleans is sometimes called a *mask* because of how it can be used.

~~~
mask = subset > 10000
print(subset[mask])
~~~
{: .language-python}
~~~
             gdpPercap_1962  gdpPercap_1967  gdpPercap_1972
country
Italy                   NaN     10022.40131     12269.27378
Montenegro              NaN             NaN             NaN
Netherlands     12790.84956     15363.25136     18794.74567
Norway          13450.40151     16361.87647     18965.05551
Poland                  NaN             NaN             NaN
~~~
{: .output}

So the mask will produce a DataFrame where the values that meet our condition are there, and the other values are replaced with NaN (Not a Number). This is useful because NaNs are ignored by operations like max, min, average, etc.

~~~
print(subset[subset > 10000].describe())
~~~
{: .language-python}
~~~
       gdpPercap_1962  gdpPercap_1967  gdpPercap_1972
count        2.000000        3.000000        3.000000
mean     13120.625535    13915.843047    16676.358320
std        466.373656     3408.589070     3817.597015
min      12790.849560    10022.401310    12269.273780
25%      12955.737547    12692.826335    15532.009725
50%      13120.625535    15363.251360    18794.745670
75%      13285.513523    15862.563915    18879.900590
max      13450.401510    16361.876470    18965.055510
~~~
{: .output}

> ## Selection of Individual Values
>
> Assume Pandas has been imported into your notebook
> and the Gapminder GDP data for Europe has been loaded:
>
> ~~~
> import pandas
>
> df = pandas.read_csv('data/gapminder_gdp_europe.csv', index_col='country')
> ~~~
> {: .language-python}
>
> Write an expression to find the Per Capita GDP of Serbia in 2007.
{: .challenge}
>
> > ## Solution
> > The selection can be done by using the labels for both the row ("Serbia") and the column ("gdpPercap_2007"):
> > ~~~
> > print(df.loc['Serbia', 'gdpPercap_2007'])
> > ~~~
> > {: .language-python}
> > The output is
> > ~~~
> > 9786.534714
> > ~~~
> >{: .output}
> {: .solution}
{: .challenge}

> ## Extent of Slicing
>
> 1.  Do the two statements below produce the same output?
> 2.  Based on this,
>     what rule governs what is included (or not) in numerical slices and named slices in Pandas?
>
> ~~~
> print(data.iloc[0:2, 0:2])
> print(data.loc['Albania':'Belgium', 'gdpPercap_1952':'gdpPercap_1962'])
> ~~~
> {: .language-python}
>
{: .challenge}
>
> > ## Solution
> > No, they do not produce the same output! The output of the first statement is:
> > ~~~
> >         gdpPercap_1952  gdpPercap_1957
> > country
> > Albania     1601.056136     1942.284244
> > Austria     6137.076492     8842.598030
> > ~~~
> >{: .output}
> > The second statement gives:
> > ~~~
> >         gdpPercap_1952  gdpPercap_1957  gdpPercap_1962
> > country
> > Albania     1601.056136     1942.284244     2312.888958
> > Austria     6137.076492     8842.598030    10750.721110
> > Belgium     8343.105127     9714.960623    10991.206760
> > ~~~
> >{: .output}
> > Clearly, the second statement produces an additional column and an additional row compared to the first statement.
> > What conclusion can we draw? We see that a numerical slice, 0:2, *omits* the final index (i.e. index 2)
> > in the range provided,
> > while a named slice, 'gdpPercap_1952':'gdpPercap_1962', *includes* the final element.
> {: .solution}
{: .challenge}

> ## Reconstructing Data
>
> Explain what each line in the following short program does:
> what is in `first`, `second`, etc.?
>
> ~~~
> first = pandas.read_csv('data/gapminder_all.csv', index_col='country')
> second = first[first['continent'] == 'Americas']
> third = second.drop('Puerto Rico')
> fourth = third.drop('continent', axis = 1)
> fourth.to_csv('result.csv')
> ~~~
> {: .language-python}
{: .challenge}
>
> > ## Solution
> > Let's go through this piece of code line by line.
> > ~~~
> > first = pandas.read_csv('data/gapminder_all.csv', index_col='country')
> > ~~~
> > {: .language-python}
> > This line loads the dataset containing the GDP data from all countries into a dataframe called
> > `first`. The `index_col='country'` parameter selects which column to use as the
> > row labels in the dataframe.
> > ~~~
> > second = first[first['continent'] == 'Americas']
> > ~~~
> > {: .language-python}
> > This line makes a selection: only those rows of `first` for which the 'continent' column matches
> > 'Americas' are extracted. Notice how the Boolean expression inside the brackets,
> > `first['continent'] == 'Americas'`, is used to select only those rows where the expression is true.
> > Try printing this expression! Can you print also its individual True/False elements?
> > (hint: first assign the expression to a variable)
> > ~~~
> > third = second.drop('Puerto Rico')
> > ~~~
> > {: .language-python}
> > As the syntax suggests, this line drops the row from `second` where the label is 'Puerto Rico'. The
> > resulting dataframe `third` has one row less than the original dataframe `second`.
> > ~~~
> > fourth = third.drop('continent', axis = 1)
> > ~~~
> > {: .language-python}
> > Again we apply the drop function, but in this case we are dropping not a row but a whole column.
> > To accomplish this, we need to specify also the `axis` parameter (we want to drop the second column
> > which has index 1).
> > ~~~
> > fourth.to_csv('result.csv')
> > ~~~
> > {: .language-python}
> > The final step is to write the data that we have been working on to a csv file. Pandas makes this easy
> > with the `to_csv()` function. The only required argument to the function is the filename. Note that the
> > file will be written in the directory from which you started the Jupyter or Python session.
> {: .solution}
{: .challenge}

> ## Selecting Indices
>
> Explain in simple terms what `idxmin` and `idxmax` do in the short program below.
> When would you use these methods?
>
> ~~~
> data = pandas.read_csv('data/gapminder_gdp_europe.csv', index_col='country')
> print(data.idxmin())
> print(data.idxmax())
> ~~~
> {: .language-python}
{: .challenge}
>
> > ## Solution
> > For each column in `data`, `idxmin` will return the index value corresponding to each column's minimum;
> > `idxmax` will do accordingly the same for each column's maximum value.
> >
> > You can use these functions whenever you want to get the row index of the minimum/maximum value and not the actual minimum/maximum value.
> {: .solution}
{: .challenge}

> ## Practice with Selection
>
> Assume Pandas has been imported and the Gapminder GDP data for Europe has been loaded.
> Write an expression to select each of the following:
>
> 1.  GDP per capita for all countries in 1982.
> 2.  GDP per capita for Denmark for all years.
> 3.  GDP per capita for all countries for years *after* 1985.
> 4.  GDP per capita for each country in 2007 as a multiple of
>     GDP per capita for that country in 1952.
{: .challenge}
>
> > ## Solution
> > 1:
> > ~~~
> > data['gdpPercap_1982']
> > ~~~
> > {: .language-python}
> >
> > 2:
> > ~~~
> > data.loc['Denmark',:]
> > ~~~
> > {: .language-python}
> >
> > 3:
> > ~~~
> > data.loc[:,'gdpPercap_1985':]
> > ~~~
> > {: .language-python}
> > Pandas is smart enough to recognize the number at the end of the column label and does not give you an error, although no column named `gdpPercap_1985` actually exists. This is useful if new columns are added to the CSV file later.
> >
> > 4:
> > ~~~
> > data['gdpPercap_2007']/data['gdpPercap_1952']
> > ~~~
> > {: .language-python}
> {: .solution}
{: .challenge}


> ## Using the dir function to see available methods
>
> Python includes a `dir` function that can be used to display all of the available methods (functions) that are built into a data object.  As an example, the  functions available for a [list data type](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists) are:
> ~~~
> potatoes = ["Russet", "Norkota", "Yukon Gold", "Pontiac"]
> dir(potatoes)
> ~~~
> {: .language-python}
>
> This command returns:
> ~~~
> ['__add__',
> ...
> '__subclasshook__',
>  'append',
>  'clear',
>  'copy',
>  'count',
> 'extend',
> 'index',
> 'insert',
> 'pop',
> 'remove',
> 'reverse',
> 'sort']
> ~~~
> {: .language-python}
>
> The double underscore functions can be ignored for now; functions that are not surrounded by double underscores are the *public interface* of the [list type](https://docs.python.org/3/tutorial/datastructures.html#more-on-lists). So, if you want to sort the list of potatoes, according to `dir` you should try,
> ~~~
> potatoes.sort()
> ~~~
> {: .language-python}
>
> Assume Pandas has been imported and the Gapminder GDP data for Europe has been loaded as `data`.  Then, use `dir` to find the function that prints out the median per-capita GDP across all European countries for each year that information is available.
{: .challenge}
>
> > ## Solution
> > Among many choices, dir lists the `median()` function as a possibility.  Thus,
> > ~~~
> > data.median()
> > ~~~
> > {: .language-python}
> {: .solution}
{: .challenge}

[pandas-dataframe]: https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html
[pandas-series]: https://pandas.pydata.org/pandas-docs/stable/generated/pandas.Series.html
[numpy]: http://www.numpy.org/
