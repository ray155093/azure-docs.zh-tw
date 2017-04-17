---
title: "將 Linux 電腦連線到 Azure Log Analytics | Microsoft Docs"
description: "使用 Log Analytics，您可以收集和處理從 Linux 電腦所產生的資料。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: ab5b76d8-9ab5-406e-8768-76fb0632d830
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: b01b0d3d61168c1eec52f3fd040b829e0c51a878
ms.lasthandoff: 03/30/2017


---
# <a name="connect-your-linux-computers-to-log-analytics"></a>將 Linux 電腦連線到 Log Analytics
使用 Log Analytics，您可以收集和處理從 Linux 電腦所產生的資料。 將從 Linux 收集的資料加入 OMS 中，可讓您管理 Linux 系統和容器解決方案 (如 Docker)，無論您的電腦位於何處 (含虛擬的任何位置)。 資料來源可能位於內部部署資料中心內，以做為實體伺服器、雲端託管服務 (如 Amazon Web Services (AWS) 或 Microsoft Azure) 中的虛擬電腦，甚至桌上的膝上型電腦。 此外，OMS 同樣也會收集來自 Windows 電腦的資料，因此支援真正混合式 IT 環境。

您可以使用單一管理入口網站，來檢視和管理來自具有 OMS 中 Log Analytics 之所有來源的資料。 這會降低使用許多不同系統監視它的需求、讓您輕鬆地使用它，而且您可以將喜歡的任何資料匯出至任何您已經有的商務分析解決方案或系統。

這篇文章是快速入門指南，可協助您使用 OMS Agent for Linux 來收集和管理 Linux 電腦的資料。 如需其他技術詳細資訊 (例如 Proxy 伺服器組態、CollectD 計量相關資訊，以及自訂 JSON 資料來源)，您可在 GitHub 上的 [OMS Agent for Linux 概觀 (英文)](https://github.com/Microsoft/OMS-Agent-for-Linux) 和 [OMS Agent for Linux 完整文件 (英文)](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md) 中找到該資訊。

目前，您可以從 Linux 電腦收集下列類型的資料︰

* 效能度量
* Syslog 事件
* 來自 Nagios 和 Zabbix 的警示
* Docker 容器效能計量、清查和記錄檔

## <a name="supported-linux-versions"></a>支援的 Linux 版本
各種 Linux 散發套件正式支援 x86 和 x64 版本。 不過，OMS Agent for Linux 也可能在未列出的其他散發套件上執行。

* Amazon Linux 2012.09 到 2015.09
* CentOS Linux 5、6 和 7
* Oracle Linux 5、6 和 7
* Red Hat Enterprise Linux Server 5、6 和 7
* Debian GNU/Linux 6、7 和 8
* Ubuntu 12.04 LTS、14.04 LTS、15.04、15.10
* SUSE Linux Enterprise Server 11 和 12

## <a name="oms-agent-for-linux"></a>OMS Agent for Linux
Operations Management Suite Agent for Linux 包含多個封裝。 發行檔案包含下列封裝 (搭配執行殼層組合與 `--extract`即可取得)。

| **Package** | **版本** | **說明** |
| --- | --- | --- |
| omsagent |1.1.0 |Operations Management Suite Agent for Linux |
| omsconfig |1.1.1 |OMS 代理程式的組態代理程式 |
| omi |1.0.8.3 |開放式管理基礎結構 (OMI) -- 輕量型 CIM 伺服器 |
| scx |1.6.2 |作業系統效能計量的 OMI CIM 提供者 |
| apache-cimprov |1.0.0 |OMI 的 Apache HTTP 伺服器效能監視提供者。 僅在偵測到 Apache HTTP 伺服器時才安裝。 |
| mysql-cimprov |1.0.0 |OMI 的 MySQL 伺服器效能監視提供者。 僅在偵測到 MySQL/MariaDB 伺服器時才安裝。 |
| docker-cimprov |0.1.0 |OMI 的 Docker 提供者。 僅在偵測到 Docker 時才安裝。 |

### <a name="additional-installation-artifacts"></a>其他安裝構件
安裝 OMS Agent for Linux 封裝之後，會套用下列額外的全系統組態變更。 解除安裝 omsagent 封裝時，會移除這些構件。

* 會建立名為 `omsagent` 的非特殊權限使用者。 這是 omsagent 精靈所執行的帳戶
* sudoers “include” 檔案建立於 /etc/sudoers.d/omsagent。這會授權 omsagent 重新啟動 syslog 和 omsagent 精靈。 如果已安裝的 sudo 版本不支援 sudo “include” 指示詞，這些項目將寫入 /etc/sudoers。
* syslog 組態修改成將事件子集轉送給代理程式。 如需詳細資訊，請參閱下面 **設定資料收集** 一節。

### <a name="linux-data-collection-details"></a>Linux 資料收集詳細資料
下表顯示資料收集方法和其他資料收集方式的詳細資料。

| 來源 | 直接代理程式 | SCOM 代理程式 | Azure 儲存體 | SCOM 是否為必要項目？ | 透過管理群組傳送的 SCOM 代理程式資料 | 收集頻率 |
| --- | --- | --- | --- | --- | --- | --- |
| Zabbix |![是](./media/log-analytics-linux-agents/oms-bullet-green.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |1 分鐘 |
| Nagios |![是](./media/log-analytics-linux-agents/oms-bullet-green.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |與抵達同時 |
| syslog |![是](./media/log-analytics-linux-agents/oms-bullet-green.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |從 Azure 儲存體：10 分鐘；從代理程式：與抵達同時 |
| Linux 效能計數器 |![是](./media/log-analytics-linux-agents/oms-bullet-green.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |依排程，最少 10 秒 |
| 變更追蹤 |![是](./media/log-analytics-linux-agents/oms-bullet-green.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |![否](./media/log-analytics-linux-agents/oms-bullet-red.png) |每小時 |

### <a name="package-requirements"></a>封裝需求
| **必要封裝** | **說明** | **最低版本** |
| --- | --- | --- |
| Glibc |GNU C 程式庫 |2.5-12 |
| Openssl |OpenSSL 程式庫 |0.9.8e 或 1.0 |
| Curl |cURL Web 用戶端 |7.15.5 |
| Python-ctypes |函數程式庫 |n/a |
| PAM |插入式驗證模組 |n/a |

> [!NOTE]
> 需要有 rsyslog 或 syslog-ng，才能收集 syslog 訊息。 Red Hat Enterprise Linux 第 5 版、CentOS 和 Oracle Linux 版本 (sysklog) 不支援預設 syslog 精靈，進行 syslog 事件收集。 若要從此版的這些散發套件收集 syslog 資料，rsyslog 精靈應該安裝和設定為取代 sysklog。
>
>

## <a name="quick-install"></a>快速安裝
執行下列命令來下載 omsagent、驗證總和檢查碼，然後安裝代理程式並將之登入。 命令是針對 64 位元。 工作區識別碼和主要金鑰位於 [連接的來源] 索引標籤上 [設定] 下的 OMS 入口網站。

![工作區詳細資料](./media/log-analytics-linux-agents/oms-direct-agent-primary-key.png)

```
wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR OMS WORKSPACE ID> -s <YOUR OMS WORKSPACE PRIMARY KEY>
```

有各種其他方法可安裝代理程式，並將它升級。 您可以在 [OMS Agent for Linux 安裝步驟](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#steps-to-install-the-oms-agent-for-linux)深入閱讀它們。

您也可以檢視 [Azure 影片逐步解說](https://www.youtube.com/watch?v=mF1wtHPEzT0)。

## <a name="choose-your-linux-data-collection-method"></a>選擇 Linux 資料收集方法
如何選擇您想要收集的資料類型，取決於您是否要使用 OMS 入口網站，或者是否想要直接編輯 Linux 用戶端上的各種組態檔。 如果您選擇使用入口網站，會自動將組態傳送給所有 Linux 用戶端。 如果不同的 Linux 用戶端需要不同的組態，則需要個別編輯用戶端檔案，或使用替代方法 (如 PowerShell DSC、Chef 或 Puppet)。

您可以指定您想要使用 Linux 電腦上組態檔所收集的 syslog 事件和效能計數器。 *如果您選擇編輯代理程式組態檔來設定資料收集，則應該停用集中式組態。*  提供下面的指示，以在代理程式的組態檔中設定資料收集，以及停用所有 OMS Agent for Linux 或個別電腦的集中式組態。

### <a name="disable-oms-management-for-an-individual-linux-computer"></a>停用個別 Linux 電腦的 OMS 管理
使用 OMS_MetaConfigHelper.py 指令碼，針對個別 Linux 電腦停用組態資料的集中式資料收集。 如果有一部分的電腦應該有特製化組態，則這十分有用。

停用集中式組態︰

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```

重新啟用集中式組態︰

```
sudo /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py –enable
```

## <a name="linux-performance-counters"></a>Linux 效能計數器
Linux 效能計數器類似於 Windows 效能計數器 (兩者的運作方式類似)。 您可以使用下列程序來加入和設定它們。 將它們加入 OMS 之後，會每 30 秒收集一次資料。

### <a name="to-add-a-linux-performance-counter-in-oms"></a>在 OMS 中加入 Linux 效能計數器
1. 若要使用 OMS 入口網站設定 OMS Agent for Linux，您可以在 [設定] 頁面上加入 Linux 效能計數器，然後按一下 [資料] 。  
2. 在 [設定] 頁面的 [資料] 下方，按一下 [Linux 效能計數器]，然後選取或輸入您想要新增的計數器名稱。  
    ![資料](./media/log-analytics-linux-agents/oms-settings-data01.png)
3. 如果您不知道計數器的完整名稱，可以開始輸入局部名稱，並會出現可用的計數器清單。 當您找到想要加入的計數器時，請按一下清單中的名稱，然後按一下加號圖示加入計數器。
4. 計數器在加入之後會出現在以彩色列反白顯示的計數器清單中。
5. 預設會選取 [將下列組態套用至我的電腦]  選項。 如果您想要停止傳送組態資料，請清除選取項目。
6. 當您完成效能計數器的修改時，請按一下頁面底部的 [儲存]  完成變更。 您進行的組態變更接著會傳送給所有向 OMS 註冊的 OMS Agent for Linux，通常是在 5 分鐘內。

### <a name="configure-linux-performance-counters-in-oms"></a>在 OMS 中設定 Linux 效能計數器
對於 Windows 效能計數器，您可以選擇每個效能計數器的特定執行個體。 不過，對於 Linux 效能計數器，不管您選擇什麼計數器執行個體都會套用至父計數器的所有子計數器。 下表顯示可用於 Linux 和 Windows 效能計數器的常見執行個體。

| **執行個體名稱** | **意義** |
| --- | --- |
| \_總計 |所有執行個體的總數 |
| \* |所有執行個體 |
| (/&#124;/var) |比對名為 / 或 /var 的執行個體 |

同樣地，您針對父計數器選擇的取樣間隔會套用至其所有子計數器。 換句話說，所有子計數器取樣間隔和執行個體都會連結在一起。

### <a name="add-and-configure-performance-metrics-with-linux"></a>使用 Linux 加入和設定效能計量
要收集的效能計量是由 /etc/opt/microsoft/omsagent/&lt;工作區識別碼&gt;/conf/omsagent.conf 中的組態所控制。 如需 OMS Agent for Linux 的可用類別和計量，請參閱 [可用的效能計量](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#appendix-available-performance-metrics) 。

要收集之效能計量的每個物件或類別都應該當成單一 `<source>` 元素定義於組態檔中。 語法遵循下面的模式。

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

* **Object\_name**︰集合的物件名稱。
* **Instance\_regex**：定義要收集之執行個體的「規則運算式」。 值 `.*` 指定所有執行個體。 若只要收集 \_Total 執行個體的處理器計量，您可以指定 `_Total`。 若只要收集 crond 或 sshd 執行個體的程序計量，您可以指定： `(crond|sshd)`。
* **Counter\_name\_regex**：定義要收集之計數器 (針對物件) 的「規則運算式」。 若要收集物件的所有計數器，請指定︰ `.*`。 若只要收集記憶體物件的交換空間計數器，您可以指定︰ `.+Swap.+`
* **Interval:**︰物件計數器的收集頻率。

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

### <a name="enable-mysql-performance-counters-using-linux-commands"></a>使用 Linux 命令啟用 MySQL 效能計數器
如果在安裝 omsagent 組合時於電腦上偵測到 MySQL 伺服器或 MariaDB 伺服器，則會自動安裝 MySQL 伺服器的效能監視提供者。 此提供者會連接到本機 MySQL/MariaDB 伺服器，以公開效能統計資料。 您需要設定 MySQL 使用者認證，讓提供者可以存取 MySQL 伺服器。

若要在 localhost 上定義 MySQL 伺服器的預設使用者帳戶，請使用下列命令範例。

> [!NOTE]
> omsagent 帳戶必須可讀取認證檔案。 建議以 omsgent 身分執行 mycimprovauth 命令。
>
>

```
sudo su omsagent -c '/opt/microsoft/mysql-cimprov/bin/mycimprovauth default 127.0.0.1 <username> <password>

sudo /opt/omi/bin/service_control restart
```


或者，您可以在檔案中指定必要 MySQL 認證，方法是建立檔案：/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth。 如需管理透過 mysql-auth 檔案進行監視之 MySQL 認證的詳細資訊，請參閱 [在驗證檔案中管理 MySQL 監視認證](#manage-mysql-monitoring-credentials-in-the-authentication-file)。

如需 MySQL 使用者收集 MySQL 伺服器效能資料所需物件權限的詳細資料，請參閱 [MySQL 效能計數器所需的資料庫權限](#database-permissions-required-for-mysql-performance-counters) 。

### <a name="enable-apache-http-server-performance-counters-using-linux-commands"></a>使用 Linux 命令啟用 Apache HTTP 伺服器效能計數器
如果在安裝 omsagent 組合時於電腦上偵測到 Apache HTTP 伺服器，則會自動安裝 Apache HTTP 伺服器的效能監視提供者。 此提供者所依賴的 Apache「模組」必須載入至 Apache HTTP 伺服器，才能存取效能資料。

您可以使用下列命令載入模組：

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -c
```

若要卸載 Apache 監視模組，請執行下列命令︰

```
sudo /opt/microsoft/apache-cimprov/bin/apache_config.sh -u
```
### <a name="to-view-performance-data-with-log-analytics"></a>使用 Log Analytics 檢視效能資料
1. 在 Operations Management Suite 入口網站中，按一下 [記錄檔搜尋] 磚。
2. 在搜尋列中，輸入 `* (Type=Perf)` 檢視所有效能計數器。

因為 OMS 也會收集 Windows 效能計數器資料，所以您應該將搜尋的範圍縮小為 Linux 特定資料。 因此，下列範例會顯示名為 Chorizo21 的範例 Linux 伺服器特有的效能資料。

```
Type=Perf Computer=chorizo*
```

![搜尋結果中所顯示的範例伺服器](./media/log-analytics-linux-agents/oms-perfsearch01.png)

在結果中，您可以按一下 [計量]  檢視已收集其資料的計數器。 每個計數器的即時資料會顯示為圖形。

![metrics](./media/log-analytics-linux-agents/oms-perfmetrics01.png)

## <a name="syslog"></a>syslog
Syslog 是與 Windows 事件記錄檔類似的事件記錄通訊協定 (在 OMS 中顯示時，兩者的運作方式類似)。

### <a name="to-add-a-new-linux-syslog-facility-in-oms"></a>在 OMS 中加入新的 Linux syslog 設備
1. 在 **[設定]** 頁面的 **[資料]**下方，按一下**[Syslog]** ，然後在加號圖示左邊，輸入您想要新增的 syslog 設備名稱。
    ![Linux syslog](./media/log-analytics-linux-agents/oms-linuxsyslog01.png)
2. 如果您不知道設備的完整名稱，可以開始輸入局部名稱，並會出現可用的 syslog 設備清單。 當您找到想要加入的 syslog 設備時，請按一下清單中的名稱，然後按一下加號圖示加入 syslog 設備。
3. 設備在加入之後會出現在以彩色列反白顯示的清單中。 接下來，選擇您想要收集的嚴重性 (syslog 設備資訊的類別)。
4. 按一下頁面底部的 [儲存]  來完成您的變更。 您進行的組態變更接著會傳送給所有向 OMS 註冊的 OMS Agent for Linux，通常是在 5 分鐘內。

### <a name="configure-linux-syslog-facilities-in-linux"></a>在 Linux 中設定 Linux syslog 設備
Syslog 事件會從 syslog 精靈 (例如 rsyslog 或 syslog-ng) 傳送給代理程式正在接聽的本機連接埠。 連接埠預設為 25224。 安裝代理程式時，會套用預設 syslog 組態。 這可以在下列項目中找到︰

Rsyslog：/etc/rsyslog.d/rsyslog-oms.conf

Syslog-ng：/etc/syslog-ng/syslog-ng.conf

預設 OMS 代理程式 syslog 組態會上傳來自所有設備的 syslog 事件，其嚴重性為警告或更高等級。

> [!NOTE]
> 如果您編輯 syslog 組態，則必須重新啟動 syslog 精靈，變更才會生效。
>
>

OMS 的 OMS Agent for Linux 的預設 syslog 組態如下：

#### <a name="rsyslog"></a>Rsyslog
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

#### <a name="syslog-ng"></a>Syslog-ng
```
#OMS_facility = all
filter f_warning_oms { level(warning); };
destination warning_oms { tcp("127.0.0.1" port(25224)); };
log { source(src); filter(f_warning_oms); destination(warning_oms); };
```

### <a name="to-view-all-syslog-events-with-log-analytics"></a>使用 Log Analytics 檢視所有 Syslog 事件
1. 在 Operations Management Suite 入口網站中，按一下 [記錄檔搜尋]  磚。
2. 在 [記錄檔管理]  分組中，選擇預先定義的 syslog 搜尋，然後選取其中一個來執行它。

此範例示範所有 Syslog 事件。

![記錄檔搜尋中所顯示的 Syslog 事件](./media/log-analytics-linux-agents/oms-linux-syslog.png)

現在，您可以切入至搜尋結果。

## <a name="linux-alerts"></a>Linux 警示
如果您使用 Nagios 或 Zabbix 管理 Linux 電腦，則 OMS 可以接收這些工具所產生的警示。 不過，目前沒有任何方法可以使用 OMS 入口網站來設定傳入警示資料。 相反地，您需要編輯組態檔，才能開始將警示傳送給 OMS。

### <a name="collect-alerts-from-nagios"></a>收集來自 Nagios 的警示
若要收集來自 Nagios 伺服器的警示，您需要進行下列組態變更。

1. 將 Nagios 記錄檔 (即 /var/log/nagios/nagios.log) 的 **omsagent** 讀取權授與使用者。 假設 nagios.log 檔案是由 **nagios** 群組所擁有，您可以將使用者 **omsagent** 新增至 **nagios** 群組。

    ```
    sudo usermod –a -G nagios omsagent
    ```
2. 修改 omsagent.confconfiguration 檔案 (/etc/opt/microsoft/omsagent/&lt;工作區識別碼&gt;/conf/omsagent.conf)。 確定下列項目存在且未標成註解︰

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
    sudo /opt/microsoft/omsagent/bin/service_control restart
    ```

### <a name="collect-alerts-from-zabbix"></a>收集來自 Zabbix 的警示
若要收集來自 Zabbix 伺服器的警示，您將執行與上面 Nagios 警示類似的步驟，除了您需要以「純文字」 指定使用者和密碼之外。 這不是理想的做法，可能很快就會變更。 若要解決這個問題，建議您建立使用者，而且只將監視權限授與它。

Zabbix 之 omsagent.conf 組態檔 (/etc/opt/microsoft/omsagent/&lt;工作區識別碼&gt;/conf/omsagent.conf) 的範例區段應該與下面類似：

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

### <a name="view-alerts-in-log-analytics-search"></a>在 Log Analytics 搜尋中檢視警示
設定您的 Linux 電腦以將警示傳送給 OMS 之後，即可使用幾個簡單的記錄檔搜尋查詢來檢視警示。 下列搜尋查詢範例會傳回已產生的所有記錄的警示。 例如，如果 IT 基礎結構發生某種問題，則下列範例查詢的結果可能會表示問題的起源。 而且，您可以輕鬆地切入來源系統的警示，協助縮小調查範圍。 優點是您不一定需要一開始就移至各種管理系統，但如果您的警示是傳送給 OMS，則可以從該處開始。

```
Type=Alert
```

#### <a name="to-view-all-nagios-alerts-with-log-analytics"></a>使用 Log Analytics 檢視所有 Nagios 警示
1. 在 Operations Management Suite 入口網站中，按一下 [記錄檔搜尋]  磚。
2. 在查詢列中，輸入下列搜尋查詢：

    ```
    Type=Alert SourceSystem=Nagios
    ```
   ![記錄檔搜尋中所顯示的 Nagios 警示](./media/log-analytics-linux-agents/oms-linux-nagios-alerts.png)

看到搜尋結果之後，即可切入其他詳細資料 (例如 *AlertState*)。

### <a name="to-view-all-zabbix-alerts-with-log-analytics"></a>使用 Log Analytics 檢視所有 Zabbix 警示
1. 在 Operations Management Suite 入口網站中，按一下 [記錄檔搜尋]  磚。
2. 在查詢列中，輸入下列搜尋查詢：

    ```
    Type=Alert SourceSystem=Zabbix
    ```
   ![記錄檔搜尋中所顯示的 Zabbix 警示](./media/log-analytics-linux-agents/oms-linux-zabbix-alerts.png)

看到搜尋結果之後，即可切入其他詳細資料 (例如 *AlertName*)。

## <a name="compatibility-with-system-center-operations-manager"></a>與 System Center Operations Manager 的相容性
OMS Agent for Linux 會與 System Center Operations Manager 代理程式共用代理程式二進位檔。 在 Operations Manager 目前所管理的系統上安裝 OMS Agent for Linux，將電腦上的 OMI 和 SCX 封裝升級到較新版本。 OMS Agent for Linux 和 System Center 2012 R2 相容。 不過， **System Center 2012 SP1 和舊版本目前與 OMS Agent for Linux 不相容或不受其支援。**

> [!NOTE]
> 如果 OMS Agent for Linux 安裝到 Operations Manager 目前未管理的電腦，而且您之後想要使用 Operations Manager 管理電腦，則必須先修改 OMI 組態，再探索到電腦。 **如果先安裝 Operations Manager 代理程式，再安裝 OMS Agent for Linux，則不需要這個步驟。**
>
>

### <a name="to-enable-the-oms-agent-for-linux-to-communicate-with-operations-manager"></a>啟用 OMS Agent for Linux 以與 Operations Manager 通訊
1. 編輯 /etc/opt/omi/conf/omiserver.conf 檔案
2. 確認開頭為 **httpsport=** 的行定義連接埠 1270。 例如 `httpsport=1270`
3. 重新啟動 OMI 伺服器：

    ```
    sudo /opt/omi/bin/service_control restart
    ```

## <a name="database-permissions-required-for-mysql-performance-counters"></a>MySQL 效能計數器所需的資料庫權限
若要將權限授與 MySQL 監視使用者，則授與使用者必須具有 'GRANT option' 權限以及正在授與的權限。

為了讓 MySQL 使用者傳回效能資料，使用者需要存取下列查詢︰

```
SHOW GLOBAL STATUS;
SHOW GLOBAL VARIABLES:
```

除了這些查詢之外，MySQL 使用者需要下列預設資料表的 SELECT 存取權︰

* information_schema
* mysql

執行下列 grant 命令，可以授與這些權限。

```
GRANT SELECT ON information_schema.* TO ‘monuser’@’localhost’;
GRANT SELECT ON mysql.* TO ‘monuser’@’localhost’;
```

## <a name="manage-mysql-monitoring-credentials-in-the-authentication-file"></a>在驗證檔案中管理 MySQL 監視認證
下列各節可協助您管理 MySQL 認證。

### <a name="configure-the-mysql-omi-provider"></a>設定 MySQL OMI 提供者
MySQL OMI 提供者需要預先設定的 MySQL 使用者，並已安裝 MySQL 用戶端程式庫，才能從 MySQL 執行個體查詢效能/健全狀況資訊。

### <a name="mysql-omi-authentication-file"></a>MySQL OMI 驗證檔案
MySQL OMI 提供者使用驗證檔案，判斷 MySQL 執行個體所接聽的繫結位址和連接埠，以及要用來收集計量的認證。 安裝期間，MySQL OMI 提供者將掃描繫結位址和連接埠的 MySQL my.cnf 組態檔 (預設位置)，並局部設定 MySQL OMI 驗證檔案。

若要完成 MySQL 伺服器執行個體的監視，請將預先產生的 MySQL OMI 驗證檔案加入正確的目錄。

### <a name="authentication-file-format"></a>驗證檔案格式
MySQL OMI 驗證檔案是包含下列相關資訊的文字檔︰

* 連接埠
* 繫結位址
* MySQL 使用者名稱
* Base64 編碼的密碼

MySQL OMI 驗證檔案只會授與產生它之 Linux 使用者的讀取/寫入權限。

```
[Port]=[Bind-Address], [username], [Base64 encoded Password]
(Port)=(Bind-Address), (username), (Base64 encoded Password)
(Port)=(Bind-Address), (username), (Base64 encoded Password)
AutoUpdate=[true|false]
```

預設 MySQL OMI 驗證檔案包含預設執行個體和連接埠號碼 (根據可用的資訊，以及從找到的 MySQL 組態檔進行剖析的資訊)。

預設執行個體是讓管理一部 Linux 主機上多個 MySQL 執行個體更為容易的方法，並且以連接埠為 0 的執行個體表示。 所有加入的執行個體都會繼承透過預設執行個體所設定的屬性。 例如，如果加入接聽通訊埠 '3308' 的 MySQL 執行個體，預設執行個體的繫結位址、使用者名稱和 Base64 編碼的密碼將會用來嘗試和監視接聽 3308 的執行個體。 如果 3308 上的執行個體繫結至另一個位址，並使用相同的 MySQL 使用者名稱和密碼配對，則只需要重新指定繫結位址，就會繼承其他屬性。

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
| 連接埠 |連接埠代表 MySQL 執行個體目前正在接聽的連接埠。  連接埠 0 表示後面的屬性用於預設執行個體。 |
| 繫結位址 |繫結位址是目前 MySQL 繫結位址 |
| username |這是您想要用來監視 MySQL 伺服器執行個體之 MySQL 使用者的使用者名稱。 |
| Base64 編碼的密碼 |這是以 Base64 編碼之 MySQL 監視使用者的密碼。 |
| AutoUpdate |升級 MySQL OMI 提供者時，提供者將重新掃描 my.cnf 檔案中的變更，並覆寫 MySQL OMI 驗證檔案。 將此旗標設定為 true 或 false (視 MySQL OMI 驗證檔案的必要更新而定)。 |

#### <a name="authentication-file-location"></a>驗證檔案位置
MySQL OMI 驗證檔案應該位於下列位置，並命名為 "mysql-auth"：

/var/opt/microsoft/mysql-cimprov/auth/omsagent/mysql-auth

omsagent 使用者應該會擁有此檔案 (和 auth/omsagent 目錄)。

## <a name="agent-logs"></a>代理程式記錄檔
OMS Agent for Linux 記錄檔位於：

/var/opt/microsoft/omsagent/&lt;工作區識別碼&gt;/log/

omsconfig (代理程式組態) 程式的 OMS Agent for Linux 記錄檔位於：

/var/opt/microsoft/omsconfig/log/

OMI 和 SCX 元件的記錄檔 (其提供效能計量資料) 位於：

/var/opt/omi/log/ and /var/opt/microsoft/scx/log

## <a name="troubleshooting-the-oms-agent-for-linux"></a>針對 OMS Agent for Linux 進行疑難排解
使用下列資訊來診斷和排解常見問題。

如果這一節中的疑難排解資訊對您沒有幫助，您也可以使用下列資源來協助您解決問題。

* 具有頂級支援的客戶可以透過[頂級支援](https://premier.microsoft.com/)記錄支援案例
* 擁有 Azure 支援合約的客戶可以在 [Azure 入口網站](https://manage.windowsazure.com/?getsupport=true)記錄支援案例
* 將 [GitHub 問題](https://github.com/Microsoft/OMS-Agent-for-Linux/issues) 歸檔
* 各種想法的意見反應論壇，並建立錯誤 (bug) 報告 [http://aka.ms/opinsightsfeedback](http://aka.ms/opinsightsfeedback)

### <a name="important-log-locations"></a>重要記錄檔位置
| 檔案 | 路徑 |
| --- | --- |
| OMS Agent for Linux 記錄檔 |`/var/opt/microsoft/omsagent/<workspace id>/log/omsagent.log ` |
| OMS Agent 組態記錄檔 |`/var/opt/microsoft/omsconfig/omsconfig.log` |

### <a name="important-configuration-files"></a>重要組態檔案
| 分類 | 檔案位置 |
| --- | --- |
| syslog |`/etc/syslog-ng/syslog-ng.conf`、`/etc/rsyslog.conf` 或 `/etc/rsyslog.d/95-omsagent.conf` |
| 效能、Nagios、Zabbix、OMS 輸出和一般代理程式 |`/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` |
| 其他組態 |`/etc/opt/microsoft/omsagent/<workspace id>/omsagent.d/*.conf` |

> [!NOTE]
> 編輯效能計數器的組態檔，而若已啟用 OMS 入口網站組態，則會覆寫 syslog。 您可以在 OMS 入口網站中停用組態 (適用於所有節點)，或對單一節點執行下列命令︰
>
>

```
sudo su omsagent -c /opt/microsoft/omsconfig/Scripts/OMS_MetaConfigHelper.py --disable
```


### <a name="enable-debug-logging"></a>啟用偵錯記錄
若要啟用偵錯記錄，您可以使用 OMS 輸出外掛程式和詳細資訊輸出。

#### <a name="oms-output-plugin"></a>OMS 輸出外掛程式
FluentD 可讓外掛程式為不同記錄檔層級的輸入和輸出，指定記錄層級。 若要為 OMS 輸出指定不同的記錄檔層級，在 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` 檔案中編輯一般代理程式組態。

在組態檔的底部附近，將 `log_level` 屬性從 `info` 變更為 `debug`。

 ```
 <match oms.** docker.**>
  type out_oms
  log_level debug
  num_threads 5
  buffer_chunk_limit 5m
  buffer_type file
  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
  buffer_queue_limit 10
  flush_interval 20s
  retry_limit 10
  retry_wait 30s
</match>
 ```

偵錯記錄功能可讓您查看 OMS 服務的分批上傳 (依類型、資料項目總，以及傳送所花費的時間分隔)。

*啟用偵錯的記錄檔範例︰*

```
Success sending oms.nagios x 1 in 0.14s
Success sending oms.omi x 4 in 0.52s
Success sending oms.syslog.authpriv.info x 1 in 0.91s
```

#### <a name="verbose-output"></a>詳細資訊輸出
除了使用 OMS 輸出外掛程式，您也可以將資料項目直接輸出至 `stdout`，其顯示在 OMS Agent for Linux 記錄檔中。

在位於 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` OMS 一般代理程式組態檔中，在每一行前面新增 `#`，以將 OMS 輸出註解化。

```
#<match oms.** docker.**>
#  type out_oms
#  log_level info
#  num_threads 5
#  buffer_chunk_limit 5m
#  buffer_type file
#  buffer_path /var/opt/microsoft/omsagent/<workspace id>/state/out_oms*.buffer
#  buffer_queue_limit 10
#  flush_interval 20s
#  retry_limit 10
#  retry_wait 30s
#</match>
```

在輸出外掛程式下面，移除每一行開頭的 `#` 符號，以將下一節中的註解移除。

```
<match **>
  type stdout
</match>
```

### <a name="forwarded-syslog-messages-do-not-appear-in-the-log"></a>轉送的 Syslog 訊息不會出現在記錄檔中
#### <a name="probable-causes"></a>可能的原因
* 套用到 Linux 伺服器的組態不允許收集傳送的設備和 (或) 記錄檔層級
* Syslog 並未正確地轉送到 Linux 伺服器
* 每秒所轉送的訊息數目太大，以致無法處理 OMS Agent for Linux 的基本組態

#### <a name="resolutions"></a>解決方式
* 確認 OMS 入口網站 (適用於 Syslog) 中的組態具有所有設備和正確的記錄檔層級
  * **OMS 入口網站 > 設定 > 資料 > Syslog**
* 確認原生 syslog 傳訊精靈 (`rsyslog`、`syslog-ng`) 能夠接收轉送的郵件
* 檢查 Syslog 伺服器上的防火牆設定，確定不會封鎖訊息
* 使用 `logger` 命令模擬給 OMS 的 Syslog 訊息 - 例如︰
  * `logger -p local0.err "This is my test message"`

### <a name="problems-connecting-to-oms-when-using-a-proxy"></a>使用 Proxy 時連線到 OMS 的問題
#### <a name="probable-causes"></a>可能的原因
* 在安裝及設定代理程式時指定的 Proxy 不正確
* OMS 服務端點未列入您資料中心的允許清單

#### <a name="resolutions"></a>解決方式
* 使用已啟用 `-v` 選項的下列命令來重新安裝 OMS Agent for Linux。 這可讓透過 Proxy 連接到 OMS 服務的代理程式產生詳細資訊輸出。
  * `/opt/microsoft/omsagent/bin/omsadmin.sh -w <OMS Workspace ID> -s <OMS Workspace Key> -p <Proxy Conf> -v`
  * 檢閱 OMS Proxy 文件 ([設定代理程式與 HTTP Proxy 伺服器搭配使用](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#configuring-the-agent-for-use-with-an-http-proxy-server))
* 確認下列 OMS 服務端點已列入允許清單

| 代理程式資源 | 連接埠 |
| --- | --- |
| &#42;.ods.opinsights.azure.com |連接埠 443 |
| &#42;.oms.opinsights.azure.com |連接埠 443 |
| ods.systemcenteradvisor.com |連接埠 443 |
| &#42;.blob.core.windows.net/ |連接埠 443 |

### <a name="a-403-error-is-displayed-when-onboarding"></a>上架時會顯示 403 錯誤
#### <a name="probable-causes"></a>可能的原因
* Linux 伺服器上的日期和時間不正確
* 使用的工作區識別碼和工作區金鑰不正確

#### <a name="resolution"></a>解決方案
* 利用 `date` 命令確認 Linux 伺服器上的時間。 如果從目前時間算起，資料大於或小於 15 分鐘，則會上架失敗。 若要修正此問題，請更新 Linux 伺服器的日期和/或時區。
* 如果時間差異導致上架失敗，最新版的 OMS Agent for Linux 則會通知您
* 使用正確的工作區識別碼和工作區金鑰來重新上架 如需詳細資訊，請參閱[使用命令列進行上架](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)。

### <a name="a-500-error-or-404-error-appears-in-the-log-file-after-onboarding"></a>上架後，500 錯誤或 404 錯誤會出現於記錄檔中
這是第一次將 Linux 資料上傳到 OMS 工作區期間發生的已知問題。 這不會影響正在傳送的資料或其他問題。 您可以在最初上架時忽略這些錯誤。

### <a name="nagios-data-does-not-appear-in-the-oms-portal"></a>Nagios 資料不會顯示在 OMS 入口網站中
#### <a name="probable-causes"></a>可能的原因
* omsagent 使用者沒有從 Nagios 記錄檔讀取資料的權限
* Nagios 來源和篩選區段仍會在 omsagent.conf 檔案中註解

#### <a name="resolutions"></a>解決方式
* 新增 omsagent 使用者以便從 Nagios 檔案讀取資料。 如需詳細資訊，請參閱 [Nagios 警示](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#nagios-alerts)。
* 在位於 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf` 的 OMS Agent for Linux 一般組態檔中，確保 Nagios 來源和篩選器區段**都**已移除註解，如下列範例所示。

```
<source>
  type tail
  path /var/log/nagios/nagios.log
  format none
  tag oms.nagios
</source>

<filter oms.nagios>
  type filter_nagios_log
</filter>
```


### <a name="linux-data-doesnt-appear-in-the-oms-portal"></a>Linux 資料不會顯示在 OMS 入口網站中
#### <a name="probable-causes"></a>可能的原因
* 上架至 OMS 服務失敗
* 對 OMS 服務的連線遭到封鎖
* 已備份 OMS Agent for Linux 資料

#### <a name="resolutions"></a>解決方式
* 驗證 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` 是否存在，以確認上架至 OMS 服務成功。
* 使用 omsadmin.sh 命令列重新上架。 如需詳細資訊，請參閱[使用命令列進行上架](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)。
* 如果使用 proxy，請使用上述的 Proxy 疑難排解步驟
* 在某些情況下，當 OMS Agent for Linux 無法與 OMS 服務通訊時，Agent 上的資料會備份至 50 MB 的完整緩衝區大小。 執行 `/opt/microsoft/omsagent/bin/service_control restart` 命令以重新啟動 OMS Agent for Linux。
  >[AZURE.NOTE] 此問題已在代理程式 1.1.0-28 版和更新版本中修正。

### <a name="syslog-linux-performance-counter-configuration-is-not-applied-in-the-oms-portal"></a>Syslog Linux 效能計數器組態不會套用於 OMS 入口網站
#### <a name="probable-causes"></a>可能的原因
* OMS Agent for Linux 中的設定代理程式尚未從 OMS 入口網站擷取最新組態。
* 未套用入口網站中經過修訂的設定

#### <a name="resolutions"></a>解決方式
`omsconfig` 是 OMS Agent for Linux 中的設定代理程式，會每隔 5 分鐘擷取一次 OMS 入口網站組態變更。 此組態接著會套用到位於 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` 的 OMS Agent for Linux 組態檔。

* 在某些情況下，OMS Agent for Linux 設定代理程式可能無法與入口網站組態服務進行通訊，以致未套用最新的組態。
* 確認已利用下列項目安裝 `omsconfig` 代理程式︰

  * `dpkg --list omsconfig` 或 `rpm -qi omsconfig`
  * 若未安裝，請重新安裝最新版的 OMS Agent for Linux
* 確認 `omsconfig` 代理程式可以與 OMS 服務進行通訊

  * 執行 `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` 命令
    * 上述命令會傳回代理程式從入口網站擷取的組態，包括 Syslog 設定、Linux 效能計數器以及自訂記錄檔
    * 如果上述命令失敗，請執行 `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py` 命令。 此命令會強制 omsconfig 代理程式與 OMS 服務進行通訊，以擷取最新的組態。

### <a name="custom-linux-log-data-does-not-appear-in-the-oms-portal"></a>自訂 Linux 記錄檔資料不會顯示在 OMS 入口網站中
#### <a name="probable-causes"></a>可能的原因
* 上架至 OMS 服務失敗
* 尚未選取 [將下列組態套用至我的 Linux 伺服器] 設定
* omsconfig 尚未從入口網站挑選最新的自訂記錄檔
* 由於權限問題或找不到 `omsagent`，所以 `omsagent` 用法無法存取自訂記錄檔。 在此情況下，您會看到下列輸出︰
  * `[DATETIME] [warn]: file not found. Continuing without tailing it.`
  * `[DATETIME] [error]: file not accessible by omsagent.`
* 這是在 OMS Agent for Linux 1.1.0-217 版中修正的已知競爭情況問題

#### <a name="resolutions"></a>解決方式
* 藉由判斷 `/etc/opt/microsoft/omsagent/<workspace id>/conf/omsadmin.conf` 檔案是否存在，以確認您已成功上架。
  * 必要時，再次使用 omsadmin.sh 命令列進行上架。 如需詳細資訊，請參閱[使用命令列進行上架](https://github.com/Microsoft/OMS-Agent-for-Linux/blob/master/docs/OMS-Agent-for-Linux.md#onboarding-using-the-command-line)。
* 在 OMS 入口網站中，於 [資料] 索引標籤的 [設定] 之下，確定已選取 [將下列組態套用至我的 Linux 伺服器] 設定。  
  ![套用組態](./media/log-analytics-linux-agents/customloglinuxenabled.png)
* 確認 `omsconfig` 代理程式可以與 OMS 服務進行通訊

  * 執行 `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/GetDscConfiguration.py'` 命令
  * 上述命令會傳回代理程式從入口網站擷取的組態，包括 Syslog 設定、Linux 效能計數器以及自訂記錄檔
  * 如果上述命令失敗，請執行 `sudo su omsagent -c 'python /opt/microsoft/omsconfig/Scripts/PerformRequiredConfigurationChecks.py` 命令。 此命令會強制 omsconfig 代理程式與 OMS 服務進行通訊，以擷取最新的組態。

OMS Agent for Linux 使用者不會以特殊權限使用者 `root` 身分執行，而是以 `omsagent` 使用者身分執行 OMS Agent for Linux。 在大部分情況下，必須將明確的權限授與給使用者，才能讀取特定檔案。

若要授與權限給 `omsagent` 使用者，請執行下列命令︰

1. 利用 `sudo usermod -a -G <GROUPNAME> <USERNAME>`將 `omsagent` 使用者新增至特定群組
2. 利用 `sudo chmod -R ugo+rw <FILE DIRECTORY>` 權授必要檔案的通用讀取權限

OMS Agent for Linux 1.1.0-217 版修正了已知的競爭情況問題。 更新為最新的代理程式後，請執行下列命令，以取得最新版的輸出外掛程式︰

```
sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.conf /etc/opt/microsoft/omsagent/<workspace id>/conf/omsagent.conf
```

## <a name="known-limitations"></a>已知限制
請檢閱下列各節來了解 OMS Agent for Linux 的目前限制。

### <a name="azure-diagnostics"></a>Azure 診斷
對於在 Azure 中執行的 Linux 虛擬機器，可能需要額外的步驟，才能允許 Azure Diagnostics 和 Operations Management Suite 的資料收集。 **版本 2.2** 的 Diagnostics Extension for Linux，才能與 OMS Agent for Linux 相容。

如需安裝和設定 Diagnostic Extension for Linux 的詳細資訊，請參閱 [使用 Azure CLI 命令啟用 Linux Diagnostic Extension](../virtual-machines/linux/classic/diagnostic-extension.md#use-the-azure-cli-command-to-enable-the-linux-diagnostic-extension)。

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

這些命令範例參考名為 PrivateConfig.json 的檔案。 該檔案的格式應該與下列範例類似。

```
    {
    "storageAccountName":"the storage account to receive data",
    "storageAccountKey":"the key of the account"
    }
```

### <a name="sysklog-is-not-supported"></a>不支援 Sysklog
需要有 rsyslog 或 syslog-ng，才能收集 syslog 訊息。 Red Hat Enterprise Linux 第 5 版、CentOS 和 Oracle Linux 版本 (sysklog) 不支援預設 syslog 精靈，進行 syslog 事件收集。 若要從此版的這些散發套件收集 syslog 資料，rsyslog 精靈應該安裝和設定為取代 sysklog。 如需將 sysklog 取代為 rsyslog 的詳細資訊，請參閱 [安裝新建置的 rsyslog RPM](http://wiki.rsyslog.com/index.php/Rsyslog_on_CentOS_success_story#Install_the_newly_built_rsyslog_RPM)。

## <a name="next-steps"></a>後續步驟
* [從方案庫加入 Log Analytics 方案](log-analytics-add-solutions.md) ，以加入功能和收集資料。
* 熟悉 [記錄檔搜尋](log-analytics-log-searches.md) 以檢視方案所收集的詳細資訊。
* 使用 [儀表板](log-analytics-dashboards.md) 以儲存和顯示您自己的自訂搜尋。

