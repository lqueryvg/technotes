# Pandas

```python
import pandas as pd

import psycopg2

conn = psycopg2.connect(
    host="localhost",
    dbname="postgres",
    user="postgres",
    password="some_password")

df = pd.read_sql_query('select * from person', con=conn)
print(df)
```

## Query

These are the equivalent:

```python
df[(df['Age'] > 25) & (df['Salary'] < 50000) & (df['Department'] == 'HR')]
df.query('Age > 25 and Salary < 50000 and Department == "HR"')
```

## Misc

```python
df.columns
df['Name']        # single column
df.Name           # as above, but only works for single word columns
df['Name'][0:5]   # first 5 names
df['Name', 'Type', 'Size']  # 3 columns
df.iloc[1]        # the second row (iloc = integer location)
df.iloc[0:3]      # first 3 rows
df.iloc[2,1]      # 3rd row, 2nd column
for index, row in df.iterrows():
  print(index, row)
df.loc[df['Name' == 'Fire']  # select rows where Name == 'Fire'
df.describe()     # lots of statistical metrics
df.sort_values(['Name', 'Type'], ascending=False)  # both ascending
df.sort_values(['Name', 'Type'], ascending=[1,0])  # different ascending
df['Total'] = df['Attack'] + df['Defense'] # create a new column

df = df.groupby(
        [df['client_id'], df['time_id'].dt.month]
    )['user_id'].nunique().reset_index()
df.reset_index() # puts default column indexes back after groupby()
df.reset_index(name="count") # puts default column indexes back after groupby()
    
df.rename(columns={'time_id': 'month', 'user_id': 'users_num'})
df.drop_duplicates(subset="column")
pd.merge(left, right, on="column")
pd.merge(left, right, left_on="col1", right_on="col2", how="inner")
df.groupby("column").mean()

result = (
    dc_bikeshare_q1_2012
    .groupby(["bike_number"])["end_time"]
    .max()
    .to_frame("last_used")      # rename the end_time column
    .reset_index()
    .sort_values(by="last_used", ascending=False)
)   
df.query()
```
