# Retail-Sales
Intro to Data Science: Project 1
How does unemployement have an effect on retail sales?

[ ]
  1
  2
  3
import pandas as pd
import numpy as np
from matplotlib import pyplot as plt
[ ]
  1
  2
  3
  4
  5
# Read in Data frame
datapath = '/content/drive/MyDrive/IntroToDataScience-SamanthaMathieu/Project1/Project1: Math 3439/data/'
df_sales = pd.read_csv(datapath+'sales.csv')
df_stores = pd.read_csv(datapath+'stores.csv')
df_store_features = pd.read_csv(datapath+'store_features.csv')
[ ]
  1
  2
  3
  4
  5
  6
  7
#merge datasets
#df_sales.head()
#df_store_features.head()
#df_sales['Date'] = pd.to_datetime(df_sales['Date'])
#df_store_features['Date'] = pd.to_datetime(df_store_features['Date'])
#mergeddf = pd.merge(df_sales, df_store_features, on=['Store', 'Date', 'IsHoliday'])
#mergeddf.head()
[ ]
  1
  2
  3
  4
  5
  6
  7
  8
  9
 10
#converting dates in sales and store features to datetime
df_store_features['Date'] = pd.to_datetime(df_store_features['Date'])
df_sales['Date'] = pd.to_datetime(df_sales['Date'])
#removing dept from sales and adjusting sales amounts, based on the avg amount for that date
df_sales = df_sales.groupby(['Store', 'Date', 'IsHoliday']).agg({'Weekly_Sales': np.sum}).reset_index()
df_sales_mean = df_sales.groupby('Date').agg({'Weekly_Sales': 'mean'}).reset_index()
df_sales_mean.columns = ['Date', 'Sales_Avg_For_Date']
df_sales = pd.merge(df_sales, df_sales_mean, on='Date')
df_sales['Weekly_Sales_Adjusted'] = df_sales['Weekly_Sales'] - df_sales['Sales_Avg_For_Date']
df_sales.head()

[ ]
  1
  2
  3
  4
  5
  6
#merging dataframes
df_merged = pd.merge(df_sales, df_stores, on='Store')
df_merged.head()
df_merged = pd.merge(df_merged, df_store_features, on=['Store', 'Date', 'IsHoliday'])
df_merged.head()
df_merged.shape
(2160, 17)
[ ]
  1
  2
#writing merged dataframe to csv
df_merged.to_csv(datapath + 'merged_datasets_adj.csv', encoding='utf-8', index=False)
[ ]
  1
  2
  3
  4
  5
  6
  7
#make a scatter plot for overall weekly sales (not used b/c messy)
ax1 = df_merged.plot(x='Unemployment', y='Weekly_Sales', figsize =(12,8), kind='bar')
ax1.set_xlabel('Unemployment Rate (%)', fontsize=16)
ax1.set_ylabel('Weekly Sales', fontsize=16)
ax1.set_title('Effect of unemployment on weekly sales', fontsize = 20)
ax1.tick_params(axis="x", labelsize=12)
ax1.tick_params(axis="y", labelsize=12)

[ ]
  1
  2
  3
#scatter plot with adjusted weekly sales
df_merged.plot(x='Unemployment', y='Weekly_Sales_Adjusted', kind='scatter', figsize=(12, 8))
plt.ticklabel_format(axis='y', style='plain')

[ ]
  1
  2
#Correlation
df_merged.corr()**2

[ ]
  1
  2
#see what columns are missing data
df_merged.isna().sum()
Store                       0
Date                        0
IsHoliday                   0
Weekly_Sales                0
Sales_Avg_For_Date          0
Weekly_Sales_Adjusted       0
Type                        0
Size                        0
Temperature                 0
Fuel_Price                  0
MarkDown1                2160
MarkDown2                2160
MarkDown3                2160
MarkDown4                2160
MarkDown5                2160
CPI                         0
Unemployment                0
dtype: int64
[ ]
  1
  2
#look at merged dataframe
df_merged.head()

[ ]
  1
  2
#see avg weekly sales adjusted
df_merged[df_merged['binned'] == df_merged['binned'].unique()[0]]['Weekly_Sales_Adjusted'].mean()
223154.62625292738
[ ]
  1
  2
  3
  4
  5
  6
  7
  8
  9
 10
#take mean for each unemployment (bucket them) perhaps make a line graph
#makes a list to use so that way you don't need to type out every number to 14, if you double the 15 then you get by 0.5
bins = np.linspace(5,14, num = 10)
bins
#makes bins by the list to 14
df_merged['binned'] = pd.cut(df_merged['Unemployment'], bins)
df_merged.head()
#groupby unemployment/binned
unemploymentdf = df_merged.groupby('binned').agg({'Weekly_Sales_Adjusted':np.median})
unemploymentdf = unemploymentdf.reset_index()
[ ]
  1
  2
#look at new dataframe
unemploymentdf.head()

[ ]
  1
  2
#look at columns
unemploymentdf.columns
Index(['binned', 'Weekly_Sales_Adjusted'], dtype='object')
[ ]
  1
  2
  3
  4
  5
  6
  7
  8
#Make bar chart
unemploymentdf['binned_str'] = unemploymentdf['binned'].astype(str)
ax = unemploymentdf.plot(x='binned_str', y='Weekly_Sales_Adjusted', figsize =(12,8), kind='bar')
ax.set_xlabel('Unemployment Rate (%)', fontsize=16)
ax.set_ylabel('Weekly Sales', fontsize=16)
ax.set_title('Effect of unemployment on weekly sales', fontsize = 20)
ax.tick_params(axis="x", labelsize=12)
ax.tick_params(axis="y", labelsize=12)

[ ]
  1
  2
  3
  4
  5
  6
  7
  8
#look at max, min
unemploymentdf['binned_str'] = unemploymentdf['binned'].astype(str)
ax = unemploymentdf.plot(x='CPI', y='binned_str', figsize =(12,8), kind='bar')
ax.set_xlabel('Unemployment Rate (%)', fontsize=16)
ax.set_ylabel('Weekly Sales', fontsize=16)
ax.set_title('Effect of unemployment on weekly sales', fontsize = 20)
ax.tick_params(axis="x", labelsize=12)
ax.tick_params(axis="y", labelsize=12)
[ ]
  1
  2
  3
  4
  5
  6
  7
  8
  9
#Make a scatterplot using overall sales not adjusted
unemploymentdf['binned_str'] = unemploymentdf['binned'].astype(str)
ax1 = df_merged.plot(x='Unemployment', y='Weekly_Sales', figsize =(12,8), kind='scatter')
ax1.set_xlabel('Unemployment Rate (%)', fontsize=16)
ax1.set_ylabel('Weekly Sales', fontsize=16)
ax1.set_title('Effect of unemployment on weekly sales', fontsize = 20)
ax1.tick_params(axis="x", labelsize=12)
ax1.tick_params(axis="y", labelsize=12)
plt.ticklabel_format(axis='y', style='plain') #gets rid of scientific notation

[ ]
  1
  2
  3
  4
  5
  6
#Make boxplot
ax2 = df_merged.boxplot(column='Weekly_Sales_Adjusted', by='binned',
                  figsize =(12,8),showfliers= False)
ax2.set_xlabel('Unemployment Rate (%)', fontsize=16)
ax2.set_ylabel('Weekly Sales', fontsize=16)
plt.ticklabel_format(axis='y', style='plain')

[ ]
  1
  2
  3
  4
  5
  6
  7
#Look at CPI and Weekly Sales
ax2 = df_merged.boxplot(column='Weekly_Sales_Adjusted', by='CPI',
                  figsize =(12,8),showfliers= False)
ax2.set_xlabel('CPI', fontsize=16)
ax2.set_ylabel('Weekly Sales', fontsize=16)
ax2.set_title('CPI and Weekly Sales', fontsize = 20)
plt.ticklabel_format(axis='y', style='plain')

[ ]
  1
  2
  3
  4
  5
  6
  7
  8
  9
 10
#Another plot looking at unemployment and CPI
plt.plot(df_merged['CPI'], color='red')
plt.ylabel('CPI')
plt.xlabel('Entry (Store and Date, Chronological by Date)')
#plt.title('Total and Avg Weekly Sales')

plt.plot(df_merged['Unemployment'], color='green')
plt.title('Unemployment and CPI')

plt.ticklabel_format(axis='y', style='plain')

[ ]
  1
  2
  3
  4
  5
  6
  7
#A different plot of CPI and unemployment
ax2 = df_merged.boxplot(column='Unemployment', by='CPI',
                  figsize =(12,8),showfliers= False)
ax2.set_xlabel('CPI', fontsize=16)
ax2.set_ylabel('Unemployment', fontsize=16)
ax2.set_title('CPI and Unemployment', fontsize = 20)
plt.ticklabel_format(axis='y', style='plain')

[ ]
  1
  2
#how many points are in each bin
df_merged.groupby('binned').agg({'Weekly_Sales_Adjusted':np.size})

[ ]
  1
  2
  3
  4
#See if markdowns impact weekly sales as that relates to unemployment
merged_trimmed = df_merged.dropna()
merged_trimmed.head()
#mergeddf.plot(x='MarkDown5', y='Weekly_Sales', kind='scatter', figsize=(12, 8))

[ ]
  1
  2
  3
  4
  5
#See how much is missing from markdowns
for col in df_merged.columns:
  num_missing = df_merged[col].isna().sum()
  pct_missing = num_missing / df_merged.shape[0]
  print(f'{col}: {num_missing} ({100 * pct_missing}%)')
Store: 0 (0.0%)
Date: 0 (0.0%)
IsHoliday: 0 (0.0%)
Weekly_Sales: 0 (0.0%)
Sales_Avg_For_Date: 0 (0.0%)
Weekly_Sales_Adjusted: 0 (0.0%)
Type: 0 (0.0%)
Size: 0 (0.0%)
Temperature: 0 (0.0%)
Fuel_Price: 0 (0.0%)
MarkDown1: 2160 (100.0%)
MarkDown2: 2160 (100.0%)
MarkDown3: 2160 (100.0%)
MarkDown4: 2160 (100.0%)
MarkDown5: 2160 (100.0%)
CPI: 0 (0.0%)
Unemployment: 0 (0.0%)
binned: 120 (5.555555555555555%)
[ ]
  1
  2
  3
  4
  5
#Take out outlier cluster and see how that impacts correlation
df_merged.head()
Unemployment_No_Outliers = df_merged[df_merged['binned'].between(df_merged['binned'].quantile(.05), 
                                     df_merged['binned'].quantile(.95))]
print(len(df_merged.index) - len(Unemployment_No_Outliers.index))
240
[ ]
  1
  2
#See new correlation between Unemployment and adjusted weekly sales without outliers
Unemployment_No_Outliers.corr()**2

[ ]
  1
  2
  3
  4
#made scatter plot but not used
Unemployment_No_Outliers['Log Weekly Sales'] = np.log(Unemployment_No_Outliers['Weekly_Sales_Adjusted'])
ax = Unemployment_No_Outliers.plot(x='Unemployment', y='Log Weekly Sales', figsize =(12,8), kind='scatter')
Unemployment_No_Outliers.corr()**2

[ ]
  1
  2
#Playing with graphing but not used
Unemployment_No_Outliers.boxplot(column='Weekly_Sales_Adjusted', by='binned', showfliers= False)

[ ]
  1
  2
  3
  4
  5
  6
  7
  8
#Playing with graphing but not used
Unemployment_No_Outliers['binned_str'] = Unemployment_No_Outliers['binned'].astype(str)
ax = Unemployment_No_Outliers.plot(x='binned_str', y='Weekly_Sales_Adjusted', figsize =(12,8), kind='bar')
ax.set_xlabel('Unemployment', fontsize=16)
ax.set_ylabel('Weekly Sales', fontsize=16)
ax.set_title('Effect of unemployment on weekly sales', fontsize = 20)
ax.tick_params(axis="x", labelsize=12)
ax.tick_params(axis="y", labelsize=12)
