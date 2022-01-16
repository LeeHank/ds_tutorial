# JSON & APIs  

## Load JSON data

* 這邊先介紹JSON的三種組成方式：  

### Record Orientation  

* record就是observation的意思，就是row的意思  
* 所以把dataframe的一個一個row，組成的json，就叫row orientation/ record orientation 
* 這是最常看到的json格式，也可以說他是list of dictionaries  

```
[
  {
    "name": "Hank",
    "age": 33,
    "height": 185
  },
  {
    "name": "Pinpin"
    "age": 33,
    "height": 160
  },
  {
    "name": "Zoe",
    "age": 37,
    "height": 166
  }
]
```

### Column Orientation  

* 這就是用dataframe的一個一個column，所組成的json，所以叫column orientation  
* 你也可以稱它為dictionary of lists  

```
{
  "name": ["Hank", "Pinpin", "Zoe"],
  "age": [33, 33, 37],
  "height": [185, 160, 166]
}
```

### Split Orientation  

* 這就是把dataframe的column_name(簡稱column), row_name(簡稱index), value(簡稱data)這三塊分開放的格式：  

```
{
  "column": ["name", "age", "height"],
  "index": [0, 1, 2],
  "data": [
    ["Hank", 33, 185],
    ["Pinpin", 33, 160],
    ["Zoe", 37, 166]
  ]
}
```

* 如果你已經有.json檔了，可以這樣讀檔：  
  * 對index orientation 或 column orientation來說： `pd.read_json("data.json")`  
  * 對split orientation來說： `pd.read_json("data.json", orient = "split")`    


```python
import pandas as pd
```


```python
# Load the daily report to a data frame
pop_in_shelters = pd.read_json("dhs_daily_report.json")

# View summary stats about pop_in_shelters
print(pop_in_shelters.describe())
```

## Introduction to APIs  

* 剛剛我們是直接開啟local端的.json檔，那現在要用API，去開啟server那邊的.json檔，就差在這而已  
* 所以這邊要學的，就是如何透過server端的API，去get遠端的資料，而遠端的資料，通常都是用JSON在儲存的，所以就是讀遠端的.json檔的意思而已  
* 我們將使用`request`這個套件來處理，以get method舉例，會寫成： `response = requests.get(url_string, params, headers,...)`  
* 其中，各parameter的意思如下：  
  * url_string: 就填入該API的url，為字串格式，例如："https://172.16.99.233:8000/predict"  
  * params: 為dictionary格式，填入客製化的參數(如果原本要get的那個url所用的function，不需要你填參數，那這個argument就不用下)。例如：剛剛的url_string如果是讓你去get一筆data，然後參數要你下location="xxx"，好幫你filter出data中location == "xxx"的資料給你，那這時的params就要寫成`params = {"location": "xxx"}`  
  * headers: 填入authentication資訊，為dictionary格式  

* output的object: respone，包含data和metadata兩部分  
  * `response.json()`會把get下來的JSON資料，轉成dictionary給你。所以你可以用`pd.DataFrame()`來parse他。記得，不要用`pd.read_json()`，因為後者吃的是字串，但你現在的資料是dictionary了  

* 現在來練習吧，課程中使用"Yelp Business Search API for cafes in New York City"這個API，完整流程示範如以下的code，但因為認證的key已經過期，現在無法操作了：  


```python
import requests

api_url = "https://api.yelp.com/v3/businesses/search"
params = {'location': 'NYC', 'term': 'cafe'}
headers = {
  'Authorization': 'Bearer mhmt6jn3SFPVC1u6pfwgHWQvsa1wmWvCpKRtFGRYlo4mzA14SisQiDjyygsGMV2Dm7tEsuwdC4TYSA0Ai_GQTjKf9d5s5XLSNfQqdg1oy7jcBBh1i7iQUZBujdA_XHYx'
}

# Get data about NYC cafes from the Yelp API
response = requests.get(api_url, 
                headers=headers, 
                params=params)

# Extract JSON data from the response
data = response.json()

# Load data to a data frame
cafes = pd.DataFrame(data["businesses"])

# View the data's dtypes
print(cafes.dtypes)
```

* 那沒關係，反正只是要練習從遠端拉.json資料，那我可以改用爬蟲的方式來練習  
* 我從pchome的網站中，搜尋商品後，發現他show給你的網頁，背後是讀這個路徑的.json資料："http://ecshweb.pchome.com.tw/search/v3.3/all/results?q=sony&page=1&sort=rnk/dc"  
* 那太棒拉，我就get他就好，而且此時不用下parameter了，因為他已經結合在url裡面，也不用下header，因為不需要認證  


```python
import requests 
url = "http://ecshweb.pchome.com.tw/search/v3.3/all/results?q=sony&page=1&sort=rnk/dc"
response = requests.get(url)
data = response.json()
data
#> {'QTime': 37, 'totalRows': 32597, 'totalPage': 100, 'range': {'min': '', 'max': ''}, 'cateName': '', 'q': 'sony', 'subq': '', 'token': ['sony'], 'isMust': 1, 'prods': [{'Id': 'DEBB55-A900AXCHG', 'cateId': 'DEBB55', 'picS': '/items/DEBB55A900AXCHG/000002_1642059796.jpg', 'picB': '/items/DEBB55A900AXCHG/000001_1642059795.jpg', 'name': 'PX大通HR7PRO星光夜視行車紀錄器SONY感光元件GPS區間測速記錄器', 'describe': '每日強檔▼瘋殺特賣PX大通HR7PRO星光夜視旗艦王汽車行車紀錄器真HDR高動態SONY STARVIS感光元件GPS區間測速記錄器贈32G記憶卡\\r\\n《只有一天★限時狂降》\r\n開始﹕０１／１３(星期四)１０：００\r\n結束﹕０１／１４(星期五)１０：００\r\n網路價$4988．限時價↘$４１８８\r\n滿＄３０００再送３００ｐ幣\r\n======================================\r\n\r\n🚩【夜視新標竿】: hdr高動態錄影 +sony starvis星光級感光元件。\r\n>>真hdr:抗led或hid高強光、車牌影像清晰不過曝 starvis:提升整體亮度、解決環境過暗                \r\n🚩【安全新標竿】: ts碼流存檔機制。\r\n>>以秒截檔設計，確保錄影檔案不毀損  \r\n🚩【好用新標竿】: 一鍵進格式化。\r\n>>動動手指，輕鬆格式化    \r\n🚩【測速新標竿】: gps區間+定點測速雙警示。\r\n>>gps雙衛星，雙預警加倍安心\r\n\r\n👍👍👍多位論壇部落客一致推薦👍👍👍\r\n★pchome開箱focus專欄★大力推薦\r\n★tony的玩樂生活筆記★部落客開箱推薦\r\n★小老婆汽機車資訊網★網路車類媒體大力推薦\r\n★正宅爸3c開箱★部落客開箱推薦\r\n★星星陳世章★部落客開箱推薦\r\n★寶井秀人hyde★部落客開箱推薦\r\n★basic施鈞程★部落客開箱推薦\r\n★硬是要學★部落客開箱推薦\r\n★鑫部落★部落客開箱推薦\r\n★另有其他規格款式可選↓↓↓\r\n\r\n《★鑽石版頂規 ★ 》\r\n《★hr7 pro》\r\n《★鈦金版高規 ★ 》\r\n《★hr7g》\r\n《★白金版均規 ★ 》\r\n《★hr7》\r\n\r\n※六大優勢\r\n1. hdr高動態錄影+sony starvis星光夜視技術>>抗led強光、夜間車牌清晰\r\n2. ts碼流存檔機制>>以秒截檔設計，不正常斷電、車禍撞擊，錄影檔案不毀損\r\n3. gps區間測速>>警示功能提升\r\n4. 2吋ips高清螢幕>>廣視角，資訊顯示清楚\r\n5. 不掉落魔法貼>>全新支架設計，機身更穩固\r\n6. 一鍵進格式化>>簡易操作，確保錄影成功率\r\n\r\n※完整特色\r\n▼ 1080p 30fps hdr高動態\r\n▼ sony starvis超級星光夜視\r\n▼ f 1.4超大光圈+7玻1ir鏡頭\r\n▼ 1080p 60fps wdr寬動態\r\n▼ ips廣視角高清螢幕\r\n▼ 以秒截檔ts碼流存檔機制\r\n▼ 行車魔法貼不掉落支架\r\n▼ 148度大廣角\r\n▼ hud抬頭顯示\r\n▼ 13段曝光值調整\r\n▼ gps雙衛星接收快速定位\r\n▼ gps區間+定點測速照相雙預警\r\n▼ g-sensor碰撞自動偵測鎖檔\r\n▼ 一鍵鎖檔備份關鍵檔案 \r\n▼ 一鍵快捷啟動記憶卡格式化\r\n▼ 一鍵快速拍照\r\n▼ 記憶卡異常提醒\r\n▼ 記憶卡30天定期格式化提醒', 'price': 4188, 'originPrice': 4188, 'author': '', 'brand': '', 'publishDate': '', 'sellerId': '', 'isPChome': 1, 'isNC17': 0, 'couponActid': [], 'BU': 'ec'}, {'Id': 'DGBJGB-A900CAR5E', 'cateId': 'DGBJGB', 'picS': '/items/DGBJGBA900CAR5E/000002_1641459421.jpg', 'picB': '/items/DGBJGBA900CAR5E/000001_1641459421.jpg', 'name': 'SONY PS5原廠 DualSense 無線控制器-銀河紫 CFI-ZCT1G04', 'describe': 'SONY PS5原廠 DualSense 無線控制器銀河紫 CFI-ZCT1G04\\r\\n來自銀河系的新色 為遊戲之夜點燃熱情 \r\n創新ps5控制器 深入栩栩如生遊戲體驗 \r\n■搭載觸覺回饋和自適應扳機，更身歷其境的感受\r\n■直覺配置搭配強化操作桿，讓你掌握全局\r\n■觸覺回饋真實感受到武器後座力等動態震動\r\n■自適應扳機體驗往後拉弓、急剎車等力量與張力\r\n■內建麥克風與3.5mm耳機連接端\r\n■帶來更多方式向全世界播送冒險經歷\r\n\r\n☛看看其他銀河系新色-星塵紅', 'price': 2280, 'originPrice': 2280, 'author': '', 'brand': '', 'publishDate': '', 'sellerId': '', 'isPChome': 1, 'isNC17': 0, 'couponActid': ['C025307', 'C025308'], 'BU': 'ec'}, {'Id': 'DMAAEC-A900BFLRR', 'cateId': 'DMAAEC', 'picS': '/items/DMAAECA900BFLRR/000002_1622769045.jpg', 'picB': '/items/DMAAECA900BFLRR/000001_1622769045.jpg', 'name': 'SONY 索尼  5.1 聲道 SOUNDBAR 家庭劇院組 HT-S40R', 'describe': 'SONY 索尼 5.1 聲道 SOUNDBAR 家庭劇院組 HT-S40R\\r\\n完整的 5.1 聲道，並具備 600 w 震撼輸出\r\n支援 dolby® digital 音訊解碼\r\n無線後環繞喇叭\r\nbluetooth® 連線功能支援音樂串流\r\n支援 hdmi arc、光纖與 3.5mm 輸入，即可輕鬆設定', 'price': 9900, 'originPrice': 9900, 'author': '', 'brand': '', 'publishDate': '', 'sellerId': '', 'isPChome': 1, 'isNC17': 0, 'couponActid': [], 'BU': 'ec'}, {'Id': 'DPAD06-A900BFGWY', 'cateId': 'DPADZ9', 'picS': '/items/DPAD06A900BFGWY/000002_1630490536.jpg', 'picB': '/items/DPAD06A900BFGWY/000001_1630490536.jpg', 'name': '[21年新機上市] Sony BRAVIA 65吋 4K Google TV 顯示器 XRM-65X90J', 'describe': '送精緻安裝[21年新機上市] SONY  BRAVIA 65吋 4K Google TV 顯示器 XRM-65X90J\\r\\n★原廠註冊送統一超商2000元虛擬商品卡★65x90j_4k google tv\r\n\r\n1.全陣列 led 背光\r\n2.認知智慧處理器xr\r\n3.xr原色顯示 pro\r\n4.xr對比增強5\r\n5.google tv可自由下載應用程式(如disney+等)\r\n6.多音域環繞聲場技術\r\n7.提供多種尺寸選擇 : 75”  65”  55"  50"\r\n\r\n★保固：二年\r\n\r\n\r\n選購50吋賣場★sony bravia 50吋 4k顯示器 xrm-50x90j\r\n選購55吋賣場★sony bravia 55吋 4k顯示器 xrm-55x90j\r\n選購75吋賣場★sony bravia 75吋 4k顯示器 xrm-75x90j\r\n\r\n\r\n\r\n\r\n★★★★★★★★★★★★★★★★★★★★★★★★★★★★★★★\r\n【退貨&配送注意事項】\r\n●收到商品時，請先檢查外包裝是否完整。\r\n●鑑賞期内請勿註冊商品或開通贈品，否則不予退貨。\r\n●產品到貨 7 天猶豫期之權益，但猶豫期並非試用期，退回產品必須是回復原狀，亦即必須回復至您收到商品時:\r\n1.原始狀態（無刮傷、無髒、無字跡）。\r\n2.包裝完整(保持產品附件、包裝、廠商紙箱及所有附随文件或資料之完整性)否則退貨刮傷損壞，需負擔整新費用以售價30%計算，將由訂購人支付給出貨廠商，如商品嚴重受損以實際報價為準。', 'price': 56900, 'originPrice': 56900, 'author': '', 'brand': '', 'publishDate': '', 'sellerId': '', 'isPChome': 1, 'isNC17': 0, 'couponActid': [], 'BU': 'ec'}, {'Id': 'DYAQBC-A900BNLZQ', 'cateId': 'DYAQF4', 'picS': '/items/DYAQBCA900BNLZQ/000002_1641867255.jpg', 'picB': '/items/DYAQBCA900BNLZQ/000001_1641867254.jpg', 'name': '【SONY 索尼】WF-1000XM4 主動式降噪真無線藍牙耳機 智慧降噪 / IPX4防水(公司貨保固18+6個月)', 'describe': '▲限時加贈7-11超商商品卡300元▲【SONY 索尼】WF-1000XM4 主動式降噪真無線藍牙耳機 智慧降噪 / IPX4防水(公司貨保固12+6個月) \\r\\n《絕不手軟29折起》 \r\n開始：１／０７（五）１１：００\r\n結束：１／２０（四）１０：５９\r\n網路價$７９９０．\r\n限時價↘$７４９０\r\nsony 首款符合 hires wireless 標準的真無線耳機\r\n支援 ldac 傳輸協定，提供高品質音樂\r\n全新整合處理器 v1，提升主動降噪效能\r\n全新噪音隔離耳塞，提升被動降噪效果\r\n更清晰的通話品質\r\n自然的環境聲表現\r\nipx4 防水等級', 'price': 7490, 'originPrice': 7490, 'author': '', 'brand': '', 'publishDate': '', 'sellerId': '', 'isPChome': 1, 'isNC17': 0, 'couponActid': [], 'BU': 'ec'}, {'Id': 'DMAAEC-A900C2KD1', 'cateId': 'DMAAEC', 'picS': '/items/DMAAECA900C2KD1/000002_1638953189.jpg', 'picB': '/items/DMAAECA900C2KD1/000001_1638953189.jpg', 'name': 'SONY 索尼 HT-A7000  DOLBY ATMOS  7.1.2聲道家庭劇院+SA-RS3S+SA-SW3', 'describe': 'SONY 索尼  HT-A7000 DOLBY ATMOS 7.1.2聲道 家庭劇院+SA-RS3S+SA-SW3\\r\\n商品含:sa-rs3s 無線後環繞+sa-sw3無線重低音', 'price': 54900, 'originPrice': 54900, 'author': '', 'brand': '', 'publishDate': '', 'sellerId': '', 'isPChome': 1, 'isNC17': 0, 'couponActid': [], 'BU': 'ec'}, {'Id': 'DYAQ0R-A900BG7DX', 'cateId': 'DYAQ0X', 'picS': '/items/DYAQ0RA900BG7DX/000002_1641868504.jpg', 'picB': '/items/DYAQ0RA900BG7DX/000001_1641868504.jpg', 'name': 'SONY WF-1000XM4 黑色 降噪真無線耳機', 'describe': '▼尾牙大牌檔★夢幻清單▼★註冊送KKBOX60天再送5%P幣★SONY WF-1000XM4 黑色 降噪真無線耳機《公司貨註冊保固1年+6個月》\\r\\n《禮品選購大公開！全場下殺》\r\n開始：０１／１１（二）１１：００\r\n結束：０１／１８（二）１１：００\r\n網路價$７９９０．\r\n限時價↘$７４９０送５％ｐ幣\r\n☆註冊送kkbox無損60天儲值卡☆\r\n★即日起至2022 2 13註冊送★\r\n1. kkbox無損60天儲值卡 (此為原廠活動贈品，僅送乙組 顆。請至sony官網登記，贈品將於補寄，造成不便敬請見諒。) \r\n\r\n\r\n■ 台灣sony公司貨 官網註冊登入保固 (依官網公布為準)\r\n■ 先進的數位降噪與整合式處理器 v1\r\n■ 無線高解析音質的音質\r\n■ 智慧聆聽和清晰的通話品質\r\n■ 人體工學表面設計可穩固貼合\r\n■ 防水設計和長效電池續航力\r\n■ ncc許可字號:ccao21lp0490t1\r\n*耳塞保養祕訣：請使用乾布擦拭髒污，請勿使用酒精或濕紙巾，會加速耳塞的耗損程度。\r\n\r\n★銀色賣場請點我\r\n\r\n\r\n★sony原廠商品皆為封膜式包裝，確定商品購買前請勿拆封，若拆封後退貨，商品無法復原整新 將有可能費用產生，造成您的不便深感抱歉，謝謝!', 'price': 7490, 'originPrice': 7490, 'author': '', 'brand': '', 'publishDate': '', 'sellerId': '', 'isPChome': 1, 'isNC17': 0, 'couponActid': [], 'BU': 'ec'}, {'Id': 'DMAAEC-A900C2EF7', 'cateId': 'DMAAEC', 'picS': '/items/DMAAECA900C2EF7/000002_1638858885.jpg', 'picB': '/items/DMAAECA900C2EF7/000001_1638858885.jpg', 'name': 'SONY 索尼 HT-A7000  DOLBY ATMOS  7.1.2聲道家庭劇院', 'describe': 'SONY 索尼  HT-A7000 DOLBY ATMOS 7.1.2聲道 家庭劇院\\r\\n7.1.2 聲道支援 dolby atmos dts:x', 'price': 39900, 'originPrice': 39900, 'author': '', 'brand': '', 'publishDate': '', 'sellerId': '', 'isPChome': 1, 'isNC17': 0, 'couponActid': [], 'BU': 'ec'}, {'Id': 'DCAY7T-A900AWDDU', 'cateId': 'DCAY7T', 'picS': '/items/DCAY7TA900AWDDU/000002_1602145312.jpg', 'picB': '/items/DCAY7TA900AWDDU/000001_1642122889.jpg', 'name': 'SONY 耳罩式耳機 WH-1000XM4 無線藍牙 HD降噪 音質升級 降噪優化【保固一年】', 'describe': '▼每日強檔‧瘋殺特賣▼SONY 耳罩式耳機 WH-1000XM4 無線藍牙/有線兩用 HD降噪 音質升級 降噪優化【保固一年】\\r\\n《限時狂降★週一10點回價》\r\n開始﹕０１／１４(星期五)１０：００  \r\n結束﹕０１／１７(星期一)１０：００\r\n原價$10900．限時價↘$8590限時限量 把握機會不要錯過!\r\n\r\n\r\n【01 14~01 16 每日10點開搶 名額有限】\r\n全周邊滿$2500最高送15%p幣\r\n●玉山pi卡綁定pi錢包保證5%，無上限.\r\n●不限支付保證10%，最高送250p幣(限量~送完為止).\r\n\r\n● 附原廠耳機線 可有線 無線兩用\r\n● 智能免摘 speak to chat 聊天模式\r\n● 可同時與兩組裝置連線\r\n● 全新dsee extreme音質還原技術與ldac提供高品質音訊\r\n● hd 降噪處理器qn1領先的降噪技術\r\n★更多品牌精選耳機★\r\n★harman kardon 防水頸掛式耳機 fly bt →點我 go\r\n★sony 耳罩式降噪耳機 wh-1000xm4 公司貨藍色 →點我 go', 'price': 8590, 'originPrice': 8590, 'author': '', 'brand': '', 'publishDate': '', 'sellerId': '', 'isPChome': 1, 'isNC17': 0, 'couponActid': [], 'BU': 'ec'}, {'Id': 'DYAW4G-A900BSZ9H', 'cateId': 'DYAW4G', 'picS': '/items/DYAW4GA900BSZ9H/000002_1641139994.jpg', 'picB': '/items/DYAW4GA900BSZ9H/000001_1642125793.jpg', 'name': 'SONY Xperia 5 III (8G/256G)', 'describe': '贈SONY運動入耳式耳機WI-SP510N+原廠背包+殼貼+好禮二選一SONY Xperia 5 III #玩美合手旗艦\\r\\n6.1 吋 21：9 比例 hdr oled 螢幕 120hz 更新率、240hz 觸控採樣率\r\nram 8gb rom 256gb\r\n5g + 4g 雙卡雙待\r\nqualcomm® 驍龍™ 888 \r\n深色背景強化模式\r\n具防水功能 (ip65 68)\r\n高達 105mm 的光學望遠鏡頭\r\n4500mah 電池\r\n正反面gorilla glass 6 玻璃\r\n360 實景音效音樂 360 空間模擬音效\r\n適用於人類與動物的即時眼部偵測自動對焦功能', 'price': 24990, 'originPrice': 24990, 'author': '', 'brand': '', 'publishDate': '', 'sellerId': '', 'isPChome': 1, 'isNC17': 0, 'couponActid': [], 'BU': 'ec'}, {'Id': 'DPAD06-A900BL9KG', 'cateId': 'DPADZ9', 'picS': '/items/DPAD06A900BL9KG/000002_1636019387.jpg', 'picB': '/items/DPAD06A900BL9KG/000001_1636019387.jpg', 'name': '【SONY 索尼】BRAVIA 65型 4K OLED Google TV 顯示器 XRM-65A90J', 'describe': '[21年新上市] Sony BRAVIA  65型 4K OLED Google TV 顯示器 XRM-65A90J 《送基本安裝》\\r\\n1. 獨一無二的 oled 面板技術，呈現純粹黑色與驚人的對比度。\r\n2. 認知智慧處理器xr\r\n3. xr oled 對比增強\r\n4. 平面聲場技術進階版\r\n5. google tv: 全新使用者介面，容易操作，迎接無限串流娛樂體驗。\r\n6. 提供多種尺寸選擇: 65”、55“', 'price': 154900, 'originPrice': 154900, 'author': '', 'brand': '', 'publishDate': '', 'sellerId': '', 'isPChome': 1, 'isNC17': 0, 'couponActid': [], 'BU': 'ec'}, {'Id': 'DGBJJ3-A900BSV89', 'cateId': 'DGBJJ3', 'picS': '/items/DGBJJ3A900BSV89/000002_1632318886.jpg', 'picB': '/items/DGBJJ3A900BSV89/000001_1632318885.jpg', 'name': '【 SONY 索尼 】副廠 PS5 多功能雙層主機全收納包', 'describe': '【  SONY 索尼  】PS5  副廠多功能雙層主機全收納包\\r\\n雙層款 全收納配件包 \r\n●手提 側背皆可使用\r\n●穩定主機 收納方便\r\n●美觀造型\r\n●側邊鍊條加強\r\n●尺寸 43cm*22cm*21cn', 'price': 890, 'originPrice': 890, 'author': '', 'brand': '', 'publishDate': '', 'sellerId': '', 'isPChome': 1, 'isNC17': 0, 'couponActid': ['C024813', 'C025307', 'C025308'], 'BU': 'ec'}, {'Id': 'DGBJJ3-A900B6O9O', 'cateId': 'DGBJJ3', 'picS': '/items/DGBJJ3A900B6O9O/000002_1615435526.jpg', 'picB': '/items/DGBJJ3A900B6O9O/000001_1615435526.jpg', 'name': 'SONY 索尼 PS5 副廠 主機散熱底座 多功能遊戲手把充電支架', 'describe': 'SONY 索尼 PS5 副廠 主機散熱底座 多功能遊戲手把充電支架\\r\\n充電收納二合一\r\n光碟版數位版主機通用\r\n金屬底座風扇快速冷卻主機', 'price': 899, 'originPrice': 899, 'author': '', 'brand': '', 'publishDate': '', 'sellerId': '', 'isPChome': 1, 'isNC17': 0, 'couponActid': ['C024813', 'C025307', 'C025308'], 'BU': 'ec'}, {'Id': 'DMAAF7-A900AEN2S', 'cateId': 'DMAAE0', 'picS': '/items/DMAAF7A900AEN2S/000002_1575876436.jpg', 'picB': '/items/DMAAF7A900AEN2S/000001_1575876436.jpg', 'name': 'SONY 索尼 4K藍光播放機 UBP-X700', 'describe': 'SONY 索尼 4K藍光播放機 UBP-X700\\r\\n4k藍光播放器，影片場景更逼真\r\n廣泛支援視訊 音樂串流服務\r\n4k升頻高達60p呈現精采畫面\r\n兩個 hdmi 輸出可別運用於\r\n視訊與音訊，讓聲音更清晰', 'price': 9900, 'originPrice': 9900, 'author': '', 'brand': '', 'publishDate': '', 'sellerId': '', 'isPChome': 1, 'isNC17': 0, 'couponActid': [], 'BU': 'ec'}, {'Id': 'DYAW3U-A900BPYL0', 'cateId': 'DYAW3U', 'picS': '/items/DYAW3UA900BPYL0/000002_1641977420.jpg', 'picB': '/items/DYAW3UA900BPYL0/000001_1641977420.jpg', 'name': 'SONY Xperia 1 III (12G/256G)', 'describe': '▲虎氣滿滿新年來▲★贈+原廠背包+殼貼等好禮SONY Xperia 1 III (12G/256G)\\r\\n《好運到家18折起》 \r\n開始：１／１３（四）１１：００\r\n結束：１／２０（四）１０：５９\r\n網路價$３３９９０．限時價↘$２９９９０\r\n\r\n\r\n6.5 吋 21:9 cinemawide™ 4k hdr oled 120hz 更新率顯示幕\r\nram 12gb rom 256gb\r\n5g + 4g 雙卡雙待 \r\nqualcomm® 驍龍™ 888 \r\n全段立體聲雙揚聲器\r\n具防水功能 (ip65 68)\r\n高達 105mm 的光學望遠鏡頭\r\n4500mah 電池 最高 30w 有線快充\r\n提供無線充電、無線電量\r\n霧面玻璃背蓋  gorilla glass 6 玻璃\r\n240hz 降低動態影像模糊、h.s. 電源控制\r\n適用於人類與動物的即時眼部偵測自動對焦功能', 'price': 29990, 'originPrice': 29990, 'author': '', 'brand': '', 'publishDate': '', 'sellerId': '', 'isPChome': 1, 'isNC17': 0, 'couponActid': [], 'BU': 'ec'}, {'Id': 'DYAQ0R-A900BG6WX', 'cateId': 'DYAQ0X', 'picS': '/items/DYAQ0RA900BG6WX/000002_1642151131.jpg', 'picB': '/items/DYAQ0RA900BG6WX/000001_1642151131.jpg', 'name': 'SONY WF-1000XM4 黑色 降噪真無線耳機', 'describe': '★註冊送KKBOX 60天再送5%P幣★SONY WF-1000XM4 黑色 降噪真無線耳機《公司貨註冊保固1年6個月》\\r\\n送5%p幣活動說明 \r\n付款方式：任一付款方式並全額支付。\r\n(各商品可用方式以結帳頁標示為主，若以pchome儲值 禮券搭配其他付款方式，不符贈點資格)\r\n☆註冊送kkbox無損60天儲值卡☆\r\n★即日起至2022 2 13註冊送★\r\n1. kkbox無損60天儲值卡 (此為原廠活動贈品，僅送乙組 顆。請至sony官網登記，贈品將於補寄，造成不便敬請見諒。) \r\n\r\n\r\n■ 台灣sony公司貨 官網註冊登入保固 (依官網公布為準)\r\n■ 先進的數位降噪與整合式處理器 v1\r\n■ 無線高解析音質的音質\r\n■ 智慧聆聽和清晰的通話品質\r\n■ 人體工學表面設計可穩固貼合\r\n■ 防水設計和長效電池續航力\r\n■ ncc許可字號:ccao21lp0490t1\r\n*耳塞保養祕訣：請使用乾布擦拭髒污，請勿使用酒精或濕紙巾，會加速耳塞的耗損程度。\r\n\r\n★銀色賣場請點我\r\n\r\n\r\n★sony原廠商品皆為封膜式包裝，確定商品購買前請勿拆封，若拆封後退貨，商品無法復原整新 將有可能費用產生，造成您的不便深感抱歉，謝謝!\r\n\r\n《24h出貨請至此賣場下單》', 'price': 7490, 'originPrice': 7490, 'author': '', 'brand': '', 'publishDate': '', 'sellerId': '', 'isPChome': 1, 'isNC17': 0, 'couponActid': [], 'BU': 'ec'}, {'Id': 'DMAF03-A900BQDVK', 'cateId': 'DMAF03', 'picS': '/items/DMAF03A900BQDVK/000002_1630636674.jpg', 'picB': '/items/DMAF03A900BQDVK/000001_1630636674.jpg', 'name': 'SONY 索尼 SRS-XB13  EXTRA BASS™ 可攜式無線 藍芽喇叭', 'describe': 'SONY 索尼 SRS-XB13 EXTRA BASS™ 可攜式無線 藍芽喇叭\\r\\nextra bass 給您深沉強力的音效\r\n藍牙連線和 fast pair 技術\r\n防水防塵 (ip67 等級)\r\n輕巧體積，電池續航力 16 小時', 'price': 1890, 'originPrice': 1890, 'author': '', 'brand': '', 'publishDate': '', 'sellerId': '', 'isPChome': 1, 'isNC17': 0, 'couponActid': [], 'BU': 'ec'}, {'Id': 'DYAQ9D-A900AT4UE', 'cateId': 'DYAQ0R', 'picS': '/items/DYAQ9DA900AT4UE/000002_1641960578.jpg', 'picB': '/items/DYAQ9DA900AT4UE/000001_1641960578.jpg', 'name': 'SONY WH-1000XM4 輕巧無線藍牙降噪耳罩式耳機 - 黑色', 'describe': '送200禮券+購物袋+耳機架SONY WH-1000XM4 黑色 藍牙降噪耳罩式耳機《公司貨註冊保固2年》\\r\\n■ 台灣sony公司貨保固12個月，官網註冊延長12個月■ 電池續航力 30 個小時■ 獨家研發的 hd 降噪處理器 qn1■ 情境分為旅行、步行和等待 3 種狀態■ 快充 10 分鐘，支撐 5 小時播放\r\n▌精選贈品 ▌環保購物袋+7-11禮卷$200+專用原木耳機架乙個(贈品將隨機寄出，若沒收到贈品，請來信告知，將補寄給您，造成不便，請見諒。)\r\n貼心小提醒：網購享有7天猶豫期，但非指7天內可"隨意試用"\r\n★原廠商品包裝，確定商品購買前請勿拆封，若拆封後退貨(瑕疵品除外)，商品無法復原整新，將有可能造成3-5成費用產生，造成您的不便深感抱歉，謝謝!', 'price': 8990, 'originPrice': 8990, 'author': '', 'brand': '', 'publishDate': '', 'sellerId': '', 'isPChome': 1, 'isNC17': 0, 'couponActid': [], 'BU': 'ec'}, {'Id': 'DMAAEC-A900C2KEE', 'cateId': 'DMAAEC', 'picS': '/items/DMAAECA900C2KEE/000002_1638953497.jpg', 'picB': '/items/DMAAECA900C2KEE/000001_1638953497.jpg', 'name': 'SONY 索尼 HT-A7000  DOLBY ATMOS  7.1.2聲道家庭劇院+SA-RS3S+SA-SW5', 'describe': 'SONY 索尼  HT-A7000 DOLBY ATMOS 7.1.2聲道 家庭劇院+SA-RS3S+SA-SW5\\r\\n商品含:sa-rs3s 無線後環繞+sa-sw5無線重低音', 'price': 67900, 'originPrice': 67900, 'author': '', 'brand': '', 'publishDate': '', 'sellerId': '', 'isPChome': 1, 'isNC17': 0, 'couponActid': [], 'BU': 'ec'}, {'Id': 'DGBJA7-A900B6502', 'cateId': 'DGBJA7', 'picS': '/items/DGBJA7A900B6502/000002_1614764722.jpg', 'picB': '/items/DGBJA7A900B6502/000001_1614764722.jpg', 'name': 'SONY PS4 Pro主機CUH-7218系列 1TB-極致黑 盒損福利品', 'describe': '盒損福利全新機SONY PS4 Pro主機CUH-7218系列 1TB-極致黑\\r\\n僅盒損，內容物全新\r\n■滿足追求更高層次遊戲體驗的玩家\r\n■超高解析度，如臨其境震撼視覺享受\r\n■gpu性能升級，更細緻流暢、穩定的畫面\r\n■支援hdr，更逼真生動，如真實世界般視覺效果\r\n■外型三段傾斜設計具強烈存在感，中央點綴鏡面logo\r\n原廠一年保固，產品保固採線上登記', 'price': 9980, 'originPrice': 9980, 'author': '', 'brand': '', 'publishDate': '', 'sellerId': '', 'isPChome': 1, 'isNC17': 0, 'couponActid': ['C024813', 'C025307', 'C025308', 'C025306'], 'BU': 'ec'}]}
```

* 看到這個JSON包含超多資訊，簡要一點看，他包含哪幾大塊：  


```python
data.keys()
#> dict_keys(['QTime', 'totalRows', 'totalPage', 'range', 'cateName', 'q', 'subq', 'token', 'isMust', 'prods'])
```

* 了解，那我要的，其實只是"prods"的部分，看一下這一塊的資料  


```python
data["prods"][:2]
#> [{'Id': 'DEBB55-A900AXCHG', 'cateId': 'DEBB55', 'picS': '/items/DEBB55A900AXCHG/000002_1642059796.jpg', 'picB': '/items/DEBB55A900AXCHG/000001_1642059795.jpg', 'name': 'PX大通HR7PRO星光夜視行車紀錄器SONY感光元件GPS區間測速記錄器', 'describe': '每日強檔▼瘋殺特賣PX大通HR7PRO星光夜視旗艦王汽車行車紀錄器真HDR高動態SONY STARVIS感光元件GPS區間測速記錄器贈32G記憶卡\\r\\n《只有一天★限時狂降》\r\n開始﹕０１／１３(星期四)１０：００\r\n結束﹕０１／１４(星期五)１０：００\r\n網路價$4988．限時價↘$４１８８\r\n滿＄３０００再送３００ｐ幣\r\n======================================\r\n\r\n🚩【夜視新標竿】: hdr高動態錄影 +sony starvis星光級感光元件。\r\n>>真hdr:抗led或hid高強光、車牌影像清晰不過曝 starvis:提升整體亮度、解決環境過暗                \r\n🚩【安全新標竿】: ts碼流存檔機制。\r\n>>以秒截檔設計，確保錄影檔案不毀損  \r\n🚩【好用新標竿】: 一鍵進格式化。\r\n>>動動手指，輕鬆格式化    \r\n🚩【測速新標竿】: gps區間+定點測速雙警示。\r\n>>gps雙衛星，雙預警加倍安心\r\n\r\n👍👍👍多位論壇部落客一致推薦👍👍👍\r\n★pchome開箱focus專欄★大力推薦\r\n★tony的玩樂生活筆記★部落客開箱推薦\r\n★小老婆汽機車資訊網★網路車類媒體大力推薦\r\n★正宅爸3c開箱★部落客開箱推薦\r\n★星星陳世章★部落客開箱推薦\r\n★寶井秀人hyde★部落客開箱推薦\r\n★basic施鈞程★部落客開箱推薦\r\n★硬是要學★部落客開箱推薦\r\n★鑫部落★部落客開箱推薦\r\n★另有其他規格款式可選↓↓↓\r\n\r\n《★鑽石版頂規 ★ 》\r\n《★hr7 pro》\r\n《★鈦金版高規 ★ 》\r\n《★hr7g》\r\n《★白金版均規 ★ 》\r\n《★hr7》\r\n\r\n※六大優勢\r\n1. hdr高動態錄影+sony starvis星光夜視技術>>抗led強光、夜間車牌清晰\r\n2. ts碼流存檔機制>>以秒截檔設計，不正常斷電、車禍撞擊，錄影檔案不毀損\r\n3. gps區間測速>>警示功能提升\r\n4. 2吋ips高清螢幕>>廣視角，資訊顯示清楚\r\n5. 不掉落魔法貼>>全新支架設計，機身更穩固\r\n6. 一鍵進格式化>>簡易操作，確保錄影成功率\r\n\r\n※完整特色\r\n▼ 1080p 30fps hdr高動態\r\n▼ sony starvis超級星光夜視\r\n▼ f 1.4超大光圈+7玻1ir鏡頭\r\n▼ 1080p 60fps wdr寬動態\r\n▼ ips廣視角高清螢幕\r\n▼ 以秒截檔ts碼流存檔機制\r\n▼ 行車魔法貼不掉落支架\r\n▼ 148度大廣角\r\n▼ hud抬頭顯示\r\n▼ 13段曝光值調整\r\n▼ gps雙衛星接收快速定位\r\n▼ gps區間+定點測速照相雙預警\r\n▼ g-sensor碰撞自動偵測鎖檔\r\n▼ 一鍵鎖檔備份關鍵檔案 \r\n▼ 一鍵快捷啟動記憶卡格式化\r\n▼ 一鍵快速拍照\r\n▼ 記憶卡異常提醒\r\n▼ 記憶卡30天定期格式化提醒', 'price': 4188, 'originPrice': 4188, 'author': '', 'brand': '', 'publishDate': '', 'sellerId': '', 'isPChome': 1, 'isNC17': 0, 'couponActid': [], 'BU': 'ec'}, {'Id': 'DGBJGB-A900CAR5E', 'cateId': 'DGBJGB', 'picS': '/items/DGBJGBA900CAR5E/000002_1641459421.jpg', 'picB': '/items/DGBJGBA900CAR5E/000001_1641459421.jpg', 'name': 'SONY PS5原廠 DualSense 無線控制器-銀河紫 CFI-ZCT1G04', 'describe': 'SONY PS5原廠 DualSense 無線控制器銀河紫 CFI-ZCT1G04\\r\\n來自銀河系的新色 為遊戲之夜點燃熱情 \r\n創新ps5控制器 深入栩栩如生遊戲體驗 \r\n■搭載觸覺回饋和自適應扳機，更身歷其境的感受\r\n■直覺配置搭配強化操作桿，讓你掌握全局\r\n■觸覺回饋真實感受到武器後座力等動態震動\r\n■自適應扳機體驗往後拉弓、急剎車等力量與張力\r\n■內建麥克風與3.5mm耳機連接端\r\n■帶來更多方式向全世界播送冒險經歷\r\n\r\n☛看看其他銀河系新色-星塵紅', 'price': 2280, 'originPrice': 2280, 'author': '', 'brand': '', 'publishDate': '', 'sellerId': '', 'isPChome': 1, 'isNC17': 0, 'couponActid': ['C025307', 'C025308'], 'BU': 'ec'}]
```

* 很明顯的是一個record orientation(或說，list of dictionaries)的格式，那我直接用`pd.DataFrame()`就可以把他parse成dataframe了  


```python
pd.DataFrame(data["prods"]).head()
#>                  Id  cateId  ...         couponActid  BU
#> 0  DEBB55-A900AXCHG  DEBB55  ...                  []  ec
#> 1  DGBJGB-A900CAR5E  DGBJGB  ...  [C025307, C025308]  ec
#> 2  DMAAEC-A900BFLRR  DMAAEC  ...                  []  ec
#> 3  DPAD06-A900BFGWY  DPADZ9  ...                  []  ec
#> 4  DYAQBC-A900BNLZQ  DYAQF4  ...                  []  ec
#> 
#> [5 rows x 16 columns]
```

* 搞定拉！  

### Get data from APIs  

* 從這邊以下的資訊，就先不整理了，因為範例我都無法reproduce(因為認證的key已經不能用了)  
* 我計畫之後在爬蟲的主題(緯育+Yotta)，把這些東西處理好，畢竟爬蟲時最容易碰到這些問題  

### Set API parameters  

### Set request headers  

## Working with nested JSON's  

## Combining multiple datasets  
