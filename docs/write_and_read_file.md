# Write and read files  

## 三部曲架構介紹  

* 在python中，不管是讀or寫file，不像R都是一個function搞定，而是都要以三部曲來完成：  
  * f = open(): 打開一個file，命名為f  
  * f.write() / f.read(): 讀取或寫入  
  * f.close(): 關閉這個file

* 那首先要介紹的，就是open這個函數怎麼用。我們可以在console中打"?open"，或是在VS code 中打入 "open"，然後按住command/ctrl後，以滑鼠點擊open，就可以看到文檔  

* 從文檔中，可以看到open的第一個argument是file，這就是他的檔案名稱; 第二個argument為mode，可key入以下選擇：  
  * 'r': 打開舊file並讀取(default)  
  * 'w': 打開舊file，寫檔案，並覆蓋過原舊file  
  * 'x': 打開新file，寫檔案  
  * 'a': 打開舊file，接續在之前的最後面，寫檔案(a = append 的意思)  
  * 'b': binary mode，二元模式，會搭配前面的r/w/x/a來使用，例如mode = 'wb'，就是以二進位來寫入  
  * 't': text mode(default)，就是以一般文字來寫入，會搭配前面的r/w/x/a來使用，例如mode = 'wt'，就是以文字來寫入  

* 接下來，就先demo寫入的幾個mode: 'x', 'a', 和'w'  

## file的寫入  

### mode = 'x'  

* 首先，我們想寫一個新的file，所以要用mode = 'x'，作法如下  


```python
f = open(file = 'data/test1.txt', mode = 'x')
f.write("line1")
f.close()
```

* 就可以看到新增了一個test1.txt檔案，打開來看可以看到裡面就寫著line1  

* 接著，我們除了用`f.write()`外，也可以用`f.writelines()`，那這時的input就要是個iterable物件(e.g. list)，他就會去iterate裡面的element，一個一個寫入這樣，比如：  


```python
f = open("data/test2.txt", mode = "x")
f.writelines(["I", "am", "tom-hanks"])
f.close()
```

* 寫出來的文件長這樣  

```
Iamtom-hanks
```

* 可以發現全部黏在一起了  
* 如果要換行，就加上"\n"就好  


```python
f = open("data/test3.txt", mode = "x")
f.writelines(["I\n", "am\n", "tom-hanks\n"])
f.close()
```

* 結果就會變成  

```
I
am
tom-hanks
```

### mode = 'a'  

* 現在，我們已經有三個檔案了(test1.txt, test2.txt, test3.txt)，如果，我繼續用mode = 'x' 來寫入已存在的檔案，就會報error  


```python3
f = open('data/test3.txt', mode = 'x')
f.write("just try it")
f.close()
```

* 所以，對於已經存在的檔案，我們只能選擇mode = 'w'來覆蓋過去，或是用mode = 'a' 來append新結果  

* 先來試試mode = 'a'  


```python
f = open('data/test3.txt', mode = 'a')
f.write("this is new line")
f.close()
```

* 結果如下  

```
I
am
tom-hanks
this is new line
```

### mode = 'w'  

* 如果以mode = 'w'來寫的話，他會把之前的內容全部清空，然後寫入新內容  


```python
f = open('data/test3.txt', mode = 'w')
f.write("this is another line")
f.close()
```

* 結果就變成  

```
this is another line
```

* 然後，'w'其實是最常用的mode，因為他也可以當'x'來用。就是說，如果你原本沒有test4.txt這個檔案，那你用mode = 'w'時，他會像mode = 'x'一樣，幫你新增這個檔案，然後寫進去  


```python
f = open('data/test4.txt', mode = 'w')
f.write("Just like mode = 'x'")
f.close()
```

### 寫中文，記得加上encoding = 'utf8'  

* 剛剛都是寫入英文文件，如果今天寫中文的話，照預設來走，會報錯(因為預設的編碼方式是'ascii')，所以要加上encoding = 'utf8' (但我用mac寫的時候，不寫encoding也沒報錯，所以應該預設就是utf8)  


```python3
f = open('data/test_chinese.txt', mode = 'w', encoding = 'utf8')
f.write("寫中文行不行？")
f.close
```

## file的讀取  

* 讀取的話，就簡單多了，用mode = 'r'就好  
* 而且，mode = 'r' 是預設，所以甚至可以不用寫  


```python
f = open('data/test4.txt')
f.read()
#> "Just like mode = 'x'"
f.close()
```

