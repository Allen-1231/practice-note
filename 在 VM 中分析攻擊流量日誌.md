---
title: 在 VM 中分析攻擊流量日誌

---

# 在 VM 中監控日誌

### 從 Kaggle 下載資料集到 VM 中

我下載的是 Kaggle 上的 [Network Threat Detection Dataset](https://www.kaggle.com/datasets/rebsonramalho/network-threat-detection-dataset) 資料集。

先在 VM 中用 `hostname -I` 查詢這台電腦的 IP 位址。
![image](https://hackmd.io/_uploads/Sydm6ZUw-x.png)

接著在本地電腦的 cmd 利用 `scp` 指令複製下載的資料集檔案到 VM 中。
![image](https://hackmd.io/_uploads/Sy9Y0bIv-x.png)
因出現 `timed out`，需先在 VM 輸入 `sudo apt install openssh-server -y` 來安裝 SSH (邏輯上的服務開啟，在 Ubuntu 裝上一個「接收端」)

並且要在網路設定將「NAT」改為「橋接介面卡」(這是在處理網路實體的連通)，重啟後再次輸入 `hostname -I` 會得到一個新的 IP。
![image](https://hackmd.io/_uploads/S1esWfIvZx.png)

檔案已成功複製傳送到 VM 裡。
![image](https://hackmd.io/_uploads/rJK0-MUP-l.png)

:::info
可使用 `sudo systemctl status ssh` 來確認 SSH 是否有在跑，**active (running)** 即代表大門已經打開，可以連線。

若為 `inactive` 即代表裝成功但沒啟動，可透過輸入 `sudo systemctl start ssh` 來啟動。
![image](https://hackmd.io/_uploads/HkWwzGUw-g.png)
:::

### 安裝 MariaDB

安裝 SQL 資料庫，

```bash=
# 更新本地套件資料庫
sudo apt update

# 安裝 MariaDB (SQL 資料庫)
sudo apt install mariadb-server -y

# 啟動資料庫服務
sudo systemctl start mariadb
# 或希望下次打開 VM 時不用再手動啟動：
sudo systemctl enable mariadb
```

:::info
使用完離開 MariaDB 畫面後，可以輸入 `sudo systemctl stop mariadb`，下次要用時再重新 `...start...` 啟動一次就好，這樣可以節省 VM 的記憶體。
:::

![image](https://hackmd.io/_uploads/r1wR3BOvbl.png)

![image](https://hackmd.io/_uploads/B1f-6H_DZx.png)

![image](https://hackmd.io/_uploads/SJS76r_vbx.png)

### 建立 MariaDB 分析環境與結構化資料表

- 進入環境
    
    mysql：啟動客戶端程式，視窗會從「Linux 模式」切換到「SQL 模式」。
    
    -u root：指定使用者為最大權限的帳號 root (這裡的指的是資料庫裡的 root)
    
    ```bash=
    sudo mysql -u root
    ```
    
- 建立資料庫與建立表格

    資料庫：
    ```bash=
    # 進入 MySQL 環境後，建立資料庫並使用該資料庫
    CREATE DATABASE IF NOT EXISTS log_analysis;

    USE log_analysis;
    ```

    建立資料庫時加入 `IF NOT EXISTS` 能確保無論腳本重複執行多少次，系統狀態都能保持一致且不因報錯而中斷。

    另外在進入環境前，先查看檔案的前五行，建表時需要用以匹配對應欄位名稱。

    ```bash=
    head -n 5 dataset_1.csv
    ```

    ![image](https://hackmd.io/_uploads/rk95SIuvWl.png)

    表格：

    我直接到 [Network Threat Detection Dataset](https://www.kaggle.com/datasets/rebsonramalho/network-threat-detection-dataset) 上查看表格中有哪些內容，來決定要定義的資料型態，免得欄位有很多資料為 0 被誤認為該欄位只會有數字型態等情況發生。

    ```sql=
    CREATE TABLE IF NOT EXISTS detection (
        srcip VARCHAR(30),
        sport INT,
        dstip VARCHAR(30),
        dsport INT,
        proto VARCHAR(20),
        state VARCHAR(30),
        dur FLOAT,
        sbytes INT,
        dbytes INT,
        sttl INT,
        service VARCHAR(20),
        Sload DOUBLE,
        Dload DOUBLE,
        Spkts INT,
        Dpkts INT,
        swin INT,
        stcpb BIGINT,
        dtcpb BIGINT,
        smeansz FLOAT,
        dmeansz INT,
        trans_depth VARCHAR(100),
        res_bdy_len VARCHAR(100),
        Stime VARCHAR(50),
        Ltime VARCHAR(50),
        synack VARCHAR(30),
        ackdat VARCHAR(30),
        is_sm_ips_ports INT,
        ct_state_ttl INT,
        Label INT
    );
    ```

    ![image](https://hackmd.io/_uploads/SJmBWDOwZl.png)

### 匯入資料

利用以下指令批次匯入巨量日誌，能將原始 CSV 檔直接映射至資料庫表格中。

```sql=
LOAD DATA LOCAL INFILE 'dataset_1.csv'
INTO TABLE detection
FIELDS TERMINATED BY ','
IGNORE 1 ROWS;
```

LOAD DATA LOCAL INFILE：從這台電腦 (LOCAL) 讀取一個檔案 (INFILE) 並載入 (LOAD) 進去。

'dataset_1.csv'：要載入的檔案的檔案路徑。

INTO TABLE detection：與 `INSERT INTO` 一樣，將資料新增進 `detection` 這個表裡。

FIELDS TERMINATED BY ','：明確指示資料庫以逗號作為欄位定界符 (CSV 檔案)。

IGNORE 1 ROWS：忽略第一行。因為已經打進表格而且這行不是資料。
    
![image](https://hackmd.io/_uploads/HJw9Yw_wbl.png)

---

### 分析練習

#### 欄位解釋理解

在進行分析練習以前，先來理解各個欄位名稱所代表的意義，紀錄什麼樣的資訊，對於在練習時才能更清楚自己應該怎麼進行篩選與查詢。

1. 基礎身份資訊
    - `srcip`/`dstip`: 來源 IP 與目的 IP。
    
    - `sport`/`dsport`: 來源埠號與目的埠號。
    - `proto`: 通訊協定 (protocol)。如 TCP，UDP，ICMP。
    - `service`: 服務名稱。如 http，dns，ftp。若顯示 failed 代表該連線在應用層（L7）的識別失敗，雖然底層有傳輸資料，但資料庫的解析引擎無法判定它具體屬於哪種服務。

2. 流量統計資訊 (traffic stats)
    - `dur`: 連線持續時間。
    - `sbytes`/`dbytes`: 來源/目的傳送的資料總位元組數 (0 代表建立連線但無傳送資料)。
    
    - `Spkts`/`Dpkts`: 來源/目的傳送的封包總數。
    - `Sload`/`Dload`: 來源/目的傳送的位元速率 (bits per second)。若數值極高通常代表 DDoS 或 大型檔案外洩。

3. TCP/IP 特徵 (protocol features)
    - `sttl`: 來源存活時間 (source time-to-live)。
    - `state`: 連線狀態 
    (0：無法辨識狀態或無狀態, 
    new：剛發起的新連線, 
    closed：連線已經正常關閉, 
    established：連線成功且正在通訊)。
    
    - `swin`: TCP 視窗大小，控制流量快慢。
    - `stcpb`/`dtcpb`: 來源/目的 TCP 序號，追蹤封包順序。
    - `smeansz`/`dmeansz`: 封包平均大小，總位元組除以封包數。異常小的封包通常是掃描器，異常大的可能夾帶攻擊載荷 (payload)。。
    - `synack`/`ackdat`: TCP 三向交握，建立連線時，伺服器回應。True 代表 TCP 三向交握成功完成，0 代表失敗。

4. 進階與分類 (advanced & label)
    - `trans_depth`: HTTP 請求/回應的深度 (同一個 TCP 連線中發出的 HTTP 請求數量)。GET 代表代表客戶端正在索取伺服器上的資料 (例如讀取網頁內容或下載檔案)，0 代表該連線不具備 HTTP 交易行為 (無深度可言)。
    > *在這份檔案中它以「動作標籤」的形式表示。*
    
    - `res_bdy_len`: 回應內容的路徑 (受攻擊路徑)。
    
    - `Stime`/`Ltime`: 連線開始與結束的時間戳記 (timestamp)。
    - `is_sm_ips_ports`: 同來源同目的的標記。1 代表 `srcip` 等於 `dstip` 且 `sport` 等於 `dsport`，反之為 0。
    - `ct_state_ttl`: 狀態與存活時間計數，紀錄了在特定的連線狀態 `state` 下，擁有相同存活時間 `sttl` 的連線出現了幾次 (closed 代表該組合的連線已正常結束)。
    - `Label`: 標籤。0 代表正常流量，1 代表惡意攻擊。

---

#### 練習

##### 找出發起攻擊次數最多的來源 IP (攻擊次數最多)

```sql=
SELECT srcip
FROM detection
WHERE label = 1  -- 標註為 1 的為惡意攻擊
GROUP BY srcip
ORDER BY COUNT(srcip) DESC
LIMIT 1;
```

![螢幕擷取畫面 2026-03-26 144224](https://hackmd.io/_uploads/SJJ2HIGjbg.png)

---

##### 找出攻擊過最多不同 Port (dsport) 的前 3 名來源 IP (攻擊範圍最廣)

```sql=
SELECT srcip
FROM detection
WHERE label = 1
GROUP BY srcip
ORDER BY COUNT(DISTINCT dsport) DESC
LIMIT 3;
```

![螢幕擷取畫面 2026-03-26 181050](https://hackmd.io/_uploads/BkouOcfiZe.png)

---

##### 流量特徵比對

攻擊流量通常跟正常流量長得不一樣。像是攻擊者發送的封包可能會特別大或特別小。

目標：分別計算攻擊 (Label = 1) 與正常 (Label = 0) 的平均傳送位元組數 (sbytes)。

```sql=
SELECT label, AVG(sbytes)
FROM detection
GROUP BY label;
```

![image](https://hackmd.io/_uploads/SJpn9qzjZl.png)

封包偏大可能代表攻擊者試圖夾帶惡意指令；封包偏小可能代表是單純的掃描或連線嘗試。

由結果可看到，攻擊的平均封包相比正常流量的平均封包來得小很多，這有幾種可能：
- 掃描 (scanning)：攻擊者只是在發送極小的封包 (如 TCP SYN) 來確認 port 有沒有開，並沒有傳輸實際內容。

- DoS：透過大量短促的請求癱瘓伺服器，而不是傳送大檔案。

- 漏洞嘗試：某些簡單的攻擊指令其實非常短。

---

##### 連線狀態異常偵測

在 TCP 連線中，如果長期停留在某個狀態 (例如只有請求沒有回應)，可能代表掃描或阻斷服務。

目標：找出攻擊之中，最常出現的前 3 種連線狀態 (state)。

```sql=
SELECT state
FROM detection
WHERE label = 1
GROUP BY state
ORDER BY COUNT(*) DESC
LIMIT 3;
```

![image](https://hackmd.io/_uploads/SkhpR5GiZl.png)

0：出現最多次，若連線還沒建立就被切斷，或者只有單向的 UDP 封包 --> 盲目掃描。

new：第二多，代表攻擊者發起了連線請求 (SYN)，但連線剛建立或還在握手階段。

closed：第三多，連線很快就關閉了。這通常代表攻擊者「點一下就跑」，例如掃描 port 是否有開，一確認有回應就立刻斷開，不進行後續溝通。

---

##### 綜合結論

這個 Kaggle 資料集有以下結論：
- 行為特徵：攻擊流量的平均封包大小相比正常流量還小很多，約 68.55 bytes。大多數攻擊並非為了建立穩定連線，而是單純探測掃描 (連線狀態 0 為最多)。

- 攻擊目標：透過廣度分析到 IP 為 `192.168.56.1` 的攻擊者雖然攻擊次數最多，但廣度排名第三，表示這個攻擊 IP 行為模式比較像是針對特定服務進行的精準偵查，目標較集中，並非全範圍的隨機掃描任何 port。

- 資安威脅：攻擊流量展現了短促且小型的特徵，符合 **TCP SYN Scanning** 或**殭屍網路探測偵查 (像是 Dos 等攻擊)** 的行為。

---

##### 圖表呈現

我想在我的電腦中的 VScode 做視覺化的動作，因此需要連線到 VM 中的資料庫。
原先我連線到 VM 的 IP，`10.0.2.15`，但發生連線逾時 (10060)，Windows 找不到目的地，後來打開 VM 透過 `hostname -I` 再查一次 VM 的 IP 才發現竟然與以前的不同，詢問 Gemini 後才得知 VM 預設透過 DHCP 動態分配 IP，因此每次重啟後主機節點的 IP 可能會發生變更。
![螢幕擷取畫面 2026-05-21 160822](https://hackmd.io/_uploads/SyPyEBnyze.png)

![image](https://hackmd.io/_uploads/S11dXHnkMx.png)

重新跑一次後出現了新的錯誤，連線遭拒絕 (10061)：
![image](https://hackmd.io/_uploads/SkfamS3yzx.png)

網路層已通，但傳輸層被目標主機主動拒絕，VM 上的 MariaDB 資料庫基於內部安全機制，主動發送了一個重置封包 (TCP RST) 將連線切斷，並回應了連線遭拒。

MariaDB 預設的設定檔限制 `bind-address = 127.0.0.1`，即只監聽來自 VM 本機內部的連線，拒絕接收來自網卡外部的流量。

資料庫中的 `root` 帳號限制只能從 `localhost` 登入，當偵測到來訪者是用 `root` 帳號但來自外部 IP 時，資料庫便判定為不合法連線。

解決問題：
```bash
sudo nano /etc/mysql/mariadb.conf.d/50-server.cnf
```
將其中的 `bind-address = 127.0.0.1` 改為 `bind-address = 0.0.0.0`
![image](https://hackmd.io/_uploads/BJ3iur31Ml.png)

接著重啟 MariaDB：
```bash
sudo systemctl restart mariadb
```

![image](https://hackmd.io/_uploads/rkOhKr3Jfg.png)
`root` 為 `localhost` 代表它不准任何外面的人用 root 登入。

因此我們創建一個專門給 Windows 連線用的新帳號，並給它讀取權限：
```bash
# 建立一個叫 allen 的新帳號，允許從任何地方 (%) 連線 (@ 後接允許從哪裡登入)，並設定密碼
CREATE USER 'allen'@'%' IDENTIFIED BY '密碼';

# 把資料庫 (log_analysis) 的讀取權限 (SELECT) 給這個新帳號 (只給 SELECT 權限意味著這個帳號只有讀取的權限，以此也實施最小權限原則)
GRANT SELECT ON log_analysis.* TO 'allen'@'%';
# .* 代表允許裡面所有資料表

# 強制資料庫立刻重新整理權限表並退出
FLUSH PRIVILEGES;
EXIT;
```
![image](https://hackmd.io/_uploads/Bkh4arhyMx.png)

在 VScode 中將程式碼的 user 跟 password 修改為剛設定的後，執行程式碼：
```python
import streamlit as st
import pandas as pd
import mysql.connector

conn = mysql.connector.connect(
    host="192.168.0.101", 
    user="剛設定的帳號名稱", 
    password="剛設定的密碼", 
    database="log_analysis"
)

st.title("攻擊流量日誌分析結果")
st.write("使用 Python 寫出的網頁，用來將結果視覺化，更好閱讀了解。")

query = '''SELECT state, COUNT(*) AS number
           FROM detection
           WHERE label = 1
           GROUP BY state
           ORDER BY number DESC
           LIMIT 3;'''

df = pd.read_sql(query, conn)

st.bar_chart(data=df, x='state', y='number')
```
![image](https://hackmd.io/_uploads/r1gze9a1Mg.png)

> 原先依然無法連線到資料庫，這才知道 VM 應該保持開啟的狀態，關機了就會連不到。

![image](https://hackmd.io/_uploads/Hkfj-5ayMg.png)

成功從資料庫用 SQL 語法篩選出要的資訊，並使用 pandas 套件轉成表格，再利用 streamlit 套件中的 bar_chart 將表格轉換為長條圖。

雖然在 SQL 語句中有使用 `ORDER BY DESC` 來排序，但如果 X 軸是類別型資料 (文字種類)，streamlit 的預設繪圖引擎會自動按照字母的順序去排序，因此需要在轉成表格後強制將 state 欄位轉為具備順序性的類別型態，並指定其類別順序對齊 DataFrame 表格的原始排序。
```python
df['state'] = pd.Categorical(df['state'], categories=df['state'], ordered=True)
```
![image](https://hackmd.io/_uploads/HyxttJoTyzx.png)
![image](https://hackmd.io/_uploads/rJxjaca1ze.png)

---

