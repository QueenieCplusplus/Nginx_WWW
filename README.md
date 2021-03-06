# Nginx_WWW
web server 網站伺服器

WWW 技術解決了遠端資訊中的資料連接、資訊顯示、影像傳遞的需求，成為網路上最常見的傳播方式，web server 為提供此網路資訊瀏覽服務的終端設備。


![nginx architecture](https://raw.githubusercontent.com/QueenieCplusplus/Nginx_WWW/master/nginx_architecture.png)

# Nginx Web Server Architecture 說明：

伺服器啟動後，產生一個主處理程序 Master Process => 設定檔解析、資料結構初始化、模組註冊、網路監聽

主處理程序執行一系列工作後，產生多個工作處理程序 Worker Process => 處理程序初始化、模組呼叫、請求處理

請求處理中（用戶請求動態網站的一過程）=>『Nginx 伺服器』與『後端伺服器』之間通訊，前者將請求透過 Cache 傳到後端伺服器，後端伺服器處理請求，即進行資料處理和頁面組織，然後將結果回傳。


# Web Server 提供資訊瀏覽/查詢功能外，尚發展四種功能：

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
  
  與 Single Process 不同，代理伺服器採用 Master-Slave 模型。
  
  
  * multi-process(worker-process), 多處理程序
  
     一旦伺服器接收到使用者端（客戶端）發起之請求，便由  Master Process 運用 fork() 產生子處理程序和用戶端建立連線，
     直到連線中斷時，則子處理程序生命週期結束。
     
     子處理程序的優勢是與其他子處理程序分開進行任務，彼此獨立，倘若過程中受到干擾，產生問題時，不會影響其他子處理程序，
     不會將問題蔓延到其他子處理程序中，確保服務的穩定性。另外在生命週期結束時，也會自動的資源回收，不留下垃圾。
     
     然而一開始主處理程序產生子處理程序時，就會對系統造成負擔！
     
     
  * 預產生處理程序
     
     改進如上的多處理程序，提前了產生子處理程序的時機，在用戶端抑或使用者端的請求尚未來到之前就預先產生好，直到請求到來時，
     主處理程序可以直接分配子處理程序和用戶端連線進行互動，互動完成後，該處理程序不會結束，由主處理程序管理後再做工作任務的分配。
  
  * multi-thread (worker-thread), 多執行緒
  
     多執行緒和多處理程序方式相似，是指 server 每接受到一用戶端 client 時，會由伺服器的 Master Process 衍生出
     thread 出來和 client 互動。
     
     ![multithreads](https://raw.githubusercontent.com/QueenieCplusplus/Nginx_WWW/master/multi-threads.png)

     由於作業系統產生 thread 的負擔遠遠小於產生一個 process 的負擔，所以能減輕伺服器對系統資源的要求。然而也有缺點，
     因為多執行緒位於同一處理程序內，但是彼此間會競奪記憶體空間，相互產生影響，倘若開發者在釋放資源和回收資源上沒有做好
     相應的資源管理，則產生出錯的風險。

     運行時間一長，出錯的累積數量會導致伺服器的重大影響。（微軟的 IIS 伺服器在多執行緒方面還算穩定。）
  
  * async, 非同步
  
    * 非同步阻塞
     
      實務上很少使用，因為 server 端的 IO 如果不能馬上獲得結果，會等待傳回結果，期間不做任何工作。（反之，client 接收方發送請求後，不必等待回應，可進行其它工作）。
      
    
    * 非同步非阻塞
    
      client 接收方發送請求予伺服器接受請求後的傳回結果，雙方彼此都不等待結果，可以繼續進行其它工作任務，IO 操作完成後，伺服器等待完成狀態和結果通知 client 接收方，client 接收方再回應發送方 server。（甚至比同步阻塞還有效率）
      
      ![async block](https://raw.githubusercontent.com/QueenieCplusplus/Nginx_WWW/master/async%20block.png)
      
      圖片引用出處：https://luminousmen.com/post/asynchronous-programming-blocking-and-non-blocking
      
     * 同步阻塞
     
       client 端發送請求後，等待回應，不做任何其它工作 ; server 端處理請求的 IO 如果無法馬上獲得結果，會等待，直到傳回結果，
       期間不能執行任何其它動作。（失去效率）
       
     * 同步非阻塞
     
       只要伺服器沒有獲得處理結果，伺服器端會一直等待，確定發送完結果予 client 後，才進行下一次處理請求過程。（不使用）
     

# 處理程序

（處理程序間互動）

  伺服器啟動後，產生一個主處理程序 Master Process => 設定檔解析、資料結構初始化、模組註冊、網路監聽

  主處理程序執行一系列工作後，產生多個工作處理程序 Worker Process => 處理程序初始化、模組呼叫、請求處理
  
  * 外界 - Master
  
    主處理程序與外界透過訊號（電磁波）溝通。
  
    而處理程序間的互動，則依賴管線 Channel | Pipeline:
  
  * Master 主程序 - Workers 工作程序 (| Master-Slave)
  
    主處理程序藉由 fork 函數，產生了工作處理程序。 Nginx 伺服器啟動後，Master Process 根據設定檔決定產生的 Worker Process 數量，然後建立一張 Gloabal 工作處理程序表，用於儲存目前尚未退出的所有 Worker Processes。
    
    入表紀錄的工作處理程序與主處理程序間行程一個單向管線 Pipeline，由主處理程序指向工作處理程序：
    內容包含了 指令、工作處理程序的id、表終索引、必要檔案描述符號。
    
    工作處理程序則捕捉管線中的事件，讀取之，並且解析其中指令，採取對應措施。
  
  * Worker - Worker 工作程序間
  
    同上，工作處理程序間也是透過管線互動，但是由於工作管理程序彼此間是隔開的，因此彼此溝通，尚需要透過主管理程序。
    主處理程序透過表來區別工作處理程序的id，並且傳遞該工作處理程序的管線控制碼予其他需要與之溝通的工作處理程序。
    
  * Thread 執行緒
  
    ![single thread](https://raw.githubusercontent.com/QueenieCplusplus/Nginx_WWW/master/single-thread.png)
  
    When a process starts, it is assigned memory and resources. Each thread in the process shares that memory and resources. In single-threaded processes, the process contains one thread. The process and the thread are one and the same, and there is only one thing happening.
    
  * Event Listening Loop 事件聆聽的循環模式
  
    處理程序和執行緒如要持續不間斷的工作，都是透過事件聆聽模型，此模型可能是自動的，也可能是自定義的。
    
    設計 Run-Loop 事件時，要對事件輸入事件和對應事件作出的回應。
    
    設計事件聆聽迴圈的元件包含：
    
    * Timer, 計時器
    
    * Event, 輸入的事件來源
    
    * Observer, 監聽器（被通知者）
    
      可以設計給監聽器聆聽特定事件。
  

     流程如下：
     
     
         Event Occurs 
         
                    =>

                      Msg Log from Run-Loop 
                     
                                          => 

                                            Observer rcv the Msg Log 
                                          
                                                                 => 

                                                                    Execute ...(Async)


# 快取機制

（分散後端伺服器的壓力）

 為加強 Nginx 對請求的回應效率，並且降低對網路的壓力，故採取快取機制，將歷史回應資料快取至本機，每次 Nginx 啟動服務後一段時間，會啟動本機快取對內容重建索引，保障快取機制提取資料的速度（快速存取）。
 
 
 * 實際上運作：
 
   主要由快取索引重建 Cache Loader 和快取索引管理 Cache Manager 兩種處理程序完成工作，此兩程序程序維護的記憶體索引中繼資料庫，為工作處理程序對快取資料的快速查詢提供便利性。

   Cache Loader 由主處理程序產生（預設為一分鐘），在 Cache Metadata 產出後自動退出。

   Cache Manager 存在於主處理程序的整個生命週期中，負責對快取索引進行管理。

   快取索引重建處理程序完成的主要工作是：根據對本機磁碟上的快取檔案在記憶體中建立索引中繼資料庫。
   
   ![cache loader](https://raw.githubusercontent.com/QueenieCplusplus/Nginx_WWW/master/cache.png)

   該處理程序啟動後，對本機磁碟上儲存快取檔案的目錄結構進行掃描，檢查記憶體中已有的快取中繼資料是否正確，
   並且更新索引中繼資料庫。
   
   ![cache manager](https://raw.githubusercontent.com/QueenieCplusplus/Nginx_WWW/master/cache%20table.png)

   快取索引管理程序則是負責對中繼資料是否過期進行判斷。

# 複寫功能

https://github.com/QueenieCplusplus/Nginx_Backend


