<properties
   pageTitle="Azure Government 映像庫 | Microsoft Azure"
   description="這篇文章提供 Azure Government 映像庫與其包含之映像的概觀"
   services="Azure-Government"
   documentationCenter=""
   authors="joharve2"
   manager="chrisnie"
   editor=""/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="azure-government"
   ms.date="06/01/2016"
   ms.author="brendalee"/>


#  Microsoft Azure Government 映像庫

<p> Microsoft Azure Government 映像庫類似於公用 Azure 入口網站。客戶可以選擇部署 Microsoft 和我們的合作夥伴預先建立的映像，或是上傳自己的 VHD。這提供了依照您的需求部署自己的標準化映像的彈性。

下面提供 Azure Government 映像庫中可用映像的清單。某些預先建立的映像確實包含了特定軟體的隨用隨付授權。請先檢閱 [<a href="http://azure.microsoft.com/pricing/details/virtual-machines/">虛擬機器定價</a>] 頁面以取得詳細指引，並向您的 Microsoft 帳戶小組或轉銷商取得 Azure Government 專屬定價。


## Azure Government 映像清單

發行者|映像名稱|說明|作業系統|磁碟大小
---|---|---|---|---
Barracuda Networks, Inc.|Barracuda NextGen Firewall F-Series 6.2.1-057|注意：此應用裝置是透過用戶端應用程式所管理，且需要轉送 TCP/807 以執行這項操作。請參閱部署的讀我檔案 https://techlib.barracuda.com/NG61/DeployAzure...|Linux|80 GB
Barracuda Networks, Inc.|Barracuda Web Application Firewall 8.0.1.008-20160405|注意：此應用裝置是透過 Web UI 所管理，且需要轉送 TCP/8000 以執行這項操作。請參閱部署的讀我檔案 https://techlib.barracuda.com/WAF/Azure 以取得詳細資料。Barracuda Web...|Linux|50 GB
Bitnami|Nginx Stack 1.6|Bitnami Nginx Stack 提供完整且完全整合，並且可供執行的 PHP、MySQL 和 Nginx 開發環境。此外，它也搭售了 phpMyAdmin、SQLite、ImageMagick、FastCGI, Memcache、GD、...|Linux|30 GB
Bitnami|Ruby Stack 2.0|Bitnami RubyStack 可大幅簡化 Ruby on Rails 應用程式的開發與部署。Ruby on Rails 為以資料庫為基礎的 Web 應用程式的完整堆疊 MVC 架構...|Linux|30 GB
Bitnami|LAMP Stack 5.4|Bitnami LAMPStack 可大幅簡化 PHP 應用程式的開發和部署。它包含可供執行版本的 Apache、MySQL、PHP 與 phpMyAdmin ，以及其他所需軟體...|Linux|30 GB
Bitnami|Drupal 8|Bitnami 協助開發的 Drupal 是用來在 Microsoft Azure 上執行 Drupal 的預先設定、可供執行的映像。Drupal 是市面上其中一個最具彈性的開放原始碼內容管理系統。超過 ...|Linux|30 GB
Bitnami|Drupal 6|Bitnami 協助開發的 Drupal 是用來在 Microsoft Azure 上執行 Drupal 的預先設定、可供執行的映像。Drupal 是市面上其中一個最具彈性的開放原始碼內容管理系統。超過 ...|Linux|30 GB
Bitnami|LAMP Stack 5.6|Bitnami LAMPStack 可大幅簡化 PHP 應用程式的開發和部署。它包含可供執行版本的 Apache、MySQL、PHP 與 phpMyAdmin ，以及其他所需軟體...|Linux|30 GB
Bitnami|Ruby Stack 2.1|Bitnami RubyStack 可大幅簡化 Ruby on Rails 應用程式的開發與部署。Ruby on Rails 為以資料庫為基礎的 Web 應用程式的完整堆疊 MVC 架構...|Linux|30 GB
Bitnami|Drupal 7|Bitnami 協助開發的 Drupal 是用來在 Microsoft Azure 上執行 Drupal 的預先設定、可供執行的映像。Drupal 是市面上其中一個最具彈性的開放原始碼內容管理系統。超過 ...|Linux|30 GB
Canonical|Ubuntu Server 12.04.5-LTS|適用於 Microsoft Azure 的 Ubuntu Server 12.04.5-LTS (amd64 20160315)。Ubuntu Server 是全世界最受歡迎的雲端 Linux 環境。Ubuntu 12.04.5-LTS 的更新和修補程式即將推出...|Linux|30 GB
Canonical|Ubuntu Server 16.04 LTS|適用於 Microsoft Azure 的 Ubuntu Server 16.04 LTS (amd64 20160420.3)。Ubuntu Server 是全世界最受歡迎的雲端 Linux 環境。Ubuntu 16.04 LTS 的更新和修補程式即將推出...|Linux|30 GB
Canonical|Ubuntu Server 14.04.4-LTS|適用於 Microsoft Azure 的 Ubuntu Server 14.04.4-LTS (amd64 20160314)。Ubuntu Server 是全世界最受歡迎的雲端 Linux 環境。Ubuntu 14.04.4-LTS 的更新和修補程式即將推出...|Linux|30 GB
Check Point|Check Point vSec，R77.30-015052|適用於 Azure 的 Check Point vSEC 提供新一代的防火牆和安全性閘道。 |Linux|50 GB
CloudLink|CloudLink SecureVM 5.0 BYOL|輕鬆而有自信地控制、監視及加密 VM。EMC 的 CloudLink SecureVM 提供以原則為基礎的金鑰管理，能解除鎖定原生 Windows BitLocker 和 Linux OS 加密功能...|Linux|30 GB
CloudLink|CloudLink SecureVM|CloudLink® SecureVM 提供進行 VM 映像檔完整性驗證的 VM 磁碟區加密和開機前授權，讓您可以控制 VM 開機的時機和位置。SecureVM 解除鎖定原生 Windows B...|Linux|30 GB
coreos|CoreOS Stable|Stable 通道應用於生產叢集。CoreOS 的版本在行銷前已在 Beta 和 Alpha 通道內經過繁複測試。 |Linux|31 GB
credativ|Debian 7 Wheezy|適用於 Microsoft Azure 的 Debian 7 Wheezy (20160405.0)。Debian GNU/Linux 是最受歡迎其中一個 Linux 散發套件。 |Linux|30 GB
credativ|Debian 8 Jessie|適用於 Microsoft Azure 的 Debian 8 Jessie (20160405.0)。Debian GNU/Linux 是最受歡迎其中一個 Linux 散發套件。 |Linux|30 GB
Esri|ArcGIS 10.3.1 for Server (Windows)|ArcGIS for Server 時全世界最具彈性的 GIS 伺服器，可讓您全權控制組織的重要地理空間資產和資料。 |Windows|128 GB
Microsoft Hybrid Cloud Storage Group|StorSimple Virtual Appliance 1100，2015 年 10 月|Microsoft StorSimple Virtual Appliance 1100 |Windows|32 GB
Microsoft Hybrid Cloud Storage Group|StorSimple Virtual Appliance 1100，2015 年 5 月|Microsoft StorSimple Virtual Appliance 1100 |Windows|32 GB
Microsoft Hybrid Cloud Storage Group|StorSimple Virtual Appliance 1100，2015 年 7 月|Microsoft StorSimple Virtual Appliance 1100 |Windows|32 GB
Microsoft Hybrid Cloud Storage Group|StorSimple Virtual Appliance GU2，2016 年 2 月|Microsoft StorSimple Virtual Appliance GU2 |Windows|32 GB
Microsoft Open Technologies, Inc.|Oracle Database 11g R2 Standard Edition on Windows Server 2008 R2|Oracle Database http://www.oracle.com/database 11g R2 Standard Edition (11.2.0.4.0) 是適用於中型公司，且價格實惠、功能完整的資料管理解決方案。建議最小...|Windows|128 GB
Microsoft Open Technologies, Inc.|Oracle Database 11g R2 and WebLogic Server 11g Enterprise Edition on Windows Server 2008 R2|Oracle Database http://www.oracle.com/database 11g R2 Enterprise Edition (11.2.0.4.0) 提供豐富的功能，可輕鬆地管理最嚴苛的交易處理、商務...|Windows|128 GB
Microsoft Open Technologies, Inc.|Oracle Database 12c Enterprise Edition on Windows Server 2012|Oracle Database http://www.oracle.com/database 12c Enterprise Edition (12.1.0.1.0) 是針對雲端所設計的新一代資料庫，並提供了新的多租用戶...|Windows|128 GB
Microsoft Open Technologies, Inc.|Oracle Database 11g R2 and WebLogic Server 11g Standard Edition on Windows Server 2008 R2|Oracle Database http://www.oracle.com/database 11g R2 Standard Edition (11.2.0.4.0) 是適用於中型公司，且價格實惠、功能完整的資料管理解決方案。Oracle WebLo...|Windows|128 GB
Microsoft Open Technologies, Inc.|JDK 8 on Windows Server 2012 R2|Java Platform http://www.oracle.com/java, Standard Edition 8 (更新 25) 可開發安全、可攜式且高效能的應用程式，並包含 Java Development Kit (JDK)、Java...|Windows|128 GB
Microsoft Open Technologies, Inc.|Oracle Database 11g R2 Enterprise Edition on Windows Server 2008 R2|Oracle Database http://www.oracle.com/database 11g R2 Enterprise Edition (11.2.0.4.0) 提供豐富的功能，可輕鬆地管理最嚴苛的交易處理、商務...|Windows|128 GB
Microsoft Open Technologies, Inc.|Windows Server 2012 上的 JDK 6|Java Platform http://www.oracle.com/java, Standard Edition 6 (更新 85) 可開發安全、可攜式且高效能的應用程式，並包含 Java Development Kit (JDK)、Java...|Windows|128 GB
Microsoft Open Technologies, Inc.|JDK 7 on Windows Server 2012|Java Platform http://www.oracle.com/java, Standard Edition 7 (更新 71) 可開發安全、可攜式且高效能的應用程式，並包含 Java Development Kit (JDK)、Java...|Windows|128 GB
Microsoft Open Technologies, Inc.|Oracle Database 12c Standard Edition on Windows Server 2012|Oracle Database http://www.oracle.com/database 12c Standard Edition (12.1.0.1.0) 是適用於中型公司，且價格實惠、功能完整的資料管理解決方案。建議最小...|Windows|128 GB
Microsoft Open Technologies, Inc.|Oracle WebLogic Server 12c Standard Edition on Windows Server 2012|Oracle WebLogic Server http://www.oracle.com/weblogicserver 12c Standard Edition (12.1.2.0) 為領先的 Java EE 應用程式伺服器，提供新一代應用程式...|Windows|128 GB
Microsoft Open Technologies, Inc.|Oracle WebLogic Server 12c Enterprise Edition on Windows Server 2012|Oracle WebLogic Server http://www.oracle.com/weblogicserver 12c Enterprise Edition (12.1.2.0) 為領先的 Java EE 應用程式伺服器，提供新一代應用程式...|Windows|128 GB
Microsoft Open Technologies, Inc.|Oracle WebLogic Server 11g Standard Edition on Windows Server 2008 R2|Oracle WebLogic Server http://www.oracle.com/weblogicserver 11g Standard Edition (10.3.6) 為適用於所有企業規模的領先 Java 應用程式伺服器，提供開發人員工具...|Windows|128 GB
Microsoft Open Technologies, Inc.|Oracle WebLogic Server 11g Enterprise Edition on Windows Server 2008 R2|Oracle WebLogic Server http://www.oracle.com/weblogicserver 11g Enterprise Edition (10.3.6) 為適用於新型資料中心的領先 Java 應用程式伺服器。它完整利用最新的...|Windows|128 GB
Microsoft Open Technologies, Inc.|Oracle Database 12c and WebLogic Server 12c Standard Edition on Windows Server 2012|Oracle Database http://www.oracle.com/database 12c Standard Edition (12.1.0.1.0) 是適用於中型公司，且價格實惠、功能完整的資料管理解決方案。Oracle WebLogic...|Windows|128 GB
Microsoft Open Technologies, Inc.|Oracle Database 12c and WebLogic Server 12c Enterprise Edition on Windows Server 2012|Oracle Database http://www.oracle.com/database 12c Enterprise Edition (12.1.0.1.0) 是針對雲端所設計的新一代資料庫，並提供了新的多租用戶...|Windows|128 GB
Microsoft SQL Server 群組|Windows Server 2012 上的 SQL Server 2012 SP2 Enterprise|此映像包含完整版本的 SQL Server。部分 SQL Server 元件需要額外的設定和組態才能使用。建議您使用 A3 大小或更大的虛擬機器。...|Windows|127 GB
Microsoft SQL Server 群組|SQL Server 2014 RTM Enterprise on Windows Server 2012 R2|此映像包含完整版本的 SQL Server。我們建議您使用 A3 大小或更大的虛擬機器。此映像已針對 Microsoft Azure 預先設定，包含啟用 CEIP...|Windows|127 GB
Microsoft SQL Server 群組|Windows Server 2012 上的 SQL Server 2012 SP2 Standard|部分 SQL Server 元件需要額外的設定和組態才能使用。我們建議您使用 A2 大小或更大的虛擬機器。此映像已針對 Microsoft Azure 預先設定...|Windows|127 GB
Microsoft SQL Server 群組|SQL Server 2014 RTM Standard on Windows Server 2012 R2|此映像包含完整版本的 SQL Server。我們建議您使用 A2 大小或更大的虛擬機器。此映像已針對 Microsoft Azure 預先設定，包含啟用 CEIP...|Windows|127 GB
Microsoft SQL Server 群組|SQL Server 2014 RTM Enterprise on Windows Server 2012 R2|此映像包含完整版本的 SQL Server。我們建議您使用 A3 大小或更大的虛擬機器。此映像已針對 Microsoft Azure 預先設定，包含啟用 CEIP...|Windows|127 GB
Microsoft SQL Server 群組|SQL Server 2014 RTM Standard on Windows Server 2012 R2|此映像包含完整版本的 SQL Server。我們建議您使用 A2 大小或更大的虛擬機器。此映像已針對 Microsoft Azure 預先設定，包含啟用 CEIP...|Windows|127 GB
Microsoft Windows Server 群組|Windows Server 2016 Technical Preview 4|做為 Microsoft Cloud OS 願景的核心，Windows Server 將 Microsoft 在提供全球規模之雲端服務上豐富且高深的經驗，帶到您的資料中心基礎結構中。Windows ...|Windows|128 GB
Microsoft Windows Server 群組|Windows Server 2016 Core with Containers Tech Preview 4|Windows Server 將 Microsoft 在提供全球規模之雲端服務上豐富且高深的經驗，帶到您的資料中心基礎結構中。Windows Server 2016 Core with Containers Technical Preview 4...|Windows|127 GB
Microsoft Windows Server 群組|Windows Server 2012 Datacenter，2015 年 12 月|Windows Server 2012 結合了 Microsoft 建置及運作公用雲端的經驗，打造了一個動態、可用性高的伺服器平台。它提供一個可調整、動態且多租用戶...|Windows|128 GB
Microsoft Windows Server 群組|Windows Server 2012 Datacenter，2016 年 1 月|Windows Server 2012 結合了 Microsoft 建置及運作公用雲端的經驗，打造了一個動態、可用性高的伺服器平台。它提供一個可調整、動態且多租用戶...|Windows|128 GB
Microsoft Windows Server 群組|Windows Server 2012 Datacenter，2015 年 11 月|Windows Server 2012 結合了 Microsoft 建置及運作公用雲端的經驗，打造了一個動態、可用性高的伺服器平台。它提供一個可調整、動態且多租用戶...|Windows|128 GB
Microsoft Windows Server 群組|Windows Server 2008 R2 SP1，2016 年 4 月|Windows Server 2008 R2 是一個多用途伺服器，設計目的是要提高您的伺服器或私人雲端基礎結構的可靠性和彈性，協助您節省時間並降低成本。它提供了...|Windows|128 GB
Microsoft Windows Server 群組|Windows Server 2012 R2 Datacenter，2015 年 11 月|做為 Microsoft Cloud OS 願景的核心，Windows Server 2012 R2 將 Microsoft 在提供全球規模之雲端服務上的經驗，帶到您的基礎結構中。它提供了企業級的效能...|Windows|128 GB
Microsoft Windows Server 群組|Windows Server 2012 Datacenter，2016 年 4 月|Windows Server 2012 結合了 Microsoft 建置及運作公用雲端的經驗，打造了一個動態、可用性高的伺服器平台。它提供一個可調整、動態且多租用戶...|Windows|128 GB
Microsoft Windows Server 群組|Windows Server 2012 Datacenter，2016 年 2 月|Windows Server 2012 結合了 Microsoft 建置及運作公用雲端的經驗，打造了一個動態、可用性高的伺服器平台。它提供一個可調整、動態且多租用戶...|Windows|128 GB
Microsoft Windows Server 群組|Windows Server 2008 R2 SP1，2015 年 11 月|Windows Server 2008 R2 是一個多用途伺服器，設計目的是要提高您的伺服器或私人雲端基礎結構的可靠性和彈性，協助您節省時間並降低成本。它提供了...|Windows|128 GB
Microsoft Windows Server 群組|Windows Server Nano 2016 Tech Preview 4|Nano Server 是全新的 Windows Server 無周邊部署選項，其大小只有完整作業系統的一小部分。這個版本的 Technical Preview 允許您試用 Nano Server 及了解...|Windows|8 GB
Microsoft Windows Server 群組|Windows Server 2016 Technical Preview 4|做為 Microsoft Cloud OS 願景的核心，Windows Server 將 Microsoft 在提供全球規模之雲端服務上豐富且高深的經驗，帶到您的資料中心基礎結構中。Windows ...|Windows|128 GB
Microsoft Windows Server 群組|Windows Server 2008 R2 SP1，2016 年 2 月|Windows Server 2008 R2 是一個多用途伺服器，設計目的是要提高您的伺服器或私人雲端基礎結構的可靠性和彈性，協助您節省時間並降低成本。它提供了...|Windows|128 GB
Microsoft Windows Server 群組|Windows Server 2008 R2 SP1，2016 年 1 月|Windows Server 2008 R2 是一個多用途伺服器，設計目的是要提高您的伺服器或私人雲端基礎結構的可靠性和彈性，協助您節省時間並降低成本。它提供了...|Windows|128 GB
Microsoft Windows Server 群組|Windows Server 2008 R2 SP1，2015 年 12 月|Windows Server 2008 R2 是一個多用途伺服器，設計目的是要提高您的伺服器或私人雲端基礎結構的可靠性和彈性，協助您節省時間並降低成本。它提供了...|Windows|128 GB
Microsoft Windows Server 群組|Windows Server 2012 R2 Datacenter，2016 年 2 月|做為 Microsoft Cloud OS 願景的核心，Windows Server 2012 R2 將 Microsoft 在提供全球規模之雲端服務上的經驗，帶到您的基礎結構中。它提供了企業級的效能...|Windows|128 GB
Microsoft Windows Server 群組|Windows Server 2012 R2 Datacenter，2016 年 1 月|做為 Microsoft Cloud OS 願景的核心，Windows Server 2012 R2 將 Microsoft 在提供全球規模之雲端服務上的經驗，帶到您的基礎結構中。它提供了企業級的效能...|Windows|128 GB
Microsoft Windows Server 群組|Windows Server 2012 R2 Datacenter，2015 年 12 月|做為 Microsoft Cloud OS 願景的核心，Windows Server 2012 R2 將 Microsoft 在提供全球規模之雲端服務上的經驗，帶到您的基礎結構中。它提供了企業級的效能...|Windows|128 GB
Microsoft Windows Server 群組|Windows Server 2012 R2 Datacenter，2016 年 4 月|做為 Microsoft Cloud OS 願景的核心，Windows Server 2012 R2 將 Microsoft 在提供全球規模之雲端服務上的經驗，帶到您的基礎結構中。它提供了企業級的效能...|Windows|128 GB
Microsoft Windows Server 群組|Windows Server 2016 Technical Preview 5 - Nano Server|Nano Server 是全新的 Windows Server 無周邊部署選項，其大小只有完整作業系統的一小部分。這個版本的 Technical Preview 允許您試用 Nano Server 及了解...|Windows|8 GB
OpenLogic|OpenLogic 7.2|這個 Linux 的配送映像是以 CentOS 7.2 版為基礎，由 OpenLogic 所提供。它包含基本伺服器封裝的安裝。 |Linux|30 GB
OpenLogic|OpenLogic 6.7|這個 Linux 的配送映像是以 CentOS 6.7 版為基礎，由 OpenLogic 所提供。它包含基本伺服器封裝的安裝。 |Linux|30 GB
Oracle|Oracle Linux 7.0.0.0.0|Oracle Linux 7.0.0.0 提供極大效能、進階延展性，以及企業應用程式和系統的可靠性。針對企業工作負載最佳化，Oracle Linux 是唯一的作業...|Linux|30 GB
Oracle|Oracle Linux 6.4.0.0.0 上的 Oracle Database 12.1.0.1 Enterprise Edition|Oracle Database 12c Enterprise Edition 是針對雲端所設計的新一代資料庫，並針對快速、可延展、可靠和安全資料庫平台提供新的多租用戶架構...|Linux|40 GB
Oracle|Oracle Linux 6.4.0.0.0 上的 Oracle WebLogic Server 12.1.2|Oracle WebLogic Server 12c Enterprise Edition 為領先 Java EE 應用程式伺服器，提供關鍵性雲端平台上的新一代應用程式，含有原生雲端管理和...|Linux|30 GB
Oracle|Oracle Linux 6.4.0.0.0 上的 Oracle Database 12.1.0.1 Standard Edition|Oracle Database 12c Standard Edition 是適用於中型公司，且價格實惠、功能完整的資料管理解決方案。如需詳細資訊，請參閱http://www.oracle.com/database。 |Linux|40 GB
遠端桌面主機|Windows Server 遠端桌面工作階段主機與 Microsoft Office 365 ProPlus|此映像包含 Windows Server 2012 R2 作業系統，並且已安裝遠端桌面工作階段主機 (RD 工作階段主機) 角色以及具有共用電腦的 Microsoft Office 365 ProPlus ...|Windows|127 GB
遠端桌面主機|Windows Server 2012 R2 上的 Windows Server 遠端桌面工作階段主機|此映像包含 Windows Server 2012 R2 作業系統，並且已安裝遠端桌面工作階段主機 (RD 工作階段主機) 角色。此映像已針對 Microsoft Azure 預先設定。RD 工作階段...|Windows|127 GB
SharePoint|SharePoint Server 2016 試用版|Windows Server 2012 R2 Datacenter 上的 Microsoft SharePoint Server 2016 試用版。使用此試用版映像建立的虛擬機器將於 2016 年 9 月 19 日到期。此映像包含完整的安裝...|Windows|128 GB
SUSE|SUSE Linux Enterprise Server 12 (Premium 映像)|因為下列原因，所以可以在 Microsoft Azure 上自信地執行 SUSE Linux Enterprise Server 上的生產工作負載：知道保證服務等級，以及來自 SUSE 和 Microsoft 工程師的協助，如果您...|Linux|30 GB
SUSE|SUSE Linux Enterprise Server 12 SP1|因為下列原因，所以可以在 Microsoft Azure 上自信地執行 SUSE Linux Enterprise Server 上的生產工作負載：知道保證服務等級，以及來自 SUSE 和 Microsoft 工程師的協助，如果您...|Linux|30 GB
SUSE|SUSE Linux Enterprise Server 12 SP1|因為下列原因，所以可以在 Microsoft Azure 上自信地執行 SUSE Linux Enterprise Server 上的生產工作負載：知道保證服務等級，以及來自 SUSE 和 Microsoft 工程師的協助，如果您...|Linux|30 GB
SUSE|SUSE Linux Enterprise Server 11 SP4|因為下列原因，所以可以在 Microsoft Azure 上自信地執行 SUSE Linux Enterprise Server 上的生產工作負載：知道保證服務等級，以及來自 SUSE 和 Microsoft 工程師的協助，如果您...|Linux|30 GB
SUSE|SUSE Linux Enterprise Server 11 SP4|因為下列原因，所以可以在 Microsoft Azure 上自信地執行 SUSE Linux Enterprise Server 上的生產工作負載：知道保證服務等級，以及來自 SUSE 和 Microsoft 工程師的協助，如果您...|Linux|30 GB
SUSE|SUSE Linux Enterprise Server 12|因為下列原因，所以可以在 Microsoft Azure 上自信地執行 SUSE Linux Enterprise Server 上的生產工作負載：知道保證服務等級，以及來自 SUSE 和 Microsoft 工程師的協助，如果您...|Linux|30 GB
Visual Studio|Visual Studio Enterprise 2015 Update 2 with Azure SDK 2.9 on Windows Server 2012 R2|使用 Visual Studio Enterprise 2015 Update 2，您可以在裝置和服務之間，使用具有一致開發體驗的單一解決方案來建立應用程式。您取得工具，您需要...|Windows|128 GB



<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged -->

## <a name="next"></a>接續步驟

下面的資源應該會提供關於從映像庫部署或建立您自己的 VHD 的詳細資訊。請參考 <a href="../azure-government-developer-guide">Azure Government 開發人員指南</a>，以發現使用 Azure Government 時端點的程式設計差異。

###其他資源：

- [部署 Windows 虛擬機器](virtual-machines/virtual-machines-windows-hero-tutorial.md)

- [虛擬機器常見問題集](http://msdn.microsoft.com/library/azure/dn683781.aspx)

- [擷取 Linux VM 映像](virtual-machines/virtual-machines-linux-classic-capture-image.md)

<!--- **<A href="/azure-government-service-description">Azure Government Service Descriptions</a>**-->




<!-- Images. -->

[1]: ./media/azure-government-developer-guide/publisherguide.png
[2]: ./media/azure-government-overview/azure-gov-overview.jpg

<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: virtual-machines/virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: app-service-web/web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: storage-whatis-account.md

<!---HONumber=AcomDC_0629_2016-->