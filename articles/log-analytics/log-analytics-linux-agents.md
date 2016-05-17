<properties
	pageTitle="將 Linux 電腦連接到 Log Analytics | Microsoft Azure"
	description="使用 Log Analytics，您可以收集和處理從 Linux 電腦所產生的資料。"
	services="log-analytics"
	documentationCenter=""
	authors="bandersmsft"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="banders"/>

# 將 Linux 電腦連接到 Log Analytics

使用 Log Analytics，您可以收集和處理從 Linux 電腦所產生的資料。將從 Linux 收集的資料加入 OMS 中，可讓您管理 Linux 系統和容器解決方案 (如 Docker)，無論您的電腦位於何處 (含虛擬的任何位置)。因此，這些資料來源可能位於內部部署資料中心內，以做為實體伺服器、雲端託管服務 (如 Amazon Web Services (AWS) 或 Microsoft Azure) 中的虛擬電腦，甚至桌上的膝上型電腦。此外，OMS 同樣也會收集來自 Windows 電腦的資料，因此支援真正混合式 IT 環境。

您可以使用單一管理入口網站，來檢視和管理來自具有 OMS 中 Log Analytics 之所有來源的資料。這會降低使用許多不同系統監視它的需求、讓您輕鬆地使用它，而且您可以將喜歡的任何資料匯出至任何您已經有的商務分析解決方案或系統。

這篇文章是快速入門指南，可協助您使用 OMS Agent for Linux 來收集和管理 Linux 電腦的資料。如需其他技術詳細資訊，您可在 Github 上的 [OMS Agent for Linux 概觀](https://github.com/Microsoft/OMS-Agent-for-Linux)和 [OMS Agent for Linux 完整文件](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md)找到該資訊。


目前，您可以從 Linux 電腦收集下列類型的資料︰

- 效能度量
- Syslog 事件
- 來自 Nagios 和 Zabbix 的警示
- Docker 容器效能計量、清查和記錄檔

## 支援的 Linux 版本

各種 Linux 散發套件正式支援 x86 和 x64 版本。不過，OMS Agent for Linux 也可能在未列出的其他散發套件上執行。

- Amazon Linux 2012.09 到 2015.09
- CentOS Linux 5、6 和 7
- Oracle Linux 5、6 和 7
- Red Hat Enterprise Linux Server 5、6 和 7
- Debian GNU/Linux 6、7 和 8
- Ubuntu 12.04 LTS、14.04 LTS、15.04、15.10
- SUSE Linux Enterprise Server 11 和 12

## OMS Agent for Linux
Operations Management Suite Agent for Linux 包含多個封裝。發行檔案包含下列封裝 (搭配執行殼層組合與 `--extract` 即可取得)。

**Package** | **版本** | **說明**
----------- | ----------- | --------------
omsagent | 1\.1.0 | Operations Management Suite Agent for Linux
omsconfig | 1\.1.1 | OMS 代理程式的組態代理程式
omi | 1\.0.8.3 | 開放式管理基礎結構 (OMI) -- 輕量型 CIM 伺服器
scx | 1\.6.2 | 作業系統效能計量的 OMI CIM 提供者
apache-cimprov | 1\.0.0 | OMI 的 Apache HTTP 伺服器效能監視提供者。僅在偵測到 Apache HTTP 伺服器時才安裝。
mysql-cimprov | 1\.0.0 | OMI 的 MySQL 伺服器效能監視提供者。僅在偵測到 MySQL/MariaDB 伺服器時才安裝。
docker-cimprov | 0\.1.0 | OMI 的 Docker 提供者。僅在偵測到 Docker 時才安裝。

### 其他安裝構件
安裝 OMS Agent for Linux 封裝之後，會套用下列額外的全系統組態變更。解除安裝 omsagent 封裝時，會移除這些構件。
- 會建立名為 `omsagent` 的非特殊權限使用者。這是 omsagent 精靈所執行的帳戶
- sudoers “include” 檔案建立於 /etc/sudoers.d/omsagent。這會授權 omsagent 重新啟動 syslog 和 omsagent 精靈。如果已安裝的 sudo 版本不支援 sudo “include” 指示詞，這些項目將寫入 /etc/sudoers。
- syslog 組態修改成將事件子集轉送給代理程式。如需詳細資訊，請參閱下面**設定資料收集**一節。

### Linux 資料收集詳細資料

下表顯示資料收集方法和其他資料收集方式的詳細資料。

| 來源 | 直接代理程式 | SCOM 代理程式 | Azure 儲存體 | SCOM 是否為必要項目？ | 透過管理群組傳送的 SCOM 代理程式資料 | 收集頻率 |
|---|---|---|---|---|---|---|
|Zabbix|![是](./media/log-analytics-linux-agents/oms-bullet-green.png)|![否](./media/log-analytics-linux-agents/oms-bullet-red.png)|![否](./media/log-analytics-linux-agents/oms-bullet-red.png)| ![否](./media/log-analytics-linux-agents/oms-bullet-red.png)|![否](./media/log-analytics-linux-agents/oms-bullet-red.png)|1 分鐘|
|Nagios|![是](./media/log-analytics-linux-agents/oms-bullet-green.png)|![否](./media/log-analytics-linux-agents/oms-bullet-red.png)|![否](./media/log-analytics-linux-agents/oms-bullet-red.png)| ![否](./media/log-analytics-linux-agents/oms-bullet-red.png)|![否](./media/log-analytics-linux-agents/oms-bullet-red.png)|與抵達同時|
|syslog|![是](./media/log-analytics-linux-agents/oms-bullet-green.png)|![否](./media/log-analytics-linux-agents/oms-bullet-red.png)|![否](./media/log-analytics-linux-agents/oms-bullet-red.png)| ![否](./media/log-analytics-linux-agents/oms-bullet-red.png)|![否](./media/log-analytics-linux-agents/oms-bullet-red.png)|從 Azure 儲存體：10 分鐘；從代理程式：與抵達同時|
|Linux 效能計數器|![是](./media/log-analytics-linux-agents/oms-bullet-green.png)|![否](./media/log-analytics-linux-agents/oms-bullet-red.png)|![否](./media/log-analytics-linux-agents/oms-bullet-red.png)| ![否](./media/log-analytics-linux-agents/oms-bullet-red.png)|![否](./media/log-analytics-linux-agents/oms-bullet-red.png)|依排程，最少 10 秒|
|變更追蹤|![是](./media/log-analytics-linux-agents/oms-bullet-green.png)|![否](./media/log-analytics-linux-agents/oms-bullet-red.png)|![否](./media/log-analytics-linux-agents/oms-bullet-red.png)| ![否](./media/log-analytics-linux-agents/oms-bullet-red.png)|![否](./media/log-analytics-linux-agents/oms-bullet-red.png)|每小時|



### 封裝需求
| **必要封裝** | **說明** | **最低版本**|
|--------------------- | --------------------- | -------------------|
|Glibc |	GNU C 程式庫 | 2\.5-12|
|Openssl | OpenSSL 程式庫 | 0\.9.8e 或 1.0|
Curl | cURL Web 用戶端 | 7\.15.5
|Python-ctypes |函數程式庫 | n/a|
|PAM | 插入式驗證模組 |n/a |

>[AZURE.NOTE] 需要有 rsyslog 或 syslog-ng，才能收集 syslog 訊息。Red Hat Enterprise Linux 第 5 版、CentOS 和 Oracle Linux 版本 (sysklog) 不支援預設 syslog 精靈，進行 syslog 事件收集。若要從此版的這些散發套件收集 syslog 資料，rsyslog 精靈應該安裝和設定為取代 sysklog。

## 快速安裝

執行下列命令來下載 omsagent，並驗證總和檢查碼，然後安裝代理程式並將之登入。命令是針對 64 位元。工作區識別碼和主索引鍵位於 [連接的來源] 索引標籤上 [設定] 下的 OMS 入口網站。

![工作區詳細資料](./media/log-analytics-linux-agents/oms-direct-agent-primary-key.png)

```
wget https://github.com/Microsoft/OMS-Agent-for-Linux/releases/download/v1.1.0-28/omsagent-1.1.0-28.universal.x64.sh
sha256sum ./omsagent-1.1.0-28.universal.x64.sh
sudo sh ./omsagent-1.1.0-28.universal.x64.sh --upgrade -w <YOUR OMS WORKSPACE ID> -s <YOUR OMS WORKSPACE PRIMARY KEY>
```

有各種其他方法可安裝代理程式，並將它升級。您可以在 [OMS Agent for Linux 安裝步驟](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#steps-to-install-the-oms-agent-for-linux)深入閱讀它們。


## 選擇 Linux 資料收集方法

如何選擇您想要收集的資料類型，取決於您是否要使用 OMS 入口網站，或者是否想要直接編輯 Linux 用戶端上的各種組態檔。如果您選擇使用入口網站，會自動將組態傳送給所有 Linux 用戶端。如果不同的 Linux 用戶端需要不同的組態，則需要個別編輯用戶端檔案，或使用替代方法 (如 PowerShell DSC、Chef 或 Puppet)。

您可以指定您想要使用 Linux 電腦上組態檔所收集的 syslog 事件和效能計數器。*如果您選擇編輯代理程式組態檔來設定資料收集，則應該停用集中式組態。* 提供下面的指示，以在代理程式的組態檔中設定資料收集，以及停用所有 OMS Agent for Linux 或個別電腦的集中式組態。

### 停用個別 Linux 電腦的 OMS 管理

使用 OMS\_MetaConfigHelper.py 指令碼，針對個別 Linux 電腦停用組態資料的集中式資料收集。如果有一部分的電腦應該有特製化組態，則這十分有用。

停用集中式組態︰

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```

重新啟用集中式組態︰

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py –enable
```

## Linux 效能計數器

Linux 效能計數器類似於 Windows 效能計數器 (兩者的運作方式類似)。您可以使用下列程序來加入和設定它們。將它們加入 OMS 之後，會每 30 秒收集一次資料。

### 在 OMS 中加入 Linux 效能計數器

1. 若要使用 OMS 入口網站設定 OMS Agent for Linux，您可以在 [設定] 頁面上加入 Linux 效能計數器，然後按一下 [資料]。![data](./media/log-analytics-linux-agents/oms-settings-data01.png)
2. 在 [設定] 頁面的 [資料] 下方，按一下 [Linux 效能計數器]，然後在加號圖示左邊，輸入您想要加入的計數器名稱。![Linux 效能計數器](./media/log-analytics-linux-agents/oms-linuxperfcounter01.png)
3. 如果您不知道計數器的完整名稱，可以開始輸入局部名稱，並會出現可用的計數器清單。當您找到想要加入的計數器時，請按一下清單中的名稱，然後按一下加號圖示加入計數器。
4. 計數器在加入之後會出現在以彩色列反白顯示的計數器清單中。
5. 預設會選取 [將下列組態套用至我的電腦] 選項。如果您想要停止傳送組態資料，請清除選取項目。
6. 當您完成效能計數器的修改時，請按一下頁面底部的 [儲存] 完成變更。您進行的組態變更接著會傳送給所有向 OMS 註冊的 OMS Agent for Linux，通常是在 5 分鐘內。

### 在 OMS 中設定 Linux 效能計數器

對於 Windows 效能計數器，您可以選擇每個效能計數器的特定執行個體。不過，對於 Linux 效能計數器，不管您選擇什麼計數器執行個體都會套用至父計數器的所有子計數器。下表顯示可用於 Linux 和 Windows 效能計數器的常見執行個體。

| **執行個體名稱** | **意義** |
| --- | --- |
| \_Total | 所有執行個體的總數 |
| * | 所有執行個體 |
| (/|/var) | 比對名為 / 或 /var 的執行個體 |


同樣地，您針對父計數器選擇的取樣間隔會套用至其所有子計數器。換句話說，所有子計數器取樣間隔和執行個體都會連結在一起。

### 使用 Linux 加入和設定效能計量

要收集的效能計量是由 /etc/opt/microsoft/omsagent/conf/omsagent.conf 中的組態所控制。如需 OMS Agent for Linux 的可用類別和計量，請參閱[可用的效能計量](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#appendix-available-performance-metrics)。

要收集之效能計量的每個物件或類別都應該當成單一 `<source>` 元素定義於組態檔中。語法遵循下面的模式。

```
<source>
  type oms_omi  
  object_name "Processor"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 30s
</source>

```

這個元素的可設定參數如下︰

- **Object\_name**︰集合的物件名稱。
- **Instance\_regex**：定義要收集之執行個體的「規則運算式」。值 `.*` 指定所有執行個體。若只要收集 \_Total 執行個體的處理器計量，您可以指定 `_Total`。若只要收集 crond 或 sshd 執行個體的程序計量，您可以指定：`(crond|sshd)`。
- **Counter\_name\_regex**：定義要收集之計數器 (針對物件) 的「規則運算式」。若要收集物件的所有計數器，請指定︰`.*`。若只要收集記憶體物件的交換空間計數器，您可以指定︰`.+Swap.+`
- **Interval:**︰物件計數器的收集頻率。

效能計量的預設組態是︰

```
<source>
  type oms_omi
  object_name "Physical Disk"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 5m
</source>

<source>
  type oms_omi
  object_name "Logical Disk"
  instance_regex ".*
  counter_name_regex ".*"
  interval 5m
</source>

<source>
  type oms_omi
  object_name "Processor"
  instance_regex ".*
  counter_name_regex ".*"
  interval 30s
</source>

<source>
  type oms_omi
  object_name "Memory"
  instance_regex ".*"
  counter_name_regex ".*"
  interval 30s
</source>

```

### 使用 Linux 命令啟用 MySQL 效能計數器

如果在安裝 omsagent 組合時於電腦上偵測到 MySQL 伺服器或 MariaDB 伺服器，則會自動安裝 MySQL 伺服器的效能監視提供者。此提供者會連接到本機 MySQL/MariaDB 伺服器，以公開效能統計資料。您需要設定 MySQL 使用者認證，讓提供者可以存取 MySQL 伺服器。

若要在 localhost 上定義 MySQL 伺服器的預設使用者帳戶，請使用下列命令範例。

>[AZURE.NOTE] omsagent 帳戶必須可讀取認證檔案。建議以 omsgent 身分執行 mycimprovauth 命令。


```
sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>'

sudo service omiserverd restart
```


或者，您可以在檔案中指定必要 MySQL 認證，方法是建立檔案：/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth。如需管理透過 mysql-auth 檔案進行監視之 MySQL 認證的詳細資訊，請參閱[在驗證檔案中管理 MySQL 監視認證](#manage-mysql-monitoring-credentials-in-the-authentication-file)。

如需 MySQL 使用者收集 MySQL 伺服器效能資料所需物件權限的詳細資料，請參閱 [MySQL 效能計數器所需的資料庫權限](#database-permissions-required-for-mysql-performance-counters)。

### 使用 Linux 命令啟用 Apache HTTP 伺服器效能計數器

如果在安裝 omsagent 組合時於電腦上偵測到 Apache HTTP 伺服器，則會自動安裝 Apache HTTP 伺服器的效能監視提供者。此提供者所依賴的 Apache「模組」必須載入至 Apache HTTP 伺服器，才能存取效能資料。

您可以使用下列命令載入模組：

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

若要卸載 Apache 監視模組，請執行下列命令︰

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```
### 使用 Log Analytics 檢視效能資料

1. 在 Operations Management Suite 入口網站中，按一下 [記錄檔搜尋] 磚。
2. 在搜尋列中，輸入 `* (Type=Perf)` 檢視所有效能計數器。


因為 OMS 也會收集 Windows 效能計數器資料，所以您應該將搜尋的範圍縮小為 Linux 特定資料。因此，下列範例會顯示名為 Chorizo21 的範例 Linux 伺服器特有的效能資料。

```
Type=Perf Computer=chorizo*
```

![搜尋結果中所顯示的範例伺服器](./media/log-analytics-linux-agents/oms-perfsearch01.png)

在結果中，您可以按一下 [計量] 檢視已收集其資料的計數器。每個計數器的即時資料會顯示為圖形。

![metrics](./media/log-analytics-linux-agents/oms-perfmetrics01.png)


## Syslog

Syslog 是與 Windows 事件記錄檔類似的事件記錄通訊協定 (在 OMS 中顯示時，兩者的運作方式類似)。

### 在 OMS 中加入新的 Linux syslog 設備

1. 在 [設定] 頁面的 [資料] 下方，按一下 [Syslog]，然後在加號圖示左邊，輸入您想要加入的 syslog 設備名稱。![Linux syslog](./media/log-analytics-linux-agents/oms-linuxsyslog01.png)
2.	如果您不知道設備的完整名稱，可以開始輸入局部名稱，並會出現可用的 syslog 設備清單。當您找到想要加入的 syslog 設備時，請按一下清單中的名稱，然後按一下加號圖示加入 syslog 設備。
3.	設備在加入之後會出現在以彩色列反白顯示的清單中。接下來，選擇您想要收集的嚴重性 (syslog 設備資訊的類別)。
4.	按一下頁面底部的 [儲存] 來完成您的變更。您進行的組態變更接著會傳送給所有向 OMS 註冊的 OMS Agent for Linux，通常是在 5 分鐘內。


### 在 Linux 中設定 Linux syslog 設備

Syslog 事件會從 syslog 精靈 (例如 rsyslog 或 syslog-ng) 傳送給代理程式正在接聽的本機連接埠。連接埠預設為 25224。安裝代理程式時，會套用預設 syslog 組態。這可以在下列項目中找到︰


Rsyslog：/etc/rsyslog.d/rsyslog-oms.conf

Syslog-ng：/etc/syslog-ng/syslog-ng.conf


預設 OMS 代理程式 syslog 組態會上傳來自所有設備的 syslog 事件，其嚴重性為警告或更高等級。

>[AZURE.NOTE] 如果您編輯 syslog 組態，則必須重新啟動 syslog 精靈，變更才會生效。

OMS 的 OMS Agent for Linux 的預設 syslog 組態如下：

#### Rsyslog

```
kern.warning       @127.0.0.1:25224
user.warning       @127.0.0.1:25224
daemon.warning     @127.0.0.1:25224
auth.warning       @127.0.0.1:25224
syslog.warning     @127.0.0.1:25224
uucp.warning       @127.0.0.1:25224
authpriv.warning   @127.0.0.1:25224
ftp.warning        @127.0.0.1:25224
cron.warning       @127.0.0.1:25224
local0.warning     @127.0.0.1:25224
local1.warning     @127.0.0.1:25224
local2.warning     @127.0.0.1:25224
local3.warning     @127.0.0.1:25224
local4.warning     @127.0.0.1:25224
local5.warning     @127.0.0.1:25224
local6.warning     @127.0.0.1:25224
local7.warning     @127.0.0.1:25224
```

#### Syslog-ng

```
#OMS_facility = all
filter f_warning_oms { level(warning); };
destination warning_oms { tcp("127.0.0.1" port(25224)); };
log { source(src); filter(f_warning_oms); destination(warning_oms); };
```

### 使用 Log Analytics 檢視所有 Syslog 事件

1. 在 Operations Management Suite 入口網站中，按一下 [記錄檔搜尋] 磚。
2. 在 [記錄檔管理] 分組中，選擇預先定義的 syslog 搜尋，然後選取其中一個來執行它。

此範例示範所有 Syslog 事件。

![記錄檔搜尋中所顯示的 Syslog 事件](./media/log-analytics-linux-agents/oms-linux-syslog.png)

現在，您可以切入至搜尋結果。

## Linux 警示

如果您使用 Nagios 或 Zabbix 管理 Linux 電腦，則 OMS 可以接收這些工具所產生的警示。不過，目前沒有任何方法可以使用 OMS 入口網站來設定傳入警示資料。相反地，您需要編輯組態檔，才能開始將警示傳送給 OMS。



### 收集來自 Nagios 的警示

若要收集來自 Nagios 伺服器的警示，您需要進行下列組態變更。

1. 將 Nagios 記錄檔 (即 /var/log/nagios/nagios.log/var/log/nagios/nagios.log) 的 **omsagent** 讀取權授與使用者。假設 nagios.log 檔案是由 **nagios** 群組所擁有，您可以將使用者 **omsagent** 加入 **nagios** 群組。

    ```
    sudo usermod –a -G nagios omsagent
    ```

2. 修改 omsagent.confconfiguration 檔案 (/etc/opt/microsoft/omsagent/conf/omsagent.conf)。確定下列項目存在且未標成註解︰

    ```
    <source>
    type tail
    #Update path to point to your nagios.log
    path /var/log/nagios/nagios.log
    format none
    tag oms.nagios
    </source>

    <filter oms.nagios>
    type filter_nagios_log
    </filter>
    ```

3. 重新啟動 omsagent 精靈：

    ```
    sudo service omsagent restart
    ```

### 收集來自 Zabbix 的警示

若要收集來自 Zabbix 伺服器的警示，您將執行與上面 Nagios 警示類似的步驟，除了您需要以「純文字」指定使用者和密碼之外。這不是理想的做法，可能很快就會變更。若要解決這個問題，建議您建立使用者，而且只將監視權限授與它。

Zabbix 之 omsagent.conf 組態檔 (/etc/opt/microsoft/omsagent/conf/omsagent.conf) 的範例區段應該與下面類似：

```
<source>
  type zabbix_alerts
  run_interval 1m
  tag oms.zabbix
  zabbix_url http://localhost/zabbix/api_jsonrpc.php
  zabbix_username Admin
  zabbix_password zabbix
</source>

```

### 在 Log Analytics 搜尋中檢視警示

設定您的 Linux 電腦以將警示傳送給 OMS 之後，即可使用幾個簡單的記錄檔搜尋查詢來檢視警示。下列搜尋查詢範例會傳回已產生的所有記錄的警示。例如，如果 IT 基礎結構發生某種問題，則下列範例查詢的結果可能會表示問題的起源。而且，您可以輕鬆地切入來源系統的警示，協助縮小調查範圍。優點是您不一定需要一開始就移至各種管理系統，但如果您的警示是傳送給 OMS，則可以從該處開始。

```
Type=Alert
```

#### 使用 Log Analytics 檢視所有 Nagios 警示
1. 在 Operations Management Suite 入口網站中，按一下 [記錄檔搜尋] 磚。
2. 在查詢列中，輸入下列搜尋查詢：

    ```
    Type=Alert SourceSystem=Nagios
    ```
![記錄檔搜尋中所顯示的 Nagios 警示](./media/log-analytics-linux-agents/oms-linux-nagios-alerts.png)

看到搜尋結果之後，即可切入其他詳細資料 (例如 *AlertState*)。

### 使用 Log Analytics 檢視所有 Zabbix 警示
1. 在 Operations Management Suite 入口網站中，按一下 [記錄檔搜尋] 磚。
2. 在查詢列中，輸入下列搜尋查詢：

    ```
    Type=Alert SourceSystem=Zabbix
    ```
![記錄檔搜尋中所顯示的 Zabbix 警示](./media/log-analytics-linux-agents/oms-linux-zabbix-alerts.png)

看到搜尋結果之後，即可切入其他詳細資料 (例如 *AlertName*)。


## 與 System Center Operations Manager 的相容性

OMS Agent for Linux 會與 System Center Operations Manager 代理程式共用代理程式二進位檔。在 Operations Manager 目前所管理的系統上安裝 OMS Agent for Linux，將電腦上的 OMI 和 SCX 封裝升級到較新版本。OMS Agent for Linux 和 System Center 2012 R2 相容。不過，**System Center 2012 SP1 和舊版本目前與 OMS Agent for Linux 不相容或不受其支援。**

>[AZURE.NOTE] 如果 OMS Agent for Linux 安裝到 Operations Manager 目前未管理的電腦，而且您之後想要使用 Operations Manager 管理電腦，則必須先修改 OMI 組態，再探索到電腦。**如果先安裝 Operations Manager 代理程式，再安裝 OMS Agent for Linux，則不需要這個步驟。**

### 啟用 OMS Agent for Linux 以與 Operations Manager 通訊

1. 編輯 /etc/opt/omi/conf/omiserver.conf 檔案
2. 確認開頭為 **httpsport=** 的行定義連接埠 1270。例如 `httpsport=1270`
3. 重新啟動 OMI 伺服器：

    ```
    service omiserver restart or systemctl restart omiserver
    ```




## MySQL 效能計數器所需的資料庫權限

若要將權限授與 MySQL 監視使用者，則授與使用者必須具有 'GRANT option' 權限以及正在授與的權限。

為了讓 MySQL 使用者傳回效能資料，使用者需要存取下列查詢︰

```
SHOW GLOBAL STATUS;
SHOW GLOBAL VARIABLES:
```

除了這些查詢之外，MySQL 使用者需要下列預設資料表的 SELECT 存取權︰

- information\_schema
- mysql

執行下列 grant 命令，可以授與這些權限。

```
GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;
```

## 在驗證檔案中管理 MySQL 監視認證

下列各節可協助您管理 MySQL 認證。

### 設定 MySQL OMI 提供者

MySQL OMI 提供者需要預先設定的 MySQL 使用者，並已安裝 MySQL 用戶端程式庫，才能從 MySQL 執行個體查詢效能/健全狀況資訊。

### MySQL OMI 驗證檔案

MySQL OMI 提供者使用驗證檔案，判斷 MySQL 執行個體所接聽的繫結位址和連接埠，以及要用來收集計量的認證。安裝期間，MySQL OMI 提供者將掃描繫結位址和連接埠的 MySQL my.cnf 組態檔 (預設位置)，並局部設定 MySQL OMI 驗證檔案。

若要完成 MySQL 伺服器執行個體的監視，請將預先產生的 MySQL OMI 驗證檔案加入正確的目錄。

### 驗證檔案格式

MySQL OMI 驗證檔案是包含下列相關資訊的文字檔︰

- 連接埠
- 繫結位址
- MySQL 使用者名稱
- Base64 編碼的密碼

MySQL OMI 驗證檔案只會授與產生它之 Linux 使用者的讀取/寫入權限。

```
[Port]=[Bind-Address], [username], [Base64 encoded Password]
(Port)=(Bind-Address), (username), (Base64 encoded Password)
(Port)=(Bind-Address), (username), (Base64 encoded Password)
AutoUpdate=[true|false]
```

預設 MySQL OMI 驗證檔案包含預設執行個體和連接埠號碼 (根據可用的資訊，以及從找到的 MySQL 組態檔進行剖析的資訊)。

預設執行個體是讓管理一部 Linux 主機上多個 MySQL 執行個體更為容易的方法，並且以連接埠為 0 的執行個體表示。所有加入的執行個體都會繼承透過預設執行個體所設定的屬性。例如，如果加入接聽通訊埠 '3308' 的 MySQL 執行個體，預設執行個體的繫結位址、使用者名稱和 Base64 編碼的密碼將會用來嘗試和監視接聽 3308 的執行個體。如果 3308 上的執行個體繫結至另一個位址，並使用相同的 MySQL 使用者名稱和密碼配對，則只需要重新指定繫結位址，就會繼承其他屬性。

驗證檔案的範例與下面類似。

預設執行個體與連接埠為 3308 的執行個體：

```
0=127.0.0.1, myuser, cnBwdA==3308=, ,AutoUpdate=true
```

預設執行個體與連接埠為 3308 的執行個體 + 不同 Base64 編碼的密碼：

```
0=127.0.0.1, myuser, cnBwdA==3308=127.0.1.1, , AutoUpdate=true
```


| **屬性** | **說明** |
| --- | --- |
| 連接埠 | 連接埠代表 MySQL 執行個體目前正在接聽的連接埠。連接埠 0 表示後面的屬性用於預設執行個體。 |
| 繫結位址 | 繫結位址是目前 MySQL 繫結位址 |
| username | 這是您想要用來監視 MySQL 伺服器執行個體之 MySQL 使用者的使用者名稱。 |
| Base64 編碼的密碼 | 這是以 Base64 編碼之 MySQL 監視使用者的密碼。 |
| AutoUpdate | 升級 MySQL OMI 提供者時，提供者將重新掃描 my.cnf 檔案中的變更，並覆寫 MySQL OMI 驗證檔案。將此旗標設定為 true 或 false (視 MySQL OMI 驗證檔案的必要更新而定)。 |

#### 驗證檔案位置

MySQL OMI 驗證檔案應該位於下列位置，並命名為 "mysql-auth"：

/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth

omsagent 使用者應該會擁有此檔案 (和 auth/omsagent 目錄)。

## 代理程式記錄檔

OMS Agent for Linux 記錄檔位於：

/var/opt/microsoft/omsagent/log/

omsconfig (代理程式組態) 程式的 OMS Agent for Linux 記錄檔位於：

/var/opt/microsoft/omsconfig/log/

OMI 和 SCX 元件的記錄檔 (其提供效能計量資料) 位於：

/var/opt/omi/log/ and /var/opt/microsoft/scx/log

## 已知限制
請檢閱下列各節來了解 OMS Agent for Linux 的目前限制。

### Azure 診斷

對於在 Azure 中執行的 Linux 虛擬機器，可能需要額外的步驟，才能允許 Azure Diagnostics 和 Operations Management Suite 的資料收集。需要有**版本 2.2** 的 Diagnostics Extension for Linux，才能與 OMS Agent for Linux 相容。

如需安裝和設定 Diagnostic Extension for Linux 的詳細資訊，請參閱[使用 Azure CLI 命令啟用 Linux Diagnostic Extension](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension/#use-the-azure-cli-command-to-enable-linux-diagnostic-extension)。

**將 Linux Diagnostics Extension 從 2.0 升級到 2.2 Azure CLI ASM：**

```
azure vm extension set -u <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions 2.0
azure vm extension set <vm_name> LinuxDiagnostic Microsoft.OSTCExtensions 2.2 --private-config-path PrivateConfig.json
```

**ARM**

```
azure vm extension set -u <resource-group> <vm-name> Microsoft.Insights.VMDiagnosticsSettings Microsoft.OSTCExtensions 2.0
azure vm extension set <resource-group> <vm-name> LinuxDiagnostic Microsoft.OSTCExtensions 2.2 --private-config-path PrivateConfig.json
```

這些命令範例參考名為 PrivateConfig.json 的檔案。該檔案的格式應該與下列範例類似。

```
	{
    "storageAccountName":"the storage account to receive data",
    "storageAccountKey":"the key of the account"
	}
```

### 不支援 Sysklog

需要有 rsyslog 或 syslog-ng，才能收集 syslog 訊息。Red Hat Enterprise Linux 第 5 版、CentOS 和 Oracle Linux 版本 (sysklog) 不支援預設 syslog 精靈，進行 syslog 事件收集。若要從此版的這些散發套件收集 syslog 資料，rsyslog 精靈應該安裝和設定為取代 sysklog。如需將 sysklog 取代為 rsyslog 的詳細資訊，請參閱[安裝新建置的 rsyslog RPM](http://wiki.rsyslog.com/index.php/Rsyslog_on_CentOS_success_story#Install_the_newly_built_rsyslog_RPM)。

## 後續步驟

- [從方案庫加入 Log Analytics 方案](log-analytics-add-solutions.md)，以加入功能和收集資料。
- 熟悉[記錄檔搜尋](log-analytics-log-searches.md)以檢視方案所收集的詳細資訊。
- 使用[儀表板](log-analytics-dashboards.md)以儲存和顯示您自己的自訂搜尋。

<!---HONumber=AcomDC_0504_2016-->