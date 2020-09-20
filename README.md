# Nginx_WWW
web server 網站伺服器

WWW 技術解決了遠端資訊中的資料連接、資訊顯示、影像傳遞的需求，成為網路上最常見的傳播方式，web server 為提供此網路資訊瀏覽服務的終端設備。


![nginx architecture](https://raw.githubusercontent.com/QueenieCplusplus/Nginx_WWW/master/nginx_architecture.png)

nginx web server architecture 說明：

伺服器啟動後，產生一個主處理程序 Master Process => 設定檔解析、資料結構初始化、模組註冊、網路監聽

主處理程序執行一系列工作後，產生多個工作處理程序 Worker Process => 處理程序初始化、模組呼叫、請求處理

請求處理中（用戶請求動態網站的一過程）=>『Nginx 伺服器』與『後端伺服器』之間通訊，前者將請求透過 Cache 傳到後端伺服器，後端伺服器處理請求，即進行資料處理和頁面組織，然後將結果回傳。


web server 現在不僅僅提供資訊瀏覽/查詢功能，還發展四種功能：

* 運算效能

* 記憶體容量

* 穩定性

  (1)每分每秒都有大量請求需求，一台伺服器能否提供不間斷的服務，是種考驗。
  
  (2)如何保障網頁伺服器能在大量平行處理請求出現時候，仍然穩定執行。
  
  (3)長時間運轉中，降低出錯率。
  
  (4)產生問題之前能預先防止。
  
  (5)擁有備份，即便產生問題，也能避免資料遺失。

* 簡易性

  實施步驟簡單，部署快速。

# 請求處理

（網頁回應）

  請求處理中（用戶請求動態網站的一過程）=>『Nginx 伺服器』與『後端伺服器』之間通訊，前者將請求透過 Cache 傳到後端伺服器，後端伺  服器處理請求，即進行資料處理和頁面組織，然後將結果回傳。


# 處理程序

（處理程序間互動）

  伺服器啟動後，產生一個主處理程序 Master Process => 設定檔解析、資料結構初始化、模組註冊、網路監聽

  主處理程序執行一系列工作後，產生多個工作處理程序 Worker Process => 處理程序初始化、模組呼叫、請求處理


# 快取機制

（分散後端伺服器的壓力）

 為加強 Nginx 對請求的回應效率，並且降低對網路的壓力，故採取快取機制，將歷史回應資料快取至本機，每次 Nginx 啟動服務後一段時間，會啟動本機快取對內容重建索引，保障快取機制提取資料的速度（快速存取）。
