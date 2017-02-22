---
title: "Linux 虛擬機器 (VM) 上的 SAP NetWeaver - 部署指南 | Microsoft Docs"
description: "Linux 虛擬機器 (VM) 上的 SAP NetWeaver - 部署指南"
services: virtual-machines-linux
documentationcenter: 
author: MSSedusch
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/08/2016
ms.author: sedusch
translationtype: Human Translation
ms.sourcegitcommit: 3a5e22315e338fe089cf557688f741ee16416da2
ms.openlocfilehash: 5214f55b8e65ec758ba93fb86d548b45a1b4f21e


---
# <a name="sap-netweaver-on-azure-virtual-machines-vms--deployment-guide"></a>Azure 虛擬機器 (VM) 上的 SAP NetWeaver - 部署指南
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
[2367194]:https://launchpad.support.sap.com/#/notes/2367194

[azure-cli]:../xplat-cli-install.md
[azure-portal]:https://portal.azure.com
[azure-ps]:/powershell/azureps-cmdlets-docs
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription-limits

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

[deploy-template-cli]:../azure-resource-manager/resource-group-template-deploy-cli.md
[deploy-template-portal]:../azure-resource-manager/resource-group-template-deploy-portal.md
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

[planning-guide]:virtual-machines-linux-sap-planning-guide.md (Linux 虛擬機器 (VM) 上的 SAP NetWeaver - 規劃和實作指南) [planning-guide-1.2]:virtual-machines-linux-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (資源) [planning-guide-11]:virtual-machines-linux-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Azure 虛擬機器上執行之 SAP NetWeaver 的高可用性 (HA) 和災害復原 (DR)) [planning-guide-11.4.1]:virtual-machines-linux-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (SAP 應用程式伺服器的高可用性) [planning-guide-11.5]:virtual-machines-linux-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (對 SAP 執行個體使用自動啟動) [planning-guide-2.1]:virtual-machines-linux-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (僅限雲端 - 將虛擬機器部署到 Azure，無須倚賴內部部署客戶網路) [planning-guide-2.2]:virtual-machines-linux-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (跨單位 - 將單一或多個 SAP VM 部署到 Azure，必須完全整合到內部部署網路) [planning-guide-3.1]:virtual-machines-linux-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure 區域) [planning-guide-3.2.1]:virtual-machines-linux-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (容錯網域) [planning-guide-3.2.2]:virtual-machines-linux-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (升級網域) [planning-guide-3.2.3]:virtual-machines-linux-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Azure 可用性設定組) [planning-guide-3.2]:virtual-machines-linux-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Microsoft Azure 虛擬機器概念) [planning-guide-3.3.2]:virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure 進階儲存體) [planning-guide-5.1.1]:virtual-machines-linux-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (使用非一般化磁碟將 VM 從內部部署移至 Azure) [planning-guide-5.1.2]:virtual-machines-linux-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (使用客戶特定的映像部署 VM) [planning-guide-5.2.1]:virtual-machines-linux-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (準備使用非一般化磁碟將 VM 從內部部署移至 Azure) [planning-guide-5.2.2]:virtual-machines-linux-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (準備使用客戶特定的映像為 SAP 部署 VM) [planning-guide-5.2]:virtual-machines-linux-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (使用適用於 Azure 的 SAP 準備 VM) [planning-guide-5.3.1]:virtual-machines-linux-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Azure 磁碟與 Azure 映像之間的差異) [planning-guide-5.3.2]:virtual-machines-linux-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (將 VHD 從內部部署環境上傳至 Azure) [planning-guide-5.4.2]:virtual-machines-linux-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (在 Azure 儲存體帳戶之間複製磁碟) [planning-guide-5.5.1]:virtual-machines-linux-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (SAP 部署的 VM/VHD 結構) [planning-guide-5.5.3]:virtual-machines-linux-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (為連接的磁碟設定自動掛接) [planning-guide-7.1]:virtual-machines-linux-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (搭配 SAP NetWeaver 示範/訓練案例的單一 VM) [planning-guide-7]:virtual-machines-linux-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (SAP 執行個體的僅限雲端部署概念) [planning-guide-9.1]:virtual-machines-linux-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (適用於 SAP 的 Azure 監視解決方案) [planning-guide-azure-premium-storage]:virtual-machines-linux-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure 進階儲存體)

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
[virtual-machines-linux-capture-image-resource-manager-capture]:virtual-machines-linux-capture-image.md#step-2-capture-the-vm
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
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-ps-create.md
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

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]

Microsoft Azure 可讓公司在最短的時間內取得計算和儲存體資源，而不需要冗長的採購週期。 Azure 虛擬機器可讓公司將傳統應用程式 (例如以 SAP NetWeaver 為基礎的應用程式) 部署到 Azure，並擴充其可靠性和可用性，而不需要在內部部署提供進一步的資源。 Microsoft Azure 也支援跨單位連線能力，可讓公司主動將 Azure 虛擬機器整合到其內部部署網域、私人雲端及 SAP 系統環境。

這份技術白皮書逐步說明如何準備「Azure 虛擬機器」來部署以 SAP NetWeaver 為基礎的應用程式。 它會假設您已知道 [規劃和實作指南][planning-guide] 中所含的資訊。 否則，請先閱讀這份個別文件。

本白皮書會對「SAP 安裝文件」和「SAP 附註」進行補充說明，指出用來在指定平台上安裝和部署 SAP 軟體的主要資源。

## <a name="introduction"></a>簡介
全球已有大量公司使用以 SAP NetWeaver 為基礎的應用程式 (最主要是 SAP Business Suite) 來執行其任務關鍵性商務程序。 因此，系統健康狀態是十分重要的資產，而且在故障 (包括效能事件) 時提供企業支援的能力也成為重要需求。
Microsoft Azure 提供優越的平台工具，可容納所有商務關鍵應用程式的支援需求。 本指南會確定作為 SAP 軟體部署目標的「Microsoft Azure 虛擬機器」已設定妥當，使得不論是以何種方式建立虛擬機器 (取自 Azure Marketplace 或使用客戶特定映像)，都能對其提供企業支援。
以下詳細說明所有必要的安裝步驟。

## <a name="prerequisites-and-resources"></a>必要條件和資源
### <a name="prerequisites"></a>必要條件
開始之前，請確定符合下列各章所述的必要條件。

#### <a name="local-personal-computer"></a>本機個人電腦
安裝 Azure 虛擬機器以進行 SAP Software 部署包含數個步驟。 若要管理 Windows VM 或 Linux VM，您需要使用 PowerShell 指令碼和 Microsoft Azure 入口網站。 因此，需要執行 Windows 7 或更新版本的本機個人電腦。 如果您只想要管理 Linux VM，而且想要使用 Linux 電腦進行這項工作，則也可以使用 Azure 命令列介面 (Azure CLI)。

#### <a name="internet-connection"></a>網際網路連線
若要下載和執行必要的工具和指令碼，需要網際網路連線。 此外，執行 Azure Enhanced Monitoring Extension for SAP 的「Microsoft Azure 虛擬機器」必須能夠存取網際網路。 如果此 Azure VM 是「Azure 虛擬網路」或內部部署網域的一部分，請務必如這份文件的[設定 Proxy][deployment-guide-configure-proxy] 一章所述，設定相關的 Proxy 設定。

#### <a name="microsoft-azure-subscription"></a>Microsoft Azure 訂用帳戶
Azure 帳戶已存在，並且已知相應的登入認證。

#### <a name="topology-consideration-and-networking"></a>拓撲考量和網路
需要定義 Azure 中 SAP 部署的拓撲和結構。 下列項目的結構：

* 要使用的 Microsoft Azure 儲存體帳戶
* 要部署 SAP 系統的虛擬網路
* 要部署 SAP 系統的資源群組
* 要部署 SAP 系統的 Azure 區域
* SAP 組態 (2 層或 3 層)
* VM 大小以及要掛接到 VM 的額外 VHD 數目
* SAP Transport and Correction 系統組態

這類 Azure 儲存體帳戶或 Azure 虛擬網路應該已經建立和設定。 [規劃和實作指南][planning-guide] 涵蓋了它們的建立和設定方式。

#### <a name="sap-sizing"></a>SAP 大小
* 例如，使用 SAP Quicksizer 決定預計 SAP 工作負載，並且已知相應的 SAPS 號碼 
* SAP 系統所需的 CPU 資源和記憶體消耗應該為已知
* 每秒所需的 I/O 作業應該為已知
* 不同 VM 之間的最終通訊所需的網路頻寬為已知
* 內部部署資產與已部署 Azure 的 SAP 系統之間所需的網路頻寬為已知

#### <a name="resource-groups"></a>資源群組
資源群組是新部署概念的一部分。 資源群組的所有資源都具有相同的生命週期，例如它們的建立與刪除時間都相同。 如需有關資源群組的詳細資訊，請參閱[這篇文章][resource-group-overview]。 

### <a name="a-name42ee2bdb-1efc-4ec7-ab31-fe4c22769b94asap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>SAP 資源
在進行設定工作期間，需要下列資源︰

* SAP 附註 [1928533] 包含：
  * 支援部署 SAP 軟體的「Azure 虛擬機器」大小清單 
  * 每個 Azure 虛擬機器大小的重要容量資訊
  * 支援的 SAP 軟體和 OS 與 DB 組合
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 核心版本
  
* SAP 附註 [2015553] 列出將 SAP 軟體部署到 Microsoft Azure 時 SAP 所要支援的必要條件。
* SAP 附註 [2178632] 包含針對 Microsoft Azure 上 SAP 回報的所有監視度量相關詳細資訊。 
* SAP 附註 [1409604] 包含 Microsoft Azure 上 Windows 所需的 SAP Host Agent 版本。
* SAP 附註 [2191498] 包含 Microsoft Azure 上 Linux 所需的 SAP Host Agent 版本。
* SAP 附註 [2243692] 包含 Azure 上 SAP on Linux 的授權相關資訊
* SAP 附註 [1984787] 包含 SUSE LINUX Enterprise Server 12 的一般相關資訊
* SAP 附註 [2002167] 包含 Red Hat Enterprise Linux 7.x 的一般相關資訊
* SAP 附註 [1999351] 包含適用於 SAP 的「增強型 Azure 監視」的其他疑難排解資訊。
* [SAP WIKI (英文)](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 包含適用於 Linux 的所有必要 SAP 附註
* [Azure PowerShell][azure-ps] 所包含的 SAP 特定 PowerShell Cmdlet
* [Azure CLI][azure-cli] 所包含的 SAP 特定 Azure CLI
* [Microsoft Azure 入口網站][azure-portal]

[comment]: <> (MSSedusch TODO 新增 SAP 附註 1409604 中 SAP Host Agent 的 ARM 修補程式層級)

下列指南也涵蓋 Microsoft Azure 上的 SAP 主題︰

* [Azure 虛擬機器 (VM) 上的 SAP NetWeaver - 規劃和實作指南][planning-guide]
* [Azure 虛擬機器 (VM) 上的 SAP NetWeaver - 部署指南 (本文件)][deployment-guide]
* [Azure 虛擬機器 (VM) 上的 SAP NetWeaver - DBMS 部署指南][dbms-guide]

## <a name="a-nameb3253ee3-d63b-4d74-a49b-185e76c4088eadeployment-scenarios-of-vms-for-sap-on-microsoft-azure"></a><a name="b3253ee3-d63b-4d74-a49b-185e76c4088e"></a>適用於 Microsoft Azure 上 SAP 的 VM 部署案例
在本章中，您將了解不同的部署方式以及每種部署類型的單一步驟。

### <a name="deployment-of-vms-for-sap"></a>為 SAP 部署 VM
Microsoft Azure 提供多種方法來部署 VM 和相關聯的磁碟。 因此，請務必了解這些差異，因為 VM 的準備工作可能會因部署方法而異。 一般而言，我們會探討下列案例︰

#### <a name="deploying-a-vm-out-of-the-azure-marketplace"></a>從 Azure Marketplace 部署 VM
您想要從 Azure Marketplace 使用 Microsoft 或協力廠商提供的映像來部署 VM。 在 Microsoft Azure 上部署您的 VM 之後，請遵循相同的方針和工具在 VM 內安裝 SAP 軟體，就像是在內部部署環境中一樣。 若要在 Azure VM 中安裝 SAP 軟體，SAP 和 Microsoft 建議將 SAP 安裝媒體上傳並儲存到 Azure VHD，或建立做為「檔案伺服器」並包含所有必要 SAP 安裝媒體的 Azure VM。

[comment]: <> (MSSedusch TODO 為什麼需要建議使用檔案管理 (例如檔案伺服器或 VHD)？這是否與內部部署不同？)

如需更多詳細資料，請參閱 [案例 1：從 Azure Marketplace 為 SAP 部署 VM][deployment-guide-3.2] 一章。

#### <a name="a-name3688666f-281f-425b-a312-a77e7db2dfabadeploying-a-vm-with-a-custom-image"></a><a name="3688666f-281f-425b-a312-a77e7db2dfab"></a>使用自訂映像部署 VM
因為您 OS 或 DBMS 版本的相關特定修補程式需求，所以 Azure Marketplace 所提供的映像可能不符合您的需求。 因此，您可能必須使用您自己「私人」的 OS/DB VM 映像建立 VM，之後可多次部署此映像。
Windows 與 Linux 映像之間的私人映像建立步驟不同。

- - -
> ![Windows][Logo_Windows] Windows
> 
> 若要準備可用來部署多部虛擬機器的 Windows 映像，必須在內部部署 VM 上抽象化/一般化 Windows 設定 (如 Windows SID 和主機名稱)。 這個操作可以使用 sysprep 來完成，如 <https://msdn.microsoft.com/library/hh825084.aspx> 所述。
> 
> ![Linux][Logo_Linux] Linux
> 
> 若要準備可用來部署多部虛擬機器的 Linux 映像，必須在內部部署 VM 上抽象化/一般化一些 Linux 設定。 這個操作可以使用 waagent -deprovision 來完成，如[這篇文章][virtual-machines-linux-capture-image]或[這篇文章][virtual-machines-linux-agent-user-guide-command-line-options]所述。
> 
> 

- - -
您可以藉由使用 SAP Software Provision Manager 來安裝新的 SAP 系統、從連接到虛擬機器的 VHD 還原資料庫備份，或直接從「Azure 儲存體」還原資料庫備份 (如果 DBMS 支援的話)，來設定您的資料庫內容。 (請參閱 [DBMS 部署指南][dbms-guide])。 如果您已在內部部署 VM (特別是針對 2 層系統) 中安裝 SAP 系統，您可以在部署 Azure VM 之後，透過 SAP Software Provisioning Manager 支援的「系統重新命名」程序來調整 SAP 系統設定 (SAP 附註 [1619720])。 否則，您稍後可以在部署 Azure VM 之後安裝 SAP 軟體。

如需更多詳細資料，請參閱 [案例 2：使用自訂映像為 SAP 部署 VM][deployment-guide-3.3] 一章。

#### <a name="moving-a-vm-from-on-premises-to-microsoft-azure-with-a-non-generalized-disk"></a>使用非一般化磁碟將 VM 從內部部署移至 Microsoft Azure
您想要將特定 SAP 系統從內部部署移至 Microsoft Azure。 作法是將包含 OS、SAP 二進位檔和最終 DBMS 二進位檔的 VHD，以及包含 DBMS 資料和記錄檔的 VHD 上傳至 Microsoft Azure。 相對於上面 [使用自訂映像部署 VM][deployment-guide-3.1.2] 一章所述的案例，您可以如在內部部署環境中所設定的一樣，在 Azure VM 中保留主機名稱、SAP SID 和 SAP 使用者帳戶。 因此，並不需要一般化作業系統。 此情況最適用於跨單位案例，其中 SAP 地標的一部分是在內部部署環境中執行，一部分是在 Microsoft Azure 上執行。

如需更多詳細資料，請參閱 [案例 3：使用非一般化 Azure VHD 搭配 SAP 從內部部署環境移動 VM][deployment-guide-3.4] 一章。

### <a name="a-namedb477013-9060-4602-9ad4-b0316f8bb281ascenario-1-deploying-a-vm-out-of-the-azure-marketplace-for-sap"></a><a name="db477013-9060-4602-9ad4-b0316f8bb281"></a>案例 1：從 Azure Marketplace 為 SAP 部署 VM
Microsoft Azure 可讓您從 Azure Marketplace 部署 VM 執行個體，而 Azure Marketplace 提供 Windows Server 的一些標準 OS 映像以及不同的 Linux 散發套件。 您也可以部署包含 DBMS SKU 的映像 (例如 MS SQL Server)。 如需將那些映像與 DBMS SKU 搭配使用的詳細資料，請參閱 [DBMS 部署指南][dbms-guide]。

從 Azure Marketplace 部署 VM 的 SAP 一系列特定步驟如下：

![使用 Azure Marketplace 中的 VM 映像部署適用於 SAP 系統之 VM 的流程圖][deployment-guide-figure-100]

在流程圖之後，需要執行下列步驟︰

#### <a name="create-virtual-machine-using-the-azure-portal"></a>使用 Azure 入口網站建立虛擬機器
使用 Azure Marketplace 中的映像建立新虛擬機器的最簡單方式，是透過 Azure 入口網站。 瀏覽至 <https://portal.azure.com/#create>，或按一下「Azure 入口網站」左邊的 [+]。 在搜尋欄位中輸入您想要部署的作業系統類型 (例如 Windows、SLES 或 RHEL)，然後選取版本。 請務必選取 "Resource Manager" 部署模型，然後按一下 [建立]。

此精靈會引導您完成建立虛擬機器的必要參數，以及所有必要資源 (例如網路介面或儲存體帳戶)。 其中一些參數包括︰

1. 基本概念
   1. 名稱：資源名稱 (即虛擬機器名稱)
   1. 使用者名稱和密碼/SSH 公用金鑰︰輸入在佈建期間建立之使用者的使用者名稱和密碼。 對於 Linux 虛擬機器，您也可以輸入要用來使用 SSH 登入電腦的公開 SSH 金鑰。
   1. 訂用帳戶：選取您想要用來佈建新虛擬機器的訂用帳戶。
   1. 資源群組：資源群組的名稱。 您可以插入新資源群組的名稱，或現有資源群組的名稱。
   1. 位置︰選取應該部署新虛擬機器的位置。 如果您想要將虛擬機器連接到內部部署網路，請務必選取將 Azure 連接到內部部署網路的虛擬網路位置。 如需更多詳細資料，請參閱《規劃指南》[planning-guide]中的 [Microsoft Azure 網路][planning-guide-microsoft-azure-networking]一章。
1. 大小

    如需所支援 VM 類型的清單，請參閱 SAP 附註 [1928533]。 如果您想要使用進階儲存體，也請務必選取正確的類型。 並非所有 VM 類型都支援進階儲存體。 如需詳細資訊，請參閱 [規劃指南][planning-guide] 中的[儲存體︰Microsoft Azure 儲存體和資料磁碟][planning-guide-storage-microsoft-azure-storage-and-data-disks]一章和 [Azure 進階儲存體][planning-guide-azure-premium-storage] 一章。
    
1. 設定
   1. 儲存體帳戶：選取現有的儲存體帳戶或建立新的儲存體帳戶。 如需有關不同儲存體類型的更多詳細資料，請參閱 [DBMS 指南][dbms-guide] 的 [Microsoft Azure 儲存體][dbms-guide-2.3] 章節。 請注意，執行 SAP 應用程式時並不支援所有儲存體類型。
   1. 虛擬網路和子網路：如果您想要將虛擬機器整合到內部網路，請選取連接到內部部署網路的虛擬網路。
   1. 公用 IP 位址︰選取您想要使用的公用 IP 位址，或輸入參數來建立新的公用 IP 位址。 您可以使用公用 IP 位址，透過網際網路存取您的虛擬機器。 請務必也建立網路安全性群組來篩選對您虛擬機器的存取。
   1. 網路安全性群組：如需更多詳細資料，請參閱[什麼是網路安全性群組 (NSG)][virtual-networks-nsg]。
   1. 可用性︰選取可用性設定組，或輸入參數來建立新的可用性設定組。 如需詳細資訊，請參閱 [Azure 可用性設定組][planning-guide-3.2.3] 一章。
   1. 監視︰您可以停用診斷設定。 執行命令來啟用 Azure Enhanced Monitoring 時，會自動啟用它 (如[設定監視][deployment-guide-configure-monitoring-scenario-1]一章所述)。
   
1. 摘要︰驗證摘要頁面上所提供的資訊，然後按一下 [確定]。

完成精靈之後，您的虛擬機器將會部署在您選取的資源群組中。

#### <a name="create-virtual-machine-using-a-template"></a>使用範本建立虛擬機器
您也可以使用 [azure-quickstart-templates github 儲存機制][azure-quickstart-templates-github]中所發佈的其中一個 SAP 範本來建立部署。 或者，您可以使用 [Azure 入口網站][virtual-machines-windows-tutorial]、[PowerShell][virtual-machines-ps-create-preconfigure-windows-resource-manager-vms] 或 [Azure CLI][virtual-machines-linux-tutorial] 來手動建立虛擬機器。

* [2 層組態 (僅一部虛擬機器) 範本 (sap-2-tier-marketplace-image)][sap-templates-2-tier-marketplace-image]：如果您想要僅使用一部虛擬機器來建立一個 2 層系統，請使用此範本。
* [3 層組態 (多部虛擬機器) 範本 (sap-3-tier-marketplace-image)][sap-templates-3-tier-marketplace-image]：如果您想要使用多部虛擬機器來建立一個 3 層系統，請使用此範本。

開啟上述其中一個範本之後，「Azure 入口網站」就會瀏覽至可讓您為範本輸入參數的畫面。 輸入以下資訊：

1. 基本概念
  * **訂用帳戶**：要作為範本部署目的地的訂用帳戶
  * **資源群組**：要作為範本部署目的地的資源群組。 您可以建立新的資源群組，或選取所選訂用帳戶中的現有資源群組。
  * **位置**：要作為範本部署目的地的位置。 如果您選取了現有的資源群組，則會使用該資源群組的位置。
1. 設定
  * **SAP 系統識別碼**：SAP 系統識別碼
  * **OS 類型**：您想要部署的作業系統 (例如 Windows Server 2012 R2、SLES 12 或 RHEL 7.2)
    * 此清單並未包含所有支援的作業系統，例如此清單並未包含 Windows Server 2008 R2，但 SAP 支援此作業系統。 如需所有已支援的作業系統清單，請參閱 SAP 附註 [1928533]。
  * **SAP 系統大小**：SAP 系統的大小
    * 新系統將提供的 SAP 數量。 如果您不確定系統將需要多少 SAP，請詢問您的 SAP 技術合作夥伴或系統整合者。
  * **系統可用性**：(僅限 3 層範本) 系統可用性
    * 選取適用於 HA 安裝之組態的 HA。 將為 ASCS 建立兩部資料庫伺服器和兩部伺服器。
  * **儲存體類型**：(僅限 2 層範本) 應該使用的儲存體類型
    * 對於更大的系統，強烈建議使用進階儲存體。 如需不同儲存體類型的詳細資訊，請閱讀
      * [針對 SAP DBMS 執行個體使用 Azure 進階 SSD 儲存體][2367194]
      * [DBMS 指南][dbms-guide] 的 [Microsoft Azure 儲存體][dbms-guide-2.3]
      * [進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體][storage-premium-storage-preview-portal]
      * [Microsoft Azure 儲存體簡介][storage-introduction]
  * **管理員使用者名稱**和**管理員密碼**：使用者名稱和密碼
    * 建立可用來登入電腦的新使用者。
  * **新的或現有的子網路**︰決定應該建立新的虛擬網路和子網路，還是應該使用現有的子網路。 如果您已經有連接到內部部署網路的虛擬網路，請選取現有虛擬網路。
  * **子網路識別碼**：虛擬機器應該連接的子網路識別碼。 選取將虛擬機器連接到內部部署網路之 VPN 或快速路由虛擬網路的子網路。 識別碼通常看起來像 /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>

1. 條款及條件  
    檢閱及接受法律條款。

輸入所有參數之後，請按一下 [購買] 來確認畫面。 

請注意，使用 Azure Marketplace 中的映像時，預設會部署 Azure VM 代理程式。

#### <a name="configure-proxy-settings"></a>設定 Proxy 設定
根據內部部署網路組態，如果透過 VPN 或快速路由將虛擬機器連接到您的內部部署網路，則可能需要在虛擬機器上設定 Proxy。 否則，虛擬機器可能無法存取網際網路，也因此無法下載所需的擴充功能或收集監視資料。 請參閱這份文件的[設定 Proxy][deployment-guide-configure-proxy] 一章。

#### <a name="join-domain-windows-only"></a>加入網域 (僅限 Windows)
如果 Azure 中的部署是透過「Azure 站對站」或 Express Route (在 [規劃和實作指南][planning-guide] 中也稱為「跨單位」) 連接到內部部署 AD/DNS，則 VM 應該加入內部部署網域。 關於此步驟的考量，請參閱這份文件的 [將 VM 加入內部部署網域 (僅限 Windows)][deployment-guide-4.3] 一章。

#### <a name="a-nameec323ac3-1de9-4c3a-b770-4ff701def65baconfigure-monitoring"></a><a name="ec323ac3-1de9-4c3a-b770-4ff701def65b"></a>設定監視
為了確保擁有 SAP 支援的環境，請依照這份文件的 [設定 Azure Enhanced Monitoring Extension for SAP][deployment-guide-4.5] 一章所述，設定 Azure Enhanced Monitoring Extension for SAP。

請從這份文件的 [SAP 資源][deployment-guide-2.2] 一章所列的資源中，檢查「SAP 監視」的必要條件，以找出所需的最低 SAP 核心和 SAP Host Agent 版本。

#### <a name="monitoring-check"></a>監視檢查
依照[針對 Azure 上的 SAP 進行端對端監視設定檢查及疑難排解][deployment-guide-troubleshooting-chapter]一章所述，檢查監視是否正常運作。

#### <a name="post-deployment-steps"></a>後置部署步驟
建立 VM 之後，將會部署 VM，而您則必須將所有必要的軟體元件安裝到 VM。 因此，這種類型的 VM 部署會需要所要安裝的軟體是在 Microsoft Azure 的某個其他 VM 中，或是作為可連接的磁碟。 否則，我們所探討的就會是提供了內部部署資產 (安裝共用) 連線的跨單位案例。

### <a name="a-name54a1fc6d-24fd-4feb-9c57-ac588a55dff2ascenario-2-deploying-a-vm-with-a-custom-image-for-sap"></a><a name="54a1fc6d-24fd-4feb-9c57-ac588a55dff2"></a>案例 2：使用自訂映像為 SAP 部署 VM
如 [規劃和實作指南][planning-guide] 所述，您可以準備和建立自訂映像，並使用它來建立多個新 VM。 流程圖中的一系列步驟如下︰

![使用私人 Marketplace 中的 VM 映像部署適用於 SAP 系統之 VM 的流程圖][deployment-guide-figure-300]

在流程圖之後，需要執行下列步驟︰

#### <a name="create-the-virtual-machine"></a>建立虛擬機器
若要透過「Azure 入口網站」使用私人 OS 映像來建立部署，請使用下列其中一個 SAP 範本。 這些是 [azure-quickstart-templates github 儲存機制][azure-quickstart-templates-github]上所發佈的範本。
您也可以使用 [PowerShell][virtual-machines-upload-image-windows-resource-manager] 來手動建立虛擬機器。 

* [2 層組態 (僅一部虛擬機器) 範本 (sap-2-tier-user-image)][sap-templates-2-tier-user-image]：如果您想要僅使用一部虛擬機器和您自己的 OS 映像來建立一個 2 層系統，請使用此範本。
* [3 層組態 (多部虛擬機器) 範本 (sap-3-tier-user-image)][sap-templates-3-tier-user-image]：如果您想要使用多部虛擬機器和您自己的 OS 映像來建立一個 3 層系統，請使用此範本。

開啟上述其中一個範本之後，「Azure 入口網站」就會瀏覽至可讓您為範本輸入參數的畫面。 輸入以下資訊：

1. 基本概念
  * **訂用帳戶**：要作為範本部署目的地的訂用帳戶
  * **資源群組**：要作為範本部署目的地的資源群組。 您可以建立新的資源群組，或選取所選訂用帳戶中的現有資源群組。
  * **位置**：要作為範本部署目的地的位置。 如果您選取了現有的資源群組，則會使用該資源群組的位置。
1. 設定
  * **SAP 系統識別碼**：SAP 系統識別碼
  * **OS 類型**：您想要部署的作業系統類型 (Windows 或 Linux)
  * **SAP 系統大小**：SAP 系統的大小
    * 新系統將提供的 SAP 數量。 如果您不確定系統將需要多少 SAP，請詢問您的 SAP 技術合作夥伴或系統整合者。
  * **系統可用性**：(僅限 3 層範本) 系統可用性 
    * 選取適用於 HA 安裝之組態的 HA。 將為 ASCS 建立兩部資料庫伺服器和兩部伺服器。
  * **儲存體類型**：(僅限 2 層範本) 應該使用的儲存體類型 
    * 對於更大的系統，強烈建議使用進階儲存體。 如需不同儲存體類型的詳細資訊，請閱讀 
      * [針對 SAP DBMS 執行個體使用 Azure 進階 SSD 儲存體][2367194]
      * [DBMS 指南][dbms-guide] 的 [Microsoft Azure 儲存體][dbms-guide-2.3]
      * [進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體][storage-premium-storage-preview-portal]
      * [Microsoft Azure 儲存體簡介][storage-introduction]
  * **使用者映像 VHD URI**：私人 OS 映像 VHD 的 URI (例如 https://`<accountname`>.blob.core.windows.net/vhds/userimage.vhd)
  * **使用者映像儲存體帳戶**：儲存私人 OS 映像的儲存體帳戶名稱 (例如上述範例 URI 中的 `<accountname`>)
  * **管理員使用者名稱**和**管理員密碼**：使用者名稱和密碼
    * 建立可用來登入電腦的新使用者。
  * **新的或現有的子網路**︰決定應該建立新的虛擬網路和子網路，還是應該使用現有的子網路。 如果您已經有連接到內部部署網路的虛擬網路，請選取現有虛擬網路。
  * **子網路識別碼**：虛擬機器應該連接的子網路識別碼。 選取將虛擬機器連接到內部部署網路之 VPN 或快速路由虛擬網路的子網路。 識別碼通常看起來像 /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>

1. 條款及條件  
    檢閱及接受法律條款。

輸入所有參數之後，請按一下 [購買] 來確認畫面。

#### <a name="install-vm-agent-linux-only"></a>安裝 VM 代理程式 (僅限 Linux)
如果您想要使用上述範本，必須已在使用者映像中安裝 Linux 代理程式。 否則，部署將會失敗。 請依照這份文件的 [下載、安裝和啟用 Azure VM 代理程式][deployment-guide-4.4] 一章所述，下載「VM 代理程式」並安裝在使用者映像中。
如果您未使用上述範本，則之後也可以安裝 VM 代理程式。

#### <a name="join-domain-windows-only"></a>加入網域 (僅限 Windows)
如果 Azure 中的部署是透過「Azure 站對站」或 Express Route (在 [規劃和實作指南][planning-guide] 中也稱為「跨單位」) 連接到內部部署 AD/DNS，則 VM 應該加入內部部署網域。 若要了解此步驟的考量事項，請參閱這份文件的 [將 VM 加入內部部署網域 (僅限 Windows)][deployment-guide-4.3] 一章。

#### <a name="configure-proxy-settings"></a>設定 Proxy 設定
根據內部部署網路組態，如果透過 VPN 或快速路由將虛擬機器連接到您的內部部署網路，則可能需要在虛擬機器上設定 Proxy。 否則，虛擬機器可能無法存取網際網路，因此無法下載所需的擴充功能或收集監視資料。 請參閱這份文件的[設定 Proxy][deployment-guide-configure-proxy] 一章。

#### <a name="configure-monitoring"></a>設定監視
為了確保擁有 SAP 支援的環境，請依照這份文件的 [設定 Azure Enhanced Monitoring Extension for SAP][deployment-guide-4.5] 一章所述，設定 Azure Monitoring Extension for SAP。
請從這份文件的 [SAP 資源][deployment-guide-2.2] 一章所列的資源中，檢查「SAP 監視」的必要條件，以找出所需的最低 SAP 核心和 SAP Host Agent 版本。

#### <a name="monitoring-check"></a>監視檢查
依照[針對 Azure 上的 SAP 進行端對端監視設定檢查及疑難排解][deployment-guide-troubleshooting-chapter]一章所述，檢查監視是否正常運作。

### <a name="a-namea9a60133-a763-4de8-8986-ac0fa33aa8c1ascenario-3-moving-a-vm-from-on-premises-using-a-non-generalized-azure-vhd-with-sap"></a><a name="a9a60133-a763-4de8-8986-ac0fa33aa8c1"></a>案例 3：使用非一般化 Azure VHD 搭配 SAP 從內部部署環境移動 VM
如果您打算將 SAP 系統以其目前的形式和狀態 (相同的主機名稱和 SAP SID) 從內部部署環境移到 Azure，則在部署時，VHD 就會直接作為 OS 磁碟而不是當作映像來參照。 在此情況下，進行部署時，將不會自動安裝「VM 代理程式」。 由於「VM 代理程式」和 Azure Enhanced Monitoring Extension for SAP 是獲得 SAP 支援的先決條件，因此在建立虛擬機器之後，您必須手動下載、安裝和啟用這兩個元件。 

如需有關「Azure VM 代理程式」的詳細資料，請參閱這篇文章︰

[comment]: <> (MSSedusch TODO 更新下方的 Windows 連結) 

- - -
> ![Windows][Logo_Windows] Windows
> 
> <http://blogs.msdn.com/b/wats/archive/2014/02/17/bginfo-guest-agent-extension-for-azure-vms.aspx>
> 
> ![Linux][Logo_Linux] Linux
> 
> [Azure Linux 代理程式使用者指南][virtual-machines-linux-agent-user-guide]
> 
> 

- - -
不同步驟的工作流程如下︰

![使用 VM 磁碟部署適用於 SAP 系統之 VM 的流程圖][deployment-guide-figure-400]

如果已上傳磁碟並在 Azure 中定義 (請參閱 [規劃和實作指南][planning-guide])，請依照下列步驟操作：

#### <a name="create-virtual-machine"></a>建立虛擬機器
若要透過「Azure 入口網站」使用私人 OS 磁碟來建立部署，請使用 [azure-quickstart-templates github 儲存機制][azure-quickstart-templates-github]上所發佈的 SAP 範本。 您也可以使用 [PowerShell][virtual-machines-windows-create-vm-specialized] 來手動建立虛擬機器。

* [2 層組態 (僅一部虛擬機器) 範本 (sap-2-tier-user-disk)][sap-templates-2-tier-os-disk]
  * ：如果您想要僅使用一部虛擬機器來建立一個 2 層系統，請使用此範本。

開啟上述其中一個範本之後，「Azure 入口網站」就會瀏覽至可讓您為範本輸入參數的畫面。 輸入以下資訊：

1. 基本概念
  * **訂用帳戶**：要作為範本部署目的地的訂用帳戶
  * **資源群組**：要作為範本部署目的地的資源群組。 您可以建立新的資源群組，或選取所選訂用帳戶中的現有資源群組。
  * **位置**：要作為範本部署目的地的位置。 如果您選取了現有的資源群組，則會使用該資源群組的位置。
1. 設定
  * **SAP 系統識別碼**：SAP 系統識別碼
  * **OS 類型**：您想要部署的作業系統類型 (Windows 或 Linux)
  * **SAP 系統大小**：SAP 系統的大小
    * 新系統將提供的 SAP 數量。 如果您不確定系統將需要多少 SAP，請詢問您的 SAP 技術合作夥伴或系統整合者。
  * **儲存體類型**：(僅限 2 層範本) 應該使用的儲存體類型 
    * 對於更大的系統，強烈建議使用進階儲存體。 如需不同儲存體類型的詳細資訊，請閱讀
      * [針對 SAP DBMS 執行個體使用 Azure 進階 SSD 儲存體][2367194] 
      * [DBMS 指南][dbms-guide] 的 [Microsoft Azure 儲存體][dbms-guide-2.3]
      * [進階儲存體：Azure 虛擬機器工作負載適用的高效能儲存體][storage-premium-storage-preview-portal]
      * [Microsoft Azure 儲存體簡介][storage-introduction]
  * **OS 磁碟 VHD URI**：私人 OS 磁碟的 URI (例如 https://`<accountname`>.blob.core.windows.net/vhds/osdisk.vhd)
  * **新的或現有的子網路**︰決定應該建立新的虛擬網路和子網路，還是應該使用現有的子網路。 如果您已經有連接到內部部署網路的虛擬網路，請選取現有虛擬網路。
  * **子網路識別碼**：虛擬機器應該連接的子網路識別碼。 選取將虛擬機器連接到內部部署網路之 VPN 或快速路由虛擬網路的子網路。 識別碼通常看起來像 /subscriptions/`<subscription id`>/resourceGroups/`<resource group name`>/providers/Microsoft.Network/virtualNetworks/`<virtual network name`>/subnets/`<subnet name`>

1. 條款及條件  
    檢閱及接受法律條款。

輸入所有參數之後，請按一下 [購買] 來確認畫面。

#### <a name="install-vm-agent"></a>安裝 VM 代理程式
如果您想要使用上述範本，則「VM 代理程式」必須安裝在 OS 磁碟中。 否則，部署將會失敗。 請依照這份文件的 [下載、安裝和啟用 Azure VM 代理程式][deployment-guide-4.4] 一章所述，下載「VM 代理程式」並安裝在 VM 中。

如果您未使用上述範本，則之後也可以安裝 VM 代理程式。

#### <a name="join-domain-windows-only"></a>加入網域 (僅限 Windows)
如果 Azure 中的部署是透過「Azure 站對站」或 Express Route (在 [規劃和實作指南][planning-guide] 中也稱為「跨單位」) 連接到內部部署 AD/DNS，則 VM 應該加入內部部署網域。 關於此步驟的考量，請參閱這份文件的 [將 VM 加入內部部署網域 (僅限 Windows)][deployment-guide-4.3] 一章。

#### <a name="configure-proxy-settings"></a>設定 Proxy 設定
根據內部部署網路組態，如果透過 VPN 或快速路由將虛擬機器連接到您的內部部署網路，則可能需要在虛擬機器上設定 Proxy。 否則，虛擬機器可能無法存取網際網路，因此無法下載所需的擴充功能或收集監視資料。 請參閱這份文件的[設定 Proxy][deployment-guide-configure-proxy] 一章。

#### <a name="configure-monitoring"></a>設定監視
為了確保擁有 SAP 支援的環境，請依照這份文件的 [設定 Azure Enhanced Monitoring Extension for SAP][deployment-guide-4.5] 一章所述，設定 Azure Enhanced Monitoring Extension for SAP。

請從這份文件的 [SAP 資源][deployment-guide-2.2] 一章所列的資源中，檢查「SAP 監視」的必要條件，以找出所需的最低 SAP 核心和 SAP Host Agent 版本。

#### <a name="monitoring-check"></a>監視檢查
依照[針對 Azure 上的 SAP 進行端對端監視設定檢查及疑難排解][deployment-guide-troubleshooting-chapter]一章所述，檢查監視是否正常運作。

### <a name="scenario-4-updating-the-monitoring-configuration-for-sap"></a>案例 4︰更新 SAP 的監視組態
在下列情況下，您必須更新 SAP 監視組態︰

* 聯合 MS/SAP 小組已延伸監視功能，並要求增加或減少計數器。 
* Microsoft 推出新版本的基礎 Azure 基礎結構來提供監視資料，而 Azure Enhanced Monitoring Extension for SAP 必須配合這些變更。
* 您將額外的 VHD 掛接到 Azure VM 或是移除 VHD。 在此情況下，您需要更新儲存體相關資料的集合。 如果您透過新增端點、刪除端點或將 IP 位址指派給 VM 來變更組態，則這不會影響監視組態。
* 您變更 Azure VM 的大小 (例如從 A5 變更成任何其他 VM 大小)。
* 您為 Azure VM 新增網路介面

若要更新監視組態，請依照下列方式繼續進行︰

* 依照這份文件的 [設定 Azure Enhanced Monitoring Extension for SAP][deployment-guide-4.5] 一章中所述的步驟，更新監視基礎結構。 重新執行本章所述的指令碼將會偵測到已部署監視組態，並且將執行對監視組態的必要變更。 

## <a name="detailed-single-deployment-steps"></a>詳細單一部署步驟
### <a name="a-name604bcec2-8b6e-48d2-a944-61b0f5dee2f7adeploying-azure-powershell-cmdlets"></a><a name="604bcec2-8b6e-48d2-a944-61b0f5dee2f7"></a>部署 Azure PowerShell Cmdlet
* 移至 <https://azure.microsoft.com/downloads/>
* 在＜命令列工具＞一節下方有一個稱為＜PowerShell＞的小節。 依循 [Windows 安裝] 連結進行操作。
* 所跳出的 Microsoft 下載管理員會包含結尾為 .exe 的行項目。 選取 [執行] 選項。
* 會出現快顯視窗，詢問是否要執行 Microsoft Web Platform Installer。 按 [是]。
* 即會出現與下面類似的畫面：

![適用於 Azure PowerShell Cmdlet 的安裝畫面][deployment-guide-figure-500]
<a name="figure-5"></a>

* 按 [安裝]，並接受使用者授權合約。

經常檢查是否已更新 PowerShell Cmdlet。 通常會每個月進行更新。 最簡單的做法是依照上述安裝步驟進行操作，直到出現[這張][deployment-guide-figure-5]圖所顯示的安裝畫面為止。 在此畫面中，會顯示 Cmdlet 的發行日期以及實際版本號碼。 除非 SAP 附註 [1928533] 或 [2015553] 有不同的指定，否則建議使用最新版的 Azure PowerShell Cmdlet。

使用 PS 命令，可以檢查桌上型/膝上型電腦上目前安裝的 Azure Cmdlet 版本：

```powershell
Import-Module Azure
(Get-Module Azure).Version
```

結果應該如下面[這張][deployment-guide-figure-6]圖所示。

![Azure PS Cmdlet 版本檢查的結果][deployment-guide-figure-600]
<a name="figure-6"></a>

如果桌上型/膝上型電腦上安裝的 Azure Cmdlet 版本是最新版本，則啟動 Microsoft Web Platform Installer 後的第一個畫面會與[這張][deployment-guide-figure-5]圖所示的畫面略有不同。

請注意下面這個下[圖][deployment-guide-figure-7]中紅色圈選的部分。

![Azure PowerShell Cmdlet 的安裝畫面，表示已安裝最新版本的 Azure PS Cmdlet][deployment-guide-figure-700]
<a name="figure-7"></a>

如果畫面看起來與[上面][deployment-guide-figure-7]相同 (表示已經安裝最新的 Azure Cmdlet 版本)，則不需要繼續進行安裝。 在此情況下，您可以在這個階段 [結束] 安裝。

### <a name="a-name1ded9453-1330-442a-86ea-e0fd8ae8cab3adeploying-azure-cli"></a><a name="1ded9453-1330-442a-86ea-e0fd8ae8cab3"></a>部署 Azure CLI
* 移至 <https://azure.microsoft.com/downloads/>
* 在＜命令列工具＞小節下方，有一個小節稱為＜Azure 命令列介面＞。 請遵循適用於您作業系統的 [安裝] 連結。

經常檢查是否已更新 Azure CLI。 通常會每個月進行更新。 最簡單的作法是遵循如上所述的安裝步驟。

使用下列命令，可以檢查桌上型/膝上型電腦上目前安裝的 Azure CLI 版本：

```
azure --version
```

結果應該如下面[這張][deployment-guide-figure-azure-cli-version]圖所示。

![Azure CLI 版本檢查的結果][deployment-guide-figure-760]
<a name="0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda"></a>

### <a name="a-name31d9ecd6-b136-4c73-b61e-da4a29bbc9ccajoin-vm-into-on-premises-domain-windows-only"></a><a name="31d9ecd6-b136-4c73-b61e-da4a29bbc9cc"></a>將 VM 加入內部部署網域 (僅限 Windows)
如果您將 SAP VM 部署到其中內部部署 AD 和 DNS 會延伸到 Azure 的跨單位案例，則應該將 VM 加入內部部署網域。 將 VM 加入內部部署網域的詳細步驟以及成為內部部署網域成員所需的額外軟體是依客戶而定。 通常將 VM 加入內部部署網域，即表示安裝其他軟體 (例如惡意程式碼防護軟體或者各種備份或監視軟體代理程式)。

此外，您需要確定如果在加入網域時強制使用網際網路 Proxy 設定，則來賓 VM 中的 Windows 本機系統帳戶 (S-1-5-18) 也會有這些設定。 最簡單的方式是使用適用於網域內系統的網域群組原則來強制使用 Proxy。

### <a name="a-namec7cbb0dc-52a4-49db-8e03-83e7edc2927dadownload-install-and-enable-azure-vm-agent"></a><a name="c7cbb0dc-52a4-49db-8e03-83e7edc2927d"></a>下載、安裝和啟用 Azure VM 代理程式
針對從未一般化 (例如未針對 Windows 進行 sysprep 處理) 的 OS 映像部署的虛擬機器，您必須手動下載、安裝和啟用「Azure VM 代理程式」。 

針對從 Azure Marketplace 部署的虛擬機器，則不需進行此步驟，因為這些映像已經包含「Azure VM 代理程式」。

#### <a name="a-nameb2db5c9a-a076-42c6-9835-16945868e866awindows"></a><a name="b2db5c9a-a076-42c6-9835-16945868e866"></a>Windows
* 下載 Azure VM 代理程式︰
  * 從下列位置下載 Azure VM 代理程式安裝程式套件︰<https://go.microsoft.com/fwlink/?LinkId=394789>
  * 在膝上型電腦或伺服器上本機儲存 VM 代理程式 MSI 封裝
* 安裝 Azure VM 代理程式︰
  * 使用遠端桌面 (RDP) 連接到已部署的 Azure VM
  * 在 VM 上開啟「Windows 檔案總管」視窗，然後選擇「VM 代理程式」之 MSI 檔案的目標目錄
  * 將 Azure VM 代理程式安裝程式 MSI 檔案從本機膝上型電腦/伺服器拖放到 VM 中 VM 代理程式的目標目錄
  * 按兩下 VM 中的 MSI 檔案
  * 針對加入內部部署網域的 VM，請確定最終網際網路 Proxy 設定也適用於 VM 中的「Windows 本機系統帳戶」(S-1-5-18)，如[設定 Proxy][deployment-guide-configure-proxy] 一章所述。 VM 代理程式將在此內容中執行，而且必須可以連接到 Azure。

「Azure VM 代理程式」的更新無需使用者介入。 VM 代理程式會自動自行更新，而且不需要 VM 重新開機。

#### <a name="a-name6889ff12-eaaf-4f3c-97e1-7c9edc7f7542alinux"></a><a name="6889ff12-eaaf-4f3c-97e1-7c9edc7f7542"></a>Linux
請使用下列命令來安裝適用於 Linux 的 VM 代理程式

* **SLES**

```
sudo zypper install WALinuxAgent
```
* **RHEL**

```
sudo yum install WALinuxAgent
```

請依照[這篇文章][virtual-machines-linux-update-agent]中的步驟來更新「Azure Linux 代理程式」(如果已經安裝此代理程式)。 

### <a name="a-namebaccae00-6f79-4307-ade4-40292ce4e02daconfigure-proxy"></a><a name="baccae00-6f79-4307-ade4-40292ce4e02d"></a>設定 Proxy
在 Windows 和 Linux 中，設定 Proxy 的步驟不同。

#### <a name="windows"></a>Windows
這些設定也必須適用於 LocalSystem 帳戶，才能存取網際網路。 如果您的 Proxy 設定不是透過群組原則所設定，則您可以為 LocalSystem 帳戶進行這些設定。 請遵循下列步驟：

1. 開啟 gpedit.msc
2. 瀏覽至 [電腦設定] > [系統管理範本] > [Windows 元件] > [Internet Explorer]。 請確定 [為每台電腦建立 Proxy 設定 - 而不是每個使用者] 已設定為 [已啟用]。
3. 開啟 [控制台]，然後瀏覽至 [網路和網際網路] -> [網際網路選項]
4. 開啟 [連線] 索引標籤，然後按一下 [區域網路 (LAN) 設定]
5. 停用 [自動偵測設定]
6. 啟用 [為您的 LAN 使用 Proxy 伺服器]，然後輸入 Proxy 位址和連接埠

#### <a name="linux"></a>Linux
在 Microsoft Azure 客體代理程式的組態檔 (位於 /etc/waagent.conf) 中設定正確的 Proxy。 必須設定下列參數：

```
HttpProxy.Host=<proxy host e.g. proxy.corp.local>
HttpProxy.Port=<port of the proxy host e.g. 80>
```

變更代理程式組態之後，請重新啟動代理程式

```
sudo service waagent restart
```

/etc/waagent.conf 中的 Proxy 設定也適用於所需的 VM 擴充功能。 如果您想要使用 Azure 儲存機制，請確定這些儲存機制的流量不會經過內部部署內部網路。 如果您已建立「使用者定義的路由」來啟用「強制通道」，請務必新增路由，以將傳送給儲存機制的流量直接遞送到網際網路，而不透過站對站連線。

* **SLES** - 您需要一併為 /etc/regionserverclnt.cfg 中所列的 IP 位址新增路由。 下面的螢幕擷取畫面顯示範例。 
* **RHEL** - 您需要一併為 /etc/yum.repos.d/rhui-load-balancers 中所列的主機 IP 位址新增路由。 下面的螢幕擷取畫面顯示範例。

如需有關「使用者定義的路由」的更多詳細資料，請參閱[這篇文章][virtual-networks-udr-overview]。

![強制通道][deployment-guide-figure-50]

### <a name="a-named98edcd3-f2a1-49f7-b26a-07448ceb60caaconfigure-azure-enhanced-monitoring-extension-for-sap"></a><a name="d98edcd3-f2a1-49f7-b26a-07448ceb60ca"></a>設定 Azure Enhanced Monitoring Extension for SAP
在依照 [適用於 Microsoft Azure 上 SAP 的 VM 部署案例][deployment-guide-3] 一章所述準備好 VM 之後，「Azure VM 代理程式」就會安裝在虛擬機器中。 下一個重要步驟是部署 Azure Enhanced Monitoring Extension for SAP (可從 Microsoft Azure 全球資料中心內的 Azure 擴充功能儲存機制中取得)。 如需更多詳細資料，請參閱 [規劃和實作指南][planning-guide-9.1]。 

您可以使用 Azure PowerShell 或 Azure CLI 安裝和設定 Azure Enhanced Monitoring Extension for SAP。 如果您想要使用 Windows 電腦在 Windows 或 Linux VM 上安裝該擴充功能，請參閱 [Azure PowerShell][deployment-guide-4.5.1] 一章。 若要使用 Linux 桌上型電腦在 Linux VM 上安裝該擴充功能，請參閱 [Azure CLI][deployment-guide-4.5.2] 一章。

#### <a name="a-name987cf279-d713-4b4c-8143-6b11589bb9d4aazure-powershell-for-linux-and-windows-vms"></a><a name="987cf279-d713-4b4c-8143-6b11589bb9d4"></a>適用於 Linux 和 Windows VM 的 Azure PowerShell
若要執行安裝 Azure Enhanced Monitoring Extension for SAP 的工作，請執行下列步驟︰

* 確定您已安裝最新版本的 Microsoft Azure PowerShell Cmdlet。 請參閱這份文件的 [部署 Azure PowerShell Cmdlet][deployment-guide-4.1] 一章。  
* 執行下列 PowerShell Cmdlet。
    如需可用環境的清單，請執行 Commandlet Get-AzureRmEnvironment。 如果您想要使用公用 Azure，則您的環境是 AzureCloud。 若為中國的 Azure，請選取 AzureChinaCloud。

```powershell
    $env = Get-AzureRmEnvironment -Name <name of the environment>
    Login-AzureRmAccount -Environment $env
    Set-AzureRmContext -SubscriptionName <subscription name>

    Set-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

提供您的帳戶資料和 Azure 虛擬機器之後，指令碼會部署所需的擴充功能，並啟用所需的功能。 這可能需要數分鐘的時間。
如需有關 Set-AzureRmVMAEMExtension 的詳細資訊，請參閱[這篇 MSDN 文章][msdn-set-azurermvmaemextension]。

![成功執行 SAP 特定 Azure Cmdlet Set-AzureRmVMAEMExtension 的結果畫面][deployment-guide-figure-900]

成功執行 Set-AzureRmVMAEMExtension，將會執行設定 SAP 主機監視功能所需的所有步驟。 

指令碼應該提供的輸出如下︰

* 確認已設定基底 VHD (包含 OS) 以及掛接到 VM 之所有額外 VHD 的監視組態。
* 接下來的兩則訊息將確認特定儲存體帳戶的儲存體度量組態。 
* 其中一行輸出將提供實際更新監視組態的狀態。
* 另一行將顯示已部署或更新組態的確認。
* 輸出的最後一行是參考資訊，顯示測試監視組態的可能性。
* 若要確認 Azure Enhanced Monitoring 的所有步驟是否都已成功執行，以及「Azure 基礎結構」是否提供必要的資料，請繼續進行「Azure Enhanced Monitoring Extension for SAP 整備檢查」(如這份文件的 [Azure Enhanced Monitoring for SAP 整備檢查][deployment-guide-5.1] 一章所述)。
* 若要繼續執行此動作，請等候 15-30 分鐘，直到 Azure Diagnostics 收集到相關資料。

#### <a name="a-name408f3779-f422-4413-82f8-c57a23b4fc2faazure-cli-for-linux-vms"></a><a name="408f3779-f422-4413-82f8-c57a23b4fc2f"></a>適用於 Linux VM 的 Azure CLI
為了使用 Azure CLI 來執行安裝 Azure Enhanced Monitoring Extension for SAP 的工作，請執行下列步驟︰

1. 依照[這篇][azure-cli]文章所述的方式安裝 Azure CLI
2. 使用您的 Azure 帳戶登入
   
    ```
    azure login
    ```
3. 切換到 Azure Resource Manager 模式
   
    ```
    azure config mode arm
    ```
4. 啟用 Azure Enhanced Monitoring
   
    ```
    azure vm enable-aem <resource-group-name> <vm-name>
    ```  
5. 確認 Azure Enhanced Monitoring 在 Azure Linux VM 上為作用中狀態。 檢查 /var/lib/AzureEnhancedMonitor/PerfCounters 檔案是否存在。 如果存在，請使用下列命令來顯示 AEM 所收集的資訊︰
   
    ```
    cat /var/lib/AzureEnhancedMonitor/PerfCounters
    ```
    然後您會獲得類似以下的輸出︰
   
    ```
    2;cpu;Current Hw Frequency;;0;2194.659;MHz;60;1444036656;saplnxmon;
    2;cpu;Max Hw Frequency;;0;2194.659;MHz;0;1444036656;saplnxmon;
    …
    …
    ```

## <a name="a-name564adb4f-5c95-4041-9616-6635e83a810bachecks-and-troubleshooting-for-end-to-end-monitoring-setup-for-sap-on-azure"></a><a name="564adb4f-5c95-4041-9616-6635e83a810b"></a>針對 Azure 上的 SAP 進行端對端監視設定檢查及疑難排解
部署 Azure VM 並設定相關 Azure 監視基礎結構之後，請檢查 Azure Enhanced Monitoring 的所有元件是否正確地運作。 

因此，請執行「Azure Enhanced Monitoring Extension for SAP 整備檢查」(如 [Azure Enhanced Monitoring for SAP 整備檢查][deployment-guide-5.1] 一章所述)。 如果這項檢查的結果是正向的，並且取得所有相關效能計數器，則已成功設定 Azure 監視。 在此情況下，請繼續安裝 SAP Host Agent (如這份文件的 [SAP 資源][deployment-guide-2.2] 一章所列的「SAP 附註」所述)。 如果「整備檢查」結果指出遺失計數器，請繼續執行「Azure 監視基礎結構的健康狀態檢查」(如 [Azure 監視基礎結構組態的健康狀態檢查][deployment-guide-5.2] 一章所述)。 如果發生任何「Azure 監視組態」問題，請參閱 [適用於 SAP 之 Azure 監視基礎結構的進一步疑難排解][deployment-guide-5.3] 一章，以取得進一步的疑難排解說明。

### <a name="a-namebb61ce92-8c5c-461f-8c53-39f5e5ed91f2areadiness-check-for-azure-enhanced-monitoring-for-sap"></a><a name="bb61ce92-8c5c-461f-8c53-39f5e5ed91f2"></a>Azure Enhanced Monitoring for SAP 整備檢查
運用這項檢查，您可以確定基礎 Azure 監視基礎結構完整提供 SAP 應用程式內所顯示的度量。 

#### <a name="execute-the-readiness-check-on-a-windows-vm"></a>在 Windows VM 上執行整備檢查
為了執行「整備檢查」，請登入「Azure 虛擬機器」(不一定要使用系統管理員帳戶)，然後執行下列步驟︰

* 開啟 Windows 命令提示字元，並切換到 Azure Monitoring Extension for SAP 的安裝資料夾 C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\`<version`>\drop

上方監視擴充功能路徑中所提供的版本部分可能會不同。 如果您在安裝資料夾中看到監視擴充功能版本的多個資料夾，請檢查 Windows 服務 ‘AzureEnhancedMonitoring’ 的組態，並切換至「可執行檔的路徑」所表示的資料夾。

![執行 Azure Enhanced Monitoring Extension for SAP 之服務的內容][deployment-guide-figure-1000]

* 在命令視窗中，執行不含任何參數的 azperflib.exe。

> [!NOTE]
> azperflib.exe 會以迴圈形式執行，並每隔 60 秒更新收集到的計數器。 若要完成迴圈，請關閉命令視窗。
> 
> 

如果未安裝 Azure Enhanced Monitoring Extension 或 ‘AzureEnhancedMonitoring’ 服務未執行，即表示尚未正確設定擴充功能。 在此情況下，請依照 [適用於 SAP 之 Azure 監視基礎結構的進一步疑難排解][deployment-guide-5.3] 一章，以取得如何重新部署擴充功能的詳細指示。

##### <a name="check-the-output-of-azperflibexe"></a>檢查 azperflib.exe 的輸出
azperflib.exe 輸出會顯示適用於 SAP 的所有已填入 Azure 效能計數器。 在所收集計數器清單底部，您會看到摘要和健康狀態指標，表示 Azure 監視的狀態。 

![執行 azperflib.exe 的健康狀態檢查輸出表示沒有任何問題][deployment-guide-figure-1100]
<a name="figure-11"></a>

請檢查針對回報為空白的 ‘Counters total’ 數量輸出及 ‘Health check’ 傳回的結果，如[上圖][deployment-guide-figure-11]所示。

您可以解譯結果值，如下所示︰

| Azperflib.exe 結果值 | Azure 監視健全狀態 |
| --- | --- |
| **API 呼叫 - 無法使用** | 無法使用的計數器可能是不適用於虛擬機器組態或發生錯誤 - 請查看健全狀態 | 
| **計數器總計：空白** |下列 2 個 Azure 儲存體計數器可為空白： <ul><li>儲存體讀取 Op 延遲伺服器毫秒</li><li>儲存體讀取 Op 延遲 E2E 毫秒</li></ul>所有其他計數器則都必須包含值。 |
| **健康狀態檢查** |只有在傳回狀態顯示正常時，才是正常 |
| **診斷** |健全狀態的相關詳細資訊 |

如果健康狀態檢查值不是 OK，請依照「Azure 監視基礎結構組態的健康狀態檢查」指示操作 (如下面的 [Azure 監視基礎結構組態的健康狀態檢查][deployment-guide-5.2] 一章所述)。

#### <a name="execute-the-readiness-check-on-a-linux-vm"></a>在 Linux VM 上執行整備檢查
若要執行整備檢查，請使用 SSH 連接到 Azure 虛擬機器，然後執行下列步驟︰

* 檢查 Azure Enhanced Monitoring Extension 的輸出
  * more /var/lib/AzureEnhancedMonitor/PerfCounters
    * 應該會提供效能計數器清單。 此檔案不應為空白
  * cat /var/lib/AzureEnhancedMonitor/PerfCounters | grep Error
    * 應該傳回無錯誤的一行，例如 3;config;Error;;0;0;**none**;0;1456416792;tst-servercs;
  * more /var/lib/AzureEnhancedMonitor/LatestErrorRecord
    * 應該為空白或不應該存在
* 如果上述的第一次檢查失敗，請執行這些額外測試︰
  * 確定已安裝並啟動 waagent
    * sudo ls -al /var/lib/waagent/
      * 應該列出 waagent 目錄的內容
    * ps -ax | grep waagent
      * 應該顯示與 'python /usr/sbin/waagent -daemon' 類似的一個項目
  * 確定已安裝並啟動 Linux Diagnostic 擴充功能
    * sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-*'
      * 應該列出 Linux Diagnostic Extension 目錄的內容
    * ps -ax | grep diagnostic
      * 應該顯示一個與 'python /var/lib/waagent/Microsoft.OSTCExtensions.LinuxDiagnostic-2.0.92/diagnostic.py -daemon' 類似的項目
  * 確定已安裝並啟動 Azure Enhanced Monitoring Extension
    * sudo sh -c 'ls -al /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-*/'
      * 應該列出 Azure Enhanced Monitoring Extension 目錄的內容
    * ps -ax | grep AzureEnhanced
      * 應該顯示一個與 'python /var/lib/waagent/Microsoft.OSTCExtensions.AzureEnhancedMonitorForLinux-2.0.0.2/handler.py daemon' 類似的項目
* 安裝 SAP Host Agent (如 SAP 附註 [1031096] 所述) 並檢查 saposcol 的輸出
  * 執行 /usr/sap/hostctrl/exe/saposcol -d
  * 執行 dump ccm
  * 檢查 "Virtualization_Configuration\Enhanced Monitoring Access" 度量是否為 true
* 如果您已安裝 SAP NetWeaver ABAP 應用程式伺服器，請開啟交易 ST06，並檢查是否已啟用增強監視。

如果上述任何檢查失敗，請遵循 [適用於 SAP 之 Azure 監視基礎結構的進一步疑難排解][deployment-guide-5.3] 一章，以取得如何重新部署擴充功能的詳細指示。

### <a name="a-namee2d592ff-b4ea-4a53-a91a-e5521edb6cd1ahealth-check-for-azure-monitoring-infrastructure-configuration"></a><a name="e2d592ff-b4ea-4a53-a91a-e5521edb6cd1"></a>Azure 監視基礎結構組態的健康狀態檢查
如果未正確提供部分監視資料 (如上面 [Azure Enhanced Monitoring for SAP 整備檢查][deployment-guide-5.1] 一章所述之測試所指出)，請執行 Test-AzureRmVMAEMExtension Cmdlet，以測試 SAP 之「Azure 監視」基礎結構和「監視」擴充功能的目前組態是否正確。

若要測試監視組態，請執行下列序列︰

* 確定您已安裝最新版本的 Microsoft Azure PowerShell Cmdlet (如這份文件的 [部署 Azure PowerShell Cmdlet][deployment-guide-4.1] 一章所述)。
* 執行下列 PowerShell Cmdlet。 如需可用環境的清單，請執行 Commandlet Get-AzureRmEnvironment。 如果您想要使用公用 Azure，則您的環境是 AzureCloud。 若為中國的 Azure，請選取 AzureChinaCloud。

```powershell
$env = Get-AzureRmEnvironment -Name <name of the environment>
Login-AzureRmAccount -Environment $env
Set-AzureRmContext -SubscriptionName <subscription name>
Test-AzureRmVMAEMExtension -ResourceGroupName <resource group name> -VMName <virtual machine name>
```

* 提供您的帳戶資料和 Azure 虛擬機器之後，指令碼將測試所選擇虛擬機器的組態。

![SAP 特定 Azure Cmdlet Test-VMConfigForSAP_GUI 的輸入畫面][deployment-guide-figure-1200]

輸入您帳戶和 Azure 虛擬機器的相關資訊之後，指令碼將測試所選虛擬機器的組態。

![適用於 SAP 之 Azure 監視基礎結構的成功測試輸出][deployment-guide-figure-1300]

請確定每項檢查都標示為正常。 如果部分檢查不正常，請執行更新 Cmdlet (如這份文件的 [設定 Azure Enhanced Monitoring Extension for SAP][deployment-guide-4.5] 一章所述)。 請再多等待 15 分鐘，然後重新執行 [Azure Enhanced Monitoring for SAP 整備檢查][deployment-guide-5.1] 和 [Azure 監視基礎結構組態的健康狀態檢查][deployment-guide-5.2] 章節中所述的檢查。 如果檢查仍然指出部分或所有計數器有問題，請繼續進行 [適用於 SAP 之 Azure 監視基礎結構的進一步疑難排解][deployment-guide-5.3] 一章。

### <a name="a-namefe25a7da-4e4e-4388-8907-8abc2d33cfd8afurther-troubleshooting-of-azure-monitoring-infrastructure-for-sap"></a><a name="fe25a7da-4e4e-4388-8907-8abc2d33cfd8"></a>適用於 SAP 之 Azure 監視基礎結構的進一步疑難排解
#### <a name="windowslogowindows-azure-performance-counters-do-not-show-up-at-all"></a>![Windows][Logo_Windows] 完全未顯示 Azure 效能計數器
Windows 服務 ‘AzureEnhancedMonitoring’ 會收集 Azure 上的效能度量。 如果尚未正確安裝服務，或未在 VM 中執行服務，則根本不會收集任何效能度量。

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Azure Enhanced Monitoring Extension 的安裝目錄是空白
###### <a name="issue"></a>問題
安裝目錄 C:\Packages\Plugins\Microsoft.AzureCAT.AzureEnhancedMonitoring.AzureCATExtensionHandler\\`<version`>\drop 是空的。

###### <a name="solution"></a>方案
未安裝此擴充功能。 請檢查是否為 Proxy 問題 (如前所述)。 您可能需要重新啟動電腦，並 (或) 重新執行組態指令碼 Set-AzureRmVMAEMExtension。

##### <a name="service-for-azure-enhanced-monitoring-does-not-exist"></a>Azure Enhanced Monitoring 的服務不存在
###### <a name="issue"></a>問題
Windows 服務 ‘AzureEnhancedMonitoring’ 不存在。 Azperflib.exe：azperlib.exe 輸出擲回錯誤 (如[下圖][deployment-guide-figure-14]所示)。

![執行 azperflib.exe 即表示 Azure Enhanced Monitoring Extension for SAP 的服務未執行][deployment-guide-figure-1400]
<a name="figure-14"></a>

###### <a name="solution"></a>方案
如果服務不存在 (如[上圖][deployment-guide-figure-14]所示)，即表示尚未正確安裝 Azure Monitoring Extension for SAP。 請根據 [適用於 Microsoft Azure 上 SAP 的 VM 部署案例][deployment-guide-3] 一章中針對您部署案例所述的步驟，重新部署此擴充功能。 

部署此擴充功能之後，請在 1 個小時之後重新檢查 Azure VM 內是否提供 Azure 效能計數器。

##### <a name="service-for-azure-enhanced-monitoring-exists-but-fails-to-start"></a>Azure Enhanced Monitoring 的服務存在，但無法啟動
###### <a name="issue"></a>問題
Windows 服務 ‘AzureEnhancedMonitoring’ 存在並已啟用，但無法啟動。 如需詳細資訊，請參閱應用程式事件記錄檔。

###### <a name="solution"></a>方案
組態錯誤。 如 [設定 Azure Enhanced Monitoring Extension for SAP][deployment-guide-4.5] 一章所述，重新啟用 VM 的監視擴充功能。

#### <a name="windowslogowindows-some-azure-performance-counters-are-missing"></a>![Windows][Logo_Windows] 遺失部分 Azure 效能計數器
Azure 上效能度量的收集工作是由 Windows 服務 ‘AzureEnhancedMonitoring’ 執行，它會從數個來源取得資料。 有些組態資料是在本機進行收集、效能度量是讀取自「Azure 診斷」，而儲存體計數器的使用則是從您在儲存體訂用帳戶層級登入開始。

如果使用 SAP 附註 [1999351] 進行排解疑難沒有任何幫助，請重新執行組態指令碼 Set-AzureRmVMAEMExtension。 因為儲存體分析或診斷計數器在啟用後可能未立即建立，所以您可能必須等待一個小時的時間。 如果問題仍然存在，請在元件 BC-OP-NT-AZR (適用於 Windows) 或 BC-OP-LNX-AZR (適用於 Linux 虛擬機器) 上開啟 SAP 客戶支援訊息。

#### <a name="linuxlogolinux-azure-performance-counters-do-not-show-up-at-all"></a>![Linux][Logo_Linux] 完全未顯示 Azure 效能計數器
Deamon 會收集 Azure 上的效能度量。 如果未執行 Deamon，則根本不會收集任何效能度量。

##### <a name="the-installation-directory-of-the-azure-enhanced-monitoring-extension-is-empty"></a>Azure Enhanced Monitoring Extension 的安裝目錄是空白
###### <a name="issue"></a>問題
/var/lib/waagent/ 目錄未包含 Azure Enhanced Monitoring Extension 的子目錄。

###### <a name="solution"></a>方案
未安裝此擴充功能。 請檢查是否為 Proxy 問題 (如前所述)。 您可能需要重新啟動電腦，並 (或) 重新執行組態指令碼 Set-AzureRmVMAEMExtension。

#### <a name="linuxlogolinux-some-azure-performance-counters-are-missing"></a>![Linux][Logo_Linux] 遺失部分 Azure 效能計數器
從數個來源取得資料的 Deamon 會收集 Azure 上的效能度量。 有些組態資料是在本機進行收集、效能度量是讀取自「Azure 診斷」，而儲存體計數器的使用則是從您在儲存體訂用帳戶層級登入開始。

如需完整且最新的已知問題清單，請參閱 SAP 附註 [1999351] ，當中包含適用於 SAP 的「增強型 Azure 監視」的其他疑難排解資訊。

如果使用 SAP 附註 [1999351] 進行排解疑難沒有任何幫助，請重新執行組態指令碼 Set-AzureRmVMAEMExtension (如 [設定 Azure Enhanced Monitoring Extension for SAP][deployment-guide-4.5] 一章所述)。 因為儲存體分析或診斷計數器在啟用後可能未立即建立，所以您可能必須等待一個小時的時間。 如果問題仍然存在，請在元件 BC-OP-NT-AZR (適用於 Windows) 或 BC-OP-LNX-AZR (適用於 Linux 虛擬機器) 上開啟 SAP 客戶支援訊息。




<!--HONumber=Nov16_HO5-->


