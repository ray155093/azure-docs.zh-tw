---
title: "在 Azure 中的 Linux VM 上開始使用 SAP | Microsoft Docs"
description: "了解在 Microsoft Azure 中的虛擬機器 (VM) 上執行的 SAP 解決方案"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: ad8e5c75-0cf6-4564-ae62-ea1246b4e5f2
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 12/07/2016
ms.author: rclaus
translationtype: Human Translation
ms.sourcegitcommit: 746ed119239d5a0a71a0663e81d9c5b259354419
ms.openlocfilehash: 21b7bf78e70b7d4534220913987cdac5f04cb462
ms.lasthandoff: 01/26/2017


---
# <a name="using-sap-on-azure-virtual-machines-vms"></a>在 Azure 虛擬機器 (VM) 上使用 SAP
[767598]:https://launchpad.support.sap.com/#/notes/767598
[773830]:https://launchpad.support.sap.com/#/notes/773830
[826037]:https://launchpad.support.sap.com/#/notes/826037
[965908]:https://launchpad.support.sap.com/#/notes/965908
[1031096]:https://launchpad.support.sap.com/#/notes/1031096
[1139904]:https://launchpad.support.sap.com/#/notes/1139904
[1173395]:https://launchpad.support.sap.com/#/notes/1173395
[1245200]:https://launchpad.support.sap.com/#/notes/1245200
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1558958]:https://launchpad.support.sap.com/#/notes/1558958
[1585981]:https://launchpad.support.sap.com/#/notes/1585981
[1588316]:https://launchpad.support.sap.com/#/notes/1588316
[1590719]:https://launchpad.support.sap.com/#/notes/1590719
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1605680]:https://launchpad.support.sap.com/#/notes/1605680
[1619720]:https://launchpad.support.sap.com/#/notes/1619720
[1619726]:https://launchpad.support.sap.com/#/notes/1619726
[1619967]:https://launchpad.support.sap.com/#/notes/1619967
[1750510]:https://launchpad.support.sap.com/#/notes/1750510
[1752266]:https://launchpad.support.sap.com/#/notes/1752266
[1757924]:https://launchpad.support.sap.com/#/notes/1757924
[1757928]:https://launchpad.support.sap.com/#/notes/1757928
[1758182]:https://launchpad.support.sap.com/#/notes/1758182
[1758496]:https://launchpad.support.sap.com/#/notes/1758496
[1772688]:https://launchpad.support.sap.com/#/notes/1772688
[1814258]:https://launchpad.support.sap.com/#/notes/1814258
[1882376]:https://launchpad.support.sap.com/#/notes/1882376
[1909114]:https://launchpad.support.sap.com/#/notes/1909114
[1922555]:https://launchpad.support.sap.com/#/notes/1922555
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1941500]:https://launchpad.support.sap.com/#/notes/1941500
[1956005]:https://launchpad.support.sap.com/#/notes/1956005
[1973241]:https://launchpad.support.sap.com/#/notes/1973241
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2121797]:https://launchpad.support.sap.com/#/notes/2121797
[2134316]:https://launchpad.support.sap.com/#/notes/2134316
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[azure-cli]:../xplat-cli-install.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription

[dbms-guide]:virtual-machines-linux-sap-dbms-guide.md (Linux 虛擬機器 (VM) 上的 SAP NetWeaver - DBMS 部署指南) [dbms-guide-2.1]:virtual-machines-linux-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (VM 和 VHD 的快取) [dbms-guide-2.2]:virtual-machines-linux-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (軟體 RAID) [dbms-guide-2.3]:virtual-machines-linux-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure 儲存體) [dbms-guide-2]:virtual-machines-linux-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (RDBMS 部署結構) [dbms-guide-3]:virtual-machines-linux-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Azure VM 的相關高可用性和災害復原) [dbms-guide-5.5.1]:virtual-machines-linux-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 和更新版本) [dbms-guide-5.5.2]:virtual-machines-linux-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 和舊版) [dbms-guide-5.6]:virtual-machines-linux-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (使用來自 Microsoft Azure Marketplace 的 SQL Server 映像) [dbms-guide-5.8]:virtual-machines-linux-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (適用於 Azur上 SAP 的一般 SQL Server 摘要) [dbms-guide-5]:virtual-machines-linux-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (SQL Server RDBMS 專屬的詳細資料) [dbms-guide-8.4.1]:virtual-machines-linux-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (儲存體組態) [dbms-guide-8.4.2]:virtual-machines-linux-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (備份與還原) [dbms-guide-8.4.3]:virtual-machines-linux-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (備份與還原的效能考量) [dbms-guide-8.4.4]:virtual-machines-linux-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (其他) [dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-linux-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-linux-sap-deployment-guide.md (Linux 虛擬機器 (VM) 上的 SAP NetWeaver - 部署指南) [deployment-guide-2.2]:virtual-machines-linux-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP 資源) [deployment-guide-3.1.2]:virtual-machines-linux-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (使用自訂映像部署 VM) [deployment-guide-3.2]:virtual-machines-linux-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (案例 1：從 Azure Marketplace 為 SAP 部署 VM) [deployment-guide-3.3]:virtual-machines-linux-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (案例 2：使用自訂映像為 SAP 部署 VM) [deployment-guide-3.4]:virtual-machines-linux-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (案例 3：使用非一般化 Azure VHD 搭配 SAP 從內部部署環境移動 VM) [deployment-guide-3]:virtual-machines-linux-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (適用於 Microsoft Azure 上 SAP 的 VM 部署案例) [deployment-guide-4.1]:virtual-machines-linux-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (部署 Azure PowerShell Cmdlet) [deployment-guide-4.2]:virtual-machines-linux-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (下載並匯入與 SAP 相關的 PowerShell Cmdlet) [deployment-guide-4.3]:virtual-machines-linux-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (將 VM 加入內部部署網域 - 僅限 Windows) [deployment-guide-4.4.2]:virtual-machines-linux-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux) [deployment-guide-4.4]:virtual-machines-linux-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (下載、安裝和啟用 Azure VM 代理程式) [deployment-guide-4.5.1]:virtual-machines-linux-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell) [deployment-guide-4.5.2]:virtual-machines-linux-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI) [deployment-guide-4.5]:virtual-machines-linux-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (設定 Azure Enhanced Monitoring Extension for SAP) [deployment-guide-5.1]:virtual-machines-linux-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Azure Enhanced Monitoring for SAP 整備檢查) [deployment-guide-5.2]:virtual-machines-linux-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Azure 監視基礎結構組態的健康狀態檢查) [deployment-guide-5.3]:virtual-machines-linux-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (適用於 SAP 之 Azure 監視基礎結構的進一步疑難排解)

[deployment-guide-configure-monitoring-scenario-1]:virtual-machines-linux-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure Monitoring)
[deployment-guide-configure-proxy]:virtual-machines-linux-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure Proxy)
[deployment-guide-figure-100]:./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:virtual-machines-linux-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:virtual-machines-linux-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:virtual-machines-linux-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:virtual-machines-linux-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:virtual-machines-linux-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:virtual-machines-linux-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:virtual-machines-linux-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:virtual-machines-linux-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:virtual-machines-linux-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and Troubleshooting for End-to-End Monitoring Setup for SAP on Azure)

[deploy-template-cli]:../azure-resource-manager/resource-group-template-deploy.md
[deploy-template-portal]:../azure-resource-manager/resource-group-template-deploy.md
[deploy-template-powershell]:../azure-resource-manager/resource-group-template-deploy.md

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:virtual-machines-linux-sap-get-started.md
[getting-started-dbms]:virtual-machines-linux-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:virtual-machines-linux-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:virtual-machines-linux-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-linux-sap-planning-guide.md (Linux 虛擬機器 (VM) 上的 SAP NetWeaver - 規劃和實作指南) [planning-guide-1.2]:virtual-machines-linux-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (資源) [planning-guide-11.4.1]:virtual-machines-linux-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (SAP 應用程式伺服器的高可用性) [planning-guide-11.5]:virtual-machines-linux-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (對 SAP 執行個體使用自動啟動) [planning-guide-2.1]:virtual-machines-linux-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (僅限雲端 - 將虛擬機器部署到 Azure，無須倚賴內部部署客戶網路) [planning-guide-2.2]:virtual-machines-linux-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (跨單位 - 將單一或多個 SAP VM 部署到 Azure，必須完全整合到內部部署網路) [planning-guide-3.1]:virtual-machines-linux-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure 區域) [planning-guide-3.2.1]:virtual-machines-linux-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (容錯網域) [planning-guide-3.2.2]:virtual-machines-linux-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (升級網域) [planning-guide-3.2.3]:virtual-machines-linux-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Azure 可用性設定組) [planning-guide-3.2]:virtual-machines-linux-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Microsoft Azure 虛擬機器概念) [planning-guide-3.3.2]:virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure 進階儲存體) [planning-guide-5.1.1]:virtual-machines-linux-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (使用非一般化磁碟將 VM 從內部部署移至 Azure) [planning-guide-5.1.2]:virtual-machines-linux-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (使用客戶特定的映像部署 VM) [planning-guide-5.2.1]:virtual-machines-linux-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (準備使用非一般化磁碟將 VM 從內部部署移至 Azure) [planning-guide-5.2.2]:virtual-machines-linux-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (準備使用客戶特定的映像為 SAP 部署 VM) [planning-guide-5.2]:virtual-machines-linux-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (使用適用於 Azure 的 SAP 準備 VM) [planning-guide-5.3.1]:virtual-machines-linux-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Azure 磁碟與 Azure 映像之間的差異) [planning-guide-5.3.2]:virtual-machines-linux-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (將 VHD 從內部部署環境上傳至 Azure) [planning-guide-5.4.2]:virtual-machines-linux-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (在 Azure 儲存體帳戶之間複製磁碟) [planning-guide-5.5.1]:virtual-machines-linux-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (SAP 部署的 VM/VHD 結構) [planning-guide-5.5.3]:virtual-machines-linux-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (為連接的磁碟設定自動掛接) [planning-guide-7.1]:virtual-machines-linux-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (搭配 SAP NetWeaver 示範/訓練案例的單一 VM) [planning-guide-7]:virtual-machines-linux-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (SAP 執行個體的僅限雲端部署概念) [planning-guide-9.1]:virtual-machines-linux-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (適用於 SAP 的 Azure 監視解決方案) [planning-guide-azure-premium-storage]:virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure 進階儲存體)

[planning-guide-figure-100]:./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]:./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]:./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]:./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]:./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]:./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]:./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]:./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]:./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]:./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]:./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]:./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]:./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]:./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]:./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]:./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]:./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]:./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]:./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]:./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]:./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]:./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]:./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]:virtual-machines-linux-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure Networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:virtual-machines-linux-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and Data Disks)

[powershell-install-configure]:/powershell/azureps-cmdlets-docs
[resource-group-authoring-templates]:../azure-resource-manager/resource-group-authoring-templates.md
[resource-group-overview]:../azure-resource-manager/resource-group-overview.md
[resource-groups-networking]:../virtual-network/resource-groups-networking.md
[sap-pam]:https://support.sap.com/pam (SAP Product Availability Matrix)
[sap-templates-2-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]:../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]:../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]:../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]:../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]:../storage/storage-premium-storage.md
[storage-redundancy]:../storage/storage-redundancy.md
[storage-scalability-targets]:../storage/storage-scalability-targets.md
[storage-use-azcopy]:../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]:https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]:https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]:virtual-machines-linux-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../azure-resource-manager/resource-manager-deployment-model.md
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-linux-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-resource-manager-capture]:virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-raid]:virtual-machines-linux-configure-raid.md
[virtual-machines-linux-configure-lvm]:virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]:virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]:virtual-machines-linux-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-create-powershell.md
[virtual-machines-sizes]:virtual-machines-linux-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:./windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:./windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:./windows/sql/virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:./windows/sql/virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:./windows/sql/virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]:virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]:virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]:https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]:../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]:../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]:../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]:../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]:../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]:../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]:../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]:../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]:../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]:../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]:../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]:../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]:../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]:../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]:../vpn-gateway/vpn-gateway-site-to-site-create.md
[vpn-gateway-vpn-faq]:../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../xplat-cli-install.md
[xplat-cli-azure-resource-manager]:../xplat-cli-azure-resource-manager.md

藉由選擇 Microsoft Azure 作為符合 SAP 需求的雲端合作夥伴，您將能夠在可調整、相容且經企業證明的平台上，可靠地執行您的任務關鍵性 SAP 工作負載。  Azure 不僅具備調整規模的能力，而且彈性十足，可以為您節省成本。 Microsoft 與 SAP 的合作讓您可以在 Azure 的多種開發/測試及生產案例中執行 SAP 應用程式，最重要是能夠獲得完整的支援。 從 SAP NetWeaver 到 SAP S4/HANA、Linux 到 Windows，乃至於 SAP HANA 到 SQL，全部含括在內。 

透過 Microsoft Azure 虛擬機器服務，以及 Azure 大型執行個體上的 SAP HANA，Microsoft 提供了完整的「基礎結構即服務」(IaaS) 平台。 由於 Azure 上支援廣泛的 SAP 解決方案，因此這份「入門文件」將作為我們目前 SAP 文件集的「目錄」。 隨著有更多標題新增到我們文件庫中，您將在此處看到新增的標題。 

## <a name="sap-hana-certifications-on-microsoft-azure"></a>Microsoft Azure 上的 SAP HANA 認證
| SAP 產品 | 支援的 OS | Azure 供應項目 |
| --- | --- | --- |
| SAP HANA Developer Edition (包括由 SQLODBC、Windows 專用版的 ODBO、ODBC、JDBC 驅動程式、HANA Studio 及 HANA 資料庫組成的 HANA 用戶端軟體) |Red Hat Enterprise Linux、SUSE Linux Enterprise |A7、A8 |
| HANA One |Red Hat Enterprise Linux、SUSE Linux Enterprise |DS14_v2 (正式運作後) |
| SAP S/4HANA |Red Hat Enterprise Linux、SUSE Linux Enterprise |GS5 管制性運作、SAP HANA on Azure (大型執行個體) |
| Suite on HANA (OLTP) |Red Hat Enterprise Linux、SUSE Linux Enterprise |SAP HANA on Azure (大型執行個體) |
| HANA Enterprise for BW (OLAP) |Red Hat Enterprise Linux、SUSE Linux Enterprise |適用於單一節點部署的 GS5、SAP HANA on Azure (大型執行個體) |
| SAP BW/4HANA |Red Hat Enterprise Linux、SUSE Linux Enterprise |適用於單一節點部署的 GS5、SAP HANA on Azure (大型執行個體) |

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver 認證
Microsoft Azure 已獲認證可用於下列 SAP 產品，因此具備 Microsoft 和 SAP 的完整支援。

| SAP 產品 | 客體作業系統 | RDBMS | 虛擬機器類型 |
| --- | --- | --- | --- |
| SAP Business Suite 軟體 |Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux |SQL Server、Oracle (僅限 Windows)、DB2、SAP ASE |A5 至 A11、D11 至 D14、DS11 至 DS14、GS1 至 GS5 |
| SAP Business All-in-One |Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux |SQL Server、Oracle (僅限 Windows)、DB2、SAP ASE |A5 至 A11、D11 至 D14、DS11 至 DS14、GS1 至 GS5 |
| SAP BusinessObjects BI |Windows |N/A |A5 至 A11、D11 至 D14、DS11 至 DS14、GS1 至 GS5 |
| SAP NetWeaver |Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux |SQL Server、Oracle (僅限 Windows)、DB2、SAP ASE |A5 至 A11、D11 至 D14、DS11 至 DS14、GS1 至 GS5 |

## <a name="getting-started-with-sap-hana-on-azure"></a>在 Azure 上開始使用 SAP HANA
標題：在 Azure VM 上手動安裝 SAP HANA 的快速入門指南

摘要：本快速入門指南可協助藉由手動安裝 SAP NetWeaver 7.5 和 SAP HANA SP12，在 Azure VM 上設定單一執行個體 SAP HANA 原型/示範系統。 本指南假設讀者已熟悉 Azure IaaS 基本概念，像是如何透過 Azure 入口網站或 Powershell/CLI 部署虛擬機器或虛擬網路，包括使用 json 範本的選項。 此外還預期讀者已熟悉 SAP HANA、SAP NetWeaver 以及在內部部署進行安裝的方式。

更新日期：2016 年 12 月

[這裡可以找到本指南](virtual-machines-linux-sap-hana-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="overview-and-architecture-of-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (大型執行個體) 的概觀與架構
標題：SAP HANA on Azure (大型執行個體) 的概觀與架構

摘要：本《架構與技術部署指南》提供可協助您在 Azure 中的新 SAP HANA on Azure (大型執行個體) 上部署 SAP 的資訊。 它不是涵蓋 SAP 解決方案特定設定的完整指南，但是是您在初始部署和持續進行的作業中相當實用的資訊。 不應該取代與 SAP HANA 安裝相關的 SAP 文件 (或許多涵蓋該主題的＜SAP 支援附註＞)。 它提供概觀，並提供安裝 SAP HANA on Azure (大型執行個體) 的其他詳細資料。

更新日期：2016 年 12 月

[這裡可以找到本指南](./linux/sap-hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="infrastructure-and-connectivity-to-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (大型執行個體) 的基礎結構和連接
標題：SAP HANA on Azure (大型執行個體) 的基礎結構和連接

摘要︰SAP HANA on Azure (大型執行個體) 的購買在您與 Microsoft 企業帳戶小組之間完成之後，需要各種不同的網路組態以確保適當的連線。  本文概述必須與下列必要資訊共用的資訊。 本文概述必須收集哪些資訊以及必須執行哪些組態指令碼。 

更新日期：2016 年 12 月

[這裡可以找到本指南](./linux/sap-hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="install-sap-hana-on-sap-hana-on-azure-large-instances"></a>在 SAP HANA on Azure (大型執行個體) 上安裝 SAP HANA
標題：在 SAP HANA on Azure (大型執行個體) 上安裝 SAP HANA

摘要︰本文件概述在您的 Azure 大型執行個體上安裝 SAP HANA 的安裝程序。

更新日期：2016 年 12 月

[這裡可以找到本指南](./linux/sap-hana-overview-sap-hana-installation.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="high-availability-and-disaster-recovery-of-sap-hana-on-azure-large-instances"></a>SAP HANA on Azure (大型執行個體) 的高可用性和災害復原
標題：SAP HANA on Azure (大型執行個體) 的高可用性和災害復原

摘要︰高可用性 (HA) 和災害復原 (DR) 對於執行關鍵任務 SAP HANA on Azure (大型執行個體) 伺服器是非常重要的層面。 它匯入以使用 SAP、您的系統整合者和/或 Microsoft 以正確架構，並為您實作正確的 HA/DR 策略。 您必須考量您的環境特定的復原點目標 (RPO) 和復原時間目標 (RTO) 等重要考量。  本文件說明啟用您慣用層級 HA 和 DR 的選項。

更新日期：2016 年 12 月

[可以在這裡找到這份文件](./linux/sap-hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="troubleshooting-and-monitoring-of-sap-hana-on-azure-large-instances"></a>針對 SAP HANA on Azure (大型執行個體) 進行疑難排解和監視
標題：針對 SAP HANA on Azure (大型執行個體) 進行疑難排解和監視

摘要︰本指南提供建立監視 SAP HANA on Azure 環境的有用資訊，以及其他疑難排解資訊。 

更新日期：2016 年 12 月

[可以在這裡找到這份文件](./linux/sap-hana-overview-troubleshooting-monitoring.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="quickstart-guide-for-netweaver-on-suse-linux-on-azure"></a>Azure 之 SUSE Linux 上的 NetWeaver 快速入門指南
標題：在 Microsoft Azure SUSE Linux VM 上測試 SAP NetWeaver 

摘要：本文描述在 Microsoft Azure SUSE Linux 虛擬機器 (VM) 上執行 SAP NetWeaver 時應考量的各種事項。 自 2016 年 5 月 19 日起，在 Azure 的 SUSE Linux VM 上已正式支援 SAP NetWeaver。 如需有關 Linux 版本、SAP 核心版本等等的所有詳細資料，請參閱 SAP 附註 1928533＜Azure 上的 SAP 應用程式︰支援的產品和 Azure VM 類型＞。

更新時間：2016 年 9 月

[這裡可以找到本指南](virtual-machines-linux-sap-on-suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="a-name3da0389e-708b-4e82-b2a2-e92f132df89caplanning-and-implementation"></a><a name="3da0389e-708b-4e82-b2a2-e92f132df89c"></a>規劃和實作
標題：Linux 虛擬機器 (VM) 上的 SAP NetWeaver - 規劃和實作指南

摘要：如果您考慮在 Azure 虛擬機器中執行 SAP NetWeaver，那麼請先閱讀此白皮書。 此規劃和實作指南將協助您評估是否可以將現有或已規劃的 SAP NetWeaver 架構系統部署到 Azure 虛擬機器環境。 其內容涵蓋多個 SAP NetWeaver 部署案例，並包含 Azure 特有的 SAP 組態。 本白皮書列出並描述若要執行混合式 SAP 環境，SAP/Azure 端所需的所有必要設定資訊。 另外也會說明為確保 IaaS 上的 SAP NetWeaver 架構系統擁有高可用性，您所能採取的措施。

更新日期：2016 年 3 月

[這裡可以找到本指南][planning-guide]

## <a name="a-name6aadadd2-76b5-46d8-8713-e8d63630e955adeployment"></a><a name="6aadadd2-76b5-46d8-8713-e8d63630e955"></a>部署
標題：Linux 虛擬機器 (VM) 上的 SAP NetWeaver - 部署指南

摘要：本文件提供用來將 SAP NetWeaver 軟體部署到 Azure 虛擬機器的程序指引。 本白皮書將重點放在三個特定的部署案例，強調要啟用 Azure Monitoring Extensions for SAP，並包括 Azure Monitoring Extensions for SAP 的疑難排解建議。 本白皮書假設您已閱讀過規劃和實作指南。

更新日期：2016 年 3 月

[這裡可以找到本指南][deployment-guide]

## <a name="a-name1343ffe1-8021-4ce6-a08d-3a1553a4db82adbms-deployment-guide"></a><a name="1343ffe1-8021-4ce6-a08d-3a1553a4db82"></a>DBMS 部署指南
標題：Linux 虛擬機器 (VM) 上的 SAP NetWeaver - DBMS 部署指南

摘要：本白皮書涵蓋應該與 SAP 搭配執行之 DBMS 系統的規劃和實作考量。 第一個部分會列出和提出一般考量。 本白皮書的後續部分與在 Azure 中部署 SAP 所支援的不同 DBMS 有關。 所提出的不同 DBMS 分別是 SQL Server、SAP ASE 和 Oracle。 這幾個特定部分會探討當您在 Azure 上搭配這些 DBMS 來執行 SAP 系統時，所必須納入的考量。 本白皮書也會提出在搭配 SAP 應用程式使用時，Azure 上不同 DBMS 所支援的備份和高可用性方法等主題。

更新日期：2016 年 3 月

[這裡可以找到本指南][dbms-guide]


