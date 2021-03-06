# Database  

* 這一節只講如何連SQLite，他是把DB的檔存在local computer上的作法，所以使用上，等於是直接去開啟此DB的檔，然後開啟後裡面有多個表格，就可以開始單表格查詢或多表格合併了  
* 使用的package是`sqlalchemy`，就先把他想成R的`RJDBC`，此套件可以連各種DB，只是這堂課是介紹連SQLite而已    
* 但實際上，工作上我需要的教我連sql server或oracle，而不是這種SQLite，所以這一節我就放個連SQLite的範例，其他我就不想整理了(這一節其他內容就是一直教SQL語法而已)，等另個課程(專門講連db的)，再好好整理連各DB的方式  

## 建立連線用`sqlalchemy.create_engine()`  


```python
# Import sqlalchemy's create_engine() function
from sqlalchemy import create_engine
import pandas as pd
```



```python
# Create the database engine
engine = create_engine('sqlite:///data/data.db')

# View the tables in the database
print(engine.table_names())
#> ['boro_census', 'hpd311calls', 'weather']
#> 
#> <string>:1: SADeprecationWarning: The Engine.table_names() method is deprecated and will be removed in a future release.  Please refer to Inspector.get_table_names(). (deprecated since: 1.4)
```

## 撈資料用`pd.read_sql(query_text, engine)`  

* 如果我想撈weather資料表的內容，我可以這樣做：  


```python
# Create a SQL query to load the entire weather table
query = """
select * from weather;
"""

# Load weather with the SQL query
weather = pd.read_sql(query, engine)

# View the first few rows of data
print(weather.head())
#>        station                         name  latitude  ...  tavg  tmax tmin
#> 0  USW00094728  NY CITY CENTRAL PARK, NY US  40.77898  ...          52   42
#> 1  USW00094728  NY CITY CENTRAL PARK, NY US  40.77898  ...          48   39
#> 2  USW00094728  NY CITY CENTRAL PARK, NY US  40.77898  ...          48   42
#> 3  USW00094728  NY CITY CENTRAL PARK, NY US  40.77898  ...          51   40
#> 4  USW00094728  NY CITY CENTRAL PARK, NY US  40.77898  ...          61   50
#> 
#> [5 rows x 13 columns]
```
