# Join data  


```python
import pandas as pd
```

* Chicago provides a list of taxicab owners and vehicles licensed to operate within the city, for public safety. Your goal is to merge two tables together. 

* 手邊現在有兩個table，一個是`taxi_owners`，裡面是計程車行的相關資訊，如下：    


```python
taxi_owners = pd.read_pickle(r"data/taxi_owners.p")
taxi_owners.head()
#>      rid   vid           owner                 address    zip
#> 0  T6285  6285  AGEAN TAXI LLC     4536 N. ELSTON AVE.  60630
#> 1  T4862  4862    MANGIB CORP.  5717 N. WASHTENAW AVE.  60659
#> 2  T1495  1495   FUNRIDE, INC.     3351 W. ADDISON ST.  60618
#> 3  T4231  4231    ALQUSH CORP.   6611 N. CAMPBELL AVE.  60645
#> 4  T5971  5971  EUNIFFORD INC.     3351 W. ADDISON ST.  60618
```

* 表中各欄位意義：  
  * rid: primary key，我猜是註冊的id，register id 
  * vid: 也可當primary key， 
  * owner: 車行名稱  
  * address: 地址  
  * zip:  


```python
len(taxi_owners["vid"].drop_duplicates())
#> 3519
```


```python
taxi_owners.shape
#> (3519, 5)
```

* 另一個是`taxi_vehicles`，是計程車的車子的相關資訊


```python
taxi_vehicles = pd.read_pickle(r"data/taxi_vehicles.p")
taxi_vehicles.head()
#>     vid    make   model  year fuel_type                owner
#> 0  2767  TOYOTA   CAMRY  2013    HYBRID       SEYED M. BADRI
#> 1  1411  TOYOTA    RAV4  2017    HYBRID          DESZY CORP.
#> 2  6500  NISSAN  SENTRA  2019  GASOLINE       AGAPH CAB CORP
#> 3  2746  TOYOTA   CAMRY  2013    HYBRID  MIDWEST CAB CO, INC
#> 4  5922  TOYOTA   CAMRY  2013    HYBRID       SUMETTI CAB CO
```

* 欄位說明如下：  
  * vid: primary key, 我猜是車牌吧  
  * make: 哪個廠牌  
  * model: 哪個型號  
  * year: 出廠年份
  * fuel_type: 汽油、HYBRID、FLEX FUEL、COMPRESSED NATURAL GAS  
  * owner: 屬於哪家車行的車  


```python
# Merge the taxi_owners and taxi_veh tables setting a suffix
taxi_own_veh = taxi_owners.merge(taxi_vehicles, on='vid', suffixes=["_own", "_veh"])

# Print the column names of taxi_own_veh
print(taxi_own_veh.columns)
#> Index(['rid', 'vid', 'owner_own', 'address', 'zip', 'make', 'model', 'year',
#>        'fuel_type', 'owner_veh'],
#>       dtype='object')
```


## One to many  

* inner-join對one-to-many時，會重複量少的那邊  

## multiple match  


```python
df1.merge(df2, on = ["col1", "col2"])
```


## left join 


```python
df1.merge(df2, on = "col1", how = "left")
```

## left_id match right_id


```python
df1.merge(df2, how = "left", left_on = "left_id", right_on = "right_id")
```


## outer join == full join


```python
df1.merge(df2, how = "outer")
```

##　自己join自己蠻有趣的，可以整理一下  

## mutating joins  

* 就是剛剛的inner join, full join, left join, right join，他join完後是會增加column的那種  

## filtering join  

* join的目的，是在filitering，所以join完後，不會增加column  
  * x semi-join y，就是 x left-join y後，只留x的column，和matching column != na的部分  
  * x anti-join y，就是 x left-join y後，只留x的column，和matching column == na的部分  

* 遺憾的是，pandas沒有這semi_join和anti_join的指令，你還真的得照上面的步驟，做出結果  



