---
title: "在 SAP HANA on Azure (大型執行個體) 上安裝 SAP HANA | Microsoft Docs"
description: "如何在 SAP HANA on Azure (大型執行個體) 上安裝 SAP HANA。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 3827e74433a859e65071a1041c5ed6c8253db8df
ms.openlocfilehash: be17b2de227ef6d93f8d326ff060c3c4788eb622


---
# <a name="install-sap-hana-on-sap-hana-on-azure-large-instances"></a>在 SAP HANA on Azure (大型執行個體) 上安裝 SAP HANA

安裝 SAP HANA 是您的責任，您可以在遞交新的 SAP HANA on Azure (大型執行個體) 伺服器之後立即執行這項操作。 請注意，依據 SAP 原則，SAP HANA 的安裝必須由經過驗證的 SAP HANA 安裝者 (也就是已經通過認證 SAP 技術協會驗證的使用者 – SAP HANA 安裝認證測驗)，或 SAP 認證系統整合者 (SI) 執行。

必須考慮與 SAP HANA (伺服器端) 和 SAP HANA (用戶端) 相關的特定連線考量。 在許多情況下，SAP HANA 伺服器會將其 IP 位址傳送至用戶端，在該用戶端會快取並且用於後續連線嘗試。 由於 SAP HANA on Azure (大型執行個體) 將租用戶網路中使用的內部伺服器 IP 位址轉址以用於針對特定 Azure VNet 提供的 IP 位址範圍，SAP HANA 資料庫伺服器在設計上會傳送&quot;內部&quot; IP 位址範圍。 例如，並非是由 SAP HANA 提供已轉址的 IP 位址進行主機名稱解析，而是使用快取的內部 IP 位址。 所以使用 SAP HANA 用戶端 (ODBC、JDBC 等等) 的應用程式無法與此 IP 位址連線。 若要指示 SAP HANA 伺服器應該將已轉址的 IP 位址散佈至用戶端，就必須編輯 SAP HANA 全域系統組態檔 (global.ini)。

將下列內容新增至 global.ini (直接或透過 SAP HANA Studio)︰
```
[public\_hostname\_resolution]
use\_default\_route = no
map\_<hostname of SAP HANA on Azure (Large Instances) tenant> = <NAT IP Address (IP address that can be accessed in Azure)>
```
如需詳細資訊和範例，請參閱[SAP HANA 系統管理指南](http://help.sap.com/hana/sap_hana_administration_guide_en.pdf)中的章節_對應資料庫用戶端存取的主機名稱_。

在用戶端中 (針對 Azure 中的 SAP 應用程式伺服器)，編輯 /etc/hosts 檔案 (在 Linux 中) 或 /system32/drivers/etc/hosts (在 Windows Server 中)，並且納入 SAP HANA on Azure (大型執行個體) 租用戶及其個別已轉址的 IP 位址的主機名稱項目。

>[!NOTE] 
>安裝 SAP HANA 資料庫用戶端時，如果在安裝 SAP HANA 資料庫用戶端期間於連接至 SAP HANA on Azure (大型執行個體) 時發生錯誤，隱含地使用 HANA 大型執行個體租用戶 NAT IP 位址，而非主機名稱。

## <a name="storage"></a>儲存體

SAP HANA on Azure (大型執行個體) 的儲存體配置是由 SAP HANA on Azure Service Management 透過 SAP 建議的最佳做法進行設定，請參閱 [SAP HANA 儲存體需求](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)技術白皮書。

不同 HANA 大型執行個體的確切儲存體的組態，如下所示︰

| 記憶體大小 | HANA/data | HANA/log | HANA/shared | HANA/log/backups |
| --- | --- | --- | --- | --- |
| 768 GB | 1280 GB | 512 GB | 768 GB | 512 GB |
| 1536 GB | 3456 GB | 768 GB | 1024 GB | 768 GB |
| 3072 GB | 7680 GB | 1536 GB | 1024 GB | 1536 GB |

此外，客戶可以 1 TB 的增量購買額外儲存體容量。 此額外儲存體可以做為新的磁碟區新增至 HANA 大型執行個體。

儲存體控制器和大型執行個體戳記中的節點會同步處理至 NTP 伺服器。 當您針對 NTP 伺服器同步處理 SAP HANA on Azure (大型執行個體) 單位和 Azure VM 時，Azure 或大型執行個體戳記中的基礎結構與計算單位之間應該不會耗費太多時間。

## <a name="operating-system"></a>作業系統

[SUSE Linux Enterprise Server 12 SP1 for SAP 應用程式](https://www.suse.com/products/sles-for-sap/hana)是針對 SAP HANA on Azure (大型執行個體) 安裝的 Linux 的分佈。 這個特定的分佈提供&quot;現成&quot; SAP 特有功能，(包括預先設定的參數，有效地在 SLES 上執行 SAP)。

請參閱 SUSE 網站上的[資源程式庫/技術白皮書](https://www.suse.com/products/sles-for-sap/resource-library#white-papers)和 SAP Community Network (SCN) 上的 [SAP on SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)，以取得與在 SLES 上部署 SAP HANA 相關的有用資源 (包括高可用性的設定、SAP 作業特定的安全性強化等等)。

額外和有用的 SLES 相關連結︰

- [SUSE Linux 網站上的 SAP HANA](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [SAP 的最佳做法：加入佇列複寫 – SAP NetWeaver on SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)
- [ClamSAP – SLES Virus Protection for SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap) (包括 SLES 12 for SAP 應用程式)

SAP 支援附註適用於實作 SLES 12 SP1 上的 SAP HANA︰

- [SAP 支援附註 #1944799 – SLES 作業系統安裝的 SAP HANA 指南](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
- [SAP 支援附註 #2205917 – 針對 SLES 12 for SAP 應用程式的 SAP HANA DB 建議作業系統設定](https://launchpad.support.sap.com/#/notes/2205917/E)
- [SAP 支援附註 #1984787 – SUSE Linux Enterprise Server 12：安裝注意事項](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP 支援附註 #171356 – 在 Linux 上的 SAP 軟體︰一般資訊](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP 支援附註 #1391070 – Linux UUID 解決方案](https://launchpad.support.sap.com/#/notes/1391070)

## <a name="time-synchronization"></a>時間同步處理

SAP 對於組成 SAP 系統的各種元件的時間差異非常敏感。 具有錯誤標題 ZDATE\_LARGE\_TIME\_DIFF 的 SAP ABAP 簡短傾印可能會非常熟悉，如果您已使用 SAP (基本) 很長一段時間，因為這些簡短傾印會在不同伺服器或 VM 的系統時間離開太遠時顯示。

針對 SAP HANA on Azure (大型執行個體)，在 Azure 中完成的時間同步處理不適用於大型執行個體戳記中的計算單位。 這並不適用於在 Azure 中原生執行 SAP 應用程式 (在 VM 上)，因為 Azure 可確保正確同步處理系統時間。 如此一來，必須設定不同時間伺服器，可供 Azure VM 上執行的 SAP 應用程式伺服器與 HANA 大型執行個體上執行的 SAP HANA 資料庫執行個體使用。 大型執行個體戳記中的儲存體基礎結構與 NTP 伺服器進行時間同步處理。





<!--HONumber=Dec16_HO2-->


