# numpy basics  

## Why numpy  

* `numpy` 是 `numeric python` 的縮寫，簡單來講，就是要來做數學運算的意思  
* 之前學的 `list` 是無法做數學計算的，所以在 `numpy` 裡，會把 list 轉成 `numpy array` 這種型別，就可以做計算了。  
* 看個例子：  


```python
height = [1.73, 1.68, 1.71, 1.89, 1.79]
weight = [65.4, 59.2, 63.6, 88.4, 68.7]
weight/(height**2)
#> Error in py_call_impl(callable, dots$args, dots$keywords): TypeError: unsupported operand type(s) for ** or pow(): 'list' and 'int'
#> 
#> Detailed traceback:
#>   File "<string>", line 1, in <module>
```

* 轉成 numpy array 就可以做計算了  


```python
import numpy as np
height = np.array([1.73, 1.68, 1.71, 1.89, 1.79])
weight = np.array([65.4, 59.2, 63.6, 88.4, 68.7])
weight/(height**2)
#> array([21.85171573, 20.97505669, 21.75028214, 24.7473475 , 21.44127836])
```

* 再看個例子  


```python
python_list = [1,2,3]
print(python_list + python_list)
#> [1, 2, 3, 1, 2, 3]
numpy_array = np.array([1,2,3])
print(numpy_array + numpy_array)
#> [2 4 6]
```
## 多層 list 的介紹  

* 從剛剛的介紹中，我們知道把 list 轉成 ndarray 後，就可以做數學運算。  
* 但剛剛舉的例子都很簡單，比較像是我找10筆數據，存進 list 這個 collection 裡面。  
* 但在 python 的 data science 應用中，我們可以收到更多不同類型的數據。例如：  
  * 1張灰階影像的數據，是個矩陣型資料，如何存到 list 中？  
  * 1張彩色影像數據，會是RGB 3 個通道，每個通道都是矩陣型資料，這要如何存到 list 中？  
  * 10 張彩色影像數據，如何存到 list 中？  
  * 5批資料，每批都有 10 張彩色影像，這樣的數據要如何存到 list 中？  
  * 3個年份，每個年份都有5批資料，每批都有 10 張彩色影像，這樣的數據要如何存到 list 中？  
* 這些問題可以一直問下去，但大概已經可以發現，我只要用一層又一層的list來儲存，就可以搞定了。

### 2 層 list  

* 對於1張灰階影像資料，例如是這樣的一張矩陣型資料: $\left[\begin{array}{cc} 0 & 1\\1 & 0 \\1 & 1\end{array}\right]$，可以用數學寫成：

$$
\boldsymbol{X} \in R^{3 \times 2}
$$

* 在 python 中，會用這樣的 list 來儲存他：  


```python
a = [
  [0, 1], 
  [1, 0], 
  [1, 1]
]
a = np.array(a)
a
#> array([[0, 1],
#>        [1, 0],
#>        [1, 1]])
```

* 我們總是會很想用矩陣的角度去看他，但拜託你忍一忍，不要這樣做。因為之後要一路推廣下去。
* 所以，我們現在改成用層次的方式來理解他：$R^{3 \times 2}$ 就讀成: 總共有3列，每一列都有2筆數據。
* 那他的階層就會長成：  
  * 第一列: [0, 1]  
    * 第一列的第一個 element: 0  
    * 第一列的第二個 element: 1  
  * 第二列: [1, 0]  
    * 第二列的第一個 element: 1  
    * 第二列的第二個 element: 0  
  * 第三列: [1, 1]  
    * 第三列的第一個 element: 1  
    * 第三列的第二個 element: 1  
* 也就是第一層是 $R^{3 \times 2}$ 的 3，第二層是 $R^{3 \times 2}$ 的 2
* 所以，我們要練習，這樣寫 list：  


```python
# 第一步，先寫出第一層，有3列： a = [[], [], []] 
# 第二步，再把第二層的內容補進去，各2個element： a = [[0, 1], [1, 0], [1, 1]] 
```

* 接著，來定義一些名詞： $R^{3 \times 2}$，R的上面有`2`個數字相乘，我們稱它為`2`階張量，儲存的資料類型是 `2`d array。也就是說，這個張量的`維度是2`。然後 R 上面的長相是 $3 \times 2$，所以我們說他的 shape 是 `(3,2)`  
* 我們來看一下這個 numpy array 的 attribute，就可以驗證上面講的內容：


```python
a.ndim
#> 2
```

* ndim 是 2，就表示 ndarray 是 2d array(n=2, 有兩層，R上面有2個數字相乘)  


```python
a.shape
#> (3, 2)
```

* shape 是 (3,2)，表示他是 $R^{3 \times 2}$ 的張量  

### 3 層 list   

* 對於1張彩色影像資料，他會有3張矩陣型資料，例如長成這樣：  

$$
\left[
\left[\begin{array}{cc} 0 & 1\\1 & 0 \\1 & 1\end{array}\right],
\left[\begin{array}{cc} 0 & 0\\1 & 1 \\1 & 0\end{array}\right],
\left[\begin{array}{cc} 1 & 1\\0 & 0 \\0 & 1\end{array}\right]
\right]
$$

* 那我可以寫成這樣：$\boldsymbol{X} \in R^{3 \times 3 \times 2}$  

$$
\boldsymbol{X} = \left[
R^{3 \times 2},
G^{3 \times 2},
B^{3 \times 2}
\right]
$$
* 由 $R^{3 \times 3 \times 2}$ 已可知道，他是 3d array(所以要給他3層)。shape是 `3*3*2`，所以第一層有3個 element，第二層有3個element，第三層有2個element。  
* 那我再造 list 時，第一步就是先寫第一層：  

```
a = [
  [],
  [],
  []
]
```

* 然後第二層：  

```
a = [
  [
    [],
    [], 
    []
  ],
  [
    [],
    [], 
    []
  ],
  [
    [],
    [], 
    []
  ]
]
```

* 最後，做出第三層：  


```python
a = [
  [
    [0, 1],
    [1, 0], 
    [1, 1]
  ],
  [
    [0, 0],
    [1, 1], 
    [1, 0]
  ],
  [
    [1, 1],
    [0, 0], 
    [0, 1]
  ]
]
a = np.array(a)
a
#> array([[[0, 1],
#>         [1, 0],
#>         [1, 1]],
#> 
#>        [[0, 0],
#>         [1, 1],
#>         [1, 0]],
#> 
#>        [[1, 1],
#>         [0, 0],
#>         [0, 1]]])
```

* 驗證一下，這個 $R^{3 \times 3 \times 2}$ 是 3d array(因為R上面有3個數字相乘，或說，建立list的時候要寫到第3層)。shape是 `3*3*2`


```python
print(f"the dim of a is {a.ndim}")
#> the dim of a is 3
print(f"the shape of a is {a.shape}")
#> the shape of a is (3, 3, 2)
```

### 4 層 list   

* 剛剛介紹完，1張彩色影像資料要如何儲存。那如果 2 張彩色影像數據，要如何存到 list 中？  
* 很簡單嘛，現在變成是一個 $R^{2張 \times 3通道 \times 3列 \times 2行}$ 的資料，所以我要做一個 4D array(因為 R 上面有4個數字相乘，list要做到4層)，然後他的 shape 會是 `(2,3,3,2)`  
* 開始造 list ，第一步就是先寫第一層(2張圖片)：  

```
a = [
  [],
  []
]
```

* 然後第二層，每張圖片，都有RGB三個通道：  

```
a = [
  [
    [],
    [], 
    []
  ],
  [
    [],
    [], 
    []
  ]
]
```

* 然後，第三層，每個 RGB 中，都有三列：  


```python
a = [
  [
    [
      [],
      [],
      []
    ],
    [
      [],
      [],
      []
    ], 
    [
      [],
      [],
      []
    ]
  ],
  [
    [
      [],
      [],
      []
    ],
    [
      [],
      [],
      []
    ], 
    [
      [],
      [],
      []
    ]
  ]
]
```

* 最後，每一列裡面，都有兩個 element:  


```python
a = [
  [
    [
      [0, 1],
      [1, 0], 
      [1, 1]
    ],
    [
      [0, 0],
      [1, 1], 
      [1, 0]
    ], 
    [
      [1, 1],
      [0, 0], 
      [0, 1]
    ]
  ],
  [
    [
      [0, 0],
      [1, 0], 
      [0, 1]
    ],
    [
      [1, 1],
      [1, 1], 
      [1, 1]
    ], 
    [
      [0, 0],
      [0, 1], 
      [1, 0]
    ]
  ]
]
a = np.array(a)
a
#> array([[[[0, 1],
#>          [1, 0],
#>          [1, 1]],
#> 
#>         [[0, 0],
#>          [1, 1],
#>          [1, 0]],
#> 
#>         [[1, 1],
#>          [0, 0],
#>          [0, 1]]],
#> 
#> 
#>        [[[0, 0],
#>          [1, 0],
#>          [0, 1]],
#> 
#>         [[1, 1],
#>          [1, 1],
#>          [1, 1]],
#> 
#>         [[0, 0],
#>          [0, 1],
#>          [1, 0]]]])
```

* 驗證一下，這個 $R^{2張 \times 3通道 \times 3列 \times 2行}$是 4d array(因為R上面有4個數字相乘，或說，建立list的時候要寫到第4層)。shape是 `2*3*3*2`


```python
print(f"the dim of a is {a.ndim}")
#> the dim of a is 4
print(f"the shape of a is {a.shape}")
#> the shape of a is (2, 3, 3, 2)
print(a.size)
#> 36
len(a)
#> 2
```


## ndarray 的 dim, shape, 與 axis 

* 在 ndarray 中，這三個詞很容易搞混，但又超重要，所以在這裡好好整理一下  
* 講到維度，就會想到以前線性代數學到的東西：維度是指向量空間裡基底的數量。簡單的判斷方法，就是把 $R$ 上面的數字乘出來就對了。  
* 例如 $R^3$ 表示3維，意思是這個歐式空間中，需要3個基底向量(e.g. $\left[\begin{array}{cc} 1 \\0 \\ 0\end{array}\right]$, $\left[\begin{array}{cc} 0 \\1 \\ 0\end{array}\right]$, $\left[\begin{array}{cc} 0 \\0 \\ 1\end{array}\right]$)，才能span出這個空間。所以我們稱它為 3 維(空間)，這個空間裡的每個點，就是一個3維向量。  
* 又例如 $R^{2 \times 2}$ 是 $2 \times 2$ = 4維，意思是這個矩陣空間，需要 4 個基底向量(e.g. $\left[\begin{array}{cc} 1 & 0\\0 & 0 \end{array}\right]$, $\left[\begin{array}{cc} 0 & 1\\0 & 0 \end{array}\right]$, $\left[\begin{array}{cc} 0 & 0\\1 & 0 \end{array}\right]$, $\left[\begin{array}{cc} 0 & 0\\0 & 1 \end{array}\right]$)才能span出這個空間，所以我們稱它為 4 維，然後空間中的每個點都是 $2 \times 2$ 的矩陣。  
* 對應到 R 的資料結構，就是：  
  * 純量 ($R$), 對應到 `numeric` 的資料結構，例如 `a = 3` 
  * n維向量 ($R^n$), 對應到 n個element的`vector` 的資料結構，例如 `a = c(1,2,3)` 為 $R^3$，三維向量  
  * m by n 矩陣($R^{m \times n}$), 對應到 m*n個 element 的 `matrix` 的資料結構，例如 `a = matrix(1:6, nrow = 2, ncol = 3, byrow = TRUE)` 為 $R^{2 \times 3}$  
* 那以前只學到矩陣，所以只學到 $R^{m \times n}$，那有沒有想過， $R^{j \times m \times n}$ 這是什麼東西？甚至，$R^{i \times j \times m \times n}$ 這是什麼東西？  
* 簡單講，這種東西，就叫張量(tensor)。$R$的上面，如果有 n 個數字相乘，我們就叫他 n 階張量。例如：  
  * $R$: 0階張量，就是以前學的純量    
  * $R^3$: 1階張量(因為只有一個數字相乘)，就是以前學的向量  
  * $R^{2 \times 2}$: 2階張量(因為有2個數字相乘)，就是以前學的矩陣。對應到資料，就像是灰階影像，長寬各2個pixel。    
  * $R^{3 \times 2 \times 2}$: 3階張量(因為有3個數字相乘)，這以前就沒學過了。對應到的資料，就像是彩色影像，3個通道(RGB)，每個通道都是長寬各2個pixel。  
  * $R^{5 \times 3 \times 2 \times 2}$: 4階張量(因為有4個數字相乘)。對應到的資料，就是有5張彩色影像。每張彩色影像，都有3個通道(RGB)，每個通道，都是長寬各2個pixel。
* 那這個時候，我們講維度，就是在講 `張量` 的維度。

* 所以，以前學的`維度`，在描述的對象，都是 `向量空間` 。  
* 那接下來要講的維度，他要描述的對象，是資料結構裡的陣列(array)，在python中叫他list，在deep learning中叫他tensor(張量)。都是同義詞。我們 focus 在 list 就好。  
* 
* 但在 Python 世界，就直接推廣到 `張量(tensor)` 的概念。而張量對應的資料結構，就是 ndarray(n-dim array, n階張量)  
* 重點來了，張量是啥？n-dim 的 dim 又是啥？ 哈哈笑死，這邊的 dim(維度) 根本就和以前線性代數學的不一樣，不要執著以前學過的定義，這樣會卡死。  
* 腦袋轉一下，直接來學新的系統。`n-d array 的 d，是指 $R$ 上面有 d 個數字相乘`，所以他的維度d (d-dimension)，就是指R上面有d個數字相乘。對應到list的資料結構，就是有d個階層。以下逐一介紹：   

### 0D array = 0-dim array = 0階張量 = 0層的資料結構  

* 例如純量3， $3 \in R$，R上面有0個數字相乘，所以他叫 0D array。 
* 來看一下在 python 的資料結構會長怎樣：


```python
a = np.array(3)
print(a)
#> 3
print(type(a))
#> <class 'numpy.ndarray'>
```

* 我們可以用 `.ndim` 這個屬性，來看他的dim，也就是幾D array:  


```python
a.ndim
#> 0
```

* 很明顯的，告訴我他是個 0D array  
* 然後用 `.shape`，來看他的 shape  


```python
a.shape
#> ()
```
* 是空的，也就是 $R^{()}$ 這樣的意思，裡面就是沒東西相乘  

### 1 階張量 (1D array)  

* 1 階張量，就是 $R$的上面，有 1 個數字相乘。相乘的樣貌就叫shape  
* 舉個例子，$\left[\begin{array}{cc} 2 \\3 \\ 4\end{array}\right] \in R^{3}$，所以這樣的資料，應該是 1D array, 然後 shape = 3  
* 在 python 的資料結構中，我就是用 1 層的list來處理：  


```python
a = np.array([2,3,4])
print(a)
#> [2 3 4]
print(type(a))
#> <class 'numpy.ndarray'>
print(f"the dim of a is {a.ndim}")
#> the dim of a is 1
print(f"the shape of a is {a.shape}")
#> the shape of a is (3,)
```

### 2 階張量 (2D array)  

* 灰階影像的數據，就都是長這樣。
* 舉例來說，我拿到的影像，長寬都只有2個像素，所以長成$\left[\begin{array}{cc} 1 & 2\\3 & 4 \end{array}\right] \in R^{2 \times 2}$  
* 那$R$的上面，有 2 個數字相乘，所以是 2D array。相乘的樣貌是2*2，所以shape為(2,2)  
* 那這種2D array，在python就是存成 2-dim array，也就是 2 層的list：  


```python
# 2 層的結構  
a = [
  [1,2],
  [3,4]
]
a = np.array(a)
print(a)
#> [[1 2]
#>  [3 4]]
print(type(a))
#> <class 'numpy.ndarray'>
print(f"the dim of a is {a.ndim}")
#> the dim of a is 2
print(f"the shape of a is {a.shape}")
#> the shape of a is (2, 2)
```


```python
len(a)
#> 2
```


* 從這邊開始，要特別注意 2D array 就是 2層list結構。可以這樣思考。  
* a:  
  * [,]
    * 1
    * 2
  * [,]
    * 3
    * 4
* 所以由左到右看，第一層有2個 element，第二層也是2個element。
* 第一層我們又叫他第0軸(axis = 0)，第二層我們又叫他第1軸(axis=1)。

### 3 階張量 (3D array)  

### 4 階張量 (4D array)  


