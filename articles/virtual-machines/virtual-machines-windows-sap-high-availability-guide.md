---
title: Windows 虛擬機器 (VM) 上的 SAP NetWeaver - 高可用性指南 | Microsoft Docs
description: Windows 虛擬機器上的 SAP NetWeaver 高可用性指南
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: goraco
manager: timlt
editor: ''
tags: azure-resource-manager
keywords: ''

ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/18/2016
ms.author: goraco

---
# <a name="sap-netweaver-on-windows-virtual-machines-(vms)---high-availability-guide"></a>Windows 虛擬機器 (VM) 上的 SAP NetWeaver - 高可用性指南
[767598]:https://service.sap.com/sap/support/notes/767598
[773830]:https://service.sap.com/sap/support/notes/773830
[826037]:https://service.sap.com/sap/support/notes/826037
[965908]:https://service.sap.com/sap/support/notes/965908
[1031096]:https://service.sap.com/sap/support/notes/1031096
[1139904]:https://service.sap.com/sap/support/notes/1139904
[1173395]:https://service.sap.com/sap/support/notes/1173395
[1245200]:https://service.sap.com/sap/support/notes/1245200
[1409604]:https://service.sap.com/sap/support/notes/1409604
[1558958]:https://service.sap.com/sap/support/notes/1558958
[1585981]:https://service.sap.com/sap/support/notes/1585981
[1588316]:https://service.sap.com/sap/support/notes/1588316
[1590719]:https://service.sap.com/sap/support/notes/1590719
[1597355]:https://service.sap.com/sap/support/notes/1597355
[1605680]:https://service.sap.com/sap/support/notes/1605680
[1619720]:https://service.sap.com/sap/support/notes/1619720
[1619726]:https://service.sap.com/sap/support/notes/1619726
[1619967]:https://service.sap.com/sap/support/notes/1619967
[1750510]:https://service.sap.com/sap/support/notes/1750510
[1752266]:https://service.sap.com/sap/support/notes/1752266
[1757924]:https://service.sap.com/sap/support/notes/1757924
[1757928]:https://service.sap.com/sap/support/notes/1757928
[1758182]:https://service.sap.com/sap/support/notes/1758182
[1758496]:https://service.sap.com/sap/support/notes/1758496
[1772688]:https://service.sap.com/sap/support/notes/1772688
[1814258]:https://service.sap.com/sap/support/notes/1814258
[1882376]:https://service.sap.com/sap/support/notes/1882376
[1909114]:https://service.sap.com/sap/support/notes/1909114
[1922555]:https://service.sap.com/sap/support/notes/1922555
[1928533]:https://service.sap.com/sap/support/notes/1928533
[1941500]:https://service.sap.com/sap/support/notes/1941500
[1956005]:https://service.sap.com/sap/support/notes/1956005
[1973241]:https://service.sap.com/sap/support/notes/1973241
[1984787]:https://service.sap.com/sap/support/notes/1984787
[1999351]:https://service.sap.com/sap/support/notes/1999351
[2002167]:https://service.sap.com/sap/support/notes/2002167
[2015553]:https://service.sap.com/sap/support/notes/2015553
[2039619]:https://service.sap.com/sap/support/notes/2039619
[2121797]:https://service.sap.com/sap/support/notes/2121797
[2134316]:https://service.sap.com/sap/support/notes/2134316
[2178632]:https://service.sap.com/sap/support/notes/2178632
[2191498]:https://service.sap.com/sap/support/notes/2191498
[2233094]:https://service.sap.com/sap/support/notes/2233094
[2243692]:https://service.sap.com/sap/support/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-cli]:../xplat-cli-install.md
[azure-portal]:https://portal.azure.com
[azure-ps]:../powershell-install-configure.md
[azure-quickstart-templates-github]:https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]:https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]:../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]:../azure-subscription-service-limits.md#subscription

[dbms-guide]:virtual-machines-windows-sap-dbms-guide.md (Windows 虛擬機器 (VM) 上的 SAP NetWeaver - DBMS 部署指南) [dbms-guide-2.1]:virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f (VM 和 VHD 的快取) [dbms-guide-2.2]:virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 (軟體 RAID) [dbms-guide-2.3]:virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 (Microsoft Azure 儲存體) [dbms-guide-2]:virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 (RDBMS 部署結構) [dbms-guide-3]:virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 (Azure VM 的相關高可用性和災害復原) [dbms-guide-5.5.1]:virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 (SQL Server 2012 SP1 CU4 和更新版本) [dbms-guide-5.5.2]:virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b (SQL Server 2012 SP1 CU3 和舊版) [dbms-guide-5.6]:virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 (使用來自 Microsoft Azure Marketplace 的 SQL Server 映像) [dbms-guide-5.8]:virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 (適用於 Azure 上 SAP 的一般 SQL Server 摘要) [dbms-guide-5]:virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 (SQL Server RDBMS 專屬的詳細資料) [dbms-guide-8.4.1]:virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 (儲存體組態) [dbms-guide-8.4.2]:virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d (備份與還原) [dbms-guide-8.4.3]:virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c (備份與還原的效能考量) [dbms-guide-8.4.4]:virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 (其他) [dbms-guide-900-sap-cache-server-on-premises]:virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]:./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]:./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]:./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]:./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]:./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]:./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]:./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]:./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]:./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]:virtual-machines-windows-sap-deployment-guide.md (Windows 虛擬機器 (VM) 上的 SAP NetWeaver - 部署指南) [deployment-guide-2.2]:virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP 資源) [deployment-guide-3.1.2]:virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab (使用自訂映像部署 VM) [deployment-guide-3.2]:virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 (案例 1：從 Azure Marketplace 為 SAP 部署 VM) [deployment-guide-3.3]:virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 (案例 2：使用自訂映像為 SAP 部署 VM) [deployment-guide-3.4]:virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 (案例 3：使用非一般化 Azure VHD 搭配 SAP 從內部部署環境移動 VM) [deployment-guide-3]:virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (適用於 Microsoft Azure 上 SAP 的 VM 部署案例) [deployment-guide-4.1]:virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 (部署 Azure PowerShell Cmdlet) [deployment-guide-4.2]:virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e (下載並匯入與 SAP 相關的 PowerShell Cmdlet) [deployment-guide-4.3]:virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc (將 VM 加入內部部署網域 - 僅限 Windows) [deployment-guide-4.4.2]:virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 (Linux) [deployment-guide-4.4]:virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d (下載、安裝和啟用 Azure VM 代理程式) [deployment-guide-4.5.1]:virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 (Azure PowerShell) [deployment-guide-4.5.2]:virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f (Azure CLI) [deployment-guide-4.5]:virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca (設定 Azure Enhanced Monitoring Extension for SAP) [deployment-guide-5.1]:virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 (Azure Enhanced Monitoring for SAP 整備檢查) [deployment-guide-5.2]:virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 (Health check for Azure Monitoring Infrastructure Configuration) [deployment-guide-5.3]:virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 (適用於 SAP 之 Azure 監視基礎結構的進一步疑難排解)

[deployment-guide-configure-monitoring-scenario-1]:virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b (Configure Monitoring)
[deployment-guide-configure-proxy]:virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d (Configure Proxy)
[deployment-guide-figure-100]:./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]:./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]:virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]:./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]:./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]:./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]:virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]:./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]:./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]:./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]:virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]:./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]:./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]:virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]:./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]:virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]:./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]:./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]:./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]:virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]:virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]:virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]:virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b (Checks and Troubleshooting for End-to-End Monitoring Setup for SAP on Azure)

[deploy-template-cli]:../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]:../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]:../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]:http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:virtual-machines-windows-sap-get-started.md
[getting-started-dbms]:virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]:virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]:virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]:virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]:virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]:virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]:virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]:virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]:virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]:http://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]:virtual-machines-windows-sap-high-availability-guide.md

[install-extension-cli]:virtual-machines-linux-enable-aem.md

[Logo_Linux]:./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]:https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]:virtual-machines-windows-sap-planning-guide.md (Windows 虛擬機器 (VM) 上的 SAP NetWeaver - 規劃和實作指南) [planning-guide-1.2]:virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff (資源) [planning-guide-11]:virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 (Azure 虛擬機器上執行之 SAP NetWeaver 的高可用性 (HA) 和災害復原 (DR)) [planning-guide-11.4.1]:virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 (SAP 應用程式伺服器的高可用性) [planning-guide-11.5]:virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f (對 SAP 執行個體使用自動啟動) [planning-guide-2.1]:virtual-machines-windows-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 (僅限雲端 - 將虛擬機器部署到 Azure，無須倚賴內部部署客戶網路) [planning-guide-2.2]:virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 (跨單位 - 將單一或多個 SAP VM 部署到 Azure，必須完全整合到內部部署網路) [planning-guide-3.1]:virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a (Azure Regions) [planning-guide-3.2.1]:virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 (容錯網域) [planning-guide-3.2.2]:virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 (升級網域) [planning-guide-3.2.3]:virtual-machines-windows-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 (Azure 可用性設定組) [planning-guide-3.2]:virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 (Microsoft Azure 虛擬機器概念) [planning-guide-3.3.2]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure 進階儲存體) [planning-guide-5.1.1]:virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 (使用非一般化磁碟將 VM 從內部部署移至 Azure) [planning-guide-5.1.2]:virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c (使用客戶特定的映像部署 VM) [planning-guide-5.2.1]:virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef (準備使用非一般化磁碟將 VM 從內部部署移至 Azure) [planning-guide-5.2.2]:virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 (準備使用客戶特定的映像為 SAP 部署 VM) [planning-guide-5.2]:virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 (使用適用於 Azure 的 SAP 準備 VM) [planning-guide-5.3.1]:virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 (Azure 磁碟與 Azure 映像之間的差異) [planning-guide-5.3.2]:virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a (將 VHD 從內部部署環境上傳至 Azure) [planning-guide-5.4.2]:virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 (在 Azure 儲存體帳戶之間複製磁碟) [planning-guide-5.5.1]:virtual-machines-windows-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 (SAP 部署的 VM/VHD 結構) [planning-guide-5.5.3]:virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d (為連接的磁碟設定自動掛接) [planning-guide-7.1]:virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 (搭配 SAP NetWeaver 示範/訓練案例的單一 VM) [planning-guide-7]:virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 (SAP 執行個體的僅限雲端部署概念) [planning-guide-9.1]:virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (適用於 SAP 的 Azure 監視解決方案) [planning-guide-azure-premium-storage]:virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 (Azure 進階儲存體)

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
[planning-guide-microsoft-azure-networking]:virtual-machines-windows-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd (Microsoft Azure Networking)
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:virtual-machines-windows-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f (Storage: Microsoft Azure Storage and Data Disks)

[sap-ha-guide]:virtual-machines-windows-sap-high-availability-guide.md (High-availability SAP NetWeaver on Windows virtual machines)
[sap-ha-guide-1]:virtual-machines-windows-sap-high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c (Prerequisites)
[sap-ha-guide-2]:virtual-machines-windows-sap-high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08 (Resources)
[sap-ha-guide-3]:virtual-machines-windows-sap-high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1 (High-availability SAP with Azure Resource Manager vs. the classic deployment model)
[sap-ha-guide-3.1]:virtual-machines-windows-sap-high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686 (Resource groups)
[sap-ha-guide-3.2]:virtual-machines-windows-sap-high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860 (Clustering with Azure Resource Manager vs. the classic deployment model)
[sap-ha-guide-4]:virtual-machines-windows-sap-high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7 (Windows Server Failover Clustering)
[sap-ha-guide-4.1]:virtual-machines-windows-sap-high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2 (Quorum modes)
[sap-ha-guide-5]:virtual-machines-windows-sap-high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275 (Windows Server Failover Clustering on-premises)
[sap-ha-guide-5.1]:virtual-machines-windows-sap-high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592 (Shared storage)
[sap-ha-guide-5.2]:virtual-machines-windows-sap-high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd (Networking and name resolution)
[sap-ha-guide-6]:virtual-machines-windows-sap-high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a (Windows Server Failover Clustering in Azure)
[sap-ha-guide-6.1]:virtual-machines-windows-sap-high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341 (Shared disk in Azure with SIOS DataKeeper)
[sap-ha-guide-6.2]:virtual-machines-windows-sap-high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb (Name resolution in Azure)
[sap-ha-guide-7]:virtual-machines-windows-sap-high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa (Azure 基礎結構即服務 (IaaS) 中的 SAP NetWeaver 高可用性) [sap-ha-guide-7.1]:virtual-machines-windows-sap-high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e (高可用性 SAP 應用程式伺服器) [sap-ha-guide-7.2]:virtual-machines-windows-sap-high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db (SAP ASCS/SCS 執行個體的高可用性) [sap-ha-guide-7.2.1]:virtual-machines-windows-sap-high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51 (在 Azure 中使用 Windows Server 容錯叢集的高可用性 SAP ASCS/SCS 執行個體) [sap-ha-guide-7.3]:virtual-machines-windows-sap-high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027 (高可用性 DBMS 執行個體) [sap-ha-guide-7.4]:virtual-machines-windows-sap-high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816 (端對端高可用性部署案例) [sap-ha-guide-8]:virtual-machines-windows-sap-high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf (準備基礎結構) [sap-ha-guide-8.1]:virtual-machines-windows-sap-high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489 (部署具有公司網路連線能力 (跨單位) 的虛擬機器以供生產環境使用) [sap-ha-guide-8.2]:virtual-machines-windows-sap-high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310 (用於測試和示範的 SAP 執行個體僅限雲端部署) [sap-ha-guide-8.3]:virtual-machines-windows-sap-high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a (Azure 虛擬網路) [sap-ha-guide-8.4]:virtual-machines-windows-sap-high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e (DNS IP 位址) [sap-ha-guide-8.5]:virtual-machines-windows-sap-high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f (SAP ASCS/SCS 叢集執行個體及 DBMS 叢集執行個體的主機名稱和靜態 IP 位址) [sap-ha-guide-8.6]:virtual-machines-windows-sap-high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30 (設定 SAP 虛擬機器的靜態 IP 位址) [sap-ha-guide-8.7]:virtual-machines-windows-sap-high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e (建立內部負載平衡器的靜態 IP 位址) [sap-ha-guide-8.8]:virtual-machines-windows-sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c (Azure 內部負載平衡器的預設 ASCS/SCS 負載平衡規則) [sap-ha-guide-8.9]:virtual-machines-windows-sap-high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716 (變更 Azure 內部負載平衡器的 ASCS/SCS 預設負載平衡規則) [sap-ha-guide-8.10]:virtual-machines-windows-sap-high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c (將 Windows 虛擬機器新增到網域) [sap-ha-guide-8.11]:virtual-machines-windows-sap-high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158 (在用於 SAP ASCS/SCS 執行個體的兩個叢集節點上都新增登錄項目) [sap-ha-guide-8.12]:virtual-machines-windows-sap-high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab (SAP ASCS/SCS 執行個體的 Windows Server 容錯移轉叢集設定) [sap-ha-guide-8.12.1]:virtual-machines-windows-sap-high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79 (收集叢集組態中的叢集節點) [sap-ha-guide-8.12.2]:virtual-machines-windows-sap-high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca (設定叢集檔案共用見證) [sap-ha-guide-8.12.2.1]:virtual-machines-windows-sap-high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855 (建立檔案共用) [sap-ha-guide-8.12.2.2]:virtual-machines-windows-sap-high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d (在容錯移轉叢集管理員中設定檔案共用見證) [sap-ha-guide-8.12.3]:virtual-machines-windows-sap-high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868 (為 SAP ASCS/SCS 叢集共用磁碟安裝 SIOS DataKeeper Cluster Edition) [sap-ha-guide-8.12.3.1]:virtual-machines-windows-sap-high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3 (新增 .NET Framework 3.5) [sap-ha-guide-8.12.3.2]:virtual-machines-windows-sap-high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102 (安裝 SIOS DataKeeper) [sap-ha-guide-8.12.3.3]:virtual-machines-windows-sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 (設定 SIOS DataKeeper) [sap-ha-guide-9]:virtual-machines-windows-sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b (安裝 SAP NetWeaver 系統) [sap-ha-guide-9.1]:virtual-machines-windows-sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 (使用高可用性 ASCS/SCS 執行個體安裝 SAP) [sap-ha-guide-9.1.1]:virtual-machines-windows-sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 (為叢集 SAP ASCS/SCS 執行個體建立虛擬主機名稱) [sap-ha-guide-9.1.2]:virtual-machines-windows-sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 (安裝 SAP 的第一個叢集節點) [sap-ha-guide-9.1.3]:virtual-machines-windows-sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 (修改 ASCS/SCS 執行個體的 SAP 設定檔) [sap-ha-guide-9.1.4]:virtual-machines-windows-sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 (新增探查連接埠) [sap-ha-guide-9.2]:virtual-machines-windows-sap-high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7 (安裝資料庫執行個體) [sap-ha-guide-9.3]:virtual-machines-windows-sap-high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748 (Install the second cluster node) [sap-ha-guide-9.4]:virtual-machines-windows-sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a (變更 SAP ERS Windows 服務執行個體的啟動類型) [sap-ha-guide-9.5]:virtual-machines-windows-sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 (安裝 SAP 主要應用程式伺服器) [sap-ha-guide-9.6]:virtual-machines-windows-sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 (安裝 SAP 其他應用程式伺服器) [sap-ha-guide-10]:virtual-machines-windows-sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9 (測試 SAP ASCS/SCS 執行個體容錯移轉和 SIOS 複寫) [sap-ha-guide-10.1]:virtual-machines-windows-sap-high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445 (SAP ASCS/SCS 執行個體在叢集節點 A 上執行) [sap-ha-guide-10.2]:virtual-machines-windows-sap-high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916 (從節點 A 到節點 B 進行容錯移轉) [sap-ha-guide-10.3]:virtual-machines-windows-sap-high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5 (SAP ASCS/SCS 執行個體在叢集節點 B 上執行))


[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png


[powershell-install-configure]:../powershell-install-configure.md
[resource-group-authoring-templates]:../resource-group-authoring-templates.md
[resource-group-overview]:../resource-group-overview.md
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
[virtual-machines-windows-attach-disk-portal]:virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]:../resource-manager-deployment-model.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../resource-manager-deployment-model.md#benefits-of-using-resource-manager-and-resource-groups
[virtual-machines-azurerm-versus-azuresm]:virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]:virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]:virtual-machines-linux-cli-deploy-templates.md (Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI)
[virtual-machines-deploy-rmtemplates-powershell]:virtual-machines-windows-ps-manage.md (Manage virtual machines using Azure Resource Manager and PowerShell)
[virtual-machines-linux-agent-user-guide]:virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]:virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]:virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]:virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]:virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]:virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]:virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-configure-raid]:virtual-machines-linux-configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]:virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]:virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]:virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]:virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]:virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]:virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]:virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]:virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]:virtual-machines-windows-ps-create.md
[virtual-machines-sizes]:virtual-machines-windows-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]:virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]:virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]:virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]:virtual-machines-windows-portal-sql-alwayson-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]:virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]:virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]:virtual-machines-windows-sql-performance.md
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
[vpn-gateway-site-to-site-create]:../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]:../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]:../xplat-cli-install.md
[xplat-cli-azure-resource-manager]:../xplat-cli-azure-resource-manager.md


對於需要在最短的時間內處理計算、儲存和網路資源，而不需要漫長的採購週期的組織而言，「Azure 虛擬機器」是適用的解決方案。 您可以使用「Azure 虛擬機器」以部署傳統的應用程式，例如 SAP NetWeaver 架構 ABAP、Java，以及 ABAP+Java 堆疊。 擴充的可靠性與可用性，無須其他內部部署資源。 由於「Azure 虛擬機器」支援跨單位連線能力，貴公司可將「Azure 虛擬機器」整合到其內部部署網域、私人雲端，以及 SAP 系統環境中。

在本文中，我們將探討使用新的 Azure Resource Manager 部署模型，在 Azure 中部署高可用性 SAP 系統時可採取的步驟。 我們會引導您完成這些主要工作：

* 尋找正確的 SAP 安裝指南和附註，列在 [資源][sap-ha-guide-2] 一節中。 本文補充 SAP 安裝文件及 SAP 附註，其為有助於在特定平台上安裝及部署 SAP 軟體的主要資源。
* 了解 Azure 傳統部署模型與 Azure Resource Manager 部署模型之間的差異。
* 了解「Windows Server 容錯移轉叢集」仲裁模式，以便選取適合您 Azure 部署的模型。
* 了解 Azure 服務中的「Windows Server 容錯移轉叢集」共用儲存體。
* 了解如何在 Azure 中保護單一失敗點元件，例如 ABAP SAP Central Services (ASCS)/SAP Central Services (SCS) 和資料庫管理系統 (DBMS)，以及備援元件，例如 SAP 應用程式伺服器。
* 藉由使用 Azure 做為平台，並透過 Azure Resource Manager，遵循在 Windows Server 容錯移轉叢集的叢集中安裝及設定高可用性 SAP 系統的逐步說明範例。
* 深入了解在 Azure 中使用 Windows Server 容錯移轉叢集所需的步驟，但內部部署不需要這些步驟。

為了簡化部署和設定，在本文中，我們使用新的 SAP 三層式高可用性 Resource Manager 範本。 此範本自動部署高可用性 SAP 系統所需的整個基礎結構。 基礎結構也支援 SAPS 調整您 SAP 系統的大小。

[!INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

## <a name="<a-name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a>-prerequisites"></a><a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> 必要條件
開始之前，請確定符合下列各節所述的必要條件。 此外，請務必檢查[資源][sap-ha-guide-2]一節中列出的所有資源。

在本文中，我們針對[三層式 SAP NetWeaver](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/) 使用 Azure Resource Manager 範本。 如需範本的實用概觀，請參閱 [SAP Azure Resource Manager 範本](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)。

## <a name="<a-name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a>-resources"></a><a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> 資源
這些指南也涵蓋 Azure 中的 SAP 部署：

* [Windows 虛擬機器 (VM) 上的 SAP NetWeaver - 規劃和實作指南][planning-guide]
* [Windows 虛擬機器 (VM) 上的 SAP NetWeaver - 部署指南][deployment-guide]
* [Windows 虛擬機器 (VM) 上的 SAP NetWeaver - DBMS 部署指南][dbms-guide]
* [Windows 虛擬機器 (VM) 上的 SAP NetWeaver - 高可用性指南 [本指南] ][sap-ha-guide]

> [!NOTE]
> 文件中會儘可能使用參考的 SAP 安裝指南連結 (請參閱 [SAP 安裝指南][sap-installation-guides])。 如需安裝程序的必要條件和相關資訊，建議您詳細閱讀 SAP NetWeaver 安裝指南。 本文僅涵蓋可與 Azure 虛擬機器搭配使用之 SAP NetWeaver 架構系統的特定工作。
> 
> 

下列 SAP 附註與 Azure 上的 SAP 主題相關：

| 附註編號 | 課程名稱 |
| --- | --- |
| [1928533] |Azure 上的 SAP 應用程式︰支援的產品和大小 |
| [2015553] |Microsoft Azure 上的 SAP：支援的必要條件 |
| [1999351] |適用於 SAP 的增強型 Azure 監視功能 |
| [2178632] |Microsoft Azure 上的 SAP 主要監視度量 |
| [1999351] |Windows 上的虛擬化︰增強型監視功能 |

深入了解 [Azure 訂用帳戶限制][azure-subscription-service-limits-subscription]，包括一般預設限制和最大限制。

## <a name="<a-name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>high-availability-sap-with-azure-resource-manager-vs.-the-classic-deployment-model"></a><a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a>使用 Azure Resource Manager 的高可用性 SAP 與傳統部署模型
Azure Resource Manager 與傳統部署模型在兩種主要方面有所不同：

* 資源群組
* 叢集需求

### <a name="<a-name="f76af273-1993-4d83-b12d-65deeae23686"></a>-resource-groups"></a><a name="f76af273-1993-4d83-b12d-65deeae23686"></a> 資源群組
在 Azure Resource Manager 中，您可以使用資源群組來管理 Azure 訂用帳戶中的所有應用程式資源。 利用整合式的方法，在資源群組中，所有資源都有相同的生命週期。 例如，所有資源會同時建立，並且同時刪除。 您可以取得有關[資源群組](../resource-group-overview.md#resource-groups)的詳細資訊。

### <a name="<a-name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a>-clustering-with-azure-resource-manager-vs.-the-classic-deployment-model"></a><a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a>使用 Azure Resource Manager 叢集化與傳統部署模型
在 Azure Resource Manager 模型中，您不需要雲端服務以使用高可用性的 Azure 內部負載平衡。

若要使用 Azure 傳統模型，請依照 [Azure 上的 SAP NetWeaver：在 Azure 上使用 Windows Server 容錯移轉叢集搭配 SIOS DataKeeper 將 SAP ASCS/SCS 執行個體組成叢集](http://go.microsoft.com/fwlink/?LinkId=613056)中所述的步驟操作。

> [!NOTE]
> 強烈建議您針對 SAP 安裝使用 Azure Resource Manager 部署模型。 它提供傳統部署模型所沒有的許多好處。 您可以進一步了解 Azure [部署模型][virtual-machines-azure-resource-manager-architecture-benefits-arm]。   
> 
> 

## <a name="<a-name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a>-windows-server-failover-clustering"></a><a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Windows Server 容錯移轉叢集
Windows Server 容錯移轉叢集是 Windows 中高可用性 SAP ASCS/SCS 安裝和 DBMS 的基礎。

容錯移轉叢集是由 1+n 個獨立伺服器 (節點) 所組成的群組，這些伺服器會共同運作以提升應用程式和服務的可用性。 如果發生節點失敗，Windows Server 容錯移轉叢集會計算發生的失敗次數，並維護狀況良好的叢集，以提供定義的應用程式和服務。 您可以從不同的仲裁模式選擇以達成此目標。

### <a name="<a-name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a>-quorum-modes"></a><a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> 仲裁模式
當您使用 Windows Server 容錯移轉叢集時，可以從四種仲裁模式中選擇：

* **節點多數**。 叢集的每個節點皆可投票。 只有取得多數票 (也就是票數過半) 時，叢集才能運作。 我們建議針對節點數目為奇數的叢集使用此選項。 例如，具有七個節點的叢集中有三個節點可能會失敗，而叢集仍會達成多數並繼續執行。  
* **節點與磁碟多數**。 每個節點和叢集儲存體中的指定磁碟 (磁碟見證) 處於可用且通訊中狀態時，都可投票。 只有取得多數票 (也就是票數過半) 時，叢集才能運作。 此模式適用於節點數目為偶數的叢集環境。 如果半數的節點和該磁碟在線上，叢集就會維持狀況良好的狀態。
* **節點與檔案共用多數**。 每個節點再加上一個由系統管理員所建立的指定檔案共用 (檔案共用見證) 無論是否處於可用且通訊中狀態，皆可投票。 只有取得多數票 (也就是票數過半) 時，叢集才能運作。 此模式適用於節點數目為偶數的叢集環境。 類似於「節點與磁碟多數」模式，但它使用見證檔案共享，而不是見證磁碟。 此模式很容易實作，但如果檔案共用本身不具有高可用性，它可能會變成單一失敗點。
* **無多數：僅磁碟**。 只要有一個節點可用且正與叢集儲存體中的特定磁碟進行通訊，叢集就會擁有仲裁。 只有也在與該磁碟進行通訊的節點能夠加入叢集。 建議您不要使用此模式。
   
  
  ## <a name="<a-name="fdfee875-6e66-483a-a343-14bbaee33275"></a>-windows-server-failover-clustering-on-premises"></a><a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> Windows Server 容錯移轉叢集內部部署
  圖 1 中的範例顯示兩個節點的叢集。 如果節點之間的網路連線失敗，而且兩個節點仍保持運作，仲裁磁碟或檔案共用會決定哪一個節點將繼續提供叢集的應用程式與服務。 能夠存取仲裁磁碟或檔案共用的節點就是可以確保服務可繼續的節點。

因為此範例使用雙節點叢集，所以我們使用「節點與檔案共用多數」仲裁模式。 「節點與磁碟多數」也是有效的選項。 在生產環境中，建議您使用仲裁磁碟。 您可以使用網路和儲存體系統技術，使其成為高可用性。

![圖 1：Azure 中 SAP ASCS/SCS 之 Windows Server 容錯移轉叢集組態的範例][sap-ha-guide-figure-1000]

***圖 1：**Azure 中 SAP ASCS/SCS 之 Windows Server 容錯移轉叢集組態的範例*

### <a name="<a-name="be21cf3e-fb01-402b-9955-54fbecf66592"></a>-shared-storage"></a><a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> 共用儲存體
圖 1 也顯示兩個節點的共用儲存體叢集。 在內部部署共用儲存體叢集中，叢集中的所有節點會偵測共用儲存體。 鎖定機制可防止資料損毀。 當另一個節點失敗時，所有節點也可以偵測到。 如果一個節點發生失敗，剩餘的節點就會取得儲存體資源的擁有權並確保服務的可用性。

> [!NOTE]
> 您不需要與某些 DBMS 應用程式 (例如 SQL Server) 共用提供高可用性的磁碟。 SQL Server Always On 會將 DBMS 資料及記錄檔，從一個叢集節點的本機磁碟複寫到另一個叢集節點的本機磁碟。 在此情況下，Windows 叢集組態不需要共用磁碟。
> 
> 

### <a name="<a-name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a>-networking-and-name-resolution"></a><a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> 網路與名稱解析
用戶端電腦可透過 DNS 伺服器提供的虛擬 IP 位址和虛擬主機名稱連線叢集。 內部部署節點與 DNS 伺服器可以處理多個 IP 位址。

在一般設定中，您會使用兩個或更多個網路連線：

* 一個連接到儲存體的專用連線
* 一個用於活動訊號的叢集內部網路連線
* 一個供用戶端用來連線到叢集的公用網路

## <a name="<a-name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a>-windows-server-failover-clustering-in-azure"></a><a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> Azure 中的 Windows Server 容錯移轉叢集
相較於裸機或私人雲端部署，「Azure 虛擬機器」需要額外的步驟來設定 Windows Server 容錯移轉叢集。 當您建置共用叢集磁碟時，需要為 SAP ASCS/SCS 執行個體設定數個 IP 位址和虛擬的主機名稱。

在本文中，我們會討論在 Azure 中建置 SAP 高可用性中心服務叢集時所需要的重要概念與其他步驟。 我們會為您示範如何設定協力廠商工具 SIOS DataKeeper，以及如何設定 Azure 內部負載平衡器。 您可以使用這些工具，在 Azure 中透過檔案共用見證建立 Windows 容錯移轉叢集。

![圖 2：Azure 中沒有共用磁碟的 Windows Server 容錯移轉叢集組態][sap-ha-guide-figure-1001]

***圖 2：**Azure 中沒有共用磁碟的 Windows Server 容錯移轉叢集組態*

### <a name="<a-name="1a464091-922b-48d7-9d08-7cecf757f341"></a>-shared-disk-in-azure-with-sios-datakeeper"></a><a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> 使用 SIOS DataKeeper 在 Azure 中共用磁碟
您需要針對高可用性的 SAP ASCS/SCS 執行個體叢集共用儲存體。 自 2016 年 9 月起，Azure 即不再提供可用於建立共用儲存體叢集的共用儲存體。 您可以使用協力廠商軟體 SIOS DataKeeper Cluster Edition 以建立鏡像儲存體，以模擬叢集共用儲存體。 SIOS 解決方案提供即時同步的資料複寫。 可以為叢集建立共用磁碟資源的方式為：

1. 將額外的 Azure 虛擬硬碟 (VHD) 連接到 Windows 叢集組態中的每個虛擬機器。
2. 在兩個虛擬機器節點上執行 SIOS DataKeeper Cluster Edition。
3. 設定 SIOS DataKeeper Cluster Edition，使它將來源虛擬機器的額外 VHD 連接磁碟區內容鏡像至目標虛擬機器的額外 VHD 連接磁碟區。 SIOS DataKeeper 會提取來源和目標本機磁碟區，並將它們當作一個共用磁碟來呈現給「Windows Server 容錯移轉叢集」。

取得 [SIOS DataKeeper](http://us.sios.com/products/datakeeper-cluster/)的詳細資訊。

 ![圖 3：Azure 中沒有使用 SIOS DataKeeper 的 Windows Server 容錯移轉叢集組態][sap-ha-guide-figure-1002]

***圖 3：**Azure 中使用 SIOS DataKeeper 的 Windows Server 容錯移轉叢集組態*

> [!NOTE]
> 您不需要與某些 DBMS 產品 (例如 SQL Server) 共用提供高可用性的磁碟。 SQL Server Always On 會將 DBMS 資料及記錄檔，從一個叢集節點的本機磁碟複寫到另一個叢集節點的本機磁碟。 在此情況下，Windows 叢集組態不需要共用磁碟。
> 
> 

### <a name="<a-name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a>-name-resolution-in-azure"></a><a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Azure 中的名稱解析
 Azure 雲端平台不提供設定虛擬 IP 位址的選項，例如浮動 IP。 正因為如此，您需要一個替代解決方案來設定虛擬 IP，以便連線到雲端的叢集資源。
Azure 具有 Azure Load Balancer 服務中的內部負載平衡器。 使用內部負載平衡器，用戶端可透過叢集虛擬 IP 位址連線叢集。
您需要將內部負載平衡器部署在包含叢集節點的資源群組中。 接著，使用內部負載平衡器的探查連接埠來設定所有必要的連接埠轉送規則。
用戶端可以透過虛擬主機名稱來進行連線。 DNS 伺服器會解析叢集 IP 位址，而內部負載平衡器則會處理對作用中叢集節點的轉送。

## <a name="<a-name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a>-high-availability-sap-netweaver-in-azure-infrastructure-as-a-service-(iaas)"></a><a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> Azure 基礎結構即服務 (IaaS) 中的高可用性 SAP NetWeaver
若要達成 SAP 應用程式高可用性，例如 SAP 軟體元件，您需要保護下列元件。 這一點會在 [Windows 虛擬機器 (VM) 上的 SAP NetWeaver - 規劃和實作指南][planning-guide-11] 中詳細討論。

* SAP 應用程式伺服器
* SAP ASCS/SCS 執行個體
* DBMS 伺服器

### <a name="<a-name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a>-high-availability-sap-application-servers"></a><a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> 高可用性的 SAP 應用程式伺服器
對於 SAP 應用程式伺服器和對話方塊執行個體，您通常不需要特定的高可用性解決方案。 您可以透過備援來達成高可用性，而且您會在 Azure 虛擬機器之不同的執行個體中，設定多個對話方塊執行個體。 您應該至少要有兩個 SAP 應用程式執行個體安裝在 Azure 虛擬機器的兩個執行個體中。

![圖 4：高可用性的 SAP 應用程式伺服器][sap-ha-guide-figure-2000]

***圖 4：**高可用性的 SAP 應用程式伺服器*

所有裝載 SAP 應用程式伺服器的虛擬機器都必須放置在同一個 Azure 可用性設定組中。 Azure 可用性設定組可確保：

* 所有虛擬機器都是相同升級網域的一部分。 例如，升級網域可確保虛擬機器不會在計劃性維護停機期間同時更新。
* 所有虛擬機器都是相同容錯網域的一部分。 例如，容錯網域可確保部署虛擬機器，以便不會有任何單一失敗點影響所有虛擬機器的可用性。

深入了解如何[管理虛擬機器的可用性][virtual-machines-manage-availability]。

由於 Azure 儲存體帳戶是潛在的單一失敗點，因此您務必擁有至少兩個 Azure 儲存體帳戶，且至少要將兩個虛擬機器分散到其中。 在理想的設定中，執行 SAP 對話方塊執行個體的每一個虛擬機器會部署在不同的儲存體帳戶中。

### <a name="<a-name="f559c285-ee68-4eec-add1-f60fe7b978db"></a>-high-availability-sap-ascs/scs-instance"></a><a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> 高可用性的 SAP ASCS/SCS 執行個體
![圖 5：高可用性的 SAP ASCS/SCS 執行個體][sap-ha-guide-figure-2001]

***圖 5：**高可用性的 SAP ASCS/SCS 執行個體*

#### <a name="<a-name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a>-high-availability-sap-ascs/scs-instance-with-windows-server-failover-clustering-in-azure"></a><a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> 在 Azuer 中使用 Windows Server 容錯移轉叢集的高可用性 SAP ASCS/SCS 執行個體
相較於裸機或私人雲端部署，「Azure 虛擬機器」需要額外的步驟來設定 Windows Server 容錯移轉叢集。 為了建置 Windows 容錯移轉叢集，您需要一個共用叢集磁碟、數個 IP 位址、數個虛擬主機名稱，以及 Azure 內部負載平衡器，來將 SAP ASCS/SCS 執行個體組成叢集。

本文稍後會有詳細討論。

![圖 6：Azure 中使用 SIOS DataKeeper 之 SAP ASCS/SCS 的 Windows Server 容錯移轉叢集組態][sap-ha-guide-figure-1002]

***圖 6：**Azure 中使用 SIOS DataKeeper 之 SAP ASCS/SCS 的 Windows Server 容錯移轉叢集組態*

### <a name="<a-name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a>-high-availability-dbms-instance"></a><a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a> 高可用性的 DBMS 執行個體
DBMS 也是 SAP 系統的單一連絡點。 您需要使用高可用性的解決方案來保護它。 圖 7 示範在 Azure 中使用 Windows Server 容錯移轉叢集和 Azure 內部負載平衡器的 SQL Server Always On 高可用性解決方案的範例。 SQL Server Always On 會使用自己的 DBMS 複寫來複寫 DBMS 資料和記錄檔。 在此情況下，您不需要叢集共用磁碟，以簡化整個設定。

![圖 7：高可用性的 SAP DBMS: SQL Server Always On 的範例][sap-ha-guide-figure-2003]

***圖 7：**高可用性的 SAP DBMS: SQL Server Always On 的範例*

如需有關使用 Azure Resource Manager 部署模型在 Azure 中將 SQL Server 組成叢集的詳細資訊，請參閱下列文章：

* [使用 Resource Manager 在 Azure 虛擬機器中設定 Always On 可用性群組][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
* [在 Azure 中為 Always On 可用性群組設訂內部負載平衡器][virtual-machines-windows-portal-sql-alwayson-int-listener]

### <a name="<a-name="045252ed-0277-4fc8-8f46-c5a29694a816"></a>-end-to-end-high-availability-deployment-scenarios"></a><a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> 端對端高可用性部署案例
圖 8 顯示在 Azure 中 SAP NetWeaver 高可用性架構的範例。 在此案例中，我們會針對 SAP ASCS/SCS 執行個體使用專用叢集，並針對 DBMS 使用另一個叢集。

![圖 8：SAP HA 架構範本 1，包含用於 ASCS/SCS 的專用叢集和用於 DBMS 執行個體的專用叢集][sap-ha-guide-figure-2004]

***圖 8:** SAP HA 架構範本 1：用於 ASCS/SCS 和 DBMS 的專用叢集*

## <a name="<a-name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a>-prepare-the-infrastructure"></a><a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> 準備基礎架構
適用於 SAP 的 Azure Resource Manager 範本有助於簡化部署所需的資源。

三層式範本也支援高可用性案例，例如架構範本 1，有兩個叢集。 每個叢集是適用於 SAP ASCS/SCS 和 DBMS 的 SAP 單一失敗點。

您可以在這裡取得案例 1 的 Azure Resource Manager 範本：

* [Azure Marketplace 映像](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)  
* [自訂映像](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

當您選取 SAP 三層式 Marketplace 映像時，Azure 入口網站中會顯示此畫面：

![圖 9：指定 SAP 高可用性 Azure Resource Manager 參數][sap-ha-guide-figure-3000]

***圖 9：**指定 SAP 高可用性 Azure Resource Manager 參數*

在 **SYSTEMAVAILABILITY** 中，選取 **HA**。

範本會建立：

* **虛擬機器**：
  * SAP 應用程式伺服器虛擬機器︰<*SAPSystemSID*>-di-<*Number*>
  * ASCS/SCS 叢集虛擬機器︰<*SAPSystemSID*>-ascs-<*Number*>
  * DBMS 叢集：<*SAPSystemSID*>-db-<*Number*>
* **所有虛擬機器的網路卡，包含關聯的 IP 位址**：
  * <*SAPSystemSID*>-nic-di-<*Number*>
  * <*SAPSystemSID*>-nic-ascs-<*Number*>
  * <*SAPSystemSID*>-nic-db-<*Number*>
* **Azure 儲存體帳戶**
* 下列項目的**可用性群組**：
  * SAP 應用程式伺服器虛擬機器︰<*SAPSystemSID*>-avset-di
  * SAP ASCS/SCS 叢集虛擬機器︰<*SAPSystemSID*>-avset-ascs
  * DBMS 叢集虛擬機器︰<*SAPSystemSID*>-avset-db
* **Azure 內部負載平衡器**：
  * 包含 ASCS/SCS 執行個體的所有連接埠與 IP 位址 <*SAPSystemSID*>-lb-ascs
  * 包含 SQL Server DBMS 的所有連接埠與 IP 位址 <*SAPSystemSID*>-lb-ascs
* **網路安全性群組**：<*SAPSystemSID*>-nsg-ascs-0  
  * 包含向 <*SAPSystemSID*>-ascs-0 虛擬機器開放的外部遠端桌面通訊協定 (RDP) 連接埠

> [!NOTE]
> 網路卡的所有 IP 位址和 Azure 內部負載平衡器依預設為**動態**。 將其變更為**靜態** IP 位址。 本文稍後會加以描述。
> 
> 

### <a name="<a-name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>-deploy-virtual-machines-with-corporate-network-connectivity-(cross-premises)-to-use-in-production"></a><a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a> 使用公司網路連線能力 (跨單位) 部署虛擬機器以用於生產環境中
針對生產環境 SAP 系統，使用 Azure 站對站 VPN 或 Azure ExpressRoute，部署具有 [公司網路連線能力 (跨單位)][planning-guide-2.2] 的 Azure 虛擬機器。

> [!NOTE]
> 您可以使用您的 Azure 虛擬網路執行個體。 虛擬網路和子網路已建立並備妥。
> 
> 

在 [NEWOREXISTINGSUBNET] 中，選取 [現有]。

在 [SUBNETID] 中，新增已備妥的「Azure 網路 SubnetID」的完整字串，這是您打算用來部署 Azure 虛擬機器的位置。

執行此 PowerShell 命令來取得所有 Azure 網路子網路的清單：

```powershell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
```

[ID] 欄位顯示 **SUBNETID**。

您可以使用此 PowerShell 命令，擷取所有 **SUBNETID** 值的清單：

```PowerShell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
```

**SUBNETID** 看起來像這樣：

```
/subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
```

### <a name="<a-name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a>-cloud-only-deployment-of-sap-instances-for-test-and-demo"></a><a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> 用於測試和示範的 SAP 執行個體僅限雲端部署
您也可以在僅限雲端部署模型中部署您的高可用性 SAP 系統。

這種部署主要對於示範或測試的使用案例相當有用。 但不適用於生產環境使用案例。

在 [NEWOREXISTINGSUBNET] 中，選取 [新增]。 將 [SUBNETID] 欄位保留空白。

SAP Azure Resource Manager 範本將會自動建立 Azure 虛擬網路和子網路。

> [!NOTE]
> 您也需要在相同的 Azure 虛擬網路執行個體中，針對 Active Directory 和 DNS 部署至少一個專用的虛擬機器。 此範本不會建立這些虛擬機器。
> 
> 

### <a name="<a-name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a>-azure-virtual-network"></a><a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Azure 虛擬網路
在我們的範例中，Azure 虛擬網路的位址空間是 10.0.0.0/16。 有一個名為 **Subnet**、網址範圍為 10.0.0.0/24 的子網路。 所有虛擬機器和內部負載平衡器會部署在此虛擬網路中。

> [!NOTE]
> 請勿對客體作業系統內的網路設定進行任何變更。 包括 IP 位址、DNS 伺服器和子網路。 在 Azure 中設定所有網路設定。 動態主機設定通訊協定 (DHCP) 服務會散佈您的設定。
> 
> 

### <a name="<a-name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a>-dns-ip-addresses"></a><a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> DNS IP 位址
請確定您的虛擬網路 [DNS 伺服器] 選項是設定為 [自訂 DNS]。
然後，根據您擁有的網路類型選取設定：

* [公司網路連線能力 (跨單位)][planning-guide-2.2]：請新增內部部署 DNS 伺服器的 IP 位址。  
  
    您可以將內部部署 DNS 伺服器擴充至 Azure 中執行的虛擬機器。 在這種情況下，您可以加入執行 DNS 服務之 Azure 虛擬機器的 IP 位址。
* [僅限雲端部署][planning-guide-2.1]：在做為 DNS 伺服器的相同虛擬網路執行個體中，部署其他虛擬機器。 新增您已設定執行 DNS 服務之 Azure 虛擬機器的 IP 位址。

![圖 10：設定 Azure 虛擬網路的 DNS 伺服器][sap-ha-guide-figure-3001]

***圖 10：**設定 Azure 虛擬網路的 DNS 伺服器*

> [!NOTE]
> 如果您變更 DNS 伺服器的 IP 位址，您就必須重新啟動 Azure 虛擬機器，才能套用變更並傳播新的 DNS 伺服器。
> 
> 

在我們的範例中，是在下列 Windows 虛擬機器上安裝並設定 DNS 服務：

| 虛擬機器角色 | 虛擬機器主機名稱 | 網路卡名稱 | 靜態 IP 位址 |
| --- | --- | --- | --- |
| 第一部 DNS 伺服器 |domcontr-0 |pr1-nic-domcontr-0 |10.0.0.10 |
| 第二部 DNS 伺服器 |domcontr-1 |pr1-nic-domcontr-1 |10.0.0.11 |

### <a name="<a-name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a>-host-names-and-static-ip-addresses-for-the-sap-ascs/scs-clustered-instance-and-dbms-clustered-instance"></a><a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> SAP ASCS/SCS 叢集執行個體及 DBMS 叢集執行個體的主機名稱和靜態 IP 位址
對於內部部署，您需要下列保留的主機名稱和 IP 位址：

| 虛擬主機名稱角色 | 虛擬主機名稱 | 虛擬靜態 IP 位址 |
| --- | --- | --- |
| SAP ASCS/SCS 第一個叢集虛擬主機名稱 (用於叢集管理) |pr1-ascs-vir |10.0.0.42 |
| SAP ASCS/SCS 執行個體虛擬主機名稱 |pr1-ascs-sap |10.0.0.43 |
| SAP DBMS 第二個叢集虛擬主機名稱 (叢集管理) |pr1-dbms-vir |10.0.0.32 |

當您建立叢集時，請建立虛擬主機名稱 **pr1-ascs-vir** 和 **pr1-dbms-vir**，以及管理叢集本身的相關 IP 位址。 [收集叢集組態中的叢集節點][sap-ha-guide-8.12.1] 會說明此程序。

您可以在 DNS 伺服器上手動建立其他兩個虛擬主機名稱，**pr1-ascs-sap** 和 **pr1-dbms-sap**，以及相關的 IP 位址。 叢集 SAP ASCS/SCS 執行個體和叢集 DBMS 執行個體使用這些資源。 在 [建立叢集 SAP ASCS/SCS 的虛擬主機名稱][sap-ha-guide-9.1.1] 中會加以描述。

### <a name="<a-name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a>-set-static-ip-addresses-for-the-sap-virtual-machines"></a><a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> 設定 SAP 虛擬機器的靜態 IP 位址
部署要在叢集中使用的虛擬機器之後，您需要設定所有虛擬機器的靜態 IP 位址。 在 Azure 虛擬網路組態中執行此動作，而非在客體作業系統中執行此動作。

設定靜態 IP 位址的其中一種方法是使用 Azure 入口網站。 在 Azure 入口網站移至 [資源群組] > [網路卡] > [設定] > [IP 位址]。

在 [指派] 下方，選取 [靜態]。 在 [IP 位址] 欄位中，輸入您要使用的 IP 位址。

> [!NOTE]
> 如果您變更網路卡的 IP 位址，您就必須重新啟動 Azure 虛擬機器，才能套用變更。  
> 
> 

![圖 11：為每個虛擬機器的網路卡設定靜態 IP 位址][sap-ha-guide-figure-3002]

***圖 11：**為每個虛擬機器的網路卡設定靜態 IP 位址*

為所有網路介面重複此步驟，亦即為所有虛擬機器，包括您要用於 Active Directory/DNS 服務的虛擬機器。

在我們的範例中，有下列虛擬機器和靜態 IP 位址：

| 虛擬機器角色 | 虛擬機器主機名稱 | 網路卡名稱 | 靜態 IP 位址 |
| --- | --- | --- | --- |
| 第一部 SAP 應用程式伺服器 |pr1-di-0 |pr1-nic-di-0 |10.0.0.50 |
| 第二部 SAP 應用程式伺服器 |pr1-di-1 |pr1-nic-di-1 |10.0.0.51 |
| ... |... |... |... |
| 最後一部 SAP 應用程式伺服器 |pr1-di-5 |pr1-nic-di-5 |10.0.0.55 |
| ASCS/SCS 執行個體的第一個叢集節點 |pr1-ascs-0 |pr1-nic-ascs-0 |10.0.0.40 |
| ASCS/SCS 執行個體的第二個叢集節點 |pr1-ascs-1 |pr1-nic-ascs-1 |10.0.0.41 |
| DBMS 執行個體的第一個叢集節點 |pr1-db-0 |pr1-nic-db-0 |10.0.0.30 |
| DBMS 執行個體的第二個叢集節點 |pr1-db-1 |pr1-nic-db-1 |10.0.0.31 |

### <a name="<a-name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a>-set-a-static-ip-address-for-the-internal-load-balancer"></a><a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> 為內部負載平衡器設定靜態 IP 位址
SAP Azure Resource Manager 範本會建立用於 SAP ASCS/SCS 執行個體叢集和用於 DBMS 叢集的 Azure 內部負載平衡器。

初始部署會將內部負載平衡器的 IP 位址設定為 [動態]。 請務必將 IP 位址變更為 [靜態]。

在我們的範例中，有兩個 Azure 內部負載平衡器具備這些靜態 IP 位址：

| Azure 內部負載平衡器角色 | Azure 內部負載平衡器名稱 | 靜態 IP 位址 |
| --- | --- | --- |
| SAP ASCS/SCS 執行個體內部負載平衡器 |pr1-lb-ascs |10.0.0.43 |
| SAP DBMS 內部負載平衡器 |pr1-lb-dbms |10.0.0.33 |

> [!NOTE]
> SAP ASCS/SCS 之虛擬主機名稱的 IP 位址與 SAP ASCS/SCS 內部負載平衡器 pr1-lb-ascs 的 IP 位址相同。
> DBMS 之虛擬名稱的 IP 位址與 DBMS 內部負載平衡器 pr1-lb-dbms 的 IP 位址相同。
> 
> 

在我們的範例中，我們已將內部負載平衡器 **pr1-lb-ascs** 的 IP 位址設定為 SAP ASCS/SCS 執行個體之虛擬主機名稱的 IP 位址 (在我們的範例中為 **10.0.0.43**)。

![圖 12：為 SAP ASCS/SCS 執行個體的內部負載平衡器設定靜態 IP 位址][sap-ha-guide-figure-3003]

***圖 12：**為 SAP ASCS/SCS 執行個體的內部負載平衡器設定靜態 IP 位址*

將內部負載平衡器 **pr1-lb-dbms** 的 IP 位址設定為 DBMS 執行個體之虛擬主機名稱的 IP 位址 (在我們的範例中為 **10.0.0.33**)。

### <a name="<a-name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a>-default-ascs/scs-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Azure 內部負載平衡器的預設 ASCS/SCS 負載平衡規則
SAP Azure Resource Manager 範本會建立所需的連接埠：

* ABAP ASCS 執行個體預設的執行個體號碼為 **00**
* Java SCS 執行個體預設的執行個體號碼為 **01**

當您安裝 SAP ASCS/SCS 執行個體時，必須為 ABAP ASCS 執行個體使用預設的執行個體號碼 00，並為 Java SCS 執行個體使用預設的執行個體號碼 01。

接著，為 SAP NetWeaver ABAP ASCS 連接埠建立下列必要的內部負載平衡端點：

| 服務/負載平衡規則名稱 | 預設連接埠號碼 | (執行個體號碼為 00 的 ASCS 執行個體) (ERS 為 10) 的具體連接埠 |
| --- | --- | --- |
| 加入佇列伺服器 / *lbrule3200* |32<*InstanceNumber*> |3200 |
| ABAP 訊息伺服器 / *lbrule3600* |36<*InstanceNumber*> |3600 |
| 內部 ABAP 訊息 / *lbrule3900* |39<*InstanceNumber*> |3900 |
| 訊息伺服器 HTTP / *Lbrule8100* |81<*InstanceNumber*> |8100 |
| SAP 啟動服務 ASCS HTTP / *Lbrule50013* |5<*InstanceNumber*>13 |50013 |
| SAP 啟動服務 ASCS HTTPS / *Lbrule50014* |5<*InstanceNumber*>14 |50014 |
| 加入佇列複寫 / *Lbrule50016* |5<*InstanceNumber*>16 |50016 |
| SAP 啟動服務 ERS HTTP *Lbrule51013* |5<*InstanceNumber*>13 |51013 |
| SAP 啟動服務 ERS HTTP *Lbrule51014* |5<*InstanceNumber*>14 |51014 |
| Win RM *Lbrule5985* | |5985 |
| 檔案共用 *Lbrule445* | |445 |

***表 1：**SAP NetWeaver ABAP ASCS 執行個體的連接埠號碼*

接著，為 SAP NetWeaver Java SCS 連接埠建立下列必要的內部負載平衡端點：

| 服務/負載平衡規則名稱 | 預設連接埠號碼 | (執行個體號碼為 01 的 SCS 執行個體) (ERS 為 11) 的具體連接埠 |
| --- | --- | --- |
| 加入佇列伺服器 / *lbrule3201* |32<*InstanceNumber*> |3201 |
| 閘道伺服器 / *lbrule3301* |33<*InstanceNumber*> |3301 |
| Java 訊息伺服器 / *lbrule3900* |39<*InstanceNumber*> |3901 |
| 訊息伺服器 HTTP / *Lbrule8101* |81<*InstanceNumber*> |8101 |
| SAP 啟動服務 SCS HTTP / *Lbrule50113* |5<*InstanceNumber*>13 |50113 |
| SAP 啟動服務 SCS HTTPS / *Lbrule50114* |5<*InstanceNumber*>14 |50114 |
| 加入佇列複寫 / *Lbrule50116* |5<*InstanceNumber*>16 |50116 |
| SAP 啟動服務 ERS HTTP *Lbrule51113* |5<*InstanceNumber*>13 |51113 |
| SAP 啟動服務 ERS HTTP *Lbrule51114* |5<*InstanceNumber*>14 |51114 |
| Win RM *Lbrule5985* | |5985 |
| 檔案共用 *Lbrule445* | |445 |

***表 2：**SAP NetWeaver Java SCS 執行個體的連接埠號碼*

![圖 13：Azure 內部負載平衡器的預設 ASCS/SCS 負載平衡規則][sap-ha-guide-figure-3004]

***圖 13：**Azure 內部負載平衡器的預設 ASCS/SCS 負載平衡規則*

將負載平衡器 **pr1-lb-dbms** 的 IP 位址設定為 DBMS 執行個體之虛擬主機名稱的 IP 位址 (在我們的範例中為 **10.0.0.33**)。

### <a name="<a-name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a>-change-the-ascs/scs-default-load-balancing-rules-for-the-azure-internal-load-balancer"></a><a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> 變更 Azure 內部負載平衡器的 ASCS/SCS 預設負載平衡規則
如果您想要將其他號碼用於 SAP ASCS 或 SCS 執行個體，您就必須更新這些連接埠的名稱和值。

更新執行個體號碼的其中一種方法是使用 Azure 入口網站：

移至 **<*SID*>-lb-ascs 負載平衡器** > **負載平衡規則* *。

針對屬於 SAP ASCS 或 SCS 執行個體的所有負載平衡規則，變更下列值：

* 名稱
* 連接埠
* 後端連接埠

例如，如果您要將預設 ASCS 執行個體號碼從 00 變更為 31，您需要針對表 1 中所列的所有通訊埠進行變更。

以下是連接埠 _lbrule3200_的更新範例。

![圖 14：變更 Azure 內部負載平衡器的 ASCS/SCS 預設負載平衡規則][sap-ha-guide-figure-3005]

***圖 14：**變更 Azure 內部負載平衡器的 ASCS/SCS 預設負載平衡規則*

### <a name="<a-name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a>-add-windows-virtual-machines-to-the-domain"></a><a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> 將 Windows 虛擬機器新增至網域
將靜態 IP 位址指派給虛擬機器之後，請將虛擬機器新增至網域。

![圖 15：將虛擬機器新增至網域][sap-ha-guide-figure-3006]

***圖 15：**將虛擬機器新增至網域*

### <a name="<a-name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a>-add-registry-entries-on-both-cluster-nodes-of-the-sap-ascs/scs-instance"></a><a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> 在 SAP ASCS/SCS 執行個體的兩個叢集節點上都新增登錄項目
Azure Load Balancer 具有內部負載平衡器，會在連線閒置一段時間 (閒置逾時) 時關閉連線。 對話方塊執行個體中的 SAP 工作程序則會在需要傳送第一個加入佇列/清除佇列要求時，立即開啟對SAP 加入佇列程序的連線。 這些連線通常會持續維持已建立狀態，直到工作程序或加入佇列程序重新啟動為止。 不過，如果連線閒置一段時間，Azure 內部負載平衡器就會關閉連線。 這不是問題，因為如果連線不存在，SAP 工作程序會對加入佇列程序重新建立連線。 這些活動會記載於 SAP 程序的開發人員追蹤，但它們會對這些追蹤建立大量額外的內容。 因此，最好先變更兩個叢集節點上的 TCP/IP `KeepAliveTime` 和 `KeepAliveInterval`。 結合這些 TCP/IP 參數中的變更與 SAP 設定檔參數中的變更，稍後在本文中會加以描述。

在 SAP ASCS/SCS 的兩個 Windows 叢集節點上都新增下列 Windows 登錄項目：

| 路徑 | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| 變數名稱 |`KeepAliveTime` |
| 變數類型 |REG_DWORD (十進位) |
| 值 |120000 |
| 文件連結 |[https://technet.microsoft.com/en-us/library/cc957549.aspx](https://technet.microsoft.com/en-us/library/cc957549.aspx) |

***表 3：**變更第一個 TCP/IP 參數*

| 路徑 | HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters |
| --- | --- |
| 變數名稱 |`KeepAliveInterval` |
| 變數類型 |REG_DWORD (十進位) |
| 值 |120000 |
| 文件連結 |[https://technet.microsoft.com/en-us/library/cc957548.aspx](https://technet.microsoft.com/en-us/library/cc957548.aspx) |

***表 4：**變更第二個 TCP/IP 參數*

若要套用變更，請重新啟動這兩個叢集節點。

### <a name="<a-name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a>-set-up-a-windows-server-failover-clustering-cluster-for-an-sap-ascs/scs-instance"></a><a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> 設定 SAP ASCS/SCS 執行個體的 Windows Server 容錯移轉叢集
#### <a name="<a-name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a>-collect-the-cluster-nodes-in-a-cluster-configuration"></a><a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> 收集叢集組態中的叢集節點
第一個步驟是在兩個叢集節點上都新增容錯移轉叢集。 使用 [新增角色及功能精靈]。

第二個步驟是使用 [容錯移轉叢集管理員] 來設定容錯移轉叢集。

在 [容錯移轉叢集管理員] 中，按一下 [建立叢集]，然後只新增第一個叢集節點 A 的名稱，例如，新增 **pr1-ascs-0**。 還不要新增第二個節點。 您將在稍後的步驟來新增第二個節點。

![圖 16：加入第一個叢集節點的伺服器或虛擬機器名稱][sap-ha-guide-figure-3007]

***圖 16：**加入第一個叢集節點的伺服器或虛擬機器名稱*

接著，系統會提示您輸入叢集的網路名稱 (虛擬主機名稱)。

![圖 17︰定義叢集名稱][sap-ha-guide-figure-3008]

***圖 17：**定義叢集名稱*

建立叢集之後，會執行叢集驗證測試。

![圖 18：執行叢集驗證檢查][sap-ha-guide-figure-3009]

***圖 18：**執行叢集驗證檢查*

![圖 19︰找不到仲裁磁碟][sap-ha-guide-figure-3010]

***圖 19：**找不到仲裁磁碟*

您可以忽略此時程序中有關磁碟的任何警告。 稍後您將新增檔案共用見證與 SIOS 共用磁碟。 在這個階段，您不必擔心有仲裁。

![圖 20︰核心叢集資源需要新的 IP 位址][sap-ha-guide-figure-3011]

***圖 20：**核心叢集資源需要新的 IP 位址*

叢集無法啟動，因為伺服器的 IP 位址指向其中一個虛擬機器節點。 您需要變更核心叢集服務的 IP 位址。

例如，我們需要為叢集虛擬主機名稱 **pr1-ascs-vir** 指派 IP 位址 (在我們的範例中為 **10.0.0.42**)。 在核心叢集服務 IP 資源的屬性頁面上執行此動作，如圖 21 所示。

![圖 21：在 **屬性** 對話方塊中，變更 IP 位址][sap-ha-guide-figure-3012]

***圖 21：**在 [屬性] 對話方塊中，變更 IP 位址*

![圖 22：指派為叢集保留的 IP 位址][sap-ha-guide-figure-3013]

***圖 22：**指派為叢集保留的 IP 位址*

變更 IP 位址之後，請讓叢集虛擬主機名稱上線。

![圖 23：叢集核心服務以正確的 IP 位址啟動並正常執行][sap-ha-guide-figure-3014]

***圖 23：**叢集核心服務以正確的 IP 位址啟動並正常執行*

既然核心叢集服務已啟動並正常執行，您便可以新增第二個叢集節點。

![圖 24：新增第二個叢集節點][sap-ha-guide-figure-3015]

***圖 24：**新增第二個叢集節點*

![圖 25：新增第二個叢集節點主機名稱，例如 pr1-ascs-1][sap-ha-guide-figure-3016]

***圖 25：**新增第二個叢集節點主機名稱，例如 **pr1-ascs-1***

![圖 26：請勿選取核取方塊][sap-ha-guide-figure-3017]

***圖 26：**請*勿*選取核取方塊*

> [!IMPORTANT]
> 請確定*沒有*選取 [新增適合的儲存裝置到叢集] 核取方塊。  
> 
> 

![圖 27︰忽略有關磁碟仲裁的警告][sap-ha-guide-figure-3018]

***圖 27：**忽略有關磁碟仲裁的警告*

您可以忽略有關仲裁和磁碟的警告。 您將在稍後設定仲裁和共用磁碟，如 [為 SAP ASCS/SCS 叢集共用磁碟安裝 SIOS DataKeeper Cluster Edition][sap-ha-guide-8.12.3] 中所述。

#### <a name="<a-name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a>-configure-a-cluster-file-share-witness"></a><a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> 設定叢集檔案共用見證
##### <a name="<a-name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a>-create-a-file-share"></a><a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> 建立檔案共用
選取檔案共用見證，而不是仲裁磁碟。 SIOS DataKeeper 支援此選項。

在本文的範例中，檔案共用見證是在 Azure 中執行的 Active Directory/DNS 伺服器上。 檔案共用見證稱為 **domcontr-0**。 因為您已設定 Azure 的虛擬私人網路 (VPN) 連線 (透過網站間 VPN 或 Azure ExpressRoute)，所以您的 Active Directory/DNS 服務是內部部署，而且不適用於執行檔案共用見證。

> [!NOTE]
> 如果您的 Active Directory/DNS 服務僅在內部部署上執行，請勿在執行內部部署的 Active Directory/DNS Windows 作業系統上設定檔案共用見證。 在 Azure 和 Active Directory DNS 內部部署中執行之叢集節點之間的網路延遲可能太大，而且會造成連線問題。 請務必在執行位置靠近叢集節點的 Azure 虛擬機器上設定檔案共用見證。  
> 
> 

仲裁磁碟機至少需要 1,024 MB 的可用空間。 我們建議 2,048 MB 的可用空間。

第一個步驟是新增叢集名稱物件。

![圖 28：為叢集名稱物件指派共用上的權限][sap-ha-guide-figure-3019]

***圖 28：**為叢集名稱物件指派共用上的權限*

請確定權限能夠為叢集名稱物件 (在我們的範例中為 **pr1-ascs-vir$**) 變更共用中的資料。 若要將叢集名稱物件新增至清單中，請選取 [新增]。 變更篩選，以檢查電腦物件，除了圖 29 所示的項目以外：

![圖 29：變更物件類型以包含電腦物件][sap-ha-guide-figure-3020]

***圖 29：**變更物件類型以包含電腦物件*

![圖 30︰選取電腦物件的核取方塊][sap-ha-guide-figure-3021]

***圖 30：**選取電腦物件的核取方塊*

之後，輸入叢集名稱物件，如圖 29 所示。 由於已經建立記錄，因此您可以變更權限，如圖 28 所示。

接下來，選取共用的 [安全性] 索引標籤，然後為叢集名稱物件設定更詳細的權限。

![圖 31：為叢集名稱物件設定檔案共用仲裁上的安全性屬性][sap-ha-guide-figure-3022]

***圖 31：**為叢集名稱物件設定檔案共用仲裁上的安全性屬性*

##### <a name="<a-name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a>-set-the-file-share-witness-quorum-in-failover-cluster-manager"></a><a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> 在容錯移轉叢集管理員中設定檔案共用見證仲裁
在 [容錯移轉叢集管理員] 中，將叢集組態變更為檔案共用見證。

![圖 32：啟動設定叢集仲裁設定精靈][sap-ha-guide-figure-3023]

***圖 32：**啟動設定叢集仲裁設定精靈*

![圖 33：您可以選擇的仲裁組態][sap-ha-guide-figure-3024]

***圖 33：**您可以選擇的仲裁組態*

選取 [選取仲裁見證]。

![圖 34︰選取檔案共用見證][sap-ha-guide-figure-3025]

***圖 34：**選取檔案共用見證*

選取 [設定檔案共用見證]。

![圖 35：定義見證共用的檔案共用位置][sap-ha-guide-figure-3026]

***圖 35：**定義見證共用的檔案共用位置*

輸入檔案共用的 UNC 路徑 (在我們的範例中為 \\domcontr-0\FSW)。

選取 [下一步] 以查看您可進行變更的清單。 選取您要的變更，然後選取 [下一步]。

![圖 36︰確認您已重新設定叢集][sap-ha-guide-figure-3027]

***圖 36：**確認您已重新設定叢集*

在這最後一個步驟中，您需要成功重新設定叢集組態，如圖 36 中所示。  

### <a name="<a-name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a>-install-sios-datakeeper-cluster-edition-for-the-sap-ascs/scs-cluster-share-disk"></a><a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> 為 SAP ASCS/SCS 叢集共用磁碟安裝 SIOS DataKeeper Cluster Edition
您現在在 Azure 中有運作中的 Windows Server 容錯移轉叢集組態。 但是，若要安裝 SAP ASCS/SCS 執行個體，您需要有一個共用磁碟資源。 您無法在 Azure 中建立所需的共用磁碟資源。 SIOS DataKeeper Cluster Edition 是一種可用來建立共用磁碟資源的協力廠商解決方案。

#### <a name="<a-name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a>-add-the-.net-framework-3.5"></a><a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> 新增 .NET Framework 3.5
Microsoft .NET Framework 3.5 不會自動啟用或安裝在 Windows Server 2012 R2。 但是 SIOS DataKeeper 需要 .NET Framework 位於安裝 DataKeeper 所在的所有節點上。 因此，您必須在叢集中所有虛擬機器的客體作業系統上安裝 .NET Framework 3.5。

有兩種方式可以新增 .NET Framework 3.5。 其中一種方式是在 Windows 中使用 [新增角色及功能精靈]，如圖 37 所示。

![圖 37︰使用 [新增角色及功能精靈] 安裝 .NET Framework 3.5][sap-ha-guide-figure-3028]

***圖 37：**使用 [新增角色及功能精靈] 安裝 .NET Framework 3.5*

![圖 38︰當您使用 [新增角色及功能精靈] 安裝 .NET Framework 3.5 時的安裝進度列][sap-ha-guide-figure-3029]

***圖 38：**當您使用 [新增角色及功能精靈] 安裝 .NET Framework 3.5 時的安裝進度列*

第二種啟用 .Net Framework 3.5 功能的選項是使用命令列工具 dism.exe。 針對這類型的安裝，您需要存取 Windows 安裝媒體上的 SxS 目錄。 在提高權限的命令提示字元上執行此命令：

```
Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
```

#### <a name="<a-name="dd41d5a2-8083-415b-9878-839652812102"></a>-install-sios-datakeeper"></a><a name="dd41d5a2-8083-415b-9878-839652812102"></a> 安裝 SIOS DataKeeper
在叢集中的每個節點上安裝 SIOS DataKeeper Cluster Edition。 有了 SIOS DataKeeper，若要建立虛擬共用儲存體，請建立同步處理的鏡像，然後模擬叢集共用儲存體。

安裝 SIOS 軟體之前，請先建立網域使用者 **DataKeeperSvc**。

> [!NOTE]
> 將 **DataKeeperSvc** 使用者同時新增到兩個叢集節點上的 [本機管理員] 群組中。
> 
> 

在兩個叢集節點上都安裝 SIOS 軟體。

![SIOS 安裝程式][sap-ha-guide-figure-3030]

![圖 39：第一個 SIOS DataKeeper 安裝畫面][sap-ha-guide-figure-3031]

***圖 39：**第一個 SIOS DataKeeper 安裝畫面*

![圖 40：DataKeeper 通知將停用某個服務][sap-ha-guide-figure-3032]

***圖 40：**DataKeeper 通知將停用某個服務*

在圖 40 所示的對話方塊中，選取 [是]。

![圖 41：SIOS DataKeeper 的使用者選項][sap-ha-guide-figure-3033]

***圖 41：**SIOS DataKeeper 的使用者選項*

在圖 41 顯示的畫面上，建議您選取 [網域或伺服器帳戶]。

![圖 42：為 SIOS DataKeeper 安裝輸入網域使用者名稱和密碼][sap-ha-guide-figure-3034]

***圖 42：**為 SIOS DataKeeper 安裝輸入網域使用者名稱和密碼*

輸入您為 SIOS DataKeeper 建立的網域帳戶使用者名稱和密碼。

![圖 43：輸入您的 SIOS DataKeeper 授權][sap-ha-guide-figure-3035]

***圖 43：**輸入您的 SIOS DataKeeper 授權*

為您的 SIOS DataKeeper 執行個體安裝授權金鑰，如圖 43 所示。 安裝結束時，系統會要求您重新啟動虛擬機器。

#### <a name="<a-name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a>-set-up-sios-datakeeper"></a><a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> 設定 SIOS DataKeeper
在兩個節點上都安裝 SIOS DataKeeper 之後，您必須開始設定組態。 組態的目標是要在連接至每個虛擬機器的額外 VHD 之間進行同步資料複寫。 這些是設定這兩個節點所採取的步驟。

![圖 44：SIOS DataKeeper 管理和組態工具][sap-ha-guide-figure-3036]

***圖 44：**SIOS DataKeeper 管理和組態工具*

啟動 DataKeeper 管理與組態工具，然後選取 [連線伺服器]。 (在圖 44 中，此選項是用紅色圈起來的部分。)

![圖 45：插入管理和組態工具應連接之第一個節點的名稱或 TCP/IP 位址，然後在第二個步驟中，插入第二個節點][sap-ha-guide-figure-3037]

***圖 45：**插入管理和組態工具應連接之第一個節點的名稱或 TCP/IP 位址，然後在第二個步驟中，插入第二個節點*

下一個步驟是要在兩個節點之間建立複寫作業。

![圖 46：建立複寫作業][sap-ha-guide-figure-3038]

***l圖 46：**建立複寫作業*

精靈會引導您完成建立複寫作業的程序。

![圖 47：定義複寫作業的名稱][sap-ha-guide-figure-3039]

***l圖 47：**定義複寫作業的名稱*

![圖 48：定義節點 (應該是目前的來源節點) 的基底資料][sap-ha-guide-figure-3040]

***圖 48：**定義節點 (應該是目前的來源節點) 的基底資料*

在第一個步驟中，您必須定義來源節點的名稱、TCP/IP 位址及磁碟區。 第二個步驟是定義目標節點。 如前文所述，您必須定義目標節點的名稱、TCP/IP 位址及磁碟區。

![圖 49：定義節點 (應該是目前的目標節點) 的基底資料][sap-ha-guide-figure-3041]

***圖 49：**定義節點 (應該是目前的目標節點) 的基底資料*

接下來，定義壓縮演算法。 在我們的範例中，建議壓縮複寫資料流。 尤其是在重新同步處理的情況下，壓縮複寫資料流可大幅縮短重新同步處理時間。 請注意，壓縮會使用虛擬機器的 CPU 和 RAM 資源。 隨著壓縮率增加，使用的 CPU 資源數量也跟著增加。 您可以稍後調整此設定。

另一個您需要檢查的設定是複寫是以非同步方式還是同步方式進行。 *當您保護 SAP ASCS/SCS 組態時，您必須使用同步複寫*。  

![圖 50︰定義複寫詳細資料][sap-ha-guide-figure-3042]

***圖 50：**定義複寫詳細資料*

最後一個步驟是定義是否應該向 Windows Server 容錯移轉叢集的叢集組態指出複寫作業所複寫的磁碟區是共用磁碟。 就 SAP ASCS/SCS 組態而言，選取 [是] ，如此 Windows 叢集才會將複寫的磁碟區視為可用來作為叢集磁碟區的共用磁碟。

![圖 51： 選取 **是** 以將複寫的磁碟區設定為叢集磁碟區][sap-ha-guide-figure-3043]

***圖 51：**選取 [是] 以將複寫的磁碟區設定為叢集磁碟區*

建立磁碟區之後，DataKeeper 管理和組態工具會顯示複寫作業是使用中。

![圖 52：SAP ASCS/SCS 共用磁碟的 DataKeeper 同步鏡像處於作用中狀態][sap-ha-guide-figure-3044]

***圖 52：**SAP ASCS/SCS 共用磁碟的 DataKeeper 同步鏡像處於作用中狀態*

現在，「容錯移轉叢集管理員」顯示磁碟為 DataKeeper 磁碟，如圖 53 所示。

![圖 53︰「容錯移轉叢集管理員」顯示 DataKeeper 複寫的磁碟][sap-ha-guide-figure-3045]

***圖 53：**「容錯移轉叢集管理員」顯示 DataKeeper 複寫的磁碟*

## <a name="<a-name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a>-install-the-sap-netweaver-system"></a><a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> 安裝 SAP NetWeaver 系統
我們將不會說明 DBMS 設定，因為設定會視您使用的 DBMS 系統而異。 不過，我們會假設 DBMS 在高可用性方面的疑慮已藉由不同 DBMS 廠商為 Azure 提供的功能支援而獲得解決。 例如，適用於 SQL Server 的 Always On 或資料庫鏡像，以及適用於 Oracle 的 Oracle Data Guard。 在本文使用的案例中，我們並未對 DBMS 加入更多保護。

當不同的 DBMS 服務與 Azure 中這種叢集 SAP ASCS/SCS 組態互動時，沒有任何特殊的考量。

> [!NOTE]
> SAP NetWeaver ABAP 系統、Java 系統及 ABAP+Java 系統的安裝程序幾乎完全相同。 最顯著的差異在於 SAP ABAP 系統有一個 ASCS 執行個體。 SAP Java 系統有一個 SCS 執行個體。 SAP ABAP+Java 系統有一個 ASCS 執行個體和一個 SCS 執行個體在相同 Microsoft 容錯移轉叢集群組中執行。 我們會明確說明每個 SAP NetWeaver 安裝堆疊的所有安裝差異。 您可以假設所有其他部分都相同。  
> 
> 

### <a name="<a-name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a>-install-sap-with-a-high-availability-ascs/scs-instance"></a><a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> 使用高可用性 ASCS/SCS 執行個體安裝 SAP
> [!IMPORTANT]
> 請不要將分頁檔放在 DataKeeper 鏡像磁碟區上。 DataKeeper 不支援鏡像磁碟區。 您可以將分頁檔留在 Azure 虛擬機器的暫存磁碟機 D 中，此為預設值。 如果還不在此磁碟機中，請將 Windows 分頁檔移至 Azure 虛擬機器的磁碟機 D。
> 
> 

#### <a name="<a-name="a97ad604-9094-44fe-a364-f89cb39bf097"></a>-create-a-virtual-host-name-for-the-clustered-sap-ascs/scs-instance"></a><a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> 建立叢集 SAP ASCS/SCS 執行個體的虛擬主機名稱
首先，在「Windows DNS 管理員」中，為 ASCS/SCS 執行個體的虛擬主機名稱建立 DNS 項目。 然後，定義指派給虛擬主機名稱的 IP 位址。

> [!NOTE]
> 請記住，您指派給 ASCS/SCS 執行個體之虛擬主機名稱的 IP 位址必須與指派給 Azure Load Balancer (<*SID*>-lb-ascs) 的 IP 位址相同。  
> 
> 

虛擬 SAP ASCS/SCS 主機名稱 (pr1-ascs-sap) 的 IP 位址與 Azure Load Balancer (pr1-lb-ascs) 的 IP 位址相同。

只有一個 SAP 容錯移轉叢集角色可以在 Azure 中的某個 Windows Server 容錯移轉叢集中執行。 例如，這表示 ABAP 系統的某個 ASCS 執行個體和 Java 系統的某個 SCS 執行個體。 對於 ABAP+Java，這會是某個 ASCS 執行個體和某個 SCS 執行個體。

> [!NOTE]
> 目前，SAP 安裝指南 (請參閱 [SAP 安裝指南][sap-installation-guides]) 中所述的多 SID 叢集在 Azure 中無法運作。
> 
> 

![圖 54：定義 SAP ASCS/SCS 叢集虛擬名稱和 TCP/IP 位址的 DNS 項目][sap-ha-guide-figure-3046]

***圖 54：**定義 SAP ASCS/SCS 叢集虛擬名稱和 TCP/IP 位址的 DNS 項目*

該項目會在 [DNS 管理員] 的網域底下，如圖 55 所示。

![圖 55：SAP ASCS/SCS 叢集組態的新虛擬名稱和 TCP/IP 位址][sap-ha-guide-figure-3047]

***圖 55：**SAP ASCS/SCS 叢集組態的新虛擬名稱和 TCP/IP 位址*

#### <a name="<a-name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a>-install-the-sap-first-cluster-node"></a><a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> 安裝 SAP 的第一個叢集節點
若要安裝 SAP 第一個叢集，請執行叢集節點 A 上的第一個叢集節點選項。例如，在 **pr1-ascs-0** 主機。

如果您想要為 Azure 內部負載平衡器保留預設連接埠，請選取：

* **ABAP 系統**：**ASCS** 執行個體號碼 **00**
* **Java 系統**：**SCS** 執行個體號碼 **01**
* **ABAP + JAVA 系統**：**ASCS** 執行個體號碼 **00** 和 **SCS** 執行個體號碼 **01**

如果您想要針對 ABAP ASCS 執行個體使用 00 以外的執行個體號碼，以及針對 Java SCS 執行個體使用 01 以外的執行個體號碼，您必須先變更 Azure 內部負載平衡器預設的負載平衡規則，如 [變更 Azure 內部負載平衡器的 ASCS/SCS 預設負載平衡規則][sap-ha-guide-8.9] 所述。

然後，執行一般 SAP 安裝文件中未說明的幾個步驟。

> [!NOTE]
> SAP 安裝文件說明如何安裝第一個 ASCS/SCS 叢集節點。
> 
> 

#### <a name="<a-name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a>-modify-the-sap-profile-of-the-ascs/scs-i```powershellnstance"></a><a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> 修改 ASCS/SCS i```powershellnstance 的 SAP 設定檔
您需要新增新的設定檔參數。 此設定檔參數可避免 SAP 工作程序與加入佇列伺服器之間的連線在閒置時間太長時關閉。 我們在本文中的 [在 SAP ASCS/SCS 執行個體的兩個叢集節點上都新增登錄項目][sap-ha-guide-8.11] 已提到問題案例。 在該部分，我們也介紹了對一些基本 TCP/IP 連線參數所做的兩項變更。 在第二個步驟中，您必須設定讓加入佇列伺服器傳送 **keep_alive** 訊號，如此連線才不會達到 Azure 內部負載平衡器的閒置臨界值。

將此設定檔參數新增至 SAP ASCS/SCS 執行個體設定檔：

```
enque/encni/set_so_keepalive = true
```
在我們的範例中，路徑為：

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

例如，新增到 SAP SCS 執行個體設定檔和對應的路徑：

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`

#### <a name="<a-name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a>-add-a-probe-port"></a><a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> 新增探查連接埠
使用內部負載平衡器探查功能，讓整個叢集組態使用負載平衡器。 Azure 內部負載平衡器通常會在參與的虛擬機器之間，平均分配內送的工作負載。 不過，這對某些叢集組態不會產生作用，因為只有一個執行個體是主動的。 另一個執行個體是被動的，而且不接受任何工作負載。 Azure 內部負載平衡器僅將工作指派給主動的執行個體時，探查功能才可提供協助。 使用探查功能，內部負載平衡器便能夠偵測哪些執行個體是主動的，然後只以該執行個體作為工作負載的目標。

首先，請使用此 PowerShell 命令檢查目前 **ProbePort** 設定。 在叢集組態中的其中一個虛擬機器中執行該檢查：

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter
```

![圖 56：叢集組態探查連接埠預設為 0][sap-ha-guide-figure-3048]

***圖 56：**叢集組態探查連接埠預設為 0*

然後，定義探查連接埠。 預設探查連接埠號碼為 0。 在我們的範例中，我們會使用探查連接埠 **62300**。

SAP Azure Resource Manager 範本中已定義連接埠號碼。 您可以在 PowerShell 中指派連接埠號碼。

首先，取得 SAP 虛擬主機名稱叢集資源 **SAP WAC IP**。

```PowerShell
$var = Get-ClusterResource | Where-Object {  $_.name -eq "SAP PR1 IP"  }
```

接著，將探查連接埠設定為 **62300**。

```PowerShell
$var | Set-ClusterParameter -Multiple @{"Address"="10.0.0.43";"ProbePort"=62300;"Subnetmask"="255.255.255.0";"Network"="Cluster Network 1";"OverrideAddressMatch"=1;"EnableDhcp"=0}  
```

若要啟用變更，請將 **SAP PR1** 叢集角色停止後再啟動。

讓 **SAP PR1** 叢集角色上線之後，請確認 **ProbePort** 已設定為新值：

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter
```

![圖 57：設定新值之後，探查叢集連接埠][sap-ha-guide-figure-3049]

***圖 57：**設定新值之後，探查叢集連接埠*

**ProbePort** 已設為 **62300**。 現在您可以從其他主機 (例如 **ascsha-dbas**) 存取檔案共用 **\\\ascsha-clsap\sapmnt**。

### <a name="<a-name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a>-install-the-database-instance"></a><a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> 安裝資料庫執行個體
若要安裝資料庫執行個體，請依照 SAP 安裝文件中所述的程序。

### <a name="<a-name="8a276e16-f507-4071-b829-cdc0a4d36748"></a>-install-the-second-cluster-node"></a><a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> 安裝第二個叢集節點
若要安裝第二個叢集，請依照 SAP 安裝指南中的步驟。

### <a name="<a-name="094bc895-31d4-4471-91cc-1513b64e406a"></a>-change-the-start-type-of-the-sap-ers-windows-service-instance"></a><a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> 變更 SAP ERS Windows 服務執行個體的啟動類型
將兩個叢集節點上 SAP Enqueue Replication Server (ERS) Windows 服務的啟動類型變更為 [自動 (延遲啟動)]。

![圖 58：將 SAP ERS 執行個體的服務類型變更為延遲的自動類型][sap-ha-guide-figure-3050]

***圖 58：**將 SAP ERS 執行個體的服務類型變更為延遲的自動類型*

### <a name="<a-name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a>-install-the-sap-primary-application-server"></a><a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> 安裝 SAP 主要應用程式伺服器
在您指派來裝載 PAS 的虛擬機器上安裝主要應用程式伺服器 (PAS) 執行個體 <*SID*>-di-0。 不需要提供任何 Azure 或 DataKeeper 詳細資料。

### <a name="<a-name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a>-install-the-sap-additional-application-server"></a><a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> 安裝 SAP 其他應用程式伺服器
在您指派來裝載 SAP 應用程式伺服器的所有虛擬機器上安裝 SAP 其他應用程式伺服器 (AAS)。 例如，在 <*SID*>-di-1 到 <*SID*>-di-<n>。

## <a name="<a-name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a>-test-the-sap-ascs/scs-instance-failover-and-sios-replication"></a><a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> 測試 SAP ASCS/SCS 執行個體容錯移轉和 SIOS 複寫
使用「容錯移轉叢集管理員」和 SIOS DataKeeper UI，可輕鬆地測試及監視 SAP ASCS/SCS 執行個體容錯移轉和 SIOS 磁碟複寫。

### <a name="<a-name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a>-sap-ascs/scs-instance-is-running-on-cluster-node-a"></a><a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> SAP ASCS/SCS 執行個體在叢集節點 A 上執行
**SAP WAC** 叢集群組在叢集節點 A 上執行，例如，在 **ascsha-clna** 上。 將屬於 **SAP WAC** 叢集群組並由 ASCS/SCS 執行個體使用的共用磁碟 S，指派給叢集節點 A。

![圖 59：容錯移轉叢集管理員︰SAP <*SID*> 叢集群組在叢集節點 A 上執行][sap-ha-guide-figure-5000]

***圖 59：**容錯移轉叢集管理員︰SAP <*SID*> 叢集群組在叢集節點 A 上執行*

藉由使用 SIOS DataKeeper UI，您可以看到共用磁碟資料以同步方式從叢集節點 A 上的來源磁碟區 S 複寫到叢集節點 B 上的目標磁碟區 S。例如，從 **ascsha-clna [10.0.0.41]** 到 **ascsha-clnb [10.0.0.42]**。

![圖 60：在 SIOS DataKeeper 中，將本機磁碟區從叢集節點 A 的磁碟區複寫到叢集節點 B][sap-ha-guide-figure-5001]

***圖 60：**在 SIOS DataKeeper 中，將本機磁碟區從叢集節點 A 的磁碟區複寫到叢集節點 B*

### <a name="<a-name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a>-failover-from-node-a-to-node-b"></a><a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> 從節點 A 到節點 B 進行容錯移轉
您可以使用下列其中一個選項以起始將 SAP <*SID*> 叢集群組從叢集節點 A 到叢集節點 B 的容錯移轉：

* 使用容錯移轉叢集管理員  
* 使用容錯移轉叢集 PowerShell
  ```powershell
  Move-ClusterGroup -Name "SAP WAC"
  ```
* 在 Windows 客體作業系統內重新啟動叢集節點 A (這會起始將 SAP <*SID*> 叢集群組從節點 A 容錯移轉到節點 B 的自動容錯移轉)  
* 從 Azure 入口網站重新啟動叢集節點 A (這會起始將 SAP <*SID*> 叢集群組從節點 A 到節點 B 的自動容錯移轉)  
* 使用 Azure PowerShell 重新啟動叢集節點 A (這會起始將 SAP <*SID*> 叢集群組從節點 A 到節點 B 的自動容錯移轉)
  
  ```powershell
  Restart-AzureVM -Name ascsha-clna -ServiceName ascsha-cluster
  ```

容錯移轉之後，SAP <*SID*> 叢集群組會在叢集節點 B 上執行。例如，在 **ascsha-clnb** 上。

![圖 61：在「容錯移轉叢集管理員」中，SAP <*SID*> 叢集群組在叢集節點 B 上執行][sap-ha-guide-figure-5002]

***圖 61**：在「容錯移轉叢集管理員」中，SAP <*SID*> 叢集群組在叢集節點 B 上執行*

共用磁碟現在已掛接在叢集節點 B 上。SIOS DataKeeper 正在將資料從叢集節點 B 上的來源磁碟區 S 複寫到叢集節點 A 上的目標磁碟區 S。例如，從 **ascsha-clnb [10.0.0.42]** 到 **ascsha-clna [10.0.0.41]**。

![圖 62：SIOS DataKeeper 將本機磁碟區從叢集節點 B 複寫到叢集節點 A][sap-ha-guide-figure-5003]

***圖 62：**SIOS DataKeeper 將本機磁碟區從叢集節點 B 複寫到叢集節點 A*

<!--HONumber=Oct16_HO2-->


