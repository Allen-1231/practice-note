---
title: SOC Level 1 (On THM)

---

# SOC Level 1 (On THM)

:::success
[TOC]
:::

### 警報分類 (Alert triage)

- TP (True positive)：真警報，真威脅。
- FP (False positive)：偽警報，可能有合理的事務去誤觸警報。

![image](https://hackmd.io/_uploads/SkeE08QLZl.png)

### Alert Reporting

- alert reporting：在關閉警報或上報至二級分析師之前，需要先提交報告。可能需要詳細記錄調查過程，確保包含所有相關證據，而不是僅提供簡短的警報評論。
    - 原始 SIEM 日誌保存 3-12 個月，但警報會無限期保留，因此為了以防萬一，最好將所有上下文資訊保留在警告框內。
    - 一份撰寫良好的報告可以為二級分析師節省大量時間，有助於快速了解發生的情況。
    - 使用 5W (Who, What, When, Where, Why) 撰寫報告。
        - 範例：![image](https://hackmd.io/_uploads/ByK83JSUbg.png)
        - 練習：![image](https://hackmd.io/_uploads/BJ42CJH8bg.png)

- alert escalation：如果真陽性 (TP) 警報需要採取額外措施或進行更深入的調查，需按照既定流程上報給二級分析師進行進一步審核。
    - 何時該升級警報：
        - 發生重大網路攻擊，需要進行更深入的調查或數位鑑識與事件回應 (Digital Forensics and Incident Response, DFIR)。
        - 需要採取補救措施，例如清除惡意軟體、隔離主機或重設密碼。
        - 需要與客戶、合作夥伴、管理層或執法機構溝通。
        - 對警報內容理解得不夠透徹，需要資深分析師的協助。
    - SOC Dashboard 升級警報：
        - 將警報狀態變更為「進行中」並進行分析。
        - 寫報告、設定判定結果 (TP、FP)。
        - 如需升級處理，則將警報指派給當班的 SOC L2。

- communication：在分析過程中或分析結束後，您可能還需要與其他部門溝通。例如，詢問 IT 團隊是否確認已授予某些使用者管理權限，或聯絡人力資源部門以取得更多有關新入職員工的資訊。

### 攻擊鏈：

攻擊鏈用於解釋攻擊的各個階段。在網路安全領域，攻擊鏈則用來描述攻擊者（例如駭客或進階持續性威脅組織）接近和入侵目標所使用的方法與路徑。

威脅建模 (Threat Modelling) 是為了提升系統安全性的一系列步驟，核心在於識別風險。主要圍繞：

    1. 確定哪些系統和應用程式需要保護，以及它們在環境中發揮什麼作用。(在做什麼)
    2. 評估這些系統和應用程式可能存在的漏洞和弱點，以及它們可能被如何利用。(哪裡會出錯)
    3. 制定行動計劃，以保護這些系統和應用程式免受已發現的漏洞侵害。(要怎麼辦)
    4. 盡可能製定政策來防止這些漏洞再次發生。(做得夠好嗎，驗證風險是否降低)

STRIDE (Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service (DoS), and Elevation of Privilege) 、 DREAD (微軟用於評估電腦安全威脅風險的系統) 、 CVSS (Common Vulnerability Scoring System) 等都是專門用於威脅建模的框架。

#### 網路攻擊鏈 (Cyber Kill Chain)

1. Reconnaissance：偵查

        蒐集情報。
2. Weaponization：武器化
        
        編寫一段 PowerShell 腳本作為進行攻擊的武器。
3. Delivery：傳送

        將含有 PowerShell 指令的附件寄給目標。
4. Exploitation：開發，利用漏洞

        利用系統弱點進一步滲透。
5. Installation：安裝

        在系統內紮根，確保重啟也不會消失 (持久化)。
6. Command & Control：命令與控制

        命令與控制，建立穩定的回傳通道。
7. Actions on Objectives (Exfiltration)：滲透
        
        偷取資料。
![image](https://hackmd.io/_uploads/By0n7m_PZx.png)

#### 統一攻擊鏈 (Unified Kill Chain)

UKC (Unified Kill Chain) 框架有助於識別潛在的攻擊面以及這些系統可能被利用的方式，因此可以幫助建模 (modelling)。

UKC 指出，一個攻擊包含 18 個階段，涵蓋了整個攻擊過程(從偵察、利用、後利用，也包括辨識攻擊者的動機)，非常現代化且極為詳細，然而其他框架可能只有涵蓋少少幾個階段。

![image](https://hackmd.io/_uploads/Sy7mp1Rdbl.png)

UKC 主要分為三大部分：

#### UKC - Goal: In (侵入)

這一系列階段的主要目標是讓攻擊者獲得對系統或網路環境的存取權限。攻擊者會採用多種策略來調查系統，尋找可利用的潛在漏洞，在系統中站穩腳步。

這一系列階段也考慮了攻擊者創造某種形式的持久化 (persistence) 手段 (例如檔案或流程，使攻擊者能夠隨時連接到目標機器)。

![image](https://hackmd.io/_uploads/Sy_HEIWFZe.png)

- Reconnaissance

    描述了敵方為收集目標相關資訊而採用的技術。這可以透過被動偵察和主動偵察來實現。在此階段收集的資訊將在 UKC 的後續階段 (例如初始立足點) 得到廣泛應用。
    
    此階段收集的資訊可能包括：
    
    - 目標所運行的系統和服務。對於 weaponisation 和 exploitation 階段有幫助。
    - 可被冒充或用於社交工程或網路釣魚攻擊的聯絡人清單或員工清單。
    - 可能在後期階段 (例如轉型或初始存取) 使用的潛在憑證 (credentials)。

- Weaponization

    描述了攻擊者搭建執行攻擊所需的必要基礎設施。這可能包括建立命令與控制伺服器 (command and control server)，或建立能夠捕獲反向 shell 並將載荷 (payloads) 傳遞給目標系統的機制。
    
- Social Engineering

    描述了攻擊者可以用來操縱員工執行有助於其攻擊的操作的技術。
    
    社交工程可能包含：
    
    - 誘騙用戶開啟釣魚郵件中的惡意附件。
    - 冒充網頁並誘使用戶輸入其憑證。
    - 打電話給目標或拜訪目標，冒充使用者（例如，請求重設密碼），或能夠存取攻擊者以前無法存取的網站區域（例如，冒充公用事業工程師）。
    
- Exploitation

    描述了攻擊者如何利用系統中存在的弱點或漏洞。UKC 將「exploitation」定義為濫用漏洞來執行程式碼。
    
    例如：
    
    - 向 Web 應用程式上傳並執行反向 shell。
    - 干擾系統上的自動腳本以執行程式碼。
    - 利用 Web 應用程式漏洞在其運行的系統上執行程式碼。
    
- Persistence

    描述了攻擊者為維持對已取得初步控制權的系統存取權限而使用的技術。
    
    例如：
    
    - 在目標系統上建立一個服務，使攻擊者能夠重新獲得存取權限。
    - 將目標系統新增至命令與控制伺服器 (command and control server)，以便隨時遠端執行命令。
    - 留下其他形式的後門 (backdoors)，當系統上發生特定操作時執行（例如，當系統管理員登入時執行反向 shell）。
    
- Defence Evasion

    這是 UKC 中最有價值的階段之一。這個階段專門用於了解對手用來規避系統或網路中已部署的防禦措施的技術。
    
    像是可能包含：
    
    - Web application firewalls
    - Network firewalls
    - 目標機器上的防毒系統 (anti-virus system)
    - 入侵偵測系統 (IDS)
    
- Command & Control

    整合了敵方在 UKC 「武器化」階段所做的努力，旨在建立敵方與目標系統之間的通訊。敵方可以建立對目標系統的指揮與控制，以實現其目標。
    
    敵方可以：
    
    - 執行命令。
    - 竊取資料、憑證和其他資訊。
    - 利用受控伺服器向網路上的其他系統進行跳轉。
    
    分析這一階段有助於建立應對措施，也能為防守團隊提供信息，幫助他們在未來改進防守體系。

- Pivoting

    指攻擊者利用某種技術來存取網路中其他原本無法存取的系統（例如，未暴露於網路的系統）。網路中通常存在許多無法直接存取的系統，這些系統往往包含重要資料或安全性較弱。

    例如：攻擊者可以存取可公開存取的 Web 伺服器，從而攻擊同一網路內的其他系統（但這些系統無法透過網路存取）

#### UKC - Goal: Propagation (擴散)

這一系列的攻擊目標是在成功建立對目標網路的立足點之後進行的。攻擊者會在某個系統上建立一個據點作為其樞紐，並利用該據點收集有關內部網路的資訊。

![image](https://hackmd.io/_uploads/rydq3JVtbl.png)

- Pivoting

    一旦攻擊者獲得系統存取權限，他們就會將其用作攻擊跳板，並建立其命令操作與受害者網路之間的通道 (tunnel)。在後續階段，該系統也將被用作所有惡意軟體 (malware) 和後門 (backdoors) 的分發點。
    
- Discovery

    攻擊者會取得有關係統及其所連接網路的資訊。在此階段，知識庫將根據活躍使用者帳戶、已授予的權限、正在使用的應用程式和軟體、網頁瀏覽器活動、檔案、目錄和網路共享以及系統配置等資訊建構而成。
    
- Privilege Escalation

    在收集到相關資訊後，攻擊者會嘗試在樞紐系統中取得更高等級的權限。他們會利用已發現的帳戶漏洞和配置錯誤訊息，將存取權限提升到以下更高層級之一：
    
    - SYSTEM/ ROOT
    - Local Administrator (本地管理員)
    - 具有類似管理員權限的使用者帳戶
    - 具有特定存取權限或功能的使用者帳戶
    
- Execution

    攻擊者利用樞紐系統 (pivot system) 作為宿主，在此部署惡意程式碼。遠端木馬、 C2 (Command and Control) 腳本、惡意連結和定時任務均已部署和創建，以實現對系統的持續入侵並維持其持久性。

- Credential Access

    在 Privilege Escalation 階段，攻擊者會嘗試透過各種方法竊取帳戶名稱和密碼，包括鍵盤記錄和憑證轉儲。由於他們使用的是合法憑證，這使得攻擊更難被發現。

- Lateral Movement

    攻擊者利用取得的憑證和提升的權限，會試圖在網路中移動，並入侵其他目標系統，以實現其主要目標。

#### UKC - Goal: Exploitation (利用、滲透)

此一系列為攻擊者對目標環境發動攻擊的最終階段。現其攻擊目標。這些目標通常旨在破壞機密性、完整性和可用性 (CIA) 三要素。

![image](https://hackmd.io/_uploads/H1-1-eEKWl.png)

- Collection

    攻擊者會試圖收集所有有價值的目標資料。這會損害資料的機密性，並導致下一階段的攻擊。
    
    主要目標來源包括硬碟、瀏覽器、音訊、影片和電子郵件。
    
- Exflitration

    為了擴大攻擊範圍，攻擊者會試圖竊取數據，並使用加密和壓縮措施對數據進行打包，以避免被偵測到。
    
    在此過程中，先前部署的 C2 通道和隧道將發揮重要作用。
    
- Impact

    如果攻擊者試圖破壞資料資產的完整性和可用性，他們會操控、中斷或銷毀這些資產。目標是擾亂業務和營運流程，可能包括移除帳戶存取權限、抹除磁碟以、資料加密，例如勒索軟體攻擊、篡改網頁和拒絕服務 (DoS) 攻擊。
    
- Objectives

    攻擊者掌握了系統和網路的全部控制權和存取權限後，會試圖實現其攻擊的戰略目標。例如，如果攻擊的目的是為了獲取經濟利益，他們可能會使用勒索軟體 (ransomware) 加密檔案和系統，索取贖金以解鎖資料。在其他情況下，攻擊者可能試圖損害企業的聲譽，並將私人和機密資訊公諸於世。

### 滲透測試

滲透測試 (penetration test) 是經系統所有者授權，對電腦系統的安全性和防禦措施進行的審計。

滲透測試人員在執行滲透測試時，常常會面臨一些可能存在道德爭議的抉擇。例如，他們可能需要存取資料庫並接觸到潛在的敏感資料。或是他們可能正在對員工進行網路釣魚攻擊，以測試組織的人為安全防護能力。如果這些行動在初期階段已達成共識，那麼它們在法律上是合法的，然而其道德性值得商確。

#### 駭客分類

駭客分為三類，以道德準則和行為動機決定類別。

|         類別         | 說明                                                                             | 範例                                                       |
|:--------------------:|:-------------------------------------------------------------------------------- |:---------------------------------------------------------- |
| 白帽駭客 (White Hat) | 這些駭客被認為是好人，他們遵守法律，並利用自己的技能幫助他人。                   | 滲透測試人員對公司進行經授權的測試。                       |
| 灰帽駭客 (Grey Hat)  | 這些駭客一樣經常利用自己的技能幫助他人；然而他們並非始終遵守法律或道德標準。     | 有人擊潰了詐騙網站。                                       |
| 黑帽駭客 (Black Hat) | 這些駭客是犯罪者，他們經常試圖以犧牲他人利益為代價來破壞組織或獲得某種經濟利益。 | 勒索軟體作者會用惡意程式碼感染設備，並扣押資料以勒索贖金。 |

#### Rules of Engagement (ROE)

ROE 是滲透測試項目初期階段所建立的檔案，它定義了測試目標以及測試人員可以使用的行為與技術。該文件包含三個主要部分 (如下表所示)，最終決定了專案的執行方式。

|    部分    | 說明                                                                                                                              |
|:----------:|:--------------------------------------------------------------------------------------------------------------------------------- |
| Permission | 這一部分明確授權進行此項活動。此授權對於依法保護個人和組織所進行的活動至關重要。                                                    |
| Test Scope | 這一部分詳細說明此滲透測試的具體目標。例如，滲透測試可能僅適用於某些伺服器或應用程序，而不是整個網路。                              |
|   Rules    | 這一部分明確定義互動過程中允許使用的技術。例如，規則可能會明確規定禁止使用網路釣魚攻擊等技術，但 中間人攻擊 (MITM) 是可以接受的。 |

#### 滲透測試方法

滲透測試的目標和範圍非常廣泛，沒有兩個滲透測試是完全相同的，但所有這些方法都具有以下階段的通用主題：

|                階段                 | 說明                                                                                                                                                                                                                                        |
|:-----------------------------------:|:------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|              資訊蒐集               | 收集盡可能多的關於目標/組織的公開信息，例如開源情報 (OSINT, open source intelligence) 和研究。這不涉及掃描任何系統。                                                                                                                        |
|              列舉/掃描              | 此階段涉及發現系統上運行的應用程式和服務。例如，尋找可能存在潛在漏洞的 Web 伺服器。                                                                                                                                                         |
|                利用                 | 此階段涉及利用在系統或應用程式中發現的漏洞。此階段可能涉及使用公開的漏洞利用程式或利用應用程式邏輯。                                                                                                                                        |
|              權限提升               | 一旦成功滲透/利用了某個系統或應用程式 (稱為立足點)，接下來的階段就是嘗試擴大對系統的存取權限。可以進行橫向和縱向的權限提升，橫向提升是指訪問同一權限群組的另一個帳戶 (即另一個使用者)，而縱向提升是指存取另一個權限群組 (即管理員) 的帳戶。 |
| Post-exploitation (滲透後 / 利用後) | 此階段包含幾個子階段：1. 還可以攻擊哪些其他主機 (轉向攻擊)。2. 現在是特權用戶，還能從主機取得哪些額外資訊？3. 掩蓋蹤跡。4. 報告。                                                                                                           |

#### 滲透測試框架

- OSSTMM (Open Source Security Testing Methodology Manual)

    它提供了一套詳細的系統、軟體、應用程式、通訊、網路安全中的人為因素，的測試策略的框架。
    
    此方法論主要關注這些系統和應用程式如何通信，因此它包含的方法論：
    
        1. 電信 (電話、網路電話等)
        2. 有線網路
        3. 無線通訊

- OWASP (Open Web Application Security Project)

    此框架是一個由社群驅動且經常更新的框架，專門用於測試 Web 應用程式和服務的安全性。
    
    OWASP Top 10 是 OWASP 最具代表性的文件，大約每三到四年更新一次。它列出了當前 Web 應用程式面臨的 10 大最嚴重的安全風險。開發人員、測試人員和資安分析師通常將其作為安全評核的首要基準。
    常見的風險類別包括：壞掉的存取控制、密碼學上的失敗、注入式攻擊 (如 SQL Injection 或 Cross-site Scripting (XSS))。
    
- NIST Cybersecurity Framework 1.1 
    (NIST：National Institute of Standards and Technology)

    是一個廣受歡迎的框架，用於提升組織的網路安全標準並管理網路威脅風險，該框架普及且詳細。
    
    該框架為從關鍵基礎設施 (如發電廠等) 到商業機構的各種組織提供了安全控制指南和成功基準。

- NCSC CAF
    (NCSC：National Cyber Security Centre)

    Cyber Assessment Framework (CAF) 是一個包含十四項原則的廣泛框架，用於評估各種網路威脅的風險以及組織針對這些威脅的防禦措施。
    
    此框架適用於被認為提供「至關重要的服務和活動」的組織，例如關鍵基礎設施、銀行業等。該框架主要關注並評估以下主題：
    
        1. 資料安全
        2. 系統安全
        3. 身分和存取控制
        4. 韌性 (Resiliency)
        5. 監測
        6. 應對和恢復計劃

#### 滲透測試的主要模式

1. 黑箱測試 (Black-Box Testing)

    這個測試過程是一個高層次的過程，測試人員不會獲得任何有關應用程式或服務內部運作的資訊。
    
    這種測試無需任何程式設計知識或對程式的理解，可能包括與介面 (例如按鈕) 互動，並測試是否返回預期結果。
    
    黑箱測試增加了資訊收集和枚舉階段所花費的時間，從而難以了解目標的攻擊面。
    
    細節與目的：測試者只知道公司的名稱或網址，完全沒有內部的程式碼、架構圖或帳密。其目的為測試企業面對隨機外部攻擊的防禦力。雖然最真實，但非常耗時，且可能遺漏內部深處的漏洞。
    
2. 灰箱測試 (Grey-Box Testing)

    這種測試流程最常用於滲透測試等場景。它結合了黑盒測試和白盒測試的特性。
    
    測試人員對應用程式或軟體的內部元件有一定的了解，但仍然會像進行黑盒測試一樣與應用程式互動，然後利用自身對應用程式的了解來嘗試解決發現的問題。
    
    灰箱測試由於已知資訊有限，因此可以節省時間，並且通常用於防禦非常嚴密的攻擊面。
    
    細節與目的：測試者可能擁有一般使用者帳號、內網 IP 或部分的技術架構資訊。它平衡了效率與真實性，讓測試者能跳過基礎偵查，直接針對重要功能進行測試。

3. 白箱測試 (White-Box Testing)
    
    這種測試過程屬於底層測試，通常由了解程式設計和應用程式邏輯的軟體開發人員執行。測試人員將測試應用程式或軟體的內部元件。例如確保特定功能能夠正確運作並在合理的時間內完成。
    
    在白箱測試中，測試人員需要完全了解應用程式及其預期行為，因此比黑盒測試耗時更長。白盒測試場景下的全面了解確保了整個攻擊面都能得到驗證。
    
    細節與目的：測試者擁有完整的原始碼、網路架構圖、伺服器配置等所有資訊。它會進行最徹底的安全審查，雖然這與真實駭客情境不符，但能找出最隱蔽的邏輯漏洞。

---

|     特性     |         黑箱          |                 灰箱                 |             白箱             |
|:------------:|:---------------------:|:------------------------------------:|:----------------------------:|
|   模擬對象   |       外部駭客        | 具有一定權限的內部惡意員工或合作夥伴 | 具有最高權限的開發者或管理者 |
|    知識量    | No Knowledge (零已知) |     Partial Knowledge (部分已知)     |   Full Knowledge (全已知)    |
|   測試速度 (找到漏洞的速度)   |         最慢          |                 中等                 |             最快             |
| 發現漏洞深度 |   表面 (外部攻擊面)   |                 中等                 |        最深 (原始碼)         |
|    真實性    |         最高          |                 中等                 |             最低             |

---

### 網路流量分析

![image](https://hackmd.io/_uploads/HyDwmesPbg.png)

![image](https://hackmd.io/_uploads/Bk06vxjPZe.png)

### Log Analysis Tools:

#### Command Line

練習題目：
![image](https://hackmd.io/_uploads/ByOwO1IuZx.png)

檔案欄位快速一覽：
![image](https://hackmd.io/_uploads/SJTAOJIO-g.png)

1：
![image](https://hackmd.io/_uploads/S1d-KkI_bg.png)

2,3,4：
![image](https://hackmd.io/_uploads/S1C4FyIdWx.png)

#### Regular Expressions

下載 THM 上的 .zip 檔案後做解壓縮，找到其中的 `apache-ex2.log` 檔案。
![image](https://hackmd.io/_uploads/HyhNMRIObe.png)

稍微瀏覽檔案內容的結構
![image](https://hackmd.io/_uploads/HyXzmA8uZg.png)

每篇部落格文章都有一個唯一的 ID，該 ID 透過 `post` URL 參數從資料庫中動態取得。若我們要篩選出 ID 介於 10 到 19 之間的特定部落格文章：
![image](https://hackmd.io/_uploads/HJ5jPAU_-l.png)
`-E` 參數表示正在搜尋的是一個模式而不是一個字串，這使得我們可以使用正規表示式。

[Regex | HackMD](https://hackmd.io/@Allen1231/HypIDlCSZl)

#### CyberChef

長期以來被譽為「網路瑞士軍刀」。該應用程式 ([CyberChef](https://gchq.github.io/CyberChef/)) 擁有超過 300 種操作 (operations)，這些操作組合起來，使資料處理變得輕而易舉。

介面包含以下幾種區域：

1. Operations: 選擇要對輸入資料執行的操作。
2. Recipe: 顯示所有選擇的操作。
3. Input: 輸入想要分析的資料或來源。
4. Output: 輸入套用操作後的最終輸出。

![image](https://hackmd.io/_uploads/Bk7nSAYOWe.png)

---

dHJ5aGFja21l 是 tryhackme 的 Base64 編碼。
![image](https://hackmd.io/_uploads/HyyYwRY_-e.png)

---

如果不確定輸入的編碼是什麼，可以使用 `Magic` 操作來盡量準確地猜測出輸入是什麼，以及哪些操作可能在這裡有用。
![image](https://hackmd.io/_uploads/HycquRKuZe.png)

---

使用 Regex 對一個記錄了 SSH 身份驗證嘗試的日誌文件，輸出所有嘗試過身份驗證的 IP 位址。
![image](https://hackmd.io/_uploads/H1ma9CKO-g.png)

透過選擇輸出格式 (Output format) 中的清單匹配 (List matches)，可以去除日誌中的所有雜訊，僅輸出 IP 位址。

---

CyberChef 也可上傳檔案。
![image](https://hackmd.io/_uploads/rylB3AYdWe.png)

![image](https://hackmd.io/_uploads/HyTrhCtuZg.png)

---
練習：

1.
![image](https://hackmd.io/_uploads/rJMpRAKu-g.png)

![image](https://hackmd.io/_uploads/SyxC00KdZe.png)

2.
![image](https://hackmd.io/_uploads/r1Kkdgo_bl.png)

利用 regex 找出那串由 Base64 編碼的字串。`[A-Za-z0-9+/]{20,}=*` 為 Base64 的經典表達式。
![image](https://hackmd.io/_uploads/BJleBesdWg.png)

後面有兩個等於的是非常標準的 Base64 格式，嘗試看看解碼後。
![螢幕擷取畫面 2026-02-24 180317](https://hackmd.io/_uploads/Sk-6vgo_-x.png)

3.
![image](https://hackmd.io/_uploads/r1oqYzoO-x.png)

解碼。
![image](https://hackmd.io/_uploads/Hy3OufoOWl.png)

MAC 位址通常由 6 組兩位數的十六進制字元（0-9, A-F）組成，中間以冒號 (`:`) 或連字號 (`-`) 分隔。
![image](https://hackmd.io/_uploads/rkzYFMoubg.png)

#### Yara & Sigma

------------------------------------------------------------------------------ <font color=blue>Sigma</font> ------------------------------------------------------------------------------

Sigma 是一款高度靈活的開源工具，它以結構化的格式描述日誌事件，它可以透過模式比對在日誌檔案中尋找條目 (entries)。

它的用途包括**偵測日誌檔案中的事件**、**建立 SIEM 搜尋**、**識別 threats**。

---

YAML（Ain't Markup Language，標記語言）是一種資料序列化語言，它具有人類可讀性，可用於管理資料。Sigma 正是使用 YAML 語法來撰寫規則。

Sigma 偵測「SSH登入失敗事件」(非本人所編寫，為 THM 上的範例)：
![image](https://hackmd.io/_uploads/HypvFLndWl.png)

|      Key       | Value                                                                             | 說明                                                  |     |
|:--------------:|:--------------------------------------------------------------------------------- |:----------------------------------------------------- |:--- |
|     title      | Failed SSH Logins                                                                 | 概述 Sigma 規則的目的                                 |     |
|  description   | Searches sshd logs for failed SSH login attempts                                  | 提供 title 的補充說明                                 |     |
|     status     | experimental                                                                      | 規則的狀態。experimental 表示還需要進一步的測試與改進 |     |
|     author     | CMNatic                                                                           | 寫這規則的人                                          |     |
|   logsource    | product: linux service:sshd                                                       | 包含要查找的資料的日誌檔案在哪                        |     |
|   detection    | sshd (一種運行於伺服器後台，專門負責提供「安全遠端登錄」與「加密通訊」的守護程序) | 列出 Sigma 規則要尋找的內容                           |     |
|  a0\|contains  | a0\|contains: 'Failed'                                                            | 此例為尋找所有包含「Failed」的條目                    |     |
|  a1\|contains  | a1\|contains: 'illegal'                                                           | 此例為尋找所有包含「illegal」的條目                   |     |
| falsepositives | Users forgetting or mistyping their credentials                                   | 列出可能出現此條目但不一定表示惡意行為的情況          |     |

------------------------------------------------------------------------------ <font color=blue>Yara</font> ------------------------------------------------------------------------------

Yara 是另一款模式匹配工具，也是 YAML 格式，它基於二進位和文字模式（如十六進位和字串）識別資訊，通常用於惡意軟體分析。

---

這條 YARA 規則使用 Regex 來搜尋任何 IPv4 addresses，若正在分析的日誌檔案中含有 IP 位址，YARA 就會將其標記出來 (非本人所編寫，為 THM 上的範例)。
![image](https://hackmd.io/_uploads/BkV5yv3uZl.png)

|    Key    | Example                                        | 說明                                                              |
|:---------:|:---------------------------------------------- |:----------------------------------------------------------------- |
|   rule    | IPFinder                                       | 命名規則                                                          |
|   meta    | author                                         | 包含元資料，如此例為規則作者的名字                                |
|  strings  | $ip = /([0-9]{1,3}\.){3}[0-9]{1,3}/ wide ascii | 包含 YARA 應該尋找的值。在此例中，它使用 Regex 來尋找 IPv4 位址。 |
| condition | $ip                                            | 如果偵測到變數 $ip，則應觸發該規則                                |

將寫好的規則存成檔案 `ipfinder.yar` 並執行 yara 指令對目標檔案 `apache2.txt` 比對檔案是否符合特定的特徵 (Rule)。
掃描成功！YARA 在檔案中找到了符合 `IPFinder` 規則的內容。
![image](https://hackmd.io/_uploads/rk5vQPnO-l.png)

---

小整理：

|          |                      Sigma                      |                             Yara                              |
|:--------:|:-----------------------------------------------:|:-------------------------------------------------------------:|
|  副檔名  |                      .yml                       |                             .yar                              |
| 主要對象 | 日誌紀錄 (可檢查日誌紀錄裡有沒有人正在進行攻擊) | 靜態檔案與記憶體 (可檢查這個檔案是不是病毒、有沒有隱藏 IP 等) |
| 運作方式 |    轉譯成搜尋指令 (如 Splunk 或 ELK 的語法）    |                   掃描檔案內容是否符合特徵                    |

### Phishing Analysis

當向目標對象發送電子郵件，聲稱來自可信任的來源，以誘騙收件者提供敏感資訊時，這種攻擊稱作釣魚攻擊, Phishing attack。

#### Email Delivery

為了方便收發電子郵件，有 3 個特定的協定 (Protocol)：

- SMTP (Simple Mail Transfer Protocol)：用於處理電子郵件的傳送。
    
    一種用於將電子郵件傳送到 SMTP 伺服器的協定，更具體地說，是傳送到郵件提交代理 (MSA) 或郵件傳輸代理程式 (MTA)。
    
- POP3 (Post Office Protocol)：負責在用戶端和郵件伺服器之間傳輸電子郵件。

    一種用於接收電子郵件的替代協議，它會將電子郵件從伺服器下載到本機。使用 POP3 時，收件者無法從其他裝置再次存取其電子郵件，因為郵件儲存在本機後會從郵件伺服器中刪除。

- IMAP (Internet Message Access Protocol)- IMAP (Internet Message Access Protocol)：負責在用戶端和郵件伺服器之間傳輸電子郵件。

    一種用於接收電子郵件的協定。協定規範了技術流程，使電腦和伺服器能夠相互連接，而無需考慮它們是否使用相同的硬體或軟體。
    
POP3 和 IMAP 的定義相同，但兩者之間有差異：

| POP3                                                                                                                                       | IMAP                                       |
|:------------------------------------------------------------------------------------------------------------------------------------------ |:------------------------------------------ |
| 電子郵件會被下載並儲存在單一裝置上。                                                                                                       | 郵件儲存在伺服器上，可以下載到多個裝置上。 |
| 已發送的訊息儲存在發送郵件的相同裝置上。                                                                                                   | 已發送的訊息儲存在伺服器上。               |
| 郵件只能從下載該郵件的單一裝置存取。                                                                                                       | 訊息可以同步，並在多個裝置上存取。         |
| 若想將訊息保留在伺服器上，要確保啟用「將電子郵件保留在伺服器上」設置，否則所有訊息一旦下載到單一裝置的應用程式或軟體，就會從伺服器上刪除。 | 留白。                                     |

電子郵件如何從寄件者發送到收件者：
![image](https://hackmd.io/_uploads/H1lCs--5Wx.png)

1. Alexa 用她的郵件用戶端寫了一封電子郵件給 Billy (billy@johndoe.com)，寫完後，她點擊了發送按鈕。
2. SMTP 伺服器需要確定 Alexa 的電子郵件傳送到哪裡。它會查詢 DNS 以獲取與 johndoe.com 相關的資訊。
3. DNS 伺服器取得 johndoe.com 資訊，並將該資訊傳送到 SMTP 伺服器。
4. SMTP 伺服器透過網路將 Alexa 的電子郵件傳送到 Billy 在 johndoe.com 的郵箱。
5. 在這個階段，Alexa 的電子郵件會經過各種 SMTP 伺服器，最後轉送到目標 SMTP 伺服器。
6. Alexa 的電子郵件終於到達了目標 SMTP 伺服器。
7. Alexa 的電子郵件已送達，現在正儲存在本機 POP3 / IMAP 伺服器上，等待 Billy 的接收。
8. Billy 登入了他的電子郵件用戶端，該用戶端會向本機 POP3 / IMAP 伺服器查詢郵箱中的新郵件。
9. Alexa 的電子郵件會複製 (IMAP) 或下載 (POP3) 到 Billy 的電子郵件用戶端。

每種協定都有其對應的預設連接埠和推薦連接埠。像是 SMTP 協定的預設為 port 25。

但 SMTP 的 port 25 已過時，不建議使用。如果使用此端口，必須啟用使用者名稱/密碼驗證。否則應使用啟用 STARTTLS 的 port 587，才是安全的連線。

![image](https://hackmd.io/_uploads/SJ9GAW-5Ze.png)

> IMAP 安全傳輸連接埠：993
> POP3 安全傳輸連接埠：995

#### Email Headers and Body

電子郵件到達收件匣時由兩個部分組成：

- 電子郵件標頭 (Header)：有關電子郵件的訊息，例如轉發電子郵件的電子郵件伺服器。
- 郵件正文 (Body)：純文字和/或 HTML 格式文字。

電子郵件訊息的語法稱為網路訊息格式 IMF (Internet Message Format)。

##### Header

標頭欄位：四個最常顯示的資訊
- From 寄件者 - 發送 email 的地址。
- Subject 主題 - 郵件的標題。
- Date 日期 - 郵件發送的時間。
- To 收件者 - 接收 email 的地址。

隱藏的標頭：若要獲取相同及更多電子郵件標頭資訊，需查看原始電子郵件細節資訊。
![image](https://hackmd.io/_uploads/rJOH-rzqZe.png)

細節中有幾個值得關注的欄位：
- X-Originating-IP - 電子郵件的發送 IP 位址 (這稱為 X 標頭, X-header)。
- Smtp.mailfrom / header.from - 郵件發送的網域名稱。
- Reply-To - 回覆地址，這是回覆郵件將發送到的電子郵件地址，而不是寄件者的電子郵件地址。

##### Body

包含純文字，和 / 或 HTML 格式文字，HTML 格式使得可以加上圖片或新增附件等。

- 常見社交工程手法

    - 語氣與急迫性

        緊急性 (Urgency)：觀察是否包含「帳戶將在 24 小時內停用」、「立即點擊以避免罰款」等字眼。
    
        權威性 (Authority)：是否冒充高層主管 (CEO Fraud) 或法律部門、IT 部門。

        誘惑性 (Enticement)：提供獎金、中獎訊息或包裹領取通知。

        異常語法 (Grammar/Spelling)：非母語人士的錯誤語法或拼寫 (這通常是自動化釣魚腳本的特徵)。
    
    - 連結與按鈕

        滑鼠懸停 (Hovering)：滑鼠游標移到連結上，看左下角顯示的 URL 是否與顯示文字一致，以此檢查連結。
        
        網址 URL：觀察是否使用 `bit.ly` 或 `tinyurl` 等縮寫，這常用來隱藏真實目的地。
        
        視覺混淆 (Typosquatting)：例如 `micros0ft.com` (用 0 代替 o) 或 `g0ogle.com`。
        
    - HTML 原始碼分析

        隱藏元素：搜尋是否有字級為 0 或與背景色相同的隱藏文字（用來繞過垃圾郵件過濾器）。
        
        外部加載：觀察是否有 `<img src="...">` 標籤連往外部伺服器，這可能被用來進行 Beaconing (確認你已開啟郵件)。
        
#### Types of Phishing

惡意電子郵件可以歸類為以下幾類：
- Spam (垃圾郵件)：未經請求而大量發送給大量收件者的垃圾郵件。垃圾郵件中更具惡意性的變種被稱為惡意垃圾郵件 (MalSpam)。

- Phishing (網路釣魚)：向目標發送偽裝成可信任實體的電子郵件，誘騙個人提供敏感資訊。
- Spear phishing (魚叉式網路釣魚)：透過**針對**特定個人或組織，獲取敏感訊息，從而將網路釣魚提升到一個新的層次。
- Whaling (鯨魚釣魚)：鯨釣攻擊類似於魚叉式網路釣魚，但它專門針對 C 級**高層人員**(CEO、CFO 等)，其目的相同。
- Smishing (簡訊釣魚)：透過向行動用戶發送精心製作的短信，將網路釣魚活動轉移到行動裝置上。
- Vishing (語音釣魚)：與簡訊釣魚類似，但不同之處在於它基於語音通話進行社會工程攻擊。

網路釣魚郵件的典型共同特徵：
- 寄件者電子郵件地址偽裝成可信任實體 (電子郵件欺騙 email spoofing)。

- 電子郵件的主題或正文寫得帶有**緊迫感**，或使用了某些關鍵字，例如發票、暫停等。
- 郵件正文 (HTML) 被設計成與可信實體 (例如 Amazon) 很相似。
- 郵件正文 (HTML) 編寫品質差。
- 電子郵件正文使用通用內容，例如尊敬的先生/女士。
- 超連結 Hyperlinks，通常使用 URL 縮寫服務來隱藏其真實來源。
- 偽裝成合法文件的惡意附件。

超連結和 IP 位址應該進行「去威脅化 defanged」，使 URL/網域或電子郵件地址無法點擊，如此以避免意外點擊，進而防止嚴重的安全漏洞。
它會將電子郵件中的特殊字元 (例如 “@” 或 URL 中的 “.”) 替換為其他字元。

[CyberChef](https://gchq.github.io/CyberChef/#recipe=Defang_URL(true,true,true,'Valid%20domains%20and%20full%20URLs')&input=aHR0cDovL3d3dy5zdXNwaWNpb3VzLnRobQ&ieol=CRLF&oeol=CRLF) 是一個很好的去威脅化工具。

![image](https://hackmd.io/_uploads/S126ElBqbe.png)

:::info
BEC, Business Email Compromise, 指攻擊者控制了內部員工的帳戶，然後利用被盜用的電子郵件帳戶說服其他內部員工執行未經授權或欺詐性操作。
:::

---

### Phishing Emails in Action 釣魚郵件範例

#### Cancel your Paypal order

此範例著重在以下技術：
- Spoofed email address (偽造的電子郵件地址)
- URL shortening services (URL 縮短服務)
- HTML to impersonate a legitimate brand (假冒合法品牌的 HTML 程式碼)

---

電子郵件的標頭 (Header)：
:::spoiler
![image](https://hackmd.io/_uploads/B1aPDBUqbg.png)
:::

由圖可簡單觀察到：
1. 這是一個異常的收件者地址，它並非與該 Yahoo 帳戶關聯的電子郵件地址。若收件者欄位是一個完全不認識的子網域或隨機字串，這通常代表攻擊者正在進行盲目投遞或透過郵件轉發系統發送大量垃圾郵件。

2. 不匹配：寄件者的顯示名稱 (偽裝成 `service@paypal.com`) 與其實際的發信來源地址 (`@sultanbogor.com`) 不符。

3. 社交工程的心理戰。郵件主題暗示您進行了某種購買或交易。如果不記得這個帳戶，那麼它就會引起收件者的注意，收件者可能會心慌，疑惑自己是否有被盜刷或是不小心消費等，誘使收件者為了取消交易或好奇查看明細而點擊惡意連結。這種策略是為了促使收件者盡快與電子郵件互動。

---

電子郵件的正文 (Body)：
:::spoiler
![image](https://hackmd.io/_uploads/Hy9OoSIqWg.png)
![image](https://hackmd.io/_uploads/Hy_YjH89Wg.png)
:::

- 郵件正文與寄件者資訊和主題相符。郵件設計得像一封來自 PayPal 的合法郵件。

- 郵件沒有任何附件。郵件中唯一的互動元素是「**取消訂單**」的按鈕/連結。

- 透過查看電子郵件的原始 HTML 原始碼來進一步調查：
    - Email Hyperlinks (電子郵件超連結)：
        ![image](https://hackmd.io/_uploads/Bkt-3ewqZe.png)
        
        該連結使用了網址縮短服務 (URL shortening services)。如果不知道連結會帶到哪裡，最好不要點擊。

---

#### Track your Package

此範例著重在以下技術：
- Spoofed email address (偽造的電子郵件地址)
- Pixel tracking (像素追蹤)
- Link manipulation (連結操控)

---

:::spoiler
![image](https://hackmd.io/_uploads/S1lV2Qt5We.png)
:::

由圖可簡單觀察到：
1. 這封電子郵件經過精心設計，看起來像是從某種郵件分發中心發出的，試圖營造一種官方自動發信系統的專業感。
2. 郵件主旨行中的「追蹤號碼」更增添了這種真實感。一個隨機但看起來專業的編號會讓人覺得這是一筆真實存在的物件，從而降低戒心，它利用了人們對細節的信任。
3. 郵件正文中的連結與主題一致。這是在社交工程中為了建立連貫性。
--> 在電腦上將滑鼠懸停 (Hover) 在連結上面而不點擊，通常就能看到左下角顯示的真實惡意網址。

---

通常情況下，可以像上述所說將滑鼠懸停在連結上查看連結指向的位置，但在這個例子中，這種方法行不通，因為 Yahoo 禁用了郵件中的連結。我們可以查看郵件的原始碼來找出原因：
- Email Hyperlinks (電子郵件超連結)：
![image](https://hackmd.io/_uploads/Byixy4FcWg.png)
    - 在 HTML 中，連結是由 `<a>` 標籤構成的，在 `<a>` 與 `</a>` 之間，寫著 `Track your package: # LZ8942357486EN`。
    
    - `href` 屬性的地方，網址是 `http://devret.xyz/...`，這是實際的連結指向。`devret.xyz` 是一個可疑的網域。
    
    - 有一個名為 Tracking.png 的圖片檔，這是垃圾郵件發送者在垃圾郵件中嵌入的追蹤像素 (一種非常小的圖像)，這些追蹤器會將訊息傳回垃圾郵件發送者的伺服器，他們就用這個做監控。

---

:::success
![image](https://hackmd.io/_uploads/rJru74KcWg.png)

![image](https://hackmd.io/_uploads/B1QKQVt5Wg.png)
:::

---

#### Select your email provider to view document

此範例著重在以下技術：
- Urgency (緊急性)
- HTML to impersonate a legitimate brand (假冒合法品牌的 HTML 程式碼)
- Link manipulation (連結操控)
- Credential harvesting (憑證收集)
- Poor grammar and/or typos (語法錯誤和/或拼字錯誤)

---

:::spoiler
![image](https://hackmd.io/_uploads/SyEL4NYqbe.png)
:::

由上圖可簡單觀察到：
1. 郵件於 2021 年 7 月 15 日星期四發出。
2. 郵件表示下載連結當天就會失效，營造了一種緊迫感。
3. 這裡有一個操作需要執行。在本例中，是一個用於下載傳真 (fax) 的按鈕。

:::spoiler
![image](https://hackmd.io/_uploads/S1d4fT5qZg.png)
:::

由上圖可簡單觀察到：
1. 受害者被重新導向到一個偽裝成 OneDrive 的頁面。該 URL 與 Microsoft 沒有任何關聯。
2. 受害者還需要點擊另外兩個連結。受害者必須點擊其中一個按鈕才能取得即將過期的傳真檔案。

:::spoiler
![image](https://hackmd.io/_uploads/BJHMQa95be.png)
:::

由上圖可簡單觀察到：
1. 受害者被引導至另一個網站。這個網站的設計模仿了 Adobe 的網站，但它看起來與 Adobe 沒有任何關係。
2. 標籤頁的標題叫做 “Share Point Online”，這是微軟的產品。此外，還有幾處語法錯誤。
3. 受害者可以選擇自己使用的電子郵件服務登入。

:::spoiler
![image](https://hackmd.io/_uploads/Hy7xEp95Zg.png)
:::

由上圖可觀察到：受害者選擇使用 Outlook 登錄，因為根據說明中：「要閱讀文檔，請使用發送到此文件的有效電子郵件憑證登入。」；而該電子郵件是在 Outlook 中查看的。

:::spoiler
![image](https://hackmd.io/_uploads/rySwNpqqbg.png)
:::

可以看出受害者輸入了虛假憑證，但即使受害者輸入的是有效憑證，錯誤訊息也是一樣的。這種情況發生的原因是受害者實際上並沒有向電子郵件服務提供者進行身分驗證。相反，受害者輸入的憑證現在儲存在犯罪分子的伺服器上。犯罪者竊取憑證的目的已經達到。

---

#### Please update your payment details

此範例著重在以下技術：
- Urgency (緊急性)
- Spoofed email address (偽造的電子郵件地址)
- HTML to impersonate a legitimate brand (假冒合法品牌的 HTML 程式碼)
- Poor grammar and/or typos (語法錯誤和/或拼字錯誤)
- Attachments (附件)

---

:::spoiler
![image](https://hackmd.io/_uploads/B1TZU6q9bg.png)
:::

由圖可簡單觀察到：
1. 郵件看起來像是來自 Netflix Billing，但寄件者地址是 z99@musacombi.online。
2. 帳戶已被封禁，因此受害者必須迅速採取行動。利用事情的緊急性。
3. 正文中也展現出迫切感。

    另外，Netflix 有著拼字錯誤，郵件中是「Netllx」。
    
:::spoiler
![image](https://hackmd.io/_uploads/H1G2tnj9Wx.png)
:::

1. 受害者需要打開附件 (PDF) 來更新他們的 Netflix 帳戶。
2. 這是一個不尋常的電話號碼，不應該發送給身在美國的受害者。
3. 附件：
    :::spoiler
    ![image](https://hackmd.io/_uploads/Sy645hi9bx.png)
    :::
    
    - 附件中包含一個名為「更新付款帳戶」的嵌入式連結。

---

#### Your recent purchase

此範例著重在以下技術：
- Urgency (緊急性)
- Spoofed email address (偽造的電子郵件地址)
- Recipient is BCCed (收件人欄位異常，代表收到了這封信，但收件者的電子郵件地址並沒有出現在郵件標頭的 To:(收件人) 或 Cc:(副本) 欄位中)
- Poor grammar and/or typos (語法錯誤和/或拼字錯誤)
- Attachments (附件)

---

:::spoiler
![image](https://hackmd.io/_uploads/ByZle6sqWe.png)
:::

由圖可簡單觀察到：
1. 郵件看起來像是來自 Apple Support，但寄件者地址是 gibberish@sumpremed.com。
2. 這封郵件並非直接發送到受害者的收件匣，而是透過密送 (BCC) 發送的，旨在冒充合法的蘋果電子郵件地址。
3. Action Required 展現出緊迫性。
- 此外，寄件者和收件人的電子郵件地址都存在一些明顯的拼字錯誤：donoreply 和 payament 這兩個字。
- 並且這封郵件沒有正文，完全是空白的，只有一個附件。
    ![image](https://hackmd.io/_uploads/SkQb7pscbl.png)

:::spoiler
![image](https://hackmd.io/_uploads/B1BtQpi5Zx.png)
:::

1. 此為附件的內容，可以看到，該檔案包含一張類似 App Store 收據的大圖片。
2. 該連結包含與 Apple 相關的關鍵字：apps 和 ios。

---

#### DHL Express Courier Shipping notice

此範例著重在以下技術：
- Spoofed email address (偽造的電子郵件地址)
- HTML to impersonate a legitimate brand (假冒合法品牌的 HTML 程式碼)
- Attachments (附件)

---

:::spoiler
![image](https://hackmd.io/_uploads/SJ5l-Z6cbx.png)
:::

由圖可簡單觀察到：
1. 寄件者的電子郵件地址與被冒充的公司 (在本例中為 DHL) 不符。
2. 郵件主題給人一種錯覺，好像 DHL 會寄給你一個包裹。
3. 郵件正文中的 HTML 程式碼設計成看起來像是 DHL 傳送的。

---

:::spoiler
![image](https://hackmd.io/_uploads/SJo6WbTc-e.png)
:::

查看電子郵件的源代碼，發現用於將電子郵件作為網頁查看的連結不包含實際的目標 URL。

---

:::spoiler
![image](https://hackmd.io/_uploads/r1a8M-ac-x.png)
:::

受害者在這封電子郵件中唯一可以與之互動的部分是附件，在本例中是一個 Excel 文件。

---

:::spoiler
![image](https://hackmd.io/_uploads/ryxiGWa5be.png)
:::

附件中包含的內容。

---

:::spoiler
![image](https://hackmd.io/_uploads/H1pRzZ69be.png)
:::

該附件運行的負載 (payload) 會拋出錯誤。

---

### Web Security Essentials

網路應用程式 (Web application) 是攻擊者最常利用的入口點之一，因為它們始終可用且暴露在外。它們通常連接到後端系統，例如資料庫和其他基礎設施，這為攻擊者提供了極具破壞性的攻擊機會。

| Web App Owner                                    | Web App User                                       |
|:------------------------------------------------ |:-------------------------------------------------- |
| 網路應用程式始終在線，必須全天候24小時保持安全。 | 資料儲存在網路應用程式中，可能存在安全隱患。       |
| 世界各地的人都可以隨時存取應用程式。             | 一旦瀏覽器遭到入侵，使用者的所有帳戶都將面臨風險。 |
| 要隨時了解很多新出現的威脅是一項挑戰。           | 資料外洩可能導致身分盜竊或經濟損失。               |
| 有責任保護使用者資料的安全。                     | 隱私可能會永久受到侵犯。                           |

---

#### Web Infrastructure

request-response 的這種請求與回應的循環是網路運作的基礎。

任何網路服務 (如 tryhackme.com) 都需要三個主要元素才能運作：
- Application：應用程式，決定網站外觀和功能的程式碼、圖像、樣式和圖示。

- Web Server：託管 (host) 應用程式。它監聽請求並向使用者回傳回應。

- Host Machine：Linux 或 Windows 這種執行 Web 伺服器和應用程式的底層作業系統。

![image](https://hackmd.io/_uploads/rJ_D0v7iZg.png)

造訪網站時，瀏覽器會向伺服器發送請求，網路伺服器位於網站和應用程式的前端，它監聽傳入的請求並傳回對應的回應，也由於它們公開暴露並處理所有傳入的網路請求，因此網路伺服器也成為攻擊者的常見目標。

常見的網路伺服器：
- Apache：最受歡迎，用於託管簡單的網站和部落格。最常見的是 WordPress。
- Nginx：高效能網路 app 的業界標準。Netflix、Airbnb、GitHub 等公司都在使用。
- Internet Information Services (IIS)：微軟開發的一款網頁伺服器，常用於企業環境。

![image](https://hackmd.io/_uploads/Syj9ueSsbl.png)

---

#### Protecting the Web

- Protecting the Application

    - Secure Coding：避免使用不安全的函數，確保適當的處理錯誤，刪除敏感資訊。
    - Input Validation：驗證使用者輸入，以防止注入攻擊。
    - Access Control：根據使用者角色限制存取權限。

- Protecting the Web Server

    - Logging：用存取日誌詳細記錄所有網路請求。
    - Web Application Firewall (WAF)：根據定義的規則過濾和阻止有害流量。
    - Content Delivery Network (CDN)：減少對伺服器的直接暴露，並使用整合的 WAF。

- Protecting the Host Machine

    - Least Privilege：使用低權限使用者來提供服務。
    - System Hardening：停用不必要的服務與關閉未使用的 ports。
    - Antivirus：新增端點級保護，阻止已知惡意軟體。

> Patch Management：確保應用程式依賴項、Web 伺服器和主機都是最新的版本。

Logging：
Web 伺服器可以為收到的每個請求建立日誌 ---- 存取日誌 (access logs)。
它們會追蹤與伺服器的每次互動訊息，包括客戶端的 IP 地址、timestamp、請求的頁面或資料、伺服器的回應狀態以及使用者代理程式 (user agent)。

![image](https://hackmd.io/_uploads/B1V0K8vs-x.png)
```
GET 請求用於從伺服器檢索資源，例如特定的網頁。
POST 請求用於向伺服器提交數據，例如登入憑證。
1. 客戶端 IP 為 10.10.10.100 的使用者造訪網站的主頁 /index.html。
2. 使用者導向到 /login.html 的登入頁面。
3. 輸入憑證並提交表單，這由 POST 請求表示。
4. 最後，使用者透過 /myaccount.html 存取他們自己的帳戶頁面。
```

---

#### Defense Systems

- Content Delivery Network (CDN)
    
    CDN 將快取 (cache) 內容儲存在距離用戶更近的伺服器上，並透過這些伺服器提供快取內容，從而降低延遲。類似：有一個位於中心位置的主伺服器。該主伺服器向全球各地的邊緣伺服器提供訊息，以便客戶能夠更快、更安全地存取資料。它充當用戶和來源伺服器之間的緩衝區。
    
    安全性優點：
    - IP 地址遮罩：隱藏來源伺服器 IP 位址，讓攻擊者更難鎖定目標。
    - DDoS 保護：CDN 可以吸收大量流量，從而降低拒絕服務 (denial-of-service) 攻擊的效果。
    - 強制使用 HTTPS：大多數 CDN 預設強制使用透過 TLS 的加密通訊。
    - 整合式 WAF：許多 CDN 如 [Cloudflare CDN](https://www.cloudflare.com/zh-tw/)、[Amazon CloudFront](https://aws.amazon.com/tw/cloudfront/)、[Azure Front Door](https://learn.microsoft.com/en-us/azure/frontdoor/front-door-overview)，都有整合 web application firewalls。
    
    CDN 使 Web 應用程式能夠更有效率、更安全地向客戶提供資料。
    
    ![image](https://hackmd.io/_uploads/rykw6zdoZx.png)
    
- Web Application Firewall (WAF)

    WAF 可作為網站和 Web 應用程式的另一層保護，它們會檢查傳入的 HTTP 流量，並根據安全規則封鎖或記錄 (log) 潛在的有害請求。就像是保全一樣。
    
    WAF 類型：
    - Cloud-based (Reverse Proxy)：位於 Web 伺服器前端，易於部署且具有出色的可擴增性。
    - Host-based：軟體直接部署在 Web 伺服器上，並為每個應用程式提供控制。
    - Network-based：位於網路邊界 (perimeter) 的實體或虛擬設備。更適合企業環境。

    常用的方法與可能被封鎖的請求範例：
    | WAF 功能         | 檢測方法                         | Exp                                               |
    |:---------------- |:-------------------------------- |:------------------------------------------------- |
    | 基於特徵的檢測   | 比對已知的攻擊模式與 Payloads    | 請求的用戶代理與已知工具 `sqlmap/1.8.1` 匹配      |
    | 基於啟發式的檢測 | 分析請求的上下文和行為           | 包含特殊字元的長查詢字串 `search?q=<script%20(1)` |
    | 異常與行為分析   | 標記偏離正常流量行為的情況       | 單一 IP 地址在短時間內重複嘗試登入                |
    | 位置和 IP 過濾   | 利用位置和威脅情報來阻止 IP 地址 | 來自正常業務區域以外的 IP 地址的請求              |

    `tryhackme.thm` 的 Cloudflare dashboard，圖中顯示了過去 24 小時內的所有請求，包括被整合 Web 應用防火牆攔截的請求：
    ![image](https://hackmd.io/_uploads/Sy57x3dibl.png)
    
- Antivirus (AV)

    防毒軟體的主要目的是保護終端設備 (如桌上型電腦、筆記型電腦和伺服器) 免受已知惡意檔案和程式的侵害。大多數防毒軟體都依賴基於特徵的檢測，也就是說它們會將檔案與已知惡意軟體或模式的資料庫進行比對。
    
    防毒軟體是 defense-in-depth strategy 的其中一層，與其他安全措施結合使用，以提供更強大的保護。

---

### Web Application Basics

#### Web Application Components

Web server 負責託管和提供 Web 應用程式的內容。

- Front End 前端

    Web 應用程式允許使用者與之交互的部分，使用 HTML、CSS 和 JavaScript 等技術來實現這項功能。
    
    - HTML
    
        Hypertext Markup Language，是 Web 應用程式的基礎，它是一組用於指示網頁瀏覽器顯示什麼內容以及如何顯示的指令或程式碼。
    
    - CSS
    
        Cascading Style Sheets，描述了標準的外觀，例如特定的顏色、文字類型和版面。
    
    - JavaScript
        
        JS，支援 Web 瀏覽器中更複雜的操作。HTML 可以被視為一組簡單的指令，用於控制頁面顯示內容；而 JavaScript 則是一組更高級的指令，它允許對顯示內容進行選擇和決策。
        
- Back End 後端

    指在網頁瀏覽器中看不到但對網頁應用程式正常運作至關重要的部分。
    
    - Database
        
        資料庫是儲存、修改和檢索資訊的地方。
    
    - Infrastructure
    
        Web 應用程式的底層有許多基礎設施元件，如 Web 伺服器、應用伺服器、儲存裝置、各種網路設備以及其他支援網路應用程式的軟體。
    
    - WAF
    
        Web Application Firewall，是 Web 應用程式的一個可選元件。它可以幫助過濾掉來自 Web 伺服器的危險請求，從而提供一定的保護。(類似行星的大氣層保護紫外線的侵害。)
        
前端元件主要負責瀏覽器內的使用者體驗；後端元件是 Web 應用程式運作的底層引擎。

---

#### Uniform Resource Locator (URL)

URL 的組成：
![image](https://hackmd.io/_uploads/BychrjgTWx.png)

- Scheme
用於訪問網站的協議。最常見的有 HTTP、HTTPS。

- User
有些網址可能包含使用者的登入資訊 (通常是使用者名稱 username)，用於需要身份驗證的網站。將登入資訊放在網址中並不安全，它可能會洩露敏感訊息，從而造成安全風險。
- Host/Domain
URL 中最重要的部分，因為它代表正在造訪哪個網站。每個網域都必須是唯一的，並且透過網域註冊商進行註冊。
- Port
埠號有助於引導瀏覽器存取 Web 伺服器上的正確服務。範圍從 1 到 65,535，但最常用的是 HTTP 的 80 連接埠和 HTTPS 的 443 連接埠 。
- Path
指向伺服器上嘗試造訪的特定檔案或頁面。
- Query String
以問號 (?) 開頭，通常用於搜尋字詞或表單輸入等內容。
- Fragment
以井號 (#) 開頭，用於指向網頁的特定部分，如直接跳到特定標題或表格。

由於使用者可以修改 Query String 與 Fragment 部分，因此必須檢查並清理此處的數據，以避免注入攻擊等問題。

---

#### HTTP Messages

用戶 (客戶端) 和 Web 伺服器 (伺服器端) 之間交換的封包 (packets)。這些訊息展示了使用者請求和伺服器回應之間的通訊方式。

HTTP 訊息的兩種類型：
- HTTP Requests：由使用者傳送，以觸發 Web 應用程式上的操作。

- HTTP Responses：由伺服器傳送，回應用戶請求。

每個訊息都遵循特定的格式，其中方法、URL、標頭和狀態碼等為 HTTP 訊息的關鍵部分。

![image](https://hackmd.io/_uploads/HyKTqTZ6Wl.png)

HTTP 訊息結構：
- Start Line
就像是訊息的引言。它顯示發送的是哪種類型的訊息，是用戶請求還是伺服器回應。
這一行也提供如何處理該訊息的重要細節。

- Headers
由鍵值對組成，提供有關 HTTP 訊息的額外資訊。
它包含各種內容，如安全性、內容類型 (content-type) 等等，確保通訊順利進行。

- Empty Line
作為分隔符 (通常是 `\r\n`)，用來分隔郵件標頭 (header) 和郵件正文 (body)。
如果沒有這個空行，可能會出錯，客戶端或伺服器可能會誤解訊息內容。

- Body
存放實際資料的地方。
在請求中：可能包含使用者想要傳送給伺服器的資料 (例如表單資料)。
在回應中：伺服器會將使用者請求的內容 (例如網頁或 API 資料) 放在這裡。

---

#### HTTP Request: Request Line and Methods

![image](https://hackmd.io/_uploads/B113Gw4TZe.png)

- Request line

    HTTP 請求的第一部分，它告訴伺服器正在處理哪種類型的請求。
    
    ```ex: METHOD /path HTTP/version```
    
    - HTTP method

        它告訴伺服器，使用者想要對 URL 路徑的資源執行什麼操作。
        
        - GET

            用於從伺服器取得資料而不做任何修改。(但要避免在 GET 請求中包含密碼等敏感訊息，因為它們可能以明文形式顯示。)
            
        - POST
        
            向伺服器發送數據，通常用於建立或更新某些內容。(必須驗證並清理輸入數據，以避免 SQL 注入或 XSS 等攻擊。)
            
        - PUT

            替換或更新伺服器上的某些內容。(確保使用者擁有進行更改的權限後再接受請求。)
        
        - DELETE

            從伺服器中刪除資源。(確保使用者擁有進行更改的權限後再接受請求。)
        
    - 其它還有一些方法用於特定情況：
        
        - PATCH

            更新資源的一部分，適用於進行小幅更改而無需替換整個資源。(驗證資料以避免不一致。)

        - HEAD

            類似於 GET 請求，但只檢索 header 而不是完整內容。這便於在不下載完整回應的情況下檢查元資料。
            
        - OPTIONS

            告訴使用者特定資源有哪些可用方法，幫助客戶端了解他們可以對伺服器執行哪些操作。

        - TRACE

            與 OPTIONS 類似，它顯示允許使用的方法，通常用於 debug。許多伺服器出於安全考慮會禁用它。

        - CONNECT

            用於建立安全連接，例如 HTTPS。雖然不太常見，但對於加密通訊至關重要。
        
    - URL path
    
        它告訴伺服器，在哪裡可以找到使用者請求要的資源。
        
        在 URL `https://tryhackme.com/api/users/123` 中，路徑 `/api/users/123` 識別了一個特定的使用者。然而攻擊者常試圖篡改路徑，利用漏洞。
        
        --> 驗證 URL 路徑以避免未經授權的存取、清理路徑以避免注入攻擊、透過進行隱私和風險評估來保護敏感數據。這些做法有助於保護 Web 應用程式免受常見攻擊。
    
    - HTTP version
        
        版本顯示出客戶端和伺服器之間通訊所使用的協定版本。
        
        - HTTP/0.9
            
            第一個版本，僅支援 GET 請求。
            
        - HTTP/1.0
        
            增加了 header、更好地支援不同類型的內容、提高了快取效率。
        
        - HTTP/1.1

            持久性連接、分塊傳輸編碼和更好的快取機制。今日仍被廣泛使用。
            
        - HTTP/2

            引入了多路復用、標頭壓縮和優先排序等功能，以提高效能。
            
        - HTTP/3
    
            基於 HTTP /2 構建，但使用新的協定 (QUIC) 以實現更快、更安全的連接。
            
`即使 HTTP/2 和 HTTP/3 提供了更快的速度和安全性，但許多系統仍然使用 HTTP/1.1，因為它擁有良好的支援性，且能夠與大多數現有配置相容。然而，隨著越來越多的系統採用 HTTP /2 或 HTTP /3，升級到這些協定可以顯著提升效能和安全性。`
    
---

#### HTTP Request: Headers and Body

- 標頭 Header：

    常見的標頭：

| Request Header | Example                                                                          | 說明                                                     | 可能的攻擊點                                                                                    |
|:--------------:|:-------------------------------------------------------------------------------- |:-------------------------------------------------------- |:----------------------------------------------------------------------------------------------- |
|      Host      | `Host: tryhackme.com`                                                            | 指出請求的網路伺服器的名稱。                             | 要預防 Host Header Injection。如果伺服器沒檢查，可能導致密碼重設連結被導向駭客網站。            |
|   User-Agent   | `User-Agent: Mozilla/5.0`                                                        | 指定發出請求的網路瀏覽器。                               | 駭客常會偽裝成 GoogleBot 或修改 UA 來規避 WAF 防火牆。也會用來判斷受害者的作業系統版本。        |
|    Referer     | `Referer: https://www.google.com/`                                               | 指示請求來源的 URL。                                     | 敏感資訊洩漏：有時 URL 裡會帶有 token，若 Referer 沒設定好，這個 token 就會被洩漏給第三方網站。 |
|     Cookie     | `Cookie: user_type=student; room=introtowebapplication; room_status=in_progress` | 網路伺服器先前請求網路瀏覽器儲存的資訊保存在 cookie 中。 | 會話劫持 (Session Hijacking)，若駭客偷到使用者的 Cookie，就能直接免密碼登入該使用者的帳號。     |
|  Content-Type  | `Content-Type: application/json`                                                 | 描述請求中的資料類型或格式。                             | 駭客會偽造 `image/jpeg` 但實際上傳 `.php` 腳本來執行遠端程式碼。                                |

- 正文 Body：

常見的資料格式：

|  Data Form  | 說明                                                                                                                                 | 例子                                                |
|:-----------:|:------------------------------------------------------------------------------------------------------------------------------------ |:--------------------------------------------------- |
| URL Encoded | 資料以鍵值對的形式組成，多個鍵值對之間以 `&` 符號分隔。(`key1=value1&key2=value2`)                                                   | `name=Aleksandra&age=27&country=US`                 |
|  Form Data  | 發送多個資料塊，每個資料塊之間以邊界字串分隔。                                                                                       | ![image](https://hackmd.io/_uploads/HJOUVhrTZl.png) |
|    JSON     | JavaScript Object Notation，資料以 **名稱:值** 對 (`name:value`) 的形式呈現。多個對之間用逗號分隔，所有內容都包含在大括號 `{ }` 中。 | ![image](https://hackmd.io/_uploads/H1KDrhBpZx.png) |
|     XML     | 資料被組織在稱為 tags 的標籤中，每個標籤都有開始標籤和結束標籤。                                                                     | ![image](https://hackmd.io/_uploads/H1Q1I3BTZg.png) |

#### HTTP Response: Status Line and Status Codes

HTTP response 會告知請求是成功還是錯誤。包含一個狀態碼 (status code) 和一段簡短的解釋 (reason phrase)。

- Status Line

    每個 HTTP 回應的第一行。它有三個資訊：
    
    - HTTP Version: 使用的 HTTP 版本。
    - Status Code: 一個三位數，顯示請求結果。
    - Reason Phrase: 用人類可讀的語言解釋狀態碼的簡短訊息。

- Status Codes and Reason Phrases

    - Informational Responses (100-199):

        這些碼表示伺服器已收到部分請求，正在等待剩餘部分。這是一個「繼續處理」的訊號。
    
    - Successful Responses (200-299):

        這些碼表示一切運作正常，伺服器已處理請求並傳回了請求的資料。
        
    - Redirection Messages (300-399):

        這些碼會告知要求的資源已移至不同的位置，通常會提供新的 URL。
        
    - Client Error Responses (400-499)

        這些碼表示請求存在問題。可能是 URL 錯誤，或缺少一些如身份驗證訊息的必要訊息。
        
    - Server Error Responses (500-599)

        這些碼表示伺服器在處理請求時遇到了錯誤，通常是伺服器端的問題。

- 常見的狀態碼

    - 100 (Continue): 伺服器已收到請求的第一部分，並準備好處理其餘部分。
    
    - 200 (OK): 請求成功，伺服器正在傳回所請求的資源。
    - 301 (Moved Permanently): 請求的資源已永久遷移到新的網址。從現在開始需使用新的 URL。
    - 404 (Not Found): 伺服器無法在指定的 URL 找到資源。可能為使用者網址打錯。
    - 500 (Internal Server Error): 伺服器端發生故障，無法處理請求。
    
#### HTTP Response: Headers and Body

- Response Headers
    
    ![image](https://hackmd.io/_uploads/rk85kDdabg.png)

    標頭是鍵值對組成，它們提供了回應的重要資訊，並告訴客戶端 (通常是瀏覽器 browser) 如何處理。
    
    - 必要的回應標頭

        有些回應標頭是必要的，來確保 HTTP 回應能正常運作。
        - Date: 
            顯示伺服器生成回應的確切日期和時間。
        
        - Content-Type: 
            它告訴客戶端取得的內容是什麼類型。同時也包含字元設定，幫助瀏覽器適當的表示。(範例：`Content-Type: text/html; charset=utf-8`)
        
        - Server: 
            顯示處理該請求的伺服器軟體及版本的資訊。(範例：`Server: nginx`)
        
            對於除錯很有幫助，但也可能暴露對攻擊者有用的伺服器資訊，因此許多人會將其移除或隱藏。

    - 其它常見的標頭
        
        它們會向客戶端或瀏覽器提供額外的指示，並有助於控制如何處理回應內容。
        - Set-Cookie: 
            伺服器傳送 cookies 給客戶端，客戶端儲存起來，並在往後的請求中傳送 cookies 給伺服器端。

            為了確保安全，要確保 cookies 設定了 `HttpOnly` 的標籤 (就無法用 JavaScript 存取 cookies) 與 `Secure` 標籤 (就只能透過 HTTPS 進行傳送 cookies)。
        
            範例：`Set-Cookie: sessionId=38af1337es7a8`
        
        - Cache-Control: 
            告訴客戶端在重新詢問伺服器之前，可以將回應內容快取 (暫存) 多久。
            
            它也可以在必要時防止敏感資料被緩存 --> 用 `no-cache` 設定。
            
            範例：`Cache-Control: max-age=600`
            
        - Location: 
            用於重新導向 (狀態碼 3xx) 的回應中，它告訴客戶端如果資源已移動，下一步該去哪裡。
            
            如果使用者可以修改此標頭的值，務必進行驗證與清理，否則可能會導致開放式重定向漏洞，讓攻擊者能藉此將使用者導向惡意網站。
            
            範例：`Location: /index.html`

- Response Body

    存放實際資料的地方，伺服器傳回給客戶端的資料如 HTML、JSON、圖片等...。
    
    為了防止 Cross-Site Scripting (XSS) 等注入攻擊，必須每次在回應中之前，將任何資料 (尤其是用戶生成的內容) 先進行過濾和轉義處理。

#### Security Headers

安全標頭有助於提高 web 應用程式的安全性，絕大多數情況下，它們是由伺服器發送給瀏覽器的指令，用以告訴瀏覽器要**對該網頁進行以下限制**，因此屬於 HTTP 回應標頭。

- Content-Security-Policy (CSP)

    此標頭是可以幫助減緩常見攻擊 (如 XSS) 的額外安全層。CSP 提供了一種方式，讓管理員可以指定哪些域名或來源是安全的，並提供一層緩解這類攻擊的措施。
    
    有許多屬性，可以使得針對不同類型的資源設定不同的信任網域：
    
    - default-src：
    指定預設的 self 政策，也就是僅限當前網站。(如果沒指定其他屬性，就通通套用這個。)
    
    - script-src：
    規定哪些網域的 JavaScript 程式碼可以執行。
    
    - style-src：指定可在當前網站 (self) 上載入 CSS 樣式的政策。
    
    關鍵字：
    - `'self'`：
    代表同源，這個關鍵字告訴瀏覽器，只能執行跟該網站同一網域的程式。這能有效防止駭客從外部惡意伺服器載入攻擊腳本。
    
    ```'self' 僅信任同源網域； 'none' 是全部不信任。```
    
    範例：`Content-Security-Policy: default-src 'self'; script-src 'self' https://cdn.tryhackme.com; style-src 'self'`
    
- Strict-Transport-Security (HSTS)

    此標頭確保網頁瀏覽器始終通過 HTTPS 連接。
    
    範例：`Strict-Transport-Security: max-age=63072000; includeSubDomains; preload`
    - max-age：
    此設定的到期時間 (以秒為單位)。
    
    - includeSubDomains：
    為可選設定，指示瀏覽器將此設置也應用於所有子網域。

    - preload：
    為可選設定，允許網站被包含在預載列表中。瀏覽器可以使用預載列表在首次造訪網站之前強制執行 HSTS。
    
- X-Content-Type-Options

    此標頭用來指示瀏覽器不要猜測資源的 MIME 類型，而只使用 Content-Type 標頭。
    
    範例：`X-Content-Type-Options: nosniff`
    - nosniff：
    指示瀏覽器不要嗅探或猜測 MIME 類型。
    
    > 防止瀏覽器「自作聰明」去猜測檔案類型。例如駭客上傳一個偽裝成圖片的腳本，如果沒設這個標頭，瀏覽器可能會把它當成腳本執行。設定 nosniff 後，瀏覽器必須嚴格遵守伺服器宣告的類型。
    
- Referrer-Policy

    此標頭控制當使用者點擊網頁上的連結跳轉到另一個網站時，要帶多少來源資訊過去。(例如當使用者點擊超連結時，這個標頭允許網站管理員控制共享哪些資訊。)
    
    一些 Referrer-Policy 範例：
    - `Referrer-Policy: no-referrer`
    完全禁用關於導向來源的任何資訊的發送。
    
    - `Referrer-Policy: same-origin`
    只在目的地是相同來源的一部分時才會傳送導向來源資訊。
    
    - `Referrer-Policy: strict-origin`
    只在協定不變時才會將導向來源傳送。因此當 HTTPS 連線轉向另一個 HTTPS 連線時會傳送。並且只傳送網域，不傳送後面的路徑。
    
    - `Referrer-Policy: strict-origin-when-cross-origin`
    與 strict-origin 類似，但發出請求的網站與接收請求的網站網域完全相同。它會傳送完整的 URL 路徑。

---

### OWASP Top 10 2025: IAAA Failures

IAAA 為 Indentity, Authentication, Authorisation, Accountability。IAAA 是一種簡化的使用者及其行為驗證方式的思路。如果前面的步驟沒有執行，就無法執行後續步驟：
- Indentity：代表人或服務的唯一帳戶，如使用者 ID、電子郵件。
- Authentication：證明身分，如密碼、金鑰。
- Authorisation：授權，該身分被允許執行的操作。
- Accountability：記錄誰在何時何地做了什麼，責任歸屬。

IAAA 確保只有授權使用者才能存取系統，並且可以追蹤他們的操作。

#### A01: Broken Access Control

當伺服器未能正確執行每次請求的存取權限控制會發生的。這類常見的事件是 IDOR (Insecure Direct Object Reference，一種存取控制漏洞，當應用程式使用使用者提供的輸入直接存取物件時就會出現這種漏洞)，如果更改 ID (例如 ?id=7 → ?id=6) 可以使得查看或編輯其他人的數據，則存取控制已失效。

這會由於應用程式過於信任客戶端，造成水平權限提升 (相同角色層級，其他使用者的內容)或垂直權限提升 (跳到僅限管理員執行的操作)。

#### A07: Authentication Failures

常見的身份驗證失敗的問題：
- 使用者名稱枚舉：系統不小心告訴了攻擊者這個帳號是否存在。
- 密碼太弱，或容易猜。若無失敗鎖定限制，可能會遭攻擊者無限制暴力破解。
- 登入/註冊流程中的邏輯缺陷。
- 不安全的會話或 Cookie 處理：登入成功後，伺服器會給一張識別證 (Cookie/Session ID)，如果這張證件沒有加密，或者很容易被猜到，或者沒有設定 `HttpOnly` 屬性，攻擊者就可以透過 XSS 攻擊直接把識別證偷走。
--> 造成攻擊者不需要使用者的帳密，直接拿使用者的 Cookie 就能登入他的帳號，這稱為會話劫持 (Session Hijacking)。

#### A09: Logging & Alerting Failure

有日誌紀錄對於責任歸屬能更加明確，能夠證明誰在何時何地做了什麼事。

失敗在實務上的表現形式通常包括：
- Missing authentication events
- Vague (模糊的) error logs
- No alerting on brute-force or privilege changes
- Short retention (日誌保留時間過短)
- Logs stored where attackers can tamper (篡改) with them

---

### OWASP Top 10 2025: Application Design Flaws

#### A02: Security Misconfigurations

指系統、伺服器或應用程式部署時使用了不安全的預設設定、不完整的配置或暴露的服務。是環境、軟體或網路配置方面的錯誤，並非程式碼漏洞。

即使是微小的配置錯誤也可能洩漏敏感資料、導致權限提升，或讓攻擊者有機會可以入侵系統。

常見形式：
- 未更改預設憑證或使用弱密碼。
- 不必要的服務或端點暴露於網路中。
- 雲端儲存或權限的配置錯誤。
- 不受限制的 API 存取權限或存取權限或缺少 authentication / authorisation。
- 過於詳細的錯誤訊息洩漏了系統資訊。
- 存在有已知漏洞的過時軟體、框架或容器。
- 缺乏適當存取控制的 AI / ML 暴露端點。

如何預防：
- 強化預設配置並移除未使用的功能或服務。
- 在所有系統中強制執行強的驗證和最小權限原則。
- 限制網路暴露與將敏感資源分區儲存。
- 用修補程式 (patches) 將軟體、框架和容器保持更新。
- 從錯誤訊息中隱藏堆疊追蹤和系統訊息。
- 定期審核雲端配置和權限配置。
- 安全的 AI 端點與具備適當存取控制和監控的端點和自動化服務。
- 將配置審查和自動化安全檢查整合到部署管道中。

問題實作：
![image](https://hackmd.io/_uploads/Sk1oMPwnWe.png)

![image](https://hackmd.io/_uploads/r1QYevvh-g.png)

![image](https://hackmd.io/_uploads/Syu_-Pw3Zl.png)

當使用者 ID 為無效輸入時：
![image](https://hackmd.io/_uploads/rykIzvw3Zx.png)

![image](https://hackmd.io/_uploads/BklOMww3-x.png)

#### A03 Software Supply Chain Failures

應用程式依賴在已被破壞而暴露、過時或未經充分驗證的元件、函式庫、服務或模型上。攻擊者會利用這些弱點注入惡意程式碼、繞過安全防護或竊取敏感資料。

現代應用程式由眾多第三方套件、API 和 AI 模型建構而成，任何一個被攻破的依賴項都可能導致整個系統癱瘓，攻擊者無需修改任何程式碼即可取得存取權限。

供應鏈攻擊可以自動化且分散式進行，因此難以偵測且破壞性極大。

常見形式：
- 使用未經驗證或未維護的程式庫和依賴項。
- 無驗證就自動安裝更新。
- 過度依賴第三方 AI 模型，缺乏監控或審核。
- 不安全的建構 pipeline 或允許更改的 CI/CD 程序。
- 元件的憑證或來源追蹤不完善。
- 部署後缺乏對依賴項漏洞的監控。

如何預防：
- 使用之前驗證所有第三方元件、程式庫和 AI 模型。
- 定期監控和修補依賴項。
- 對軟體的更新與套件進行簽署、驗證和審核。
- 封閉 CI/CD pipelines 與建立防止篡改的程序。
- 追蹤所有依賴項的來源和憑證。
- 實施運行時監控，以偵測依賴項或 AI 元件的異常行為。
- 將供應鏈威脅建模整合到 SDLC (軟體開發生命週期：開發應用程式的結構化過程)，包括測試、部署和更新工作流程。

問題實作 (參考其他人完成)：
![image](https://hackmd.io/_uploads/BJjlD5u2-g.png)

![image](https://hackmd.io/_uploads/H14vPY_nZe.png)

![image](https://hackmd.io/_uploads/ryxb5Yu3bg.png)

右鍵 --> Inspect --> Network --> Reload --> 加新的請求 (`+`號) --> GET 改選為 POST --> 新增 [Content-Type] [application/json] --> 加上 Payload ({"data":"debug"})
![image](https://hackmd.io/_uploads/HyoUbcO3be.png)

![image](https://hackmd.io/_uploads/BkOSb5_2-e.png)

#### A04 Cryptographic Failures

指加密使用不當或完全沒有使用加密。這包括演算法強度不足、硬編碼密鑰 (hard-coded keys、寫死的密鑰)、密鑰管理不善或敏感資料未加密。這些缺陷使得攻擊者能夠存取本應保密的資訊。

網路應用程式在各方面都依賴加密技術：保護網路流量、保護儲存的資料、驗證身分、保護機密資訊。

攻擊者可以透過中間人攻擊 (MITM Attacks)、對弱密鑰進行暴力破解攻擊，或者只是發現從未得到妥善保護的秘密來利用這些漏洞。

常見形式：
- 使用已棄用或存在缺陷的演算法，如 MD5、SHA-1、ECB 模式。
- 程式碼或設定檔中硬編碼的密鑰。
- 密鑰管理不當。
- 儲存中或傳輸中的敏感資料缺乏加密。
- 自主簽名或無效的 TLS (傳輸層安全協定，一種加密協定，用於保護網路通訊安全。它透過證書提供身份驗證，透過檢測篡改來保證資料完整性，並透過加密資料來保證資料機密性) 證書。
- 使用 AI/ML 模型時，缺乏對模型參數或敏感輸入進行適當的保密處理。

如何預防：
- 使用強大、現代的演算法，如 AES-GCM、ChaCha20-Poly1305、enforce TLS 1.3，並且有有效證書。
- 使用安全的金鑰管理服務，如 Azure Key Vault、AWS KMS、HashiCorp Vault。
- 按照既定的加密週期，定期輪換密鑰。
- 制定並執行關鍵生命週期管理的政策和標準作業程序。
- 維護完整的證書、鑰匙及其所有者清單。
- 確保 AI 模型和自動化代理絕不會洩露未加密的秘密資訊或敏感數據。

問題實作 (參考其他人完成)：
![image](https://hackmd.io/_uploads/ByNTAAth-g.png)

![image](https://hackmd.io/_uploads/rydz9CK2-x.png)

右鍵 --> Inspect --> Debugger
查看 static/js 下的內容看到密鑰 `SECRET_KEY`
![image](https://hackmd.io/_uploads/Skayp0thZl.png)

到 [CyberChef](https://gchq.github.io/CyberChef/) 對 `Encrypted Document` 使用 `From Base64` 還原 Base64 編碼、`AES Decrypy` 解密 (輸入密鑰 `SECERT_KEY` 並改選為 UTF-8 編碼、模式選取 `ECB`、Input 選取 `Raw`)。
![image](https://hackmd.io/_uploads/B1Qfgk9nbe.png)

#### A06 Insecure Design

不安全的設計是指系統從一開始就存在邏輯或架構缺陷。這些缺陷源自於未進行威脅建模 (threat modelling)、缺乏設計需求或審計，或非預期的錯誤。

AI 系統也加劇了不安全設計，當 AI 系統能夠不受限制地產生查詢、編寫程式碼或對使用者進行分類時，風險就已根植於設計之中。

它無法修補 (patch)，因為它已經根植於工作流程與邏輯中，要修復需要重新思考系統、AI 等如何做決定。

常見的 Insecure Designs：
- 較弱的業務邏輯控制如 recovery、approval 等流程。
- 對使用者或模型行為的存在有缺陷的假設。
- 權限或存取控制未經審核的 AI 元件。
- 大型語言模型與自動化代理缺乏防護欄、缺乏限制。
- 生產環境中殘留的測試或除錯繞過機制。
- 缺乏一致的濫用案例審查或 AI 威脅建模。

AI 世代中的 Insecure Designs：

> AI 引入了新型的設計缺陷。盲目信任模型輸出會導致系統脆弱不堪，因為系統會在未經驗證或監督的情況下執行 AI 的決策，因此人工審核仍然必不可少。

> 對於來自未經核實來源或使用不安全資料進行微調的「毒化」模型，它們可能嵌入隱藏行為或後門，從內部破壞系統。

如何安全地設計：
- 在證明其可靠性之前，應將所有模型視為不可信模型。
- 驗證並篩選所有模型輸入和輸出，以確保準確性與完整性。
- 將系統提示與使用者內容分開 (以防注入攻擊)。
- 除非必要，否則不要在提示資訊中輸入敏感數據，並使用嚴格的控制措施保護敏感資料。
- 高風險 AI 行為需要人工審核。
- 記錄模型來源、監控模型行為，並針對敏感資料套用差異隱私 (就算資料被拿去訓練，也無法回推敏感個資)。
- 包括針對 prompt 攻擊、推理風險、代理濫用、整個設計過程的供應鏈暴露，的 AI 特定的威脅模型。
- 將威脅建模融入開發的每個階段。
- 實施之前為每個功能明確定義安全要求。
- 對使用者、API 和服務採用最小權限原則。
- 確保系統中適當的驗證、授權和會話 (session) 管理。
- 確保依賴項、第三方元件和供應鏈來源得到驗證並保持最新狀態。
- 持續監控和測試系統，以發現邏輯缺陷、濫用途徑和在新功能或 AI 元件新增出現時的風險。

問題實作 (參考其他人完成)：
![image](https://hackmd.io/_uploads/ry2nd4o3bl.png)

![image](https://hackmd.io/_uploads/B1SHSEs3bx.png)

![image](https://hackmd.io/_uploads/Hy9M_4j3-g.png)

![image](https://hackmd.io/_uploads/B1i6U4shbl.png)

---

### OWASP Top 10 2025: Insecure Data Handling

#### A04: Cryptographic Failures

敏感資料因缺乏加密、採用缺陷或不足的安全措施而未被充分保護時，就會發生。這包括沒有經過雜湊 (hashing) 處理的儲存密碼、使用過時或較弱的演算法 (如 MD5、SHA1、DES)、洩漏加密金鑰或在傳輸過程中未能保護資料。

How to prevent:
    
    選擇較強且現代的演算法並適當的實施。
    
    像密碼等敏感資訊應使用穩健但速度較慢的雜湊函數 (例如 bcrypt、scrypt 或 Argon2) 進行雜湊處理。
    
    不要將存取憑證嵌入原始碼、設定檔或程式碼儲存庫。
    
    應使用安全的密鑰管理系統或專門用於儲存密鑰的環境。

問題實作為照著網站指示完成：
![image](https://hackmd.io/_uploads/ry3zzJUn-x.png)

![image](https://hackmd.io/_uploads/SyhZV18nWe.png)

![image](https://hackmd.io/_uploads/S1T7VJ82-e.png)

![image](https://hackmd.io/_uploads/B1qpQy82Wx.png)

---

#### A05: Injection

當應用程式取得使用者輸入並錯誤處理時，就會發生注入攻擊。應用程式沒有安全地處理輸入，而是將其直接傳遞給可以執行命令或查詢的系統，例如資料庫、shell、模板引擎或 API。

常見的注入攻擊範例：
- SQL Injection
- Command Injection
- AI Prompts
- Server Side Template Injection (SSTI)

How to prevent:
    
    確保使用者輸入始終被視為不可信 (untrusted)。
    
    與其直接解析輸入，不如提取輸入元素進行查詢。對於 SQL 查詢，這意味著使用準備好的語句和參數化查詢，而不是透過字串拼接建構查詢。對於作業系統指令，應避免使用直接將輸入傳遞給系統 shell 的函數，而應依賴完全不呼叫 shell 的安全 API 和進程。
    
    輸入驗證和清理：在應用程式處理輸入之前，應轉義危險字元、強制執行嚴格的資料類型並進行過濾。

問題實作為照著網站指示完成：
![image](https://hackmd.io/_uploads/r1iTX94h-l.png)

![image](https://hackmd.io/_uploads/H14az543-x.png)

![image](https://hackmd.io/_uploads/ByclX943Wx.png)

![image](https://hackmd.io/_uploads/Byc5QqNhbg.png)

---

#### A08: Software or Data Integrity Failures

指應用程式依賴它認為安全的程式碼、更新或數據，而未驗證其真實性、完整性或來源。
包括未經驗證就信任軟體更新、從不信任的來源載入腳本或設定檔、未能驗證影響應用程式邏輯的數據，或在未確認資料是否已被篡改的情況下接受二進位檔案、範本或 JSON 檔案等資料。

How to prevent:

    建立信任邊界：應用程式絕不應假定程式碼、更新或關鍵資料是合法或認為是可信任的，必須驗證其完整性。
    這包括對更新套件使用像是加密檢查 (如校驗) 等方法，並確保只有受信任的來源才能修改關鍵元件。
    
    對於應用程式而言，完整性和信任邊界也應該包含在 CI / CD 等建置過程中。

:::info
CI (Continuous Integration)：
自動化測試與建置。開發者頻繁地將程式碼合併回主分支。
開發者提交程式碼 --> 系統自動觸發建置，將原始碼編譯成可執行的成品 --> 自動執行單元測試與程式碼掃描。

CD (Continuous Delivery / Deployment)：
- 持續交付 (Continuous Delivery)：
    程式碼通過 CI 測試後，會自動封裝成可發布的狀態。最後一步的部署到正式環境仍需人工點擊確認。
- 持續部署 (Continuous Deployment)：
    比持續交付更進一步，只要程式碼通過所有自動化測試，系統就會直接自動將其部署到正式環境 (Production)。
:::

問題實作為照著網站指示完成：
Deserialize: 反序列化。
![image](https://hackmd.io/_uploads/ryr1FJI3Wl.png)

![image](https://hackmd.io/_uploads/SJ2mSyUnbe.png)

![image](https://hackmd.io/_uploads/rJcvPyU2Zx.png)

![image](https://hackmd.io/_uploads/ByZ8_y82bl.png)

![image](https://hackmd.io/_uploads/rJeIBJUnWg.png)
![image](https://hackmd.io/_uploads/HkzwHyUhWx.png)

---

### Detecting Web Attacks

網路攻擊是攻擊者入侵目標系統最常見的手段之一。面向公眾的網站和網路應用程式通常位於資料庫及其他基礎架構的前端，因此成為攻擊者的理想目標。

#### Client-Side Attacks

客戶端攻擊是利用使用者行為上的弱點或使用者設備上的漏洞造成的。通常會利用瀏覽器中的安全漏洞，或誘騙使用者執行不安全的操作，從而獲得帳戶的存取權限並竊取敏感資料。

分析師可用的工具，幾乎無法讓人了解使用者瀏覽器內部發生什麼事。因此若沒有額外的客戶端安全控制或端點監控措施，安全團隊很難甚至無法偵測到這類攻擊。
![image](https://hackmd.io/_uploads/H1gCU9TpZe.png)

常見的客戶端攻擊：
- Cross-Site Scripting (XSS)：最常見的客戶端攻擊。惡意腳本會在受信任的網站上運行，並在使用者的瀏覽器中執行。

    攻擊者可能會製造彈出窗與竊取使用者的 cookies 或會話資料。當訪客訪問受注入惡意腳本的網頁時，腳本就會在使用者的瀏覽器中執行，從而導致彈出窗出現。
    
- Cross-Site Request Forgery (CSRF)：瀏覽器被欺騙，以受信任用戶的名義發送了未經授權的請求。

- Clickjacking：攻擊者在合法內容上覆蓋隱形元素，讓使用者以為自己正在跟安全的事物互動。

---

#### Server-Side Attacks

伺服器端攻擊是利用網路伺服器、應用程式的程式碼、網站或網路應用程式的後端等的漏洞來實現的。透過利用各種缺陷、配置錯誤或資料輸入處理上的漏洞，攻擊者能夠取得系統訪問權、竊取資訊、破壞正在運作的服務。

客戶端攻擊旨在操控使用者與網站的互動，然而伺服器端攻擊專門利用系統本身的漏洞。

每個發送給應用程式的網路請求都會經由伺服器處理，並被記錄在日誌或各種監控系統中，這些請求也會在網路上傳輸，因此網路流量可以幫助我們發現可疑的行為。
![image](https://hackmd.io/_uploads/SyeWt110bl.png)

常見的伺服器端攻擊：
- Brute-force Attacks：指攻擊者不斷嘗試不同的帳號或密碼，試圖獲取對某個帳戶的未經授權的訪問權限。攻擊者通常會使用自動化工具來快速發送這些請求，從而能夠快速遍歷大量的憑證和常見密碼。

- SQL Injection (SQLi)：攻擊網站背後的資料庫。當應用程式透過字串連接來建立查詢，而非使用參數化查詢時，攻擊者就能夠改變查詢的內容。

- Command Injection：當網站接收使用者輸入的內容後，網站不經過檢查就直接將其傳遞給系統時，就會發生的攻擊。攻擊者可以偷偷插入指令，讓伺服器以與該應用程式相同的權限來執行這些指令。

---

#### Log-Based Detection

每個發送給網路伺服器的請求都會在伺服器的存取日誌和錯誤日誌中留下痕跡。我們可以透過審查這些日誌記錄，發現用於掃描、嘗試入侵或其他攻擊的跡象。

日誌一般都包含以下資訊：
![image](https://hackmd.io/_uploads/HJEnTsyA-l.png)

1. Client IP Address
指標範例：已知的惡意 IP 或在預期地理範圍以外。

2. Timestamp and Requested Page
指標範例：在非正常時間提出的請求，或是在短時間內多次重複提出的請求。
3. Status Code
指標範例：`404` 表示找不到該網頁。
4. Response Size
指標範例：與正常的回應大小相比特別大或特別小。
5. Referrer
指標範例：不符合正常網站導向的網頁。
6. User-Agent
指標範例：過時的瀏覽器版本或常見的攻擊工具 (`sqlmap`、`wpscan` 等)。

---

簡化的 Log 攻擊過程範例：

![image](https://hackmd.io/_uploads/HJeK9CWR-e.png)
1. 攻擊者用目錄模糊來測試潛在可被利用的目錄和表單。回應碼 `200` 表示攻擊者找到了有效的攻擊目標。

2. 接著，攻擊者利用已透過暴力破解的方式找到的 `login.php` 表單來進行攻擊。

    在連續發出的 `POST` 請求中，最後一個的回應碼為 `302 Found`，在此例中表示登入嘗試成功，頁面接著會重新導向至在 `/account` 上使用者帳號頁面。

3. 一旦攻擊者獲得了該帳戶的存取權限，他們嘗試在 `/search` 表單上嘗試使用兩種 SQL 注入查詢，`' OR '1'='1` 和 `1' OR 'a'='a`。

    如果該應用程式是以動態方式來建立 SQL 查詢，而非使用參數化查詢，那麼攻擊者就能夠將資料庫中的資料全部盜出。

實作任務：
![image](https://hackmd.io/_uploads/Hy39T1MAWg.png)

1. What is the attacker's User-Agent while performing the directory fuzz?
    
    ![image](https://hackmd.io/_uploads/HJgoAyz0bl.png)

    A：FFUF v2.1.0
    
2. What is the name of the page on which the attacker performs a brute-force attack?
    
    ![image](https://hackmd.io/_uploads/rktTA1G0Zx.png)
    
    A：/login.php
    
3. What is the complete, decoded SQLi payload the attacker uses on the `/changeusername.php` form?
    
    ![image](https://hackmd.io/_uploads/BJOORJf0Zx.png)
    
    用 CyberChef
    ![image](https://hackmd.io/_uploads/r1gOpJf0-l.png)
    
    A：%' OR '1'='1
    
---

#### Network-Based Detection

網路流量分析讓分析人員能夠檢查用戶端與伺服器之間所交換的原始數據。透過擷取和檢查各個資料包，分析人員能更詳細地瞭解各種攻擊行為，包括底層的傳輸協定以及應用程式所傳輸的數據本身。

資料可能包括完整的 HTTP 標頭、POST 數據、Cookies，以及上傳和下載的檔案等。

以上面的 Log 攻擊過程比較在 Wireshark 中該過程的顯示方式：
![image](https://hackmd.io/_uploads/ry-bfPVAWe.png)

它可以使用各種不同的篩選器來突顯感興趣的欄位。這裡篩選了目標 IP 地址 `10.10.20.200`，並篩選了 `User-Agent` `http.user_agent`。

回顧攻擊過程：
1. 目錄模糊來測試潛在可被利用的目錄和表單。
2. 暴力破解得到的第 13 個封包登入成功了。
3. SQL 注入嘗試。

暴力破解的嘗試：查看大量針對 `login.php` 表單的 `POST` 請求中的最後一個 (第 13 個封包)，由於該請求成功了，因此可知攻擊者找到了正確的密碼 `password123`。
![image](https://hackmd.io/_uploads/HktLVPVA-x.png)

SQLi 嘗試：透過檢查 HTTP 封包，能清楚看到攻擊者所使用的負載以及攻擊的結果。在此例中，`' OR '1'='1` 負載使得攻擊者能夠獲取 Users 表中的數據，進而以明文形式查看每個用戶的姓名
![image](https://hackmd.io/_uploads/ByPLrw4C-x.png)

實作任務：
![image](https://hackmd.io/_uploads/SkzguDVRbe.png)

1. What password does the attacker successfully identify in the brute-force attack?
![image](https://hackmd.io/_uploads/B1aMojH0Wx.png)
![image](https://hackmd.io/_uploads/rJWwehrRWx.png)
![image](https://hackmd.io/_uploads/B1qtenrA-x.png)

2. What is the flag the attacker found in the database using SQLi?
![image](https://hackmd.io/_uploads/ByQex2SR-g.png)
![image](https://hackmd.io/_uploads/rJPn1hHCZl.png)

---

#### WAF (Web Application Firewall)

![image](https://hackmd.io/_uploads/HJU5UfICbe.png)

WAF 通常是保護網站和網路應用程式的第一道防線。它會檢查完整的請求封包，類似於 Wireshark，但還有解密 TLS 流量並在數據到達伺服器之前進行過濾的功能。

WAF 會根據預先定義的規則 (Rules)，來檢查網路請求，並決定是允許其通過還是完全阻擋它。

常見的規則：
|     Rule Type      | 說明                                                           | 範例                                            |
|:------------------:|:-------------------------------------------------------------- |:----------------------------------------------- |
| 阻擋常見的攻擊模式 | 阻擋已知的惡意負載與指標。                                     | 阻擋已知的惡意 User-Agents `sqlmap`。           |
| 拒絕已知的惡意來源 | 利用 IP 信用評估、威脅情報或地理阻擋技術，來阻止有風險的流量。 | 阻擋近期遭到殭屍網絡攻擊的 IP 地址。            |
|      自訂規則      | 量身打造，滿足特定應用的需求。                                 | 對 `/login` 僅允許 GET 和 POST 請求。           |
| 速率限制與濫用防範 | 限制請求頻率以防濫用行為。                                     | 將每個 IP 地址每分鐘的登入次數限制在 5 次以內。 |

例：自訂阻擋所有 User-Agent 字串中包含 `sqlmap` 的。
`If User-Agent contains "sqlmap"`
`then BLOCK`

![image](https://hackmd.io/_uploads/ByASTfIR-x.png)

![image](https://hackmd.io/_uploads/Sknl5q_Cbl.png)

---

### Network Security Essentials

![image](https://hackmd.io/_uploads/ByqBqgoAZl.png)

以一個小型企業網絡為例，來瞭解其網路的各個組成部分：
- User Workstations (Endpoints)

    員工們在工作站 (電腦、筆電) 上處理日常工作。通常攻擊者會透過網路釣魚郵件或惡意下載文件來實施攻擊。
    
    Importance：端點日誌 (Endpoint logs) 可以揭示惡意程式，但網路日誌可能首先顯示出用於命令與控制 (C2) 的連接記錄。

- File & Database Servers

    這些伺服器儲存著企業最重要的資產：數據。
    
    檔案伺服器讓人能夠集中存取共享文件；資料庫伺服器則用於管理客戶記錄、人力資源資訊或財務數據等結構化數據。
    
    Importancw：攻擊者通常會把這些伺服器作為**攻擊目標**，因為一旦攻破這些伺服器，就能取得有價值或機密的資料。
    
- Application Servers (Web, Email, VPN, etc.)
    
    這些伺服器提供員工和客戶日常所依賴的各項服務。
    
    - Web Servers：託管公司網站與網路應用程式。
    - Email Servers：處理企業的通訊溝通事務。
    - VPN Gateways：允許安全的遠端存取內部資源。
    
    Importance：應用程式伺服器是面向外部的，因此它們成為了極具價值的攻擊目標。攻擊者會不斷掃描這些伺服器，尋找軟體漏洞或配置上的弱點。一旦成功利用了其中的某個漏洞，攻擊者就能夠順利進入內部網路。
    
    我們需要監控應用程式日誌、防火牆警報以及入侵偵測系統的記錄，以便識別各種安全問題：
    
    - Exploit attempts (例如一個網路 app 上有 SQL injection)。
    - 在 email 或 VPN 服務上有暴力破解登入嘗試 (brute-force login attempts)。
    - 可疑的外部 IPs 與敏感應用程式交流。
    
- Active Directory (AD) / Authentication Servers
    
    Active Directory 是大多數企業網絡中的身份認證核心，它管理使用者 users、群組 groups、電腦以及他們的訪問權限。
    
    Importance：
    - AD 是控制網絡中所有使用者帳戶和系統的主要元素。
    - 攻擊者通常針對 AD 以實現權限提升、永久性、以及橫向移動。
    - 單一個暴露的 admin 帳號足以讓整個企業陷入癱瘓狀態。
    
    我們需要監控認證記錄，以發現任何可疑行為：
    
    - 多次登入失敗嘗試 (密碼噴射攻擊)。
    - 來自外部 IPs 或在異常時間的異常登入。
    
- Routers & Switches (Network Infrastructure)
    
    路由器連接不同的網絡，最重要的是連結企業的 LAN 與網際網路。
    
    Switches 連接同一網絡中的各個設備，確保員工的電腦、打印機和伺服器能夠順暢地互相通信。
    
    這些設備可說是企業的**循環系統**。
    
    Importence：在大多數企業環境中，路由器和交換機並不會直接暴露在外，但一旦遭到攻擊，就會讓攻擊者得以：
    - 攔截並操控網路流量 (中間人攻擊 MITM attacks)
    - 改變流量傳輸路徑來建立後門。
    - 打開通往網際網路的隱藏通道。
    
- Firewalls / Perimeter Devices

    防火牆負責控制受信任的內部網絡與不可信的網際網路之間的流量。它會檢查進入與出口的封包，並根據既定的安全規則來決定是允許其通過還是阻擋。
    
    Importance：
    - 避免企業直接與網路接觸來保護企業。
    - 防止未經授權的訪問內部服務 (如資料庫)。
    - 記錄每一次的連接嘗試，無論是成功還是失敗，因為這些記錄往往是端口掃描、暴力破解或漏洞利用等攻擊的早期跡象。
    
#### Network Visibility

能夠監控並瞭解整個網絡中發生的一切對於安全分析人員而言至關重要，而日誌能夠實現這樣的功能，因為日誌記錄了在網絡中以及各個設備上所發生的各種事件。

日誌來源：
1. Host-Centric Logs

    由網絡中的各個設備 (主機) 如伺服器、工作站、筆電等所生成的，讓我們能夠詳細了解特定主機上發生的一切。

    - 主要來源：
        - Operating System Logs：
        Windows 事件日誌、Linux `syslog`、macOS 日誌。這些日誌紀錄了如使用者登入、程式建立、服務啟用、失敗的登入嘗試等事件。
        
        - Application Logs：
        主機上運行的軟體所產生的日誌，例如網路伺服器 (Apache、Nginx)、databases (MySQL、MSSQL)、與其他應用程式。
        
        - Security Tool Logs：
        來自防毒軟體、端點偵測與回應 (EDR) 代理、與基於主機的入侵檢測系統 (HIDS)。
        
2. Network-Centric Logs
    
    由部署在網路中的各種網路設備所產生的，這些設備負責監控流經其上的數據流量，讓我們了解設備之間的互動情況。
    
    - 主要來源：
        - Firewalls：
        防火牆日誌紀錄了每個根據預定義安全規則允許或拒絕的連接，它們是檢查是否有來自網際網路中未經授權連接嘗試的第一線。
        
        - Intrusion Detection/Prevention Systems (IDS/IPS)：
        它們監控網路流量，尋找與已知惡意攻擊特徵相符的模式或異常行為。
        
        - Routers and Switches：
        路由器等設備能產生流量數據，這些數據能夠概括網絡上的各種通訊活動，包括誰與誰通訊、通訊持續了多長時間、以及共傳輸了多少數據。
        
        - Web Proxies：
        它們記錄了每位網站使用者的訪問記錄，從而幫助組織瞭解各種網路威脅、違反規定的行為，以及數據外洩的嘗試。
        
        - VPN：
        這些裝置用於管理員工的遠端存取。它們所記錄的日誌會顯示誰在何時、何地連接到企業網路，這對於監控遠端連線的安全性至關重要。
    
---
    
#### Network Perimeter

網路邊界是指將組織的內部網路與外部不可信任區域的外部網路區隔的界線，是資料進入或離開網路的入口。

網路邊界的常見組成部分：
- Firewalls：過濾內部網路與外部網路之間流量的安全設備。
- Routers / Gateways：路由流量與執行存取規則的設備。
- Demilitarized Zone (DMZ)：用於緩衝的網路區段，放置於面向公眾的伺服器 (如網頁、郵件、VPN 等)。
- Remote Access Gateways / VPNs：為在辦公室外工作的員工提供安全的連線。

網路邊界是一組相互協作的安全控制措施和網路組件，共同用來保護內部資產免受外部威脅，就像一個守門人。

小型企業中的網路邊界：
- 一個位於網際網路與內部 LAN 之間的防火牆。
- 放置在 DMZ 中的網路伺服器，使得使用者能存取網站。
- 內部伺服器 (AD、檔案、資料庫) 存放在防火牆後且僅限員工可以存取。
- 不在辦公室的員工透過 VPN gateway 來連接。

---

網路邊界的監控與防護：

監控邊界意味著要使用防火牆、IDS / IPS、以及存取控制來檢查與限制暴露威脅，和採用安全規則。如此安全分析師就能：
- 及時發現各種初期攻擊，如端口掃描或暴力破解嘗試。
- 檢測出會導致敏感服務暴露於風險中的配置錯誤。
- 識別出可能表明存在惡意軟體或資料外洩的出站流量異常。

以下為 THM 上該房間虛擬機中桌面上的 `/Perimeter_logs/task6/` 路徑的資料夾中的幾個日誌作為分析範例與練習：

##### 範例情境 1：Port Scanning

攻擊者在掃描測試看說哪些 ports 是開著的，哪些 ports 關著的，防火牆會接收並允許或阻擋該 IP。
![image](https://hackmd.io/_uploads/BJAskgkkMx.png)

> 同樣的外部 IP `203.0.113.10` 頻繁嘗試連接同一內部 IP 的多個 ports。
>
> --> 典型的端口掃描行為，攻擊者正在尋找可被攻擊的開放式服務。

##### 範例情境 2：Attacking the Web Server (SQL Injection)

網站正遭受攻擊，IDS 能提供比防火牆更詳細的資訊，可辨別出攻擊的類型。
![image](https://hackmd.io/_uploads/rya6elJ1Ml.png)
![image](https://hackmd.io/_uploads/Bk7kbxkkfx.png)

> 日誌顯示了允許和阻擋兩種操作，分析師可以篩選 `action=BLOCK` 立刻找出威脅。WAF 負責執行阻擋請求的動作，並說明阻擋的原因。
> 
> `attack_type="SQL Injection"` 警告表示攻擊者試圖盜取資料庫的資訊。
> 
> `attack_type="XSS"` 警告表示有人試圖注入惡意腳本。
>
> attack_type="Directory Traversal" 警告表示有人試圖查看伺服器上的敏感檔案。
> 
> --> WAF 成功識別並阻擋了來自可疑 IP 的多種網絡攻擊。

##### 範例情境 3：Guessing the Password (VPN Brute-Force)

攻擊者試圖猜測用戶的密碼，以便遠端存取該網路。
![image](https://hackmd.io/_uploads/S1aqeg11Mg.png)

> 日誌中有分別標示 `FAILED_AUTH` 和 `SUCCESS_AUTH` 的事件。
> 
> 有一些來自不同的 IP 都是正常的登入成功。
> 
> 問題是有大量的失敗。
>
> 為了找出攻擊來源，分析師會根據來源 IP 來將日誌進行過濾或分組。
>
> 這樣便能立刻查明是哪個可疑用戶在極短的時間內發動了數百次登入嘗試。
> 
> --> 發現一個可疑的 IP 正在對 VPN gateway 發動暴力破解攻擊。攻擊者試圖使用一些常見的用戶名稱 (如 `admin`、`root`、`test`等)，來尋找可被攻破的有效帳戶。而那些成功的登入記錄，其實都是合法員工的正常操作而已。

##### 練習 1：Examine the firewall logs. Which IP address is performing the port scan?
![image](https://hackmd.io/_uploads/H1RhdU1kfx.png)
A：`203.0.113.10`

##### 練習 2：In the WAF Logs, which single source IP is responsible for all the blocked web attacks?
![image](https://hackmd.io/_uploads/H1fvFWlkzx.png)
A：`198.51.100.12`

##### 練習 3：In the VPN logs, how many brute-force attempts failed?

![image](https://hackmd.io/_uploads/ryQb9D-yfl.png)
A：90

##### 練習 4：Which suspicious IP address was found attempting the brute-force attack against the VPN gateway?

![image](https://hackmd.io/_uploads/H1gDcwWyfx.png)
A：`45.137.22.13`

---

#### Perimeter Logs: Investigating the Breach

情境：一家中型金融服務公司最近部署了一套新的防火牆與入侵檢測系統 (IDS)，用於監控其網絡邊界。過去的一個月裡，安全分析師注意到了一些異常的網絡流量，要做的就是檢視這一個月來的邊界日誌去分析，這些日誌在 THM 的這個房間中虛擬機的 Destktop/Perimeter_logs/challenge 目錄下。

此公司的資產：
![image](https://hackmd.io/_uploads/rJ1kBnzJzl.png)

1. Examine the firewall logs. What external IP performed the most reconnaissance?
![image](https://hackmd.io/_uploads/SkSYQGNkzx.png)
A：203.0.113.45

2. In the firewall log, Which internal host was targeted by scans?
![image](https://hackmd.io/_uploads/Hyo24REJMl.png)
A：10.0.0.20

3. Which username was targeted in VPN logs?
![image](https://hackmd.io/_uploads/rkviRTEkzx.png)
A：svc_backup

4. What internal IP was assigned after successful VPN login?
![image](https://hackmd.io/_uploads/H1JecW41zx.png)
A：10.8.0.23

5. Which port was used for lateral SMB attempts?
![image](https://hackmd.io/_uploads/H1IDxRNyMl.png)
A：445

6. In the IDS logs, which host beaconed to the C2?
![image](https://hackmd.io/_uploads/rJeE4jI1Gl.png)
A：10.0.0.60

7. During the investigation, which IP was observed to be associated with C2?
![image](https://hackmd.io/_uploads/rk8YNo81fl.png)
A：198.51.100.77

8. Which host showed the exfiltration attempts?
![image](https://hackmd.io/_uploads/r1oldjUkGl.png)
A：10.0.0.51

---
    
### Network Discovery Detection

#### Network Discovery

攻擊者通常會優先嘗試識別那些開放、公共可存取的網際網路 (稱為攻擊面) 中的組織資產。攻擊者會嘗試得知他們可以存取哪些資產、這些資產運行的 IP 地址、ports、作業系統、服務、服務的版本、還有是否有哪些服務有弱點可以利用。

![image](image.png)

防禦者也會執行 network discovery 的動作。
防禦者的目標：
- 盤點組織的所有資產，並確保所有資產都有記錄。

- 確保沒有不必要的 IP 地址、port、服務被開啟，確保正在運行的所有服務都是必要的。

- 確保修補所有漏洞，或至少修補那些可被利用的漏洞。

簡而言之就是盡可能的減少攻擊面。

---

攻擊者和防禦者都會執行「網路發現」的操作，多個研究機構、網絡爬蟲、搜索引擎等，也會執行類似的發現操作，因此 SOC 團隊需要區分哪些發現操作是合法的，哪些是惡意行為。

為了應對這樣的挑戰，SOC 團隊通常採用以下技術：
- 將已知的內部與無害的外部掃描器列入允許清單，來確保這些來源不會產生任何警報。

- 結合威脅情報與檢測使用方案，並且只標記來自已知惡意的或可疑的來源的活動。

- 一些團隊會利用威脅情報來提高警報的嚴重程度，而不是僅僅觸發警報。此外還會添加一些通用的用例，用於檢測掃描行為。

---

#### External vs Internal Scanning

- External Scanning
來自組織網路外部，並掃描網絡內部的機器 (主要針對網絡邊緣的公共資產) 的掃描活動。這種攻擊的來源 IP 是外部 IP 地址，目標 IP 為屬於被攻擊組織的 IP 地址。

    這種掃描指出攻擊仍在偵查階段 (MITRE ATT&CK lifecycle 中的 Reconnaissance 階段)，攻擊者尚未在內部網路內有立足點 (foothold)，他們還在找有無什麼機會取得對內部網路的存取。

    ![alt text](image-1.png)

    這種類型的掃描是攻擊的初期階段，攻擊者尚未在網絡中建立任何可利用的入口，因此屬於低危險等級的掃描活動，分析師可以阻擋組織網路邊界防火牆上的惡意 IP 地址，然而攻擊者也有可能隱藏其 IP 地址再次發起攻擊。

- Internal Scanning
內部網路到內部網路的掃描活動，來源 IP 與目標 IP 都是組織內網中的私有 IP 地址。

    這種掃描指出攻擊已經進行到發現階段 (MITRE ATT&CK lifecycle 中的 Discovery 階段，在有些框架稱為 internal reconnaissance)，攻擊者在網路中已有立足點，並且正準備進行橫向移動。
    
    ![alt text](image2.png)

    這種掃描結果表示攻擊者已經進入了網路，因此屬於高危險等級的警報。確認這不是任何授權活動後，分析師應升級警報，並啟動 incident response 流程。

練習：在 THM 房間的虛擬機中的 `Downloads/logs` 目錄有一些從 SIEM 導出的 `CSV` 檔，回答以下問題。
![alt text](image-2.png)
![alt text](image-3.png)

1. Which file contains logs that showcase internal scanning activity?
![alt text](image-4.png)
A：`log-session-2.csv`
    > 因為 `src_ip` 與 `dst_ip` 一樣共享 `192.168.230...`。
    
    > 第一行的欄位名稱是 port 而不是 ip 的原因是在各檔案的 `@timestamp` 欄位中也有逗號，使用 `cut` 指令並指定用逗號分隔會使得欄位編號需要多一才能選取到要分析的兩個 ip 欄位。

2. How many log entries are present for the internal IP performing internal scanning activity?
![alt text](image-6.png)
A：2276

3. What is the external IP address that is performing external scanning activity?
![截圖](image-5.png)
A：`203.0.113.25`

> 第三欄的欄位名稱為來源 port 而不是來源 ip，是因為在 `@timestamp` 欄位也有逗號，若要篩選正確的來源 ip 資訊，以逗號分隔來說在第三欄位，因此忽略欄位名稱針對要分析的內容篩選而已。

---

#### Horizontal vs Vertical Scanning