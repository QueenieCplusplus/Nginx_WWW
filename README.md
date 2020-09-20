# Nginx_WWW
web server 網站伺服器

WWW 技術解決了遠端資訊中的資料連接、資訊顯示、影像傳遞的需求，成為網路上最常見的傳播方式，web server 為提供此網路資訊瀏覽服務的終端設備。


![nginx architecture](https://raw.githubusercontent.com/QueenieCplusplus/Nginx_WWW/master/nginx_architecture.png)

# Nginx Web Server Architecture 說明：

伺服器啟動後，產生一個主處理程序 Master Process => 設定檔解析、資料結構初始化、模組註冊、網路監聽

主處理程序執行一系列工作後，產生多個工作處理程序 Worker Process => 處理程序初始化、模組呼叫、請求處理

請求處理中（用戶請求動態網站的一過程）=>『Nginx 伺服器』與『後端伺服器』之間通訊，前者將請求透過 Cache 傳到後端伺服器，後端伺服器處理請求，即進行資料處理和頁面組織，然後將結果回傳。


# Web Server 現在不僅僅提供資訊瀏覽/查詢功能，還發展四種功能：

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
  
  * 外界 - Master
  
    主處理程序與外界透過訊號（電磁波）溝通。
  
    而處理程序間的互動，則依賴管線 Channel | Pipeline:
  
  * Master 主程序 - Workers 工作程序
  
    主處理程序藉由 fork 函數，產生了工作處理程序。 Nginx 伺服器啟動後，Master Process 根據設定檔決定產生的 Worker Process 數量，然後建立一張 Gloabal 工作處理程序表，用於儲存目前尚未退出的所有 Worker Processes。
    
    入表紀錄的工作處理程序與主處理程序間行程一個單向管線 Pipeline，由主處理程序指向工作處理程序：
    內容包含了 指令、工作處理程序的id、表終索引、必要檔案描述符號。
    
    工作處理程序則捕捉管線中的事件，讀取之，並且解析其中指令，採取對應措施。
  
  * Worker - Worker 工作程序間
  
    同上，工作處理程序間也是透過管線互動，但是由於工作管理程序彼此間是隔開的，因此彼此溝通，尚需要透過主管理程序。
    主處理程序透過表來區別工作處理程序的id，並且傳遞該工作處理程序的管線控制碼予其他需要與之溝通的工作處理程序。
    
  * Thread 執行緒
  
    When a process starts, it is assigned memory and resources. Each thread in the process shares that memory and resources. In single-threaded processes, the process contains one thread. The process and the thread are one and the same, and there is only one thing happening.


# 快取機制

（分散後端伺服器的壓力）

 為加強 Nginx 對請求的回應效率，並且降低對網路的壓力，故採取快取機制，將歷史回應資料快取至本機，每次 Nginx 啟動服務後一段時間，會啟動本機快取對內容重建索引，保障快取機制提取資料的速度（快速存取）。
 
 * 實際上運作：
 
   主要由快取索引重建 Cache Loader 和快取索引管理 Cache Manager 兩種處理程序完成工作，此兩程序程序維護的記憶體索引中繼資料庫，為工作處理程序對快取資料的快速查詢提供便利性。

   Cache Loader 由主處理程序產生（預設為一分鐘），在 Cache Metadata 產出後自動退出。

   Cache Manager 存在於主處理程序的整個生命週期中，負責對快取索引進行管理。

   快取索引重建處理程序完成的主要工作是：根據對本機磁碟上的快取檔案在記憶體中建立索引中繼資料庫。

   該處理程序啟動後，對本機磁碟上儲存快取檔案的目錄結構進行掃描，檢查記憶體中已有的快取中繼資料是否正確，
   並且更新索引中繼資料庫。

   快取索引管理程序則是負責對中繼資料是否過期進行判斷。
