# AutoApi (2021-6-24) ———— E5自動續期

## 說明 ##
* E5自動續期程序，但是**不保證續期**
* 設置了**週六日(UTC時間)不啓動**自動調用，周1-5每6小時自動啓動一次 （修改看教程）
* 調用api保活：
     * 查詢系api：onedrive,outkook,notebook,site等
     * 創建系api: 自動發送郵件，上傳文件，修改excel等
     
### 相關 ###
* AutoApi: https://github.com/wangziyingwen/AutoApi
* **錯誤及解決辦法/續期相關知識/更新日誌**：https://github.com/wangziyingwen/Autoapi-test
   * 大部分錯誤說明已更新進程序，詳細請運行後看action日誌報告

## 步驟 ##
* 準備工具：
   * E5開發者賬號（**非個人/私人賬號**）
       * 管理員號 ———— 必需 
   
* 步驟大綱：
   * 微軟獲取**應用程式ID**、**應用程式密碼**、**密鑰**
   * GIHTHUB方面的準備工作  （獲取Github密鑰、設置secret）
   * 試運行
   
#### 微軟方面的準備工作 ####

* **第一步，註冊應用，獲取應用id、secret**

    * 1）點擊打開[應用註冊頁面](https://go.microsoft.com/fwlink/?linkid=2083908)，點擊+**新增註冊**
    
    * 2）填入名稱，「 支援的帳戶類型 」選第三個" **任何組織目錄中的帳戶 (任何 Azure AD 目錄 - 多租用戶) 和個人 Microsoft 帳戶 (例如 Skype、Xbox)**
    
    * 重新導向 URI Web 填入 https://login.microsoftonline.com/common/oauth2/nativeclient 點擊**註冊**
        
    * 3）複製 **應用程式 (用戶端) 識別碼** 到記事本(**這是應用程式ID**！)，點選左邊管理的**憑證及祕密**，點選+**用戶端密碼**，設定到期時間，最高爲兩年，在點選新增，複製用戶端密碼的**值**保存（**這是應用程式密碼**！）
    	*  [進階應用] 抓到設定 token 的網路請求後，可以發現有一個到期的日期，請求裡面是兩年，修改請求之後重放，就可以改超長時間了！ 
       
* **第二步，取得refresh_token(微軟密鑰)**

    * 打開資料夾內的GetToken.html檔案
    * 輸入應用id = 應用程式ID
    * 輸入應用密碼 = 應用程式密碼
    * 點選管理員號旁邊的**獲取code**進去之後底下打勾，按接受，再點選底下**先點此按鈕，用管理員號登錄授權**，底下按接受。呈現空白頁正常
    * 回到 AutoApi GetToken 畫面，點選管理員號旁邊**獲取code**，呈現空白後，複製網址，貼在右邊的**輸入url-code**，接著按**獲取Token**
    * 會跳到一個新網頁，內容寫 error 是沒錯的，接下來比較複雜一點
    * Firefox 滑鼠右鍵 -> 檢查 -> 找到 Network -> 搜尋打上 Token 後，重新整理頁面 -> 底下出現一個 Token -> 右鍵 -> Copy -> cURL
    * Chrome 開啓開發者工具，一樣上面找到 Netwrok 搜尋打上 Token ......同上
    * 複製到，記事本，或者是文字編輯軟體都可以，找到 **-H 'Origin: null' \** ，刪除他
    * 然後整段複製，丟到 終端機 裏面...按下回車鍵 Enter (此爲 Mac 系統操作)
    * Windows 使用者，需另找 curl 指令安裝，或者使用 https://reqbin.com/curl 執行
    * 之後就會帶出一大段代碼找到 **refresh_token":"** 這個地方**這是密鑰**，複製後面**冒號**裏面的文字，最後**"}%** 不要複製
    
 ___________________________________________________
 #### GITHUB準備工作 ####

 * **第一步，新建repository項目**
 
     新建新的repository，設定隱私，設定名稱，上傳下載的檔案，除了.yml不用上傳，其他一律上傳
     
 * **第二步，新建github密鑰**
 
    * 1）進入你的個人設置頁面 (右上角頭像 Settings)，選擇 Developer settings -> Personal access tokens -> Generate new token
    
    * 2）設置名字為 **GH_TOKEN** , 然後勾選repo，點擊 Generate token ，最後**複製保存**生成的github密鑰（**獲得了github密鑰**，一旦離開頁面下次就看不到了！）
   
 * **第三步，新建secret**
 
    * 1）進去專案後點選 Settings -> 左邊 Secrets -> 右上 New repository secret，新建6個secret： **GH_TOKEN、MS_TOKEN、CLIENT_ID、CLIENT_SECRET、CITY、EMAIL**  

    
     **(以下填入內容注意前後不要有空格空行)**
 
     GH_TOKEN
     ```shell
     github密鑰 (第三步獲得)，例如獲得的密鑰是abc...xyz，則在secret頁面直接粘貼進去，不用做任何修改，只需保證前後沒有空格空行
     ```
     MS_TOKEN
     ```shell
     微軟密鑰（第二步獲得的refresh_token）
     ```
     CLIENT_ID
     ```shell
     應用程序ID (第一步獲得)
     ```
     CLIENT_SECRET
     ```shell
     應用程序密碼 (第一步獲得)
     ```
     CITY
     ```shell
     城市 (例如Taipei,自動發送天氣郵件要用到)
     ```
     EMAIL
     ```shell
     收件郵箱 (自動發送天氣郵件要用到)
     ```
 * **第四步，設定 workflows **

    * 1）點擊 Actions ，點選  ** set up a workflow yourself **
    * 依照資料夾內三個 ApiOfRead.yml、ApiOfWrite.yml、UpdateToken.yml 檔案內容，一樣複製貼上，並建立成三個檔案
    * 內容複製貼上後，名稱也對應打上，點選右邊 Start commit -> 點選 Commit new file 就可以存檔
    * 餐個檔案設定完後，在 Actions 底下，會出現三個名稱 Run Api.Read 、Run Api.Write、 Update Token 三個

________________________________________________

#### 試運行 ####

   * 1）點擊上欄中間的Action進入運行日誌頁面
   
       會看到左邊有三個流程，一個Run api.Read，一個Run api.Write，一個Update Token。
       
         工作流程說明
             Run api.Write：創建系api，一天自動運行一次
             Run api.Read:  查詢系api，每6小時自動運行一次
             Update Token： 微軟密鑰更新，每2天運行一次
             
   
   * 2）點擊兩次右上角的星星（Unstar，就是fork按鈕的隔壁）啓動action，
   
        再點擊上面的Action選擇Run api.Read或者api.Write流程 -> build -> run api 就能看到每次的運行日誌

       （必需點進去build裡面的run api.XXX看下，api有沒有調用到位，操作有沒有成功，有沒有出錯）

     
   * 3）再點兩次星星，查看是否能再次成功運行
   
        然後點擊Action里的 update token 流程 -> build -> update token ，日誌里顯示「微軟密鑰上傳成功」。
       
        同時，依次點擊頁面上欄右邊的 Setting -> 左欄 Secrets（也就是Github方面準備的第三步的secret頁面），應該能看到MS_TOKEN顯示剛剛update了
        
        （這一步是為了保證重新上傳到secret的token是正確的）
        
       
   * 4）最終確認 Actions 底下的 三個流程，點進去後右手邊有出現綠色打勾，才是正常執行
    
        
#### 教程最後 ####

   程序會自行按計劃啓動，不必操心。
   
        但是github更新了防止薅羊毛的規則，如果倉庫60天無任何變動，將會暫停Action，但是會發郵件通知，所以請留意郵箱，收到郵件請上來手動啓動一下action。
       （我還沒有收到過此郵件，但是據說郵件里會有啓動鏈接，或者上來按兩次星星按鈕就行）
   
   
### 教程完 ###

__________________________________________________________________________

## 額外設置 （看不懂請忽略）##
   * **定時啓動修改**

   * **多賬號/應用支持**
    
   * **超級參數設置**

#### 定時啓動修改 ####
   
   我設定的每6小時自動運行一次（週六日不啓動），每次調用3輪（點擊右上角星星/star也可以立馬調用一次），你們自行斟酌修改（我也不知道保持活躍要調用多少次、多久）：

  * 定時自動啓動修改地方：在.github/workflow/autoapi.yml(只修改這一個)文件里，自行百度cron定時任務格式，最短每5分鐘一次
