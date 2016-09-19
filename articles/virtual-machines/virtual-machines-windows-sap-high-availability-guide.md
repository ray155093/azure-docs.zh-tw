<properties
   pageTitle="Windows 虛擬機器 (VM) 上的 SAP NetWeaver - 高可用性指南 | Microsoft Azure"
   description="Windows 虛擬機器 (VM) 上的 SAP NetWeaver - 高可用性指南"
   services="virtual-machines-windows,virtual-network,storage"
   documentationCenter="saponazure"
   authors="goraco"
   manager="juergent"
   editor=""
   tags="azure-resource-manager"
   keywords=""/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="campaign-page"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="na"
   ms.date="08/18/2016"
   ms.author="goraco"/>

# Windows 虛擬機器 (VM) 上的 SAP NetWeaver - 高可用性指南

[767598]: https://service.sap.com/sap/support/notes/767598
[773830]: https://service.sap.com/sap/support/notes/773830
[826037]: https://service.sap.com/sap/support/notes/826037
[965908]: https://service.sap.com/sap/support/notes/965908
[1031096]: https://service.sap.com/sap/support/notes/1031096
[1139904]: https://service.sap.com/sap/support/notes/1139904
[1173395]: https://service.sap.com/sap/support/notes/1173395
[1245200]: https://service.sap.com/sap/support/notes/1245200
[1409604]: https://service.sap.com/sap/support/notes/1409604
[1558958]: https://service.sap.com/sap/support/notes/1558958
[1585981]: https://service.sap.com/sap/support/notes/1585981
[1588316]: https://service.sap.com/sap/support/notes/1588316
[1590719]: https://service.sap.com/sap/support/notes/1590719
[1597355]: https://service.sap.com/sap/support/notes/1597355
[1605680]: https://service.sap.com/sap/support/notes/1605680
[1619720]: https://service.sap.com/sap/support/notes/1619720
[1619726]: https://service.sap.com/sap/support/notes/1619726
[1619967]: https://service.sap.com/sap/support/notes/1619967
[1750510]: https://service.sap.com/sap/support/notes/1750510
[1752266]: https://service.sap.com/sap/support/notes/1752266
[1757924]: https://service.sap.com/sap/support/notes/1757924
[1757928]: https://service.sap.com/sap/support/notes/1757928
[1758182]: https://service.sap.com/sap/support/notes/1758182
[1758496]: https://service.sap.com/sap/support/notes/1758496
[1772688]: https://service.sap.com/sap/support/notes/1772688
[1814258]: https://service.sap.com/sap/support/notes/1814258
[1882376]: https://service.sap.com/sap/support/notes/1882376
[1909114]: https://service.sap.com/sap/support/notes/1909114
[1922555]: https://service.sap.com/sap/support/notes/1922555
[1928533]: https://service.sap.com/sap/support/notes/1928533
[1941500]: https://service.sap.com/sap/support/notes/1941500
[1956005]: https://service.sap.com/sap/support/notes/1956005
[1973241]: https://service.sap.com/sap/support/notes/1973241
[1984787]: https://service.sap.com/sap/support/notes/1984787
[1999351]: https://service.sap.com/sap/support/notes/1999351
[2002167]: https://service.sap.com/sap/support/notes/2002167
[2015553]: https://service.sap.com/sap/support/notes/2015553
[2039619]: https://service.sap.com/sap/support/notes/2039619
[2121797]: https://service.sap.com/sap/support/notes/2121797
[2134316]: https://service.sap.com/sap/support/notes/2134316
[2178632]: https://service.sap.com/sap/support/notes/2178632
[2191498]: https://service.sap.com/sap/support/notes/2191498
[2233094]: https://service.sap.com/sap/support/notes/2233094
[2243692]: https://service.sap.com/sap/support/notes/2243692

[sap-installation-guides]: http://service.sap.com/instguides

[azure-cli]: ../xplat-cli-install.md
[azure-portal]: https://portal.azure.com
[azure-ps]: ../powershell-install-configure.md
[azure-quickstart-templates-github]: https://github.com/Azure/azure-quickstart-templates
[azure-script-ps]: https://go.microsoft.com/fwlink/p/?LinkID=395017
[azure-subscription-service-limits]: ../azure-subscription-service-limits.md
[azure-subscription-service-limits-subscription]: ../azure-subscription-service-limits.md#subscription

[dbms-guide]: virtual-machines-windows-sap-dbms-guide.md "Windows 虛擬機器 (VM) 上的 SAP NetWeaver - DBMS 部署指南"
[dbms-guide-2.1]: virtual-machines-windows-sap-dbms-guide.md#c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f "VM 和 VHD 的快取"
[dbms-guide-2.2]: virtual-machines-windows-sap-dbms-guide.md#c8e566f9-21b7-4457-9f7f-126036971a91 "軟體 RAID"
[dbms-guide-2.3]: virtual-machines-windows-sap-dbms-guide.md#10b041ef-c177-498a-93ed-44b3441ab152 "Microsoft Azure 儲存體"
[dbms-guide-2]: virtual-machines-windows-sap-dbms-guide.md#65fa79d6-a85f-47ee-890b-22e794f51a64 "RDBMS 部署結構"
[dbms-guide-3]: virtual-machines-windows-sap-dbms-guide.md#871dfc27-e509-4222-9370-ab1de77021c3 "Azure VM 的相關高可用性和災害復原"
[dbms-guide-5.5.1]: virtual-machines-windows-sap-dbms-guide.md#0fef0e79-d3fe-4ae2-85af-73666a6f7268 "SQL Server 2012 SP1 CU4 和更新版本"
[dbms-guide-5.5.2]: virtual-machines-windows-sap-dbms-guide.md#f9071eff-9d72-4f47-9da4-1852d782087b "SQL Server 2012 SP1 CU3 和舊版"
[dbms-guide-5.6]: virtual-machines-windows-sap-dbms-guide.md#1b353e38-21b3-4310-aeb6-a77e7c8e81c8 "使用來自 Microsoft Azure Marketplace 的 SQL Server 映像"
[dbms-guide-5.8]: virtual-machines-windows-sap-dbms-guide.md#9053f720-6f3b-4483-904d-15dc54141e30 "適用於 Azure 上 SAP 的一般 SQL Server 摘要"
[dbms-guide-5]: virtual-machines-windows-sap-dbms-guide.md#3264829e-075e-4d25-966e-a49dad878737 "SQL Server RDBMS 專屬的詳細資料"
[dbms-guide-8.4.1]: virtual-machines-windows-sap-dbms-guide.md#b48cfe3b-48e9-4f5b-a783-1d29155bd573 "儲存體組態"
[dbms-guide-8.4.2]: virtual-machines-windows-sap-dbms-guide.md#23c78d3b-ca5a-4e72-8a24-645d141a3f5d "備份與還原"
[dbms-guide-8.4.3]: virtual-machines-windows-sap-dbms-guide.md#77cd2fbb-307e-4cbf-a65f-745553f72d2c "備份與還原的效能考量"
[dbms-guide-8.4.4]: virtual-machines-windows-sap-dbms-guide.md#f77c1436-9ad8-44fb-a331-8671342de818 "其他"
[dbms-guide-900-sap-cache-server-on-premises]: virtual-machines-windows-sap-dbms-guide.md#642f746c-e4d4-489d-bf63-73e80177a0a8

[dbms-guide-figure-100]: ./media/virtual-machines-shared-sap-dbms-guide/100_storage_account_types.png
[dbms-guide-figure-200]: ./media/virtual-machines-shared-sap-dbms-guide/200-ha-set-for-dbms-ha.png
[dbms-guide-figure-300]: ./media/virtual-machines-shared-sap-dbms-guide/300-reference-config-iaas.png
[dbms-guide-figure-400]: ./media/virtual-machines-shared-sap-dbms-guide/400-sql-2012-backup-to-blob-storage.png
[dbms-guide-figure-500]: ./media/virtual-machines-shared-sap-dbms-guide/500-sql-2012-backup-to-blob-storage-different-containers.png
[dbms-guide-figure-600]: ./media/virtual-machines-shared-sap-dbms-guide/600-iaas-maxdb.png
[dbms-guide-figure-700]: ./media/virtual-machines-shared-sap-dbms-guide/700-livecach-prod.png
[dbms-guide-figure-800]: ./media/virtual-machines-shared-sap-dbms-guide/800-azure-vm-sap-content-server.png
[dbms-guide-figure-900]: ./media/virtual-machines-shared-sap-dbms-guide/900-sap-cache-server-on-premises.png

[deployment-guide]: virtual-machines-windows-sap-deployment-guide.md "Windows 虛擬機器 (VM) 上的 SAP NetWeaver - 部署指南"
[deployment-guide-2.2]: virtual-machines-windows-sap-deployment-guide.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 "SAP 資源"
[deployment-guide-3.1.2]: virtual-machines-windows-sap-deployment-guide.md#3688666f-281f-425b-a312-a77e7db2dfab "使用自訂映像部署 VM"
[deployment-guide-3.2]: virtual-machines-windows-sap-deployment-guide.md#db477013-9060-4602-9ad4-b0316f8bb281 "案例 1：從 Azure Marketplace 為 SAP 部署 VM"
[deployment-guide-3.3]: virtual-machines-windows-sap-deployment-guide.md#54a1fc6d-24fd-4feb-9c57-ac588a55dff2 "案例 2：使用自訂映像為 SAP 部署 VM"
[deployment-guide-3.4]: virtual-machines-windows-sap-deployment-guide.md#a9a60133-a763-4de8-8986-ac0fa33aa8c1 "案例 3：使用非一般化 Azure VHD 搭配 SAP 從內部部署環境移動 VM"
[deployment-guide-3]: virtual-machines-windows-sap-deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e "適用於 Microsoft Azure 上 SAP 的 VM 部署案例"
[deployment-guide-4.1]: virtual-machines-windows-sap-deployment-guide.md#604bcec2-8b6e-48d2-a944-61b0f5dee2f7 "部署 Azure PowerShell Cmdlet"
[deployment-guide-4.2]: virtual-machines-windows-sap-deployment-guide.md#7ccf6c3e-97ae-4a7a-9c75-e82c37beb18e "下載並匯入與 SAP 相關的 PowerShell Cmdlet"
[deployment-guide-4.3]: virtual-machines-windows-sap-deployment-guide.md#31d9ecd6-b136-4c73-b61e-da4a29bbc9cc "將 VM 加入內部部署網域 - 僅限 Windows"
[deployment-guide-4.4.2]: virtual-machines-windows-sap-deployment-guide.md#6889ff12-eaaf-4f3c-97e1-7c9edc7f7542 "Linux"
[deployment-guide-4.4]: virtual-machines-windows-sap-deployment-guide.md#c7cbb0dc-52a4-49db-8e03-83e7edc2927d "下載、安裝和啟用 Azure VM 代理程式"
[deployment-guide-4.5.1]: virtual-machines-windows-sap-deployment-guide.md#987cf279-d713-4b4c-8143-6b11589bb9d4 "Azure PowerShell"
[deployment-guide-4.5.2]: virtual-machines-windows-sap-deployment-guide.md#408f3779-f422-4413-82f8-c57a23b4fc2f "Azure CLI"
[deployment-guide-4.5]: virtual-machines-windows-sap-deployment-guide.md#d98edcd3-f2a1-49f7-b26a-07448ceb60ca "設定 Azure Enhanced Monitoring Extension for SAP"
[deployment-guide-5.1]: virtual-machines-windows-sap-deployment-guide.md#bb61ce92-8c5c-461f-8c53-39f5e5ed91f2 "Azure Enhanced Monitoring for SAP 整備檢查"
[deployment-guide-5.2]: virtual-machines-windows-sap-deployment-guide.md#e2d592ff-b4ea-4a53-a91a-e5521edb6cd1 "Azure 監視基礎結構組態的健康狀態檢查"
[deployment-guide-5.3]: virtual-machines-windows-sap-deployment-guide.md#fe25a7da-4e4e-4388-8907-8abc2d33cfd8 "適用於 SAP 之 Azure 監視基礎結構的進一步疑難排解"

[deployment-guide-configure-monitoring-scenario-1]: virtual-machines-windows-sap-deployment-guide.md#ec323ac3-1de9-4c3a-b770-4ff701def65b "設定監視"
[deployment-guide-configure-proxy]: virtual-machines-windows-sap-deployment-guide.md#baccae00-6f79-4307-ade4-40292ce4e02d "設定 Proxy"
[deployment-guide-figure-100]: ./media/virtual-machines-shared-sap-deployment-guide/100-deploy-vm-image.png
[deployment-guide-figure-1000]: ./media/virtual-machines-shared-sap-deployment-guide/1000-service-properties.png
[deployment-guide-figure-11]: virtual-machines-windows-sap-deployment-guide.md#figure-11
[deployment-guide-figure-1100]: ./media/virtual-machines-shared-sap-deployment-guide/1100-azperflib.png
[deployment-guide-figure-1200]: ./media/virtual-machines-shared-sap-deployment-guide/1200-cmd-test-login.png
[deployment-guide-figure-1300]: ./media/virtual-machines-shared-sap-deployment-guide/1300-cmd-test-executed.png
[deployment-guide-figure-14]: virtual-machines-windows-sap-deployment-guide.md#figure-14
[deployment-guide-figure-1400]: ./media/virtual-machines-shared-sap-deployment-guide/1400-azperflib-error-servicenotstarted.png
[deployment-guide-figure-300]: ./media/virtual-machines-shared-sap-deployment-guide/300-deploy-private-image.png
[deployment-guide-figure-400]: ./media/virtual-machines-shared-sap-deployment-guide/400-deploy-using-disk.png
[deployment-guide-figure-5]: virtual-machines-windows-sap-deployment-guide.md#figure-5
[deployment-guide-figure-50]: ./media/virtual-machines-shared-sap-deployment-guide/50-forced-tunneling-suse.png
[deployment-guide-figure-500]: ./media/virtual-machines-shared-sap-deployment-guide/500-install-powershell.png
[deployment-guide-figure-6]: virtual-machines-windows-sap-deployment-guide.md#figure-6
[deployment-guide-figure-600]: ./media/virtual-machines-shared-sap-deployment-guide/600-powershell-version.png
[deployment-guide-figure-7]: virtual-machines-windows-sap-deployment-guide.md#figure-7
[deployment-guide-figure-700]: ./media/virtual-machines-shared-sap-deployment-guide/700-install-powershell-installed.png
[deployment-guide-figure-760]: ./media/virtual-machines-shared-sap-deployment-guide/760-azure-cli-version.png
[deployment-guide-figure-900]: ./media/virtual-machines-shared-sap-deployment-guide/900-cmd-update-executed.png
[deployment-guide-figure-azure-cli-installed]: virtual-machines-windows-sap-deployment-guide.md#402488e5-f9bb-4b29-8063-1c5f52a892d0
[deployment-guide-figure-azure-cli-version]: virtual-machines-windows-sap-deployment-guide.md#0ad010e6-f9b5-4c21-9c09-bb2e5efb3fda
[deployment-guide-install-vm-agent-windows]: virtual-machines-windows-sap-deployment-guide.md#b2db5c9a-a076-42c6-9835-16945868e866
[deployment-guide-troubleshooting-chapter]: virtual-machines-windows-sap-deployment-guide.md#564adb4f-5c95-4041-9616-6635e83a810b "針對 Azure 上的 SAP 進行端對端監視設定檢查及疑難排解"

[deploy-template-cli]: ../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows
[deploy-template-portal]: ../resource-group-template-deploy.md#deploy-with-the-preview-portal
[deploy-template-powershell]: ../resource-group-template-deploy.md#deploy-with-powershell

[dr-guide-classic]: http://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]: virtual-machines-windows-sap-get-started.md
[getting-started-dbms]: virtual-machines-windows-sap-get-started.md#1343ffe1-8021-4ce6-a08d-3a1553a4db82
[getting-started-deployment]: virtual-machines-windows-sap-get-started.md#6aadadd2-76b5-46d8-8713-e8d63630e955
[getting-started-planning]: virtual-machines-windows-sap-get-started.md#3da0389e-708b-4e82-b2a2-e92f132df89c

[getting-started-windows-classic]: virtual-machines-windows-classic-sap-get-started.md
[getting-started-windows-classic-dbms]: virtual-machines-windows-classic-sap-get-started.md#c5b77a14-f6b4-44e9-acab-4d28ff72a930
[getting-started-windows-classic-deployment]: virtual-machines-windows-classic-sap-get-started.md#f84ea6ce-bbb4-41f7-9965-34d31b0098ea
[getting-started-windows-classic-dr]: virtual-machines-windows-classic-sap-get-started.md#cff10b4a-01a5-4dc3-94b6-afb8e55757d3
[getting-started-windows-classic-ha-sios]: virtual-machines-windows-classic-sap-get-started.md#4bb7512c-0fa0-4227-9853-4004281b1037
[getting-started-windows-classic-planning]: virtual-machines-windows-classic-sap-get-started.md#f2a5e9d8-49e4-419e-9900-af783173481c

[ha-guide-classic]: http://go.microsoft.com/fwlink/?LinkId=613056

[ha-guide]: virtual-machines-windows-sap-high-availability-guide.md

[install-extension-cli]: virtual-machines-linux-enable-aem.md

[Logo_Linux]: ./media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]: ./media/virtual-machines-shared-sap-shared/Windows.png

[msdn-set-azurermvmaemextension]: https://msdn.microsoft.com/library/azure/mt670598.aspx

[planning-guide]: virtual-machines-windows-sap-planning-guide.md "Windows 虛擬機器 (VM) 上的 SAP NetWeaver - 規劃和實作指南"
[planning-guide-1.2]: virtual-machines-windows-sap-planning-guide.md#e55d1e22-c2c8-460b-9897-64622a34fdff "資源"
[planning-guide-11]: virtual-machines-windows-sap-planning-guide.md#7cf991a1-badd-40a9-944e-7baae842a058 "Azure 虛擬機器上執行之 SAP NetWeaver 的高可用性 (HA) 和災害復原 (DR)"
[planning-guide-11.4.1]: virtual-machines-windows-sap-planning-guide.md#5d9d36f9-9058-435d-8367-5ad05f00de77 "SAP 應用程式伺服器的高可用性"
[planning-guide-11.5]: virtual-machines-windows-sap-planning-guide.md#4e165b58-74ca-474f-a7f4-5e695a93204f "對 SAP 執行個體使用自動啟動"
[planning-guide-2.1]: virtual-machines-windows-sap-planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803 "僅限雲端 - 將虛擬機器部署到 Azure，無須倚賴內部部署客戶網路"
[planning-guide-2.2]: virtual-machines-windows-sap-planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10 "跨單位 - 將單一或多個 SAP VM 部署到 Azure，必須完全整合到內部部署網路"
[planning-guide-3.1]: virtual-machines-windows-sap-planning-guide.md#be80d1b9-a463-4845-bd35-f4cebdb5424a "Azure 區域"
[planning-guide-3.2.1]: virtual-machines-windows-sap-planning-guide.md#df49dc09-141b-4f34-a4a2-990913b30358 "容錯網域"
[planning-guide-3.2.2]: virtual-machines-windows-sap-planning-guide.md#fc1ac8b2-e54a-487c-8581-d3cc6625e560 "升級網域"
[planning-guide-3.2.3]: virtual-machines-windows-sap-planning-guide.md#18810088-f9be-4c97-958a-27996255c665 "Azure 可用性設定組"
[planning-guide-3.2]: virtual-machines-windows-sap-planning-guide.md#8d8ad4b8-6093-4b91-ac36-ea56d80dbf77 "Microsoft Azure 虛擬機器概念"
[planning-guide-3.3.2]: virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Azure 進階儲存體"
[planning-guide-5.1.1]: virtual-machines-windows-sap-planning-guide.md#4d175f1b-7353-4137-9d2f-817683c26e53 "使用非一般化磁碟將 VM 從內部部署移至 Azure"
[planning-guide-5.1.2]: virtual-machines-windows-sap-planning-guide.md#e18f7839-c0e2-4385-b1e6-4538453a285c "使用客戶特定的映像部署 VM"
[planning-guide-5.2.1]: virtual-machines-windows-sap-planning-guide.md#1b287330-944b-495d-9ea7-94b83aff73ef "準備使用非一般化磁碟將 VM 從內部部署移至 Azure"
[planning-guide-5.2.2]: virtual-machines-windows-sap-planning-guide.md#57f32b1c-0cba-4e57-ab6e-c39fe22b6ec3 "準備使用客戶特定的映像為 SAP 部署 VM"
[planning-guide-5.2]: virtual-machines-windows-sap-planning-guide.md#6ffb9f41-a292-40bf-9e70-8204448559e7 "使用適用於 Azure 的 SAP 準備 VM"
[planning-guide-5.3.1]: virtual-machines-windows-sap-planning-guide.md#6e835de8-40b1-4b71-9f18-d45b20959b79 "Azure 磁碟與 Azure 映像之間的差異"
[planning-guide-5.3.2]: virtual-machines-windows-sap-planning-guide.md#a43e40e6-1acc-4633-9816-8f095d5a7b6a "將 VHD 從內部部署環境上傳至 Azure"
[planning-guide-5.4.2]: virtual-machines-windows-sap-planning-guide.md#9789b076-2011-4afa-b2fe-b07a8aba58a1 "在 Azure 儲存體帳戶之間複製磁碟"
[planning-guide-5.5.1]: virtual-machines-windows-sap-planning-guide.md#4efec401-91e0-40c0-8e64-f2dceadff646 "SAP 部署的 VM/VHD 結構"
[planning-guide-5.5.3]: virtual-machines-windows-sap-planning-guide.md#17e0d543-7e8c-4160-a7da-dd7117a1ad9d "為連接的磁碟設定自動掛接"
[planning-guide-7.1]: virtual-machines-windows-sap-planning-guide.md#3e9c3690-da67-421a-bc3f-12c520d99a30 "搭配 SAP NetWeaver 示範/訓練案例的單一 VM"
[planning-guide-7]: virtual-machines-windows-sap-planning-guide.md#96a77628-a05e-475d-9df3-fb82217e8f14 "SAP 執行個體的僅限雲端部署概念"
[planning-guide-9.1]: virtual-machines-windows-sap-planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 "適用於 SAP 的 Azure 監視解決方案"
[planning-guide-azure-premium-storage]: virtual-machines-windows-sap-planning-guide.md#ff5ad0f9-f7f4-4022-9102-af07aef3bc92 "Azure 進階儲存體"

[planning-guide-figure-100]: ./media/virtual-machines-shared-sap-planning-guide/100-single-vm-in-azure.png
[planning-guide-figure-1300]: ./media/virtual-machines-shared-sap-planning-guide/1300-ref-config-iaas-for-sap.png
[planning-guide-figure-1400]: ./media/virtual-machines-shared-sap-planning-guide/1400-attach-detach-disks.png
[planning-guide-figure-1600]: ./media/virtual-machines-shared-sap-planning-guide/1600-firewall-port-rule.png
[planning-guide-figure-1700]: ./media/virtual-machines-shared-sap-planning-guide/1700-single-vm-demo.png
[planning-guide-figure-1900]: ./media/virtual-machines-shared-sap-planning-guide/1900-vm-set-vnet.png
[planning-guide-figure-200]: ./media/virtual-machines-shared-sap-planning-guide/200-multiple-vms-in-azure.png
[planning-guide-figure-2100]: ./media/virtual-machines-shared-sap-planning-guide/2100-s2s.png
[planning-guide-figure-2200]: ./media/virtual-machines-shared-sap-planning-guide/2200-network-printing.png
[planning-guide-figure-2300]: ./media/virtual-machines-shared-sap-planning-guide/2300-sapgui-stms.png
[planning-guide-figure-2400]: ./media/virtual-machines-shared-sap-planning-guide/2400-vm-extension-overview.png
[planning-guide-figure-2500]: ./media/virtual-machines-shared-sap-planning-guide/2500-vm-extension-details.png
[planning-guide-figure-2600]: ./media/virtual-machines-shared-sap-planning-guide/2600-sap-router-connection.png
[planning-guide-figure-2700]: ./media/virtual-machines-shared-sap-planning-guide/2700-exposed-sap-portal.png
[planning-guide-figure-2800]: ./media/virtual-machines-shared-sap-planning-guide/2800-endpoint-config.png
[planning-guide-figure-2900]: ./media/virtual-machines-shared-sap-planning-guide/2900-azure-ha-sap-ha.png
[planning-guide-figure-300]: ./media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png
[planning-guide-figure-3000]: ./media/virtual-machines-shared-sap-planning-guide/3000-sap-ha-on-azure.png
[planning-guide-figure-3200]: ./media/virtual-machines-shared-sap-planning-guide/3200-sap-ha-with-sql.png
[planning-guide-figure-400]: ./media/virtual-machines-shared-sap-planning-guide/400-vm-services.png
[planning-guide-figure-600]: ./media/virtual-machines-shared-sap-planning-guide/600-s2s-details.png
[planning-guide-figure-700]: ./media/virtual-machines-shared-sap-planning-guide/700-decision-tree-deploy-to-azure.png
[planning-guide-figure-800]: ./media/virtual-machines-shared-sap-planning-guide/800-portal-vm-overview.png
[planning-guide-microsoft-azure-networking]: virtual-machines-windows-sap-planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd "Microsoft Azure 網路"
[planning-guide-storage-microsoft-azure-storage-and-data-disks]: virtual-machines-windows-sap-planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f "儲存體：Microsoft Azure 儲存體和資料磁碟"

[sap-ha-guide]: virtual-machines-windows-sap-high-availability-guide.md "Windows 虛擬機器 (VM) 上的 SAP NetWeaver - 高可用性指南"
[sap-ha-guide-1]: virtual-machines-windows-sap-high-availability-guide.md#217c5479-5595-4cd8-870d-15ab00d4f84c "必要條件"
[sap-ha-guide-2]: virtual-machines-windows-sap-high-availability-guide.md#42b8f600-7ba3-4606-b8a5-53c4f026da08 "資源"
[sap-ha-guide-3]: virtual-machines-windows-sap-high-availability-guide.md#42156640c6-01cf-45a9-b225-4baa678b24f1 "Azure Resource Manager 與傳統部署模型之間的 SAP HA 差異"
[sap-ha-guide-3.1]: virtual-machines-windows-sap-high-availability-guide.md#f76af273-1993-4d83-b12d-65deeae23686 "資源群組"
[sap-ha-guide-3.2]: virtual-machines-windows-sap-high-availability-guide.md#3e85fbe0-84b1-4892-87af-d9b65ff91860 "Azure Resource Manager 與傳統部署模型的叢集比較"
[sap-ha-guide-4]: virtual-machines-windows-sap-high-availability-guide.md#8ecf3ba0-67c0-4495-9c14-feec1a2255b7 "Windows Server 容錯移轉叢集 (WSFC)"
[sap-ha-guide-4.1]: virtual-machines-windows-sap-high-availability-guide.md#1a3c5408-b168-46d6-99f5-4219ad1b1ff2 "仲裁模式"
[sap-ha-guide-5]: virtual-machines-windows-sap-high-availability-guide.md#fdfee875-6e66-483a-a343-14bbaee33275 "內部部署環境中的 Windows 容錯移轉叢集"
[sap-ha-guide-5.1]: virtual-machines-windows-sap-high-availability-guide.md#be21cf3e-fb01-402b-9955-54fbecf66592 "共用儲存體"
[sap-ha-guide-5.2]: virtual-machines-windows-sap-high-availability-guide.md#ff7a9a06-2bc5-4b20-860a-46cdb44669cd "網路 / 名稱解析"
[sap-ha-guide-6]: virtual-machines-windows-sap-high-availability-guide.md#2ddba413-a7f5-4e4e-9a51-87908879c10a "與 Microsoft Azure 搭配使用的 Windows 容錯移轉叢集"
[sap-ha-guide-6.1]: virtual-machines-windows-sap-high-availability-guide.md#1a464091-922b-48d7-9d08-7cecf757f341 "使用 SIOS DataKeeper 在 Microsoft Azure 上建立共用磁碟"
[sap-ha-guide-6.2]: virtual-machines-windows-sap-high-availability-guide.md#44641e18-a94e-431f-95ff-303ab65e0bcb "Microsoft Azure 上的名稱解析"
[sap-ha-guide-7]: virtual-machines-windows-sap-high-availability-guide.md#2e3fec50-241e-441b-8708-0b1864f66dfa "Azure IaaS 上的 SAP NetWeaver 高可用性"
[sap-ha-guide-7.1]: virtual-machines-windows-sap-high-availability-guide.md#93faa747-907e-440a-b00a-1ae0a89b1c0e "SAP 應用程式伺服器的高可用性"
[sap-ha-guide-7.2]: virtual-machines-windows-sap-high-availability-guide.md#f559c285-ee68-4eec-add1-f60fe7b978db "SAP (A)SCS 執行個體的高可用性"
[sap-ha-guide-7.2.1]: virtual-machines-windows-sap-high-availability-guide.md#b5b1fd0b-1db4-4d49-9162-de07a0132a51 "在 Azuer 中使用 Windows 容錯移轉叢集提供 SAP (A)SCS 執行個體高可用性"
[sap-ha-guide-7.3]: virtual-machines-windows-sap-high-availability-guide.md#ddd878a0-9c2f-4b8e-8968-26ce60be1027 "DBMS 執行個體的高可用性"
[sap-ha-guide-7.4]: virtual-machines-windows-sap-high-availability-guide.md#045252ed-0277-4fc8-8f46-c5a29694a816 "可能的端對端 HA 部署案例"
[sap-ha-guide-8]: virtual-machines-windows-sap-high-availability-guide.md#78092dbe-165b-454c-92f5-4972bdbef9bf "基礎結構準備"
[sap-ha-guide-8.1]: virtual-machines-windows-sap-high-availability-guide.md#c87a8d3f-b1dc-4d2f-b23c-da4b72977489 "部署具有公司網路連線能力 (跨單位) 的 VM 以供生產環境使用"
[sap-ha-guide-8.2]: virtual-machines-windows-sap-high-availability-guide.md#7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310 "用於測試/示範的 SAP 執行個體僅限雲端部署"
[sap-ha-guide-8.3]: virtual-machines-windows-sap-high-availability-guide.md#47d5300a-a830-41d4-83dd-1a0d1ffdbe6a "Azure 虛擬網路"
[sap-ha-guide-8.4]: virtual-machines-windows-sap-high-availability-guide.md#b22d7b3b-4343-40ff-a319-097e13f62f9e "DNS IP 位址"
[sap-ha-guide-8.5]: virtual-machines-windows-sap-high-availability-guide.md#9fbd43c0-5850-4965-9726-2a921d85d73f "SAP ASCS/SCS 叢集執行個體及 DBMS 叢集執行個體的主機名稱和靜態 IP 位址"
[sap-ha-guide-8.6]: virtual-machines-windows-sap-high-availability-guide.md#84c019fe-8c58-4dac-9e54-173efd4b2c30 "為 SAP VM 設定靜態 IP 位址"
[sap-ha-guide-8.7]: virtual-machines-windows-sap-high-availability-guide.md#7a8f3e9b-0624-4051-9e41-b73fff816a9e "為內部負載平衡器 (ILB) 設定靜態 IP 位址"
[sap-ha-guide-8.8]: virtual-machines-windows-sap-high-availability-guide.md#f19bd997-154d-4583-a46e-7f5a69d0153c "Azure Internal Load Balancer (ILB) 的預設 ASCS/SCS 負載平衡規則"
[sap-ha-guide-8.9]: virtual-machines-windows-sap-high-availability-guide.md#fe0bd8b5-2b43-45e3-8295-80bee5415716 "變更 Azure Internal Load Balancer (ILB) 的 ASCS/SCS 預設負載平衡規則"
[sap-ha-guide-8.10]: virtual-machines-windows-sap-high-availability-guide.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c "將 Windows 機器新增到網域"
[sap-ha-guide-8.11]: virtual-machines-windows-sap-high-availability-guide.md#661035b2-4d0f-4d31-86f8-dc0a50d78158 "在用於 SAP ASCS/SCS 執行個體的兩個叢集節點上都新增登錄項目"
[sap-ha-guide-8.12]: virtual-machines-windows-sap-high-availability-guide.md#0d67f090-7928-43e0-8772-5ccbf8f59aab "SAP ASCS/SCS 執行個體的 Windows Server 容錯移轉叢集設定"
[sap-ha-guide-8.12.1]: virtual-machines-windows-sap-high-availability-guide.md#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79 "收集叢集組態中的叢集節點"
[sap-ha-guide-8.12.2]: virtual-machines-windows-sap-high-availability-guide.md#e49a4529-50c9-4dcf-bde7-15a0c21d21ca "設定叢集檔案共用見證"
[sap-ha-guide-8.12.2.1]: virtual-machines-windows-sap-high-availability-guide.md#06260b30-d697-4c4d-b1c9-d22c0bd64855 "建立檔案共用"
[sap-ha-guide-8.12.2.2]: virtual-machines-windows-sap-high-availability-guide.md#4c08c387-78a0-46b1-9d27-b497b08cac3d "在容錯移轉叢集管理員中設定檔案共用見證"
[sap-ha-guide-8.12.3]: virtual-machines-windows-sap-high-availability-guide.md#5c8e5482-841e-45e1-a89d-a05c0907c868 "為 SAP ASCS/SCS 叢集共用磁碟安裝 SIOS DataKeeper Cluster Edition"
[sap-ha-guide-8.12.3.1]: virtual-machines-windows-sap-high-availability-guide.md#1c2788c3-3648-4e82-9e0d-e058e475e2a3 "新增 Microsoft .NET Framework 3.5 功能"
[sap-ha-guide-8.12.3.2]: virtual-machines-windows-sap-high-availability-guide.md#dd41d5a2-8083-415b-9878-839652812102 "安裝 SIOS DataKeeper"
[sap-ha-guide-8.12.3.3]: virtual-machines-windows-sap-high-availability-guide.md#d9c1fc8e-8710-4dff-bec2-1f535db7b006 "設定 SIOS DataKeeper"
[sap-ha-guide-9]: virtual-machines-windows-sap-high-availability-guide.md#a06f0b49-8a7a-42bf-8b0d-c12026c5746b "SAP NetWeaver 系統安裝"
[sap-ha-guide-9.1]: virtual-machines-windows-sap-high-availability-guide.md#31c6bd4f-51df-4057-9fdf-3fcbc619c170 "具有高可用性 ASCS/SCS 執行個體的 SAP 安裝"
[sap-ha-guide-9.1.1]: virtual-machines-windows-sap-high-availability-guide.md#a97ad604-9094-44fe-a364-f89cb39bf097 "建立叢集 SAP ASCS/SCS 的虛擬主機名稱"
[sap-ha-guide-9.1.2]: virtual-machines-windows-sap-high-availability-guide.md#eb5af918-b42f-4803-bb50-eff41f84b0b0 "安裝 SAP 的第一個叢集節點"
[sap-ha-guide-9.1.3]: virtual-machines-windows-sap-high-availability-guide.md#e4caaab2-e90f-4f2c-bc84-2cd2e12a9556 "修改 ASCS/SCS 執行個體的 SAP 設定檔"
[sap-ha-guide-9.1.4]: virtual-machines-windows-sap-high-availability-guide.md#10822f4f-32e7-4871-b63a-9b86c76ce761 "新增探查連接埠"
[sap-ha-guide-9.2]: virtual-machines-windows-sap-high-availability-guide.md#85d78414-b21d-4097-92b6-34d8bcb724b7 "安裝資料庫執行個體"
[sap-ha-guide-9.3]: virtual-machines-windows-sap-high-availability-guide.md#8a276e16-f507-4071-b829-cdc0a4d36748 "安裝第二個叢集節點"
[sap-ha-guide-9.4]: virtual-machines-windows-sap-high-availability-guide.md#094bc895-31d4-4471-91cc-1513b64e406a "變更 SAP ERS 執行個體的 Windows 服務啟動類型"
[sap-ha-guide-9.5]: virtual-machines-windows-sap-high-availability-guide.md#2477e58f-c5a7-4a5d-9ae3-7b91022cafb5 "SAP 主要應用程式伺服器 (PAS) 安裝"
[sap-ha-guide-9.6]: virtual-machines-windows-sap-high-availability-guide.md#0ba4a6c1-cc37-4bcf-a8dc-025de4263772 "SAP 其他應用程式伺服器 (AAS) 安裝"
[sap-ha-guide-10]: virtual-machines-windows-sap-high-availability-guide.md#18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9 "測試 SAP ASCS/SCS 執行個體容錯移轉和 SIOS 複寫"
[sap-ha-guide-10.1]: virtual-machines-windows-sap-high-availability-guide.md#65fdef0f-9f94-41f9-b314-ea45bbfea445 "起點 – SAP ASCS/SCS 執行個體在執行叢集節點 A 上執行"
[sap-ha-guide-10.2]: virtual-machines-windows-sap-high-availability-guide.md#5e959fa9-8fcd-49e5-a12c-37f6ba07b916 "從節點 A 到節點 B 的容錯移轉程序"
[sap-ha-guide-10.3]: virtual-machines-windows-sap-high-availability-guide.md#755a6b93-0099-4533-9f6d-5c9a613878b5 "最終結果 – SAP ASCS/SCS 執行個體在叢集節點 B 上執行"


[sap-ha-guide-figure-1000]: ./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]: ./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]: ./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]: ./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]: ./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]: ./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]: ./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-3000]: ./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]: ./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]: ./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]: ./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]: ./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]: ./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]: ./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]: ./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]: ./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]: ./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]: ./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]: ./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]: ./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]: ./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]: ./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]: ./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]: ./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]: ./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]: ./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]: ./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]: ./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]: ./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]: ./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]: ./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]: ./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]: ./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]: ./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]: ./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]: ./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]: ./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]: ./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]: ./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]: ./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]: ./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]: ./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]: ./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]: ./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]: ./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]: ./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]: ./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]: ./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]: ./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]: ./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]: ./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]: ./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]: ./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]: ./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]: ./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]: ./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]: ./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]: ./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]: ./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]: ./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]: ./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]: ./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png


[powershell-install-configure]: ../powershell-install-configure.md
[resource-group-authoring-templates]: ../resource-group-authoring-templates.md
[resource-group-overview]: ../resource-group-overview.md
[resource-groups-networking]: ../virtual-network/resource-groups-networking.md
[sap-pam]: https://support.sap.com/pam "SAP 產品可用性矩陣"
[sap-templates-2-tier-marketplace-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-2-tier-os-disk]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-disk%2Fazuredeploy.json
[sap-templates-2-tier-user-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-2-tier-user-image%2Fazuredeploy.json
[sap-templates-3-tier-marketplace-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image%2Fazuredeploy.json
[sap-templates-3-tier-user-image]: https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-user-image%2Fazuredeploy.json
[storage-azure-cli]: ../storage/storage-azure-cli.md
[storage-azure-cli-copy-blobs]: ../storage/storage-azure-cli.md#copy-blobs
[storage-introduction]: ../storage/storage-introduction.md
[storage-powershell-guide-full-copy-vhd]: ../storage/storage-powershell-guide-full.md#how-to-copy-blobs-from-one-storage-container-to-another
[storage-premium-storage-preview-portal]: ../storage/storage-premium-storage.md
[storage-redundancy]: ../storage/storage-redundancy.md
[storage-scalability-targets]: ../storage/storage-scalability-targets.md
[storage-use-azcopy]: ../storage/storage-use-azcopy.md
[template-201-vm-from-specialized-vhd]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-from-specialized-vhd
[templates-101-simple-windows-vm]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-simple-windows-vm
[templates-101-vm-from-user-image]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image
[virtual-machines-linux-attach-disk-portal]: virtual-machines-linux-attach-disk-portal.md
[virtual-machines-windows-attach-disk-portal]: virtual-machines-windows-attach-disk-portal.md
[virtual-machines-azure-resource-manager-architecture]: ../resource-manager-deployment-model.md
[virtual-machines-azure-resource-manager-architecture-benefits-arm]: ../resource-manager-deployment-model.md#benefits-of-using-resource-manager-and-resource-groups
[virtual-machines-azurerm-versus-azuresm]: virtual-machines-windows-compare-deployment-models.md
[virtual-machines-windows-classic-configure-oracle-data-guard]: virtual-machines-windows-classic-configure-oracle-data-guard.md
[virtual-machines-linux-cli-deploy-templates]: virtual-machines-linux-cli-deploy-templates.md "使用 Azure 資源管理員範本和 Azure CLI 部署和管理虛擬機器"
[virtual-machines-deploy-rmtemplates-powershell]: virtual-machines-windows-ps-manage.md "使用 Azure 資源管理員和 PowerShell 部署以及管理虛擬機器"
[virtual-machines-linux-agent-user-guide]: virtual-machines-linux-agent-user-guide.md
[virtual-machines-linux-agent-user-guide-command-line-options]: virtual-machines-linux-agent-user-guide.md#command-line-options
[virtual-machines-linux-capture-image]: virtual-machines-linux-capture-image.md
[virtual-machines-linux-capture-image-capture]: virtual-machines-linux-capture-image.md#capture-the-vm
[virtual-machines-windows-capture-image]: virtual-machines-windows-capture-image.md
[virtual-machines-windows-capture-image-capture]: virtual-machines-windows-capture-image.md#capture-the-vm
[virtual-machines-linux-configure-lvm]: virtual-machines-linux-configure-lvm.md
[virtual-machines-linux-configure-raid]: virtual-machines-linux-configure-raid.md
[virtual-machines-linux-classic-create-upload-vhd-step-1]: virtual-machines-linux-classic-create-upload-vhd.md#step-1-prepare-the-image-to-be-uploaded
[virtual-machines-linux-create-upload-vhd-suse]: virtual-machines-linux-suse-create-upload-vhd.md
[virtual-machines-linux-redhat-create-upload-vhd]: virtual-machines-linux-redhat-create-upload-vhd.md
[virtual-machines-linux-how-to-attach-disk]: virtual-machines-linux-add-disk.md
[virtual-machines-linux-how-to-attach-disk-how-to-initialize-a-new-data-disk-in-linux]: virtual-machines-linux-add-disk.md#connect-to-the-linux-vm-to-mount-the-new-disk
[virtual-machines-linux-tutorial]: virtual-machines-linux-quick-create-cli.md
[virtual-machines-linux-update-agent]: virtual-machines-linux-update-agent.md
[virtual-machines-manage-availability]: virtual-machines-windows-manage-availability.md
[virtual-machines-ps-create-preconfigure-windows-resource-manager-vms]: virtual-machines-windows-ps-create.md
[virtual-machines-sizes]: virtual-machines-windows-sizes.md
[virtual-machines-windows-classic-ps-sql-alwayson-availability-groups]: virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md
[virtual-machines-windows-classic-ps-sql-int-listener]: virtual-machines-windows-classic-ps-sql-int-listener.md
[virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]: virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md
[virtual-machines-windows-portal-sql-alwayson-int-listener]: virtual-machines-windows-portal-sql-alwayson-int-listener.md
[virtual-machines-sql-server-high-availability-and-disaster-recovery-solutions]: virtual-machines-windows-sql-high-availability-dr.md
[virtual-machines-sql-server-infrastructure-services]: virtual-machines-windows-sql-server-iaas-overview.md
[virtual-machines-sql-server-performance-best-practices]: virtual-machines-windows-sql-performance.md
[virtual-machines-upload-image-windows-resource-manager]: virtual-machines-windows-upload-image.md
[virtual-machines-windows-tutorial]: virtual-machines-windows-hero-tutorial.md
[virtual-machines-workload-template-sql-alwayson]: https://azure.microsoft.com/documentation/templates/sql-server-2014-alwayson-dsc/
[virtual-network-deploy-multinic-arm-cli]: ../virtual-network/virtual-network-deploy-multinic-arm-cli.md
[virtual-network-deploy-multinic-arm-ps]: ../virtual-network/virtual-network-deploy-multinic-arm-ps.md
[virtual-network-deploy-multinic-arm-template]: ../virtual-network/virtual-network-deploy-multinic-arm-template.md
[virtual-networks-configure-vnet-to-vnet-connection]: ../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md
[virtual-networks-create-vnet-arm-pportal]: ../virtual-network/virtual-networks-create-vnet-arm-pportal.md
[virtual-networks-manage-dns-in-vnet]: ../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md
[virtual-networks-multiple-nics]: ../virtual-network/virtual-networks-multiple-nics.md
[virtual-networks-nsg]: ../virtual-network/virtual-networks-nsg.md
[virtual-networks-reserved-private-ip]: ../virtual-network/virtual-networks-static-private-ip-arm-ps.md
[virtual-networks-static-private-ip-arm-pportal]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md
[virtual-networks-udr-overview]: ../virtual-network/virtual-networks-udr-overview.md
[vpn-gateway-about-vpn-devices]: ../vpn-gateway/vpn-gateway-about-vpn-devices.md
[vpn-gateway-create-site-to-site-rm-powershell]: ../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md
[vpn-gateway-cross-premises-options]: ../vpn-gateway/vpn-gateway-plan-design.md
[vpn-gateway-site-to-site-create]: ../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md
[vpn-gateway-vpn-faq]: ../vpn-gateway/vpn-gateway-vpn-faq.md
[xplat-cli]: ../xplat-cli-install.md
[xplat-cli-azure-resource-manager]: ../xplat-cli-azure-resource-manager.md
 

Microsoft Azure 可讓公司在最短的時間內取得計算、儲存體及網路資源，而不需要冗長的採購週期。「Azure 虛擬機器」可讓公司將 SAP NetWeaver 型應用程式 (ABAP、Java 及 ABAP+Java 堆疊) 這類傳統應用程式部署到 Azure 中並擴充其可靠性和可用性，而不需要在內部部署環境中提供進一步的資源。「Azure 虛擬機器」也支援跨單位連線能力，可讓公司積極地將「Azure 虛擬機器」整合到其內部部署網域、「私人雲端」及「SAP 系統環境」中。


本文件詳述使用我們的新方法搭配新的 Azure Resource Manager 部署模型，在 Azure 中部署高可用性 SAP 系統所需的一切步驟。本指南將引導您完成主要的步驟：


- 尋找在稍後名為[資源][sap-ha-guide-2]的一節中所列出的適當 SAP 安裝指南和附註。本白皮書會對「SAP 安裝文件」和「SAP 附註」進行補充說明，指出用來在指定平台上安裝和部署 SAP 軟體的主要資源。

- 了解目前 Azure 傳統部署模型與這個新的 Azure Resource Manager 部署模型之間的差異。

- 了解「Windows Server 容錯移轉叢集」(WSFC) 仲裁模式，以便選取適合您 Azure 部署的模型

- 了解 Azure 中的「Windows Server 容錯移轉叢集」(WSFC) 共用儲存體

- 了解在 Azure 中如何保護 SAP 單點失敗元件 (例如 SAP ASCS/SCS 及 DBMS) 和備援元件 (例如 SAP 應用程式伺服器)

- 透過逐步方法，了解如何使用 Microsoft Azure 作為平台及新的 Azure Resource Manager，在「Windows Server 容錯移轉叢集」(WSFC) 中安裝並設定高可用性 (HA) SAP 系統。

- Azure 中的 WSFC 所需但內部部署不需的其他步驟


為了簡化部署和組態，我們會使用新的 SAP 3 層 HA Azure Resource Manager 範本，此範本會以自動化方式部署高可用性 SAP 系統所需的整個基礎結構，並且支援想要的 SAP 系統 SAPS 大小。

[AZURE.INCLUDE [windows-warning](../../includes/virtual-machines-linux-sap-warning.md)]

##  <a name="217c5479-5595-4cd8-870d-15ab00d4f84c"></a> 必要條件

在您開始之前，請先確定符合下列各章所述的必要條件，並且已檢查＜資源＞一章所列的所有資源。

我們會將 Azure Resource Manager 範本用於 3 層 SAP NetWeaver：[https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image/)

以下提供 SAP Azure Resource Manager 範本的概觀︰[https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)


##  <a name="42b8f600-7ba3-4606-b8a5-53c4f026da08"></a> 資源

以下提供有關在 Azure 上部署 SAP 之主題的其他指南︰

- [Windows 虛擬機器 (VM) 上的 SAP NetWeaver - 規劃和實作指南][planning-guide]
- [Windows 虛擬機器 (VM) 上的 SAP NetWeaver - 部署指南][deployment-guide]
- [Windows 虛擬機器 (VM) 上的 SAP NetWeaver - DBMS 部署指南][dbms-guide]
- [Windows 虛擬機器 (VM) 上的 SAP NetWeaver - 高可用性指南 (本指南)][sap-ha-guide]


> [AZURE.NOTE] 文件中會儘可能使用參考的《SAP 安裝指南》連結 (請參閱 [SAP 安裝指南][sap-installation-guides])。由於本文件只涵蓋「Microsoft Azure 虛擬機器」中所安裝之 SAP NetWeaver 型系統的特定工作，因此如需了解必要條件和安裝程序，應一律仔細閱讀《SAP NetWeaver 安裝指南》。

下列 SAP 附註與 Azure 上的 SAP 主題相關︰


| 附註編號 | 課程名稱                                                    
| ------------- |----------------------------------------------------------
| [1928533] | Azure 上的 SAP 應用程式︰支援的產品和大小 
| [2015553] | Microsoft Azure 上的 SAP：支援的必要條件         
| [1999351] | 適用於 SAP 的增強型 Azure 監視功能                        
| [2178632] | Microsoft Azure 上的 SAP 主要監視度量        
| [1999351] | Windows 上的虛擬化︰增強型監視功能           


如需 Azure 訂用帳戶的一般預設限制和最大限制，請參閱[這篇文章][azure-subscription-service-limits-subscription]。

##  <a name="42156640c6-01cf-45a9-b225-4baa678b24f1"></a> Azure Resource Manager 與傳統部署模型之間的 SAP HA 差異 

> [AZURE.NOTE] 傳統部署模型也稱為「Azure 服務管理」(ASM) 模型。

### <a name="f76af273-1993-4d83-b12d-65deeae23686"></a> 資源群組
資源群組是新的概念，內含具有相同生命週期 (例如同時建立和刪除) 的所有資源。如需有關資源群組的詳細資訊，請參閱這篇文章。

### <a name="3e85fbe0-84b1-4892-87af-d9b65ff91860"></a> Azure Resource Manager 與傳統部署模型的叢集比較 

與傳統部署模型相比，新的 Azure Resource Manager 模型針對 HA 提供下列變更︰

- 不需有雲端服務，即可使用 Azure Internal Load Balancer (ILB)

如果您仍然想要使用舊的 Azure 傳統模型，您就必須依照 [Azure 上的 SAP NetWeaver - 在 Azure 上使用 Windows Server 容錯移轉叢集搭配 SIOS DataKeeper 將 SAP ASCS/SCS 執行個體組成叢集](http://go.microsoft.com/fwlink/?LinkId=613056)白皮書所述的步驟操作。

> [AZURE.NOTE] 強烈建議使用新的 Azure Resource Manager 部署模型來進行 SAP 安裝，因為與傳統部署模型相比，它可提供許多好處。如需詳細資訊，請參閱[這篇文章][virtual-machines-azure-resource-manager-architecture-benefits-arm]。


## <a name="8ecf3ba0-67c0-4495-9c14-feec1a2255b7"></a> Windows Server 容錯移轉叢集 (WSFC) 

Microsoft WSFC 是 Windows 上高可用性 SAP ASCS/SCS 安裝和 DBMS 的技術基礎。

容錯移轉叢集是由 1+n 個獨立伺服器 (節點) 所組成的群組，這些伺服器會共同運作以提升應用程式和服務的可用性。當發生節點失敗時，WSFC 必須決定可發生多少錯誤而仍然能讓叢集維持狀況良好，以便提供定義的應用程式和/或服務。有不同的仲裁模式可用來達成這個目的。
 

### <a name="1a3c5408-b168-46d6-99f5-4219ad1b1ff2"></a> 仲裁模式

使用 WSFC 時，有四種不同的仲裁模式可供使用︰

- **節點多數︰**每個節點都可投票。只有取得多數票 (也就是過半數) 時，叢集才能運作。當節點數目為奇數時，建議使用這個選項。例如︰在含有 7 個節點的叢集中，可以有 3 個節點失敗，在此情況下，叢集仍可達到佔多數的優勢而繼續執行。

- **節點與磁碟多數：**每個節點再加上一個叢集儲存體中的指定磁碟 (磁碟見證) 只要是處於可用且通訊中狀態，都可投票。只有取得多數票 (也就是過半數) 時，叢集才能運作。此模式適用於節點數目為偶數的叢集環境。只要半數的節點再加上該磁碟在線上，叢集就會維持狀況良好的狀態。

- **節點與檔案共用多數：**每個節點再加上一個由系統管理員所建立的指定檔案共用 (檔案共用見證) 只要是處於可用且通訊中狀態，都可投票。只有取得多數票 (也就是過半數) 時，叢集才能運作。此模式適用於節點數目為偶數的叢集環境，並且類似於「節點與磁碟多數」模式，只是它使用的是見證檔案共用，而不是見證磁碟。它很容易實作，但如果檔案共用本身不具有高可用性，則它可能會變成單一失敗點。

- **非大部分：僅磁碟：**只要有一個節點可用且正與叢集儲存體中的特定磁碟進行通訊，叢集就會擁有仲裁。只有也在與該磁碟進行通訊的節點能夠加入叢集。不建議使用此模式。 

## <a name="fdfee875-6e66-483a-a343-14bbaee33275"></a> 內部部署環境中的 Windows 容錯移轉叢集

在此範例中，我們擁有一個由兩個節點組成的叢集。如果節點之間的網路連線失敗，但兩個節點都保持在已啟動並正常執行的狀態，就有必要釐清哪個節點應該繼續提供叢集的應用程式和服務。仲裁磁碟或檔案共用便可用於這個用途。能夠存取仲裁磁碟或檔案共用的節點就是可以確保服務可存取性的節點。

在此範例中，我們使用的是含有兩個節點的叢集。基於這個理由，我們選擇了節點與檔案共用仲裁模式。節點與磁碟多數也是有效的選項。在生產環境中，建議改用仲裁磁碟，並使用網路和儲存體系統技術讓它變成具有高可用性。

![圖 1：針對 Azure 上的 SAP ASCS/SCS 建議使用的 Windows Server 容錯移轉叢集組態][sap-ha-guide-figure-1000]

**圖 1：**針對 Azure 上的 SAP ASCS/SCS 建議使用的 Windows Server 容錯移轉叢集組態

### <a name="be21cf3e-fb01-402b-9955-54fbecf66592"></a> 共用儲存體

上圖顯示包含兩個節點的共用儲存體叢集。在內部部署環境中的共用儲存體叢集中，有一個該叢集內所有節點都看得到的共用儲存體。鎖定機制可防止資料損毀。此外，所有節點也可以偵測到另一個節點是否發生失敗。如果一個節點發生失敗，剩餘的節點就會取得儲存體資源的擁有權並確保服務的可用性。

> [AZURE.NOTE] 就某些 DBMS (例如 SQL Server) 而言，達到高可用性並不一定需要用到共用磁碟。SQL Server AlwaysOn 會將 DBMS 資料及記錄檔，從一個叢集節點的本機磁碟複寫到另一個叢集節點的本機磁碟。因此，Windows 叢集組態並不需要用到共用磁碟。

### <a name="ff7a9a06-2bc5-4b20-860a-46cdb44669cd"></a> 網路 / 名稱解析

透過 DNS 伺服器所提供的虛擬 IP 位址和虛擬主機名稱，即可連線到叢集。內部部署環境中的節點與「DNS 伺服器」可以處理多個 IP 位址。

在一般設定中，會使用兩個或更多個網路連線：

- 一個連接到儲存體的專用連線；和
- 一個用於活動訊號的叢集內部網路連線；以及
- 一個供用戶端用來連線到叢集的公用網路。



## <a name="2ddba413-a7f5-4e4e-9a51-87908879c10a"></a> 與 Microsoft Azure 搭配使用的 Windows 容錯移轉叢集

相較於裸機或私人雲端部署，「Microsoft Azure 虛擬機器」需要額外的步驟來設定 WSFC。為了建置「共用叢集磁碟」，SAP ASCS/SCS 執行個體必須要有數個 IP 位址和虛擬的主機名稱。

下面討論在 Microsoft Azure 上建置「SAP HA 中央服務」叢集時所需的其他概念和步驟。這些步驟示範如何設定協力廠商工具 SIOS DataKeeper，以及設定 Azure Internal Load Balancer。這些工具將可讓我們使用「檔案共用見證」在 Microsoft Azure 中建立「Windows 容錯移轉叢集」。


![圖 2：Azure 中沒有共用磁碟的 Windows Server 容錯移轉叢集組態結構描述][sap-ha-guide-figure-1001]

**圖 2：**Azure 中沒有共用磁碟的 Windows Server 容錯移轉叢集組態結構描述


### <a name="1a464091-922b-48d7-9d08-7cecf757f341"></a> 使用 SIOS DataKeeper 在 Microsoft Azure 上建立共用磁碟

自 2016 年 9 月起，Microsoft Azure 即不再提供共用儲存體來建立共用儲存體叢集。不過，高可用性 SAP ASCS/SCS 執行個體需要叢集共用儲存體。

協力廠商軟體 SIOS DataKeeper Cluster Edition 可讓使用者建立鏡像儲存體，以模擬叢集共用儲存體。SIOS 解決方案提供即時同步的資料複寫。為叢集建立共用磁碟資源的方式為︰

- 將額外的 Azure VHD 連接到「Windows 叢集」組態中的每個 VM。
- 在兩個 VM 節點上都執行 SIOS DataKeeper Cluster Edition。
- 設定 SIOS DataKeeper Cluster Edition，使它將來源 VM 的額外 VHD 連接磁碟區內容鏡像至目標 VM 的額外 VHD 連接磁碟區。SIOS DataKeeper 會提取來源和目標本機磁碟區，並將它們當作一個共用磁碟來呈現給「Windows 容錯移轉叢集」。

如需有關 SIOS DataKeeper 產品的詳細資訊，請參閱此來源︰[http://us.sios.com/products/datakeeper-cluster/](http://us.sios.com/products/datakeeper-cluster/)

 ![圖 2：Azure 中使用 SIOS DataKeeper 的 Windows Server 容錯移轉叢集組態結構描述][sap-ha-guide-figure-1002]

**圖 3：**Azure 中使用 SIOS DataKeeper 的 Windows Server 容錯移轉叢集組態結構描述

> [AZURE.NOTE] 就某些 DBMS (例如 SQL Server) 而言，達到高可用性並不一定需要用到共用磁碟。SQL Server AlwaysOn 會將 DBMS 資料及記錄檔，從一個叢集節點的本機磁碟複寫到另一個叢集節點的本機磁碟。因此，Windows 叢集組態並不需要用到共用磁碟。

### <a name="44641e18-a94e-431f-95ff-303ab65e0bcb"></a> Microsoft Azure 上的名稱解析

Microsoft Azure 雲端平台並不允許設定虛擬 IP 位址，例如浮動 IP。基於這個理由，您需要一個替代解決方案來設定虛擬 IP，以便連線到雲端的叢集資源。Azure 提供「內部負載平衡器」(ILB)。有了 ILB，便可透過叢集虛擬 IP 位址來連線到叢集。因此，您必須將 ILB 部署在包含叢集節點的資源群組中。接著，您必須使用 ILB 的探查連接埠來設定所有必要的連接埠轉送規則。用戶端可以透過虛擬主機名稱來進行連線。「DNS 伺服器」會解析「叢集 IP 位址」，而 ILB 則會處理對作用中叢集節點的轉送。

## <a name="2e3fec50-241e-441b-8708-0b1864f66dfa"></a> Azure IaaS 上的 SAP NetWeaver 高可用性

如 HA 章節 [Windows 虛擬機器 (VM) 上的 SAP NetWeaver - 規劃和實作指南的 Azure 虛擬機器 (VM) 上的 SAP NetWeaver][planning-guide-11] 中已討論到的，若要達成 SAP 應用程式高可用性 (例如 SAP 軟體元件的 HA)，我們必須保護下列元件︰

- SAP 應用程式伺服器
- SAP ASCS/SCS 執行個體
- DBMS 伺服器

### <a name="93faa747-907e-440a-b00a-1ae0a89b1c0e"></a> SAP 應用程式伺服器的高可用性

針對 SAP 應用程式伺服器/對話方塊執行個體，通常並不需要特定的高可用性解決方案。您可以透過備援，藉此在不同的 Azure 虛擬機器上設定多個對話方塊執行個體，來達成高可用性。您應該至少要有兩個 SAP 應用程式執行個體安裝在兩個 Azure VM 中。

![圖 4：SAP 應用程式伺服器 HA][sap-ha-guide-figure-2000]

**圖 4：**SAP 應用程式伺服器 HA


所有裝載 SAP 應用程式伺服器的虛擬機器都必須放置在同一個「Azure 可用性設定組」中。「Azure 可用性設定組」將可確保：

- 所有 VM 皆屬於相同的升級網域，例如它會確認避免在計劃性維護停機時間內同時更新 VM。
- 以及所有 VM 都會屬於相同的容錯網域，例如它會確保 VM 的部署方式能夠避免可能會影響所有 VM 可用性的單一失敗點。

如需詳細資訊，請參閱這篇文章：[管理虛擬機器的可用性][virtual-machines-manage-availability]。

由於 Azure 儲存體帳戶可能是潛在的「單一失敗點」，因此您務必至少要擁有兩個 Azure 儲存體帳戶，且至少要將兩個 VM 分散到其中。在理想情況下，每個執行 SAP 對話方塊執行個體的 VM 都應該部署在不同的儲存體帳戶中。

### <a name="f559c285-ee68-4eec-add1-f60fe7b978db"></a> SAP (A)SCS 執行個體的高可用性 

![圖 5：高可用性 SAP ASCS / SCS 執行個體 HA][sap-ha-guide-figure-2001]

**圖 5：**高可用性 SAP ASCS / SCS 執行個體 HA


#### <a name="b5b1fd0b-1db4-4d49-9162-de07a0132a51"></a> 在 Azuer 中使用 Windows 容錯移轉叢集提供 SAP (A)SCS 執行個體高可用性

相較於裸機或私人雲端部署，「Microsoft Azure 虛擬機器」需要額外的步驟來設定 WSFC。為了建置 Windows 容錯移轉叢集，必須要有一個「共用磁碟叢集」、數個 IP 位址和虛擬主機名稱以及 Azure 內部負載平衡器 (ILB)，來將 SAP ASCS/SCS 執行個體組成叢集。

在本文件稍後，將會對此有更詳細的討論。

![圖 6：Azure 中使用 SIOS DataKeeper 之 SAP ASCS/SCS 組態的 Windows Server 容錯移轉叢集結構描述][sap-ha-guide-figure-1002]

**圖 6：**Azure 中使用 SIOS DataKeeper 之 SAP ASCS/SCS 組態的 Windows Server 容錯移轉叢集結構描述


### <a name="ddd878a0-9c2f-4b8e-8968-26ce60be1027"></a> DBMS 執行個體的高可用性

DBMS 也是 SAP 系統的 SPOF，必須使用 HA 解決方案來保護它。以下是 Azure 中的 SQL Server AlwaysOn HA 解決方案範例，使用的是「Windows Server 容錯移轉叢集」和 Azure Internal Load Balancer。SQL Server AlwaysOn 會使用自己的 DBMS 複寫來複寫 DBMS 資料和記錄檔。因此，我們不需要叢集共用磁碟，這可以簡化整個設定。


![圖 7：SAP DBMS 伺服器 HA – SQL Server AlwaysOn HA 設定範例][sap-ha-guide-figure-2003]

**圖 7：**SAP DBMS 伺服器 HA – SQL Server AlwaysOn HA 設定範例


本文件並未涵蓋 DBMS 叢集。

如需有關使用 Azure Resource Manager 部署模型在 Azure 中將 SQL Server 組成叢集的詳細資訊，請參閱下列文章：

- [在 Azure VM 中手動設定 Always On 可用性群組 - Resource Manager][virtual-machines-windows-portal-sql-alwayson-availability-groups-manual]
- [在 Azure 中設定 AlwaysOn 可用性群組的內部負載平衡器][virtual-machines-windows-portal-sql-alwayson-int-listener]

### <a name="045252ed-0277-4fc8-8f46-c5a29694a816"></a> 可能的端對端 HA 部署案例

以下是 Azure 中的完整 SAP NetWeaver HA 架構範例，其中我們將一個專用叢集用於 SAP ASCS/SCS 執行個體，另一個用於 DBMS。

![圖 8：SAP HA 架構範本 1 – 包含用於 ASCS/SCS 的專用叢集和用於 DBMS 執行個體的專用叢集][sap-ha-guide-figure-2004]

**圖 8：**SAP HA 架構範本 1 – 包含用於 ASCS/SCS 的專用叢集和用於 DBMS 執行個體的專用叢集

## <a name="78092dbe-165b-454c-92f5-4972bdbef9bf"></a> 基礎結構準備

為了簡化 SAP 所需資源的部署，我們為 SAP 開發了 Azure Resource Manager 範本。

這些 3 層範本也支援高可用性案例，例如︰

- **架構範本 1** – 包含兩個叢集，分別用於 SAP ASCS/SCS 和 DBMS 的「SAP 單點失敗」

以下提供案例 1 的 Azure Resource Manager 範本：

- Azure Marketplace 映像：[https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-marketplace-image)
- 自訂映像：[https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image](https://github.com/Azure/azure-quickstart-templates/tree/master/sap-3-tier-user-image)

當您按一下 SAP 3 層 Marketplace 映像時，您會在 Azure 入口網站中看見下列 UI：

![圖 9：指定 SAP HA Azure Resource Manager 參數][sap-ha-guide-figure-3000]

**圖 9：**指定 SAP HA Azure Resource Manager 參數


請務必針對 [SYSTEMAVAILABILITY] 選項選擇 [HA]。

範本將會建立︰

- 下列項目所需的一切 **VM**︰
    - SAP 應用程式伺服器 VM：`<SAPSystemSID>-di-<Number>`
    - ASCS/SCS 叢集 VM：`<SAPSystemSID>-ascs-<Number>`
    - DBMS 叢集：`<SAPSystemSID>-db-<Number>`
- **所有 VM 的網路卡並包含關聯的 IP 位址**：
    - `<SAPSystemSID>-nic-di-<Number>`
    - `<SAPSystemSID>-nic-ascs-<Number>`
    - `<SAPSystemSID>-nic-db-<Number>`
- **Azure 儲存體帳戶**
- 下列項目的**可用性群組**︰
    - SAP 應用程式伺服器 VM：`<SAPSystemSID>-avset-di`
    - SAP ASCS/SCS 叢集 VM：`<SAPSystemSID>-avset-ascs`
    - DBMS 叢集 VM：`<SAPSystemSID>-avset-db`
- **Azure Internal Load Balancer (ILB)** 並包含 ASCS/SCS 執行個體和 IP 位址 `<SAPSystemSID>-lb-ascs` 的所有連接埠
-	**Azure Internal Load Balancer (ILB)** 並包含 SQL Server DBMS 和 IP 位址 `<SAPSystemSID>-lb-db` 的所有連接埠
- **網路安全性群組**：`<SAPSystemSID>-nsg-ascs-0` 具有對 `<SAPSystemSID>-ascs-0` VM 開放的外部 RDP 連接埠


> [AZURE.NOTE]  所有網路卡和 Azure ILB 的 IP 位址一開始都是建立為「動態」位址。您必須如本文件稍後所述，將它們變更為「靜態」IP 位址。


### <a name="c87a8d3f-b1dc-4d2f-b23c-da4b72977489"></a>部署具有公司網路連線能力 (跨單位) 的 VM 以供生產環境使用

針對生產環境 SAP 系統，您將使用 Azure 站對站 (VPN) 或 Azure ExpressRoute，部署具有[公司網路連線能力 (跨單位)][planning-guide-2.2] 的 Azure VM。

> [AZURE.NOTE]  在此案例中，已經建立並備妥您的 Azure VNET 和子網路。


在 [NEWOREXISTINGSUBNET] 欄位中，選擇 [existing]。

在 [SUBNETID] 文字欄位中，您必須新增已備妥的「Azure 網路 SubnetID」的完整字串，這是您打算用來部署 Azure VM 的位置。

您可以使用此 PowerShell 命令來取得所有 Azure 網路子網路的清單：

```powershell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets
```


**SUBNETID** 會顯示在欄位識別碼中。

例如，您可以使用下列 PowerShell 命令來擷取所有 **SUBNETID** 的清單：

```PowerShell
(Get-AzureRmVirtualNetwork -Name <azureVnetName>  -ResourceGroupName <ResourceGroupOfVNET>).Subnets.Id
```

**SUBNETID** 看起來像這樣︰

```
/subscriptions/<SubscriptionId>/resourceGroups/<VPNName>/providers/Microsoft.Network/virtualNetworks/azureVnet/subnets/<SubnetName>
```

### <a name="7fe9af0e-3cce-495b-a5ec-dcb4d8e0a310"></a> 用於測試/示範的 SAP 執行個體僅限雲端部署

您也可以在所謂的僅限雲端部署模型中部署您的 HA SAP 系統。

此部署主要適用於示範使用案例，但不適用於生產環境使用案例。

在 [NEWOREXISTINGSUBNET] 欄位中，選擇 [new]。將 SUBNETID 欄位保留「空白」。

SAP Azure Resource Manager 範本將會自動建立 Azure VNET 和子網路。

> [AZURE.NOTE] 此外，您必須在相同的 VNET 中至少為 AD/DNS 部署一個專用 VM。這些 VM 不是由範本所建立。


### <a name="47d5300a-a830-41d4-83dd-1a0d1ffdbe6a"></a> Azure 虛擬網路

在我們的範例中，Azure VNET 的位址空間是 10.0.0.0/16。有一個名為 _**Subnet**_、網址範圍為 10.0.0.0/24 的子網路。所有 VM 和 ILB 都會部署在此 VNET 中。
  
> [AZURE.NOTE] 請勿對客體內的網路設定 (例如 IP 位址、 DNS 伺服器、子網路等) 進行任何變更。所有網路設定都是透過 Azure 來完成，並透過 DHCP 服務來傳播。

### <a name="b22d7b3b-4343-40ff-a319-097e13f62f9e"></a> DNS IP 位址

請確定您的 VNET [DNS 伺服器] 選項是設定為 [自訂 DNS]。如果是︰

- **[公司網路連線能力 (跨單位)][planning-guide-2.2]**︰請新增內部部署 DNS 伺服器的 IP 位址。內部部署 DNS 伺服器可以延伸到在 Azure 中執行的 VM。在此情況下，您可以新增這些被設定來執行 DNS 服務之 Azure VM 的 IP 位址。

-	**[僅限雲端部署][planning-guide-2.1]**︰請在相同的 VNET 中部署將作為 DNS 伺服器的額外 VM。請新增這些被設定來執行 DNS 服務之 Azure VM 的 IP 位址。


![圖 10：設定 Azure VNET 的 DNS 伺服器][sap-ha-guide-figure-3001]

**圖 10：**設定 Azure VNET 的 DNS 伺服器

> [AZURE.NOTE] 如果您變更 DNS 伺服器的 IP 位址，您就必須重新啟動 Azure VM，才能套用變更並傳播新的 DNS 伺服器。在我們的範例中，是在下列 Windows VM 上安裝並設定 DNS 服務



| VM 角色 | VM 主機名稱 | 網路卡名稱 | 靜態 IP 位址  
| ---------------|-------------|--------------------|-------------------
| 第 1 部 DNS 伺服器 | domcontr-0 | pr1-nic-domcontr-0 | 10\.0.0.10         
| 第 2 部 DNS 伺服器 | domcontr-1 | pr1-nic-domcontr-1 | 10\.0.0.11         


### <a name="9fbd43c0-5850-4965-9726-2a921d85d73f"></a> SAP ASCS/SCS 叢集執行個體及 DBMS 叢集執行個體的主機名稱和靜態 IP 位址

與內部部署類似，我們需要下列保留的主機名稱/IP 位址︰

| 虛擬主機名稱角色 | 虛擬主機名稱 | 虛擬靜態 IP 位址 
| ----------------------------------------------------------------------------|------------------|---------------------------
| SAP ASCS/SCS 第 1 個叢集虛擬主機名稱 (用於叢集管理) | pr1-ascs-vir | 10\.0.0.42                 
| SAP ASCS/SCS **INSTANCE** 虛擬主機名稱 | pr1-ascs-sap | `10.0.0.43`             
| SAP DBMS 第 2 個叢集虛擬主機名稱 (用於叢集管理) | pr1-dbms-vir | 10\.0.0.32                 
 

用來管理叢集本身的虛擬主機名稱 _**pr1-ascs-vir**_ 和 _**pr1-dbms-vir**_ 及關聯的 IP 位址會在建立叢集時建立，如[收集叢集組態中的叢集節點][sap-ha-guide-8.12.1]一章所述。

其他兩個由叢集 SAP ASCS/SCS 執行個體和叢集 DBMS 執行個體所使用的虛擬主機名稱 _**pr1-ascs-sap**_ 和 _**pr1-dbms-sap**_ 及關聯的 IP 位址，則可透過手動方式在 DNS 伺服器上建立，如[建立叢集 SAP ASCS/SCS 的虛擬主機名稱][sap-ha-guide-9.1.1]一章所述。

### <a name="84c019fe-8c58-4dac-9e54-173efd4b2c30"></a> 為 SAP VM 設定靜態 IP 位址

部署用於叢集的虛擬機器之後，我們必須為所有 VM 設定靜態 IP 位址。此操作無法在「客體 OS」內完成，而是必須在「Azure 虛擬網路」組態中設定。

若要執行此操作，其中一個方法是使用 Azure 入口網站。在「Azure 入口網站」中，瀏覽至：

```
<Resource Group> -> <Network Card> -> Settings -> IP Address
```

將欄位指派從 [動態] 變更為 [靜態]，然後輸入想要的「IP 位址」。

> [AZURE.NOTE] 如果您變更網路卡的 IP 位址，您就必須重新啟動 Azure VM，才能套用變更。


![圖 11：為每個 VM 的網路卡設定靜態 IP 位址][sap-ha-guide-figure-3002]

**圖 11：**為每個 VM 的網路卡設定靜態 IP 位址

為所有網路介面 (亦即所有 VM) 重複此步驟，包括要用於 AD/DNS 服務的 VM。

在我們的範例中，有下列 VM 和靜態 IP 位址：

| VM 角色 | VM 主機名稱 | 網路卡名稱 | 靜態 IP 位址  
| ----------------------------------------|--------------|--------------------|-------------------
| 第 1 部 SAP 應用程式伺服器 | pr1-di-0 | pr1-nic-di-0 | 10\.0.0.50         
| 第 2 部 SAP 應用程式伺服器 | pr1-di-1 | pr1-nic-di-1 | 10\.0.0.51         
| ... | ... | ... | ...               
| 最後一部 SAP 應用程式伺服器 | pr1-di-5 | pr1-nic-di-5 | 10\.0.0.55         
| ASCS/SCS 執行個體的第 1 個叢集節點 | pr1-ascs-0 | pr1-nic-ascs-0 | 10\.0.0.40         
| ASCS/SCS 執行個體的第 2 個叢集節點 | pr1-ascs-1 | pr1-nic-ascs-1 | 10\.0.0.41         
| DBMS 執行個體的第 1 個叢集節點 | pr1-db-0 | pr1-nic-db-0 | 10\.0.0.30         
| DBMS 執行個體的第 2 個叢集節點 | pr1-db-1 | pr1-nic-db-1 | 10\.0.0.31         

### <a name="7a8f3e9b-0624-4051-9e41-b73fff816a9e"></a> 為內部負載平衡器 (ILB) 設定靜態 IP 位址

SAP Azure Resource Manager 範本會建立用於 SAP ASCS/SCS 執行個體和用於 DBMS 執行個體的 Azure Internal Load Balancer (ILB)。

初始部署會將 ILB 的 IP 位址設定為 [動態]。請務必將 IP 位址變更為 [靜態]。

在我們的範例中，有兩個具有下列靜態 IP 位址的 Azure ILB：

| Azure ILB 角色 | Azure ILB 名稱 | 靜態 IP 位址 
| ---------------------------|----------------|-------------------
| SAP ASCS/SCS 執行個體 ILB | pr1-lb-ascs | `10.0.0.43`         
| SAP DBMS ILB | pr1-lb-dbms | 10\.0.0.33         


> [AZURE.NOTE]  
**SAP ASCS/SCS 的虛擬主機名稱 IP 位址 = SAP ASCS/SCS Azure Load Balancer pr1-lb-ascs 的 IP 位址** **DBMS 的虛擬名稱 IP 位址 = DBMS Azure Load Balancer pr1-lb-dbms 的 IP 位址**

在我們的範例中，請將「內部負載平衡器」_pr1-lb-ascs_ 的 IP 位址設定為 SAP ASCS/SCS 執行個體的虛擬主機名稱 IP 位址 (`10.0.0.43`)

![圖 12：為 SAP ASCS/SCS 執行個體的內部負載平衡器 (ILB) 設定靜態 IP 位址][sap-ha-guide-figure-3003]

**圖 12：**為 SAP ASCS/SCS 執行個體的內部負載平衡器 (ILB) 設定靜態 IP 位址

以相同的方式，將「負載平衡器」_pr1-lb-dbms_ 的 IP 位址設定為 DBMS 執行個體的虛擬主機名稱 IP 位址 (在我們的範例中為 10.0.0.33)。

### <a name="f19bd997-154d-4583-a46e-7f5a69d0153c"></a> Azure Internal Load Balancer (ILB) 的預設 ASCS/SCS 負載平衡規則

SAP Azure Resource Manager 範本預設會為下列項目建立所需的一切連接埠︰

- 預設執行個體號碼為 **00** 的 ABAP ASCS 執行個體
- 預設執行個體號碼為 **01** 的 Java SCS 執行個體

因此，在安裝 SAP ASCS/SCS 執行個體時，您必須針對 ABAP ASCS 和/或 Java SCS 執行個體使用這些預設的執行個體號碼 00 和 01。

以下是就 SAP NetWeaver ABAP ASCS 連接埠而言，所需要且會建立的 Azure ILB 端點：


| 服務 / 負載平衡規則名稱 | 預設連接埠號碼 | (執行個體號碼為 00 的 ASCS 執行個體) (ERS 為 10) 的具體連接埠  
| ---------------------------------------------|-----------------------|--------------------------------------------------------------------------
| 加入佇列伺服器 / _lbrule3200_ | 32`<InstanceNumber>` | 3200                                                                     
| ABAP 訊息伺服器 / _lbrule3600_ | 32`<InstanceNumber>` | 3600                                                                     
| 內部 ABAP 訊息 / _lbrule3900_ | 39`<InstanceNumber>` | 3900                                                                     
| 訊息伺服器 HTTP / _Lbrule8100_ | 81`<InstanceNumber>` | 8100                                                                     
| SAP 啟動服務 ASCS HTTP / _Lbrule50013_ | 5`<InstanceNumber>`13 | 50013                                                                    
| SAP 啟動服務 ASCS HTTPS / _Lbrule50014_ | 5`<InstanceNumber>`14 | 50014                                                                    
| 加入佇列複寫 / _Lbrule50016_ | 5`<InstanceNumber>`16 | 50016                                                                    
| SAP 啟動服務 ERS HTTP _Lbrule51013_ | 5`<InstanceNumber>`13 | 51013                                                                    
| SAP 啟動服務 ERS HTTP _Lbrule51014_ | 5`<InstanceNumber>`14 | 51014                                                                    
| Win RM _Lbrule5985_ | | 5985                                                                     
| 檔案共用 _Lbrule445_ | | 445                                                                      

**表 1：**SAP NetWeaver ABAP ASCS 執行個體的連接埠號碼


以下是就 SAP NetWeaver Java SCS 連接埠而言，所需要且必須建立的 Azure ILB 端點：

| 服務 / 負載平衡規則名稱 | 預設連接埠號碼 | (執行個體號碼為 01 的 SCS 執行個體) (ERS 為 11) 的具體連接埠  
| ---------------------------------------------|-----------------------|--------------------------------------------------------------------------
| 加入佇列伺服器 / _lbrule3201_ | 32`<InstanceNumber>` | 3201                                                                     
| 閘道伺服器 / _lbrule3301_ | 33`<InstanceNumber>` | 3301                                                                     
| Java 訊息伺服器 / _lbrule3900_ | 39`<InstanceNumber>` | 3901                                                                     
| 訊息伺服器 HTTP / _Lbrule8101_ | 81`<InstanceNumber>` | 8101                                                                     
| SAP 啟動服務 SCS HTTP / _Lbrule50113_ | 5`<InstanceNumber>`13 | 50113                                                                    
| SAP 啟動服務 SCS HTTPS / _Lbrule50114_ | 5`<InstanceNumber>`14 | 50114                                                                    
| 加入佇列複寫 / _Lbrule50116_ | 5`<InstanceNumber>`16 | 50116                                                                    
| SAP 啟動服務 ERS HTTP _Lbrule51113_ | 5`<InstanceNumber>`13 | 51113                                                                    
| SAP 啟動服務 ERS HTTP _Lbrule51114_ | 5`<InstanceNumber>`14 | 51114                                                                    
| Win RM _Lbrule5985_ | | 5985                                                                     
| 檔案共用 _Lbrule445_ | | 445                                                                      

**表 2：**SAP NetWeaver Java SCS 執行個體的連接埠號碼


![圖 13：Azure Internal Load Balancer (ILB) 的預設 ASCS/SCS 負載平衡規則][sap-ha-guide-figure-3004]

**圖 13：**Azure Internal Load Balancer (ILB) 的預設 ASCS/SCS 負載平衡規則

以相同的方式，將「負載平衡器」_**pr1-lb-dbms**_ 的 IP 位址設定為 DBMS 執行個體的虛擬主機名稱 IP 位址 (在我們的範例中為 _**10.0.0.33**_)。

### <a name="fe0bd8b5-2b43-45e3-8295-80bee5415716"></a> 變更 Azure Internal Load Balancer (ILB) 的 ASCS/SCS 預設負載平衡規則

如果您想要將其他執行個體號碼用於 SAP ASCS 或 SCS 執行個體，您就必須更新這些連接埠的名稱和值。

若要執行這項更新，其中一個方法是使用 Azure 入口網站。

移至 `<SID>-lb-ascs load balancer -> Load Balancing Rules`。

針對屬於 SAP ASCS 或 SCS 執行個體的所有負載平衡規則，變更︰

- 名稱
- 連接埠
- 後端連接埠

例如，如果我們想要將預設 ASCS 執行個體號碼從 00 變更為 31，您就必須為**表 1：**SAP NetWeaver ABAP ASCS 執行個體的連接埠號碼中列出的所有連接埠進行變更。

以下是連接埠 _lbrule3200_ 的更新範例。

![圖 14：變更 Azure Internal Load Balancer (ILB) 的 ASCS/SCS 預設負載平衡規則][sap-ha-guide-figure-3005]

**圖 14：**變更 Azure 內部負載平衡器 (ILB) 的 ASCS/SCS 預設負載平衡規則


### <a name="e69e9a34-4601-47a3-a41c-d2e11c626c0c"></a> 將 Windows 機器新增到網域

將靜態 IP 位址指派給 VM 之後，請將 VM 新增到網域。

![圖 15：將 VM 新增到網域][sap-ha-guide-figure-3006]

**圖 15：**將 VM 新增到網域


### <a name="661035b2-4d0f-4d31-86f8-dc0a50d78158"></a> 在用於 SAP ASCS/SCS 執行個體的兩個叢集節點上都新增登錄項目

Azure Load Balancer (包括 Azure Internal Load Balancer) 會在連線閒置達一段特定的時間 (閒置逾時) 之後關閉這些連線。另一方面，對話方塊執行個體中的 SAP 工作程序則會在需要傳送第一個加入佇列/清除佇列要求時，立即開啟對「SAP 加入佇列程序」的連線。這些連線通常會持續維持已建立狀態，直到工作程序或加入佇列程序重新啟動為止。不過，如果連線閒置達一段時間，Azure ILB 就會關閉這些連線。這實際上不構成問題，因為如果與加入佇列程序的連線已不存在，SAP 工作程序將會重新建立該連線。不過，系統會將這些活動記載在 SAP 程序的開發人員追蹤內，也因此會在沒有真正正當理由的情況下，在這些追蹤內建立大量內容。因此，我們建議同時變更兩個叢集節點上的 TCP/IP `KeepAliveTime` 和 `KeepAliveInterval`。TCP/IP 參數的變更需要與 SAP 設定檔參數結合，我們將在本文件的稍後說明。

在 SAP ASCS/SCS 執行個體的兩個叢集節點上都新增下列 Windows 登錄項目：

| 路徑 | HKLM\\SYSTEM\\CurrentControlSet\\Services\\Tcpip\\Parameters   
| ----------------------|-----------------------------------------------------------
| 變數名稱 | `KeepAliveTime`                                              
| 變數類型 | REG\_DWORD (十進位)                                        
| 值 | 120000                                                     
| 文件連結 | [https://technet.microsoft.com/zh-TW/library/cc957549.aspx](https://technet.microsoft.com/zh-TW/library/cc957549.aspx) 


**表 3：**要變更的第一個 TCP/IP 參數


| Path | HKLM\\SYSTEM\\CurrentControlSet\\Services\\Tcpip\\Parameters   
| ----------------------|-----------------------------------------------------------
| 變數名稱 | `KeepAliveInterval`                                          
| 變數類型 | REG\_DWORD (十進位)                                        
| 值 | 120000                                                     
| 文件連結 | [https://technet.microsoft.com/zh-TW/library/cc957548.aspx](https://technet.microsoft.com/zh-TW/library/cc957548.aspx)


**表 4：**要變更的第二個 TCP/IP 參數

然後**重新啟動這兩個叢集節點**，以便套用這些變更。

### <a name="0d67f090-7928-43e0-8772-5ccbf8f59aab"></a> SAP ASCS/SCS 執行個體的 Windows Server 容錯移轉叢集設定

#### <a name="5eecb071-c703-4ccc-ba6d-fe9c6ded9d79"></a> 收集叢集組態中的叢集節點

第一個步驟是在兩個叢集節點上都新增「容錯移轉叢集功能」。此步驟是使用「新增角色及功能精靈」來完成，應該不需要任何其他說明。

第二個步驟是使用「Windows 容錯移轉叢集管理員」來設定「容錯移轉叢集」。

在 [容錯移轉叢集管理員] MMC 中，按一下 [建立叢集]，然後只新增第一個叢集節點 A 的名稱，例如 _**pr1-ascs-0**_。還不要新增第二個節點。在稍後的步驟中將會新增它。

![圖 16：新增容錯移轉叢集組態的第一個步驟 – 新增第一個節點 (應該是叢集節點) 的伺服器/VM 名稱][sap-ha-guide-figure-3007]

**圖 16：**新增容錯移轉叢集組態的第一個步驟 – 新增第一個節點 (應該是叢集節點) 的伺服器/VM 名稱

在接下來的步驟中，系統會要求您提供叢集的網路名稱 (虛擬主機名稱)。

![圖 17：新增容錯移轉叢集組態的第二個步驟 – 定義叢集的名稱][sap-ha-guide-figure-3008]

**圖 17：**新增容錯移轉叢集組態的第二個步驟 – 定義叢集的名稱


建立叢集之後，即會執行「叢集驗證測試」

![圖 18：新增容錯移轉叢集組態的最後一個步驟 – 執行叢集驗證檢查][sap-ha-guide-figure-3009]

**圖 18：**新增容錯移轉叢集組態的最後一個步驟 – 執行叢集驗證檢查


![圖 19：新增容錯移轉叢集組態的最後一個步驟 – 叢集驗證檢查會顯示有關找不到仲裁磁碟的警告][sap-ha-guide-figure-3010]

**圖 19：**新增容錯移轉叢集組態的最後一個步驟 – 叢集驗證檢查會顯示有關找不到仲裁磁碟的警告

在這個階段可以忽略任何有關磁碟的警告，稍後將會連同 SIOS 共用磁碟一起新增檔案共用見證。在這個階段，我們並不在乎仲裁。

![圖 20：已定義叢集核心資源及 IP 位址 – 但是需要新的 IP 位址][sap-ha-guide-figure-3011]

**圖 20：**已定義叢集核心資源及 IP 位址 – 但是需要新的 IP 位址


由於伺服器的 IP 位址是指向其中一個 VM 節點，因此叢集無法啟動。我們現在需要變更核心叢集服務的 IP 位址。

例如，我們需要為叢集虛擬主機名稱 _**pr1-ascs-vir**_ 指派 IP 位址 (在我們的範例中為 _**10.0.0.42**_)。這項操作是透過核心叢集服務之 IP 資源的屬性頁面來完成，如以下所示

![圖 21：使用 [屬性] 來變更為正確的 IP 位址][sap-ha-guide-figure-3012]

**圖 21：**使用 [屬性] 來變更為正確的 IP 位址


![圖 22：指派為叢集保留的 IP 位址][sap-ha-guide-figure-3013]

**圖 22：**指派為叢集保留的 IP 位址

變更 IP 位址之後，請讓叢集虛擬主機名稱上線。

![圖 23：叢集核心服務以正確的 IP 位址啟動並正常執行][sap-ha-guide-figure-3014]

**圖 23：**叢集核心服務以正確的 IP 位址啟動並正常執行

既然「核心叢集服務」已啟動並正常執行，您便可以新增第二個叢集節點

![圖 24：新增第二個叢集節點][sap-ha-guide-figure-3015]

**圖 24：**新增第二個叢集節點

![圖 25：新增第二個叢集節點主機名稱，例如 pr1-ascs-1][sap-ha-guide-figure-3016]

**圖 25：**新增第二個叢集節點主機名稱，例如 pr1-ascs-1

![圖 26：請「勿」選取核取方塊！][sap-ha-guide-figure-3017]

**圖 26：**請「勿」選取核取方塊！

> [AZURE.NOTE]  
請確定「沒有」選取 [新增適合的儲存裝置到叢集] 核取方塊！

![圖 27：同樣忽略有關磁碟仲裁的警告][sap-ha-guide-figure-3018]

**圖 27：**同樣忽略有關磁碟仲裁的警告

您可以忽略有關「仲裁」和磁碟的警告。仲裁和共用磁碟設定將在稍後設定，如**[為 SAP ASCS/SCS 叢集共用磁碟安裝 SIOS DataKeeper Cluster Edition][sap-ha-guide-8.12.3]** 一章所述。

#### <a name="e49a4529-50c9-4dcf-bde7-15a0c21d21ca"></a> 設定叢集檔案共用見證

##### <a name="06260b30-d697-4c4d-b1c9-d22c0bd64855"></a> 建立檔案共用

我們選擇「檔案共用見證」，而不是「仲裁磁碟」。SIOS DataKeeper 支援此選項。

在我們針對此文件的說明使用的組態中，是在於 Azure 中執行且名為 _**domcontr-0**_ 的 AD/DNS 伺服器上設定「檔案共用見證」。由於您會已設定對 Azure 的 VPN 連線 (透過站對站或 ExpressRoute)，因此您的 AD/DNS 會位於內部部署環境中，所以不適合用來執行「檔案共用見證」。

> [AZURE.NOTE] 如果您的 AD/DNS 只在內部部署環境中執行，則請勿在於內部部署環境中執行的 AD/DNS Windows OS 上設定「檔案共用見證」，因為在 Azure 上執行的叢集節點與在內部部署環境中的 AD/DNS 之間，網路延遲可能會太大而造成連線問題。請務必在執行位置靠近叢集節點的 Azure Windows VM 上設定「檔案共用見證」。

「仲裁磁碟機」至少需要 1024 MB 的可用空間。建議值為 2048 MB

第一個步驟是新增叢集名稱物件

![圖 28：為叢集名稱物件指派共用上的權限][sap-ha-guide-figure-3019]

**圖 28：**為叢集名稱物件指派共用上的權限

請確定權限包括能夠為叢集名稱物件 (在我們的範例中為 _**pr1-ascs-vir$**_) 變更共用中的資料。為了將叢集名稱物件新增到上面顯示的清單中，您必須按 [新增]，然後變更篩選以允許一併檢查電腦物件，如以下所示。

![圖 29：變更物件類型以一併包含電腦物件][sap-ha-guide-figure-3020]

**圖 29：**變更物件類型以一併包含電腦物件

![圖 30：選取電腦物件的方塊][sap-ha-guide-figure-3021]

**圖 30：**選取電腦物件的方塊

之後，輸入叢集名稱物件，如圖 29 所示。由於現在應該已經建立記錄，因此您可以變更權限，如圖 28 所示。

下一個步驟是使用共用的 [安全性] 索引標籤，為叢集名稱物件定義更細微的權限。

![圖 31：為叢集名稱物件設定檔案共用仲裁上的安全性屬性][sap-ha-guide-figure-3022]

**圖 31：**為叢集名稱物件設定檔案共用仲裁上的安全性屬性

##### <a name="4c08c387-78a0-46b1-9d27-b497b08cac3d"></a> 在容錯移轉叢集管理員中設定檔案共用見證

下一個步驟是使用「容錯移轉叢集管理員」將叢集組態變更為「檔案共用見證」。

![圖 32：呼叫「設定叢集仲裁設定精靈」(如這裡所示)][sap-ha-guide-figure-3023]

**圖 32：**呼叫「設定叢集仲裁設定精靈」(如這裡所示)

![圖 33：不同仲裁組態的選取畫面][sap-ha-guide-figure-3024]

**圖 33：**不同仲裁組態的選取畫面

在這個選取項目中，您必須選擇 [選取仲裁見證]。

![圖 34：檔案共用見證的選取畫面][sap-ha-guide-figure-3025]

**圖 34：**檔案共用見證的選取畫面

在我們的案例中，您必須選擇 [設定檔案共用見證]。

![圖 35：定義見證共用的檔案共用位置][sap-ha-guide-figure-3026]

**圖 35：**定義見證共用的檔案共用位置


輸入檔案共用的 UNC 路徑 (在我們的範例中為 `\\domcontr-0\FSW`)

按 [下一步]，這會顯示您想要進行的變更清單。檢查它們，然後再按一次 [下一步]，即可變更叢集組態。

![圖 36：顯示在叢集中重新設定成功的畫面][sap-ha-guide-figure-3027]

**圖 36：**顯示在叢集中重新設定成功的畫面

在這最後一個步驟中，叢集組態應該會重新設定成功。

### <a name="5c8e5482-841e-45e1-a89d-a05c0907c868"></a> 為 SAP ASCS/SCS 叢集共用磁碟安裝 SIOS DataKeeper Cluster Edition

我們目前所處的狀態就是在 Azure 中有一個運作中的「Windows Server 容錯移轉叢集」組態。不過，這個叢集組態目前還沒有共用的磁碟資源。為了安裝 SAP ASCS/SCS，我們需要有一個共用磁碟資源。這就是 SIOS DataKeeper Cluster Edition 能夠起作用的地方。由於 Azure 不允許我們建立具有必要功能的共用磁碟資源，因此我們必須依賴諸如 SIOS DataKeeper 來提供這項功能。

#### <a name="1c2788c3-3648-4e82-9e0d-e058e475e2a3"></a> 新增 Microsoft .NET Framework 3.5 功能

Microsoft.NET Framework 3.5 不會自動在最新的 Windows Server 版本上啟用或安裝。不過，SIOS DataKeeper 會要求在將要安裝本產品的所有節點上都要有 .NET Framework。因此，必須在不同 VM 的所有「客體 OS」上安裝 .NET 3.5。

有兩種方式可以新增 .Net 3.5 Framework。第一種可能性是使用 Windows 中的 [新增角色及功能]，如以下所示︰

![圖 37：透過「新增角色及功能精靈」安裝 .Net Framework 3.5][sap-ha-guide-figure-3028]

**圖 37：**透過「新增角色及功能精靈」安裝 .Net Framework 3.5

![圖 38：透過「新增角色及功能精靈」安裝 .Net Framework 3.5 的進度列][sap-ha-guide-figure-3029]

**圖 38：**透過「新增角色及功能精靈」安裝 .Net Framework 3.5 的進度列

第二種啟用 .Net Framework 3.5 功能的可能性是使用命令列工具 _**dism.exe**_。針對這類型的安裝，您必須將 Windows 安裝媒體的 ‘sxs’ 目錄設為可存取。下列命令必須在已提升權限的命令列示窗中執行：

```
Dism /online /enable-feature /featurename:NetFx3 /All /Source:installation_media_drive:\sources\sxs /LimitAccess
```

#### <a name="dd41d5a2-8083-415b-9878-839652812102"></a> 安裝 SIOS DataKeeper

讓我們逐步執行 SIOS DataKeeper Cluster Edition 的安裝。在叢集中的兩個節點上都必須安裝它。SIOS DataKeeper 會透過建立同步的鏡像及模擬「叢集共用儲存體」，讓您能夠建立虛擬共用儲存體。

在安裝 SIOS 軟體之前，您必須先建立網域使用者 _**DataKeeperSvc**_。

> [AZURE.NOTE] 請將這個 _**DataKeeperSvc**_ 使用者同時新增到兩個叢集節點上的「本機系統管理員」群組中。
  
在兩個叢集節點上都安裝 SIOS 軟體

![SIOS 安裝程式][sap-ha-guide-figure-3030]

![圖 39：第一個 SIOS DataKeeper 安裝畫面][sap-ha-guide-figure-3031]

**圖 39：**第一個 SIOS DataKeeper 安裝畫面

![圖 40：DataKeeper 通知要停用某個服務][sap-ha-guide-figure-3032]

**圖 40：**DataKeeper 通知要停用某個服務

當您收到圖 40 中的快顯視窗時，請選擇 [是]。

![圖 41：SIOS DataKeeper 的使用者選項][sap-ha-guide-figure-3033]

**圖 41：**SIOS DataKeeper 的使用者選項


在上述畫面中，我們建議選擇 [網域或伺服器帳戶]。

![圖 42：為 SIOS DataKeeper 安裝提供網域使用者和密碼][sap-ha-guide-figure-3034]

**圖 42：**為 SIOS DataKeeper 安裝提供網域使用者和密碼

指定您為 SIOS DataKeeper 建立的網域帳戶及該帳戶的密碼。

![圖 43：提供您的 SIOS DataKeeper 授權][sap-ha-guide-figure-3035]

**圖 43：**提供您的 SIOS DataKeeper 授權

安裝您 SIOS DataKeeper 的授權金鑰，如 43 圖所示。在安裝結束時，系統會要求您「重新啟動 VM」。

#### <a name="d9c1fc8e-8710-4dff-bec2-1f535db7b006"></a> 設定 SIOS DataKeeper

在兩個節點上都安裝 SIOS DataKeeper 之後，我們必須開始設定組態。組態的目標是要在連接至每個 VM 的額外 VHD 之間進行同步資料複寫。下列步驟顯示兩個節點上的組態。

![圖 44：DataKeeper 管理和組態工具][sap-ha-guide-figure-3036]

**圖 44：**DataKeeper 管理和組態工具


啟動 DataKeeper 的「管理和組態工具」，然後按 [連接伺服器] 連結 (上圖中以紅色圈起的部分)

![圖 45：插入管理工具所應連接之第一個節點的名稱或 TCP/IP 位址，然後在第二個步驟中，插入第二個節點的相關資料][sap-ha-guide-figure-3037]

**圖 45：**插入管理工具所應連接之第一個節點的名稱或 TCP/IP 位址，然後在第二個步驟中，插入第二個節點的相關資料

下一個步驟是要在兩個節點之間建立「複寫作業」

![圖 46：建立複寫作業][sap-ha-guide-figure-3038]

**圖 46：**建立複寫作業

精靈會引導您完成此程序

![圖 47：定義複寫作業的名稱][sap-ha-guide-figure-3039]

**圖 47：**定義複寫作業的名稱

![圖 48：定義節點 (應該是目前的來源節點) 的基底資料][sap-ha-guide-figure-3040]

**圖 48：**定義節點 (應該是目前的來源節點) 的基底資料

在第一個步驟中，必須定義來源節點的名稱、TCP/IP 位址及磁碟區。第二個步驟是定義目標節點。同樣地，必須定義目標節點的名稱、TCP/IP 位址及磁碟區。

![圖 49：定義節點 (應該是目前的目標節點) 的基底資料][sap-ha-guide-figure-3041]

**圖 49：**定義節點 (應該是目前的目標節點) 的基底資料

下一個步驟是定義應套用的壓縮演算法。就我們的目的而言，建議壓縮複寫資料流。尤其是在重新同步處理的情況下，壓縮複寫資料流可大幅縮短重新同步處理時間。請注意，壓縮會使用 VM 的 CPU 和 RAM 資源。因此，壓縮率越高，CPU 使用率也越高。您可以在稍後調整和變更此設定。

另一個您需要檢查的設定是複寫是以非同步方式還是同步方式執行。**為了保護 SAP ASCS/SCS 組態，必須使用「同步複寫」設定**。

![圖 50：定義複寫的詳細資料][sap-ha-guide-figure-3042]

**圖 50：**定義複寫的詳細資料

最後一個步驟是定義是否應該向 WSFC 叢集組態指出複寫作業所複寫的磁碟區是共用磁碟。就 SAP ASCS/SCS 組態而言，我們必須選取 [是]，如此 Windows 叢集才會將複寫的磁碟區視為可用來作為叢集磁碟區的共用磁碟。

![圖 51： 按 [是] 以讓複寫的磁碟區作為叢集磁碟區][sap-ha-guide-figure-3043]

**圖 51：** 按 [是] 以讓複寫的磁碟區作為叢集磁碟區

建立完成之後，「DataKeeper 管理工具」就會將複寫作業列示為作用中狀態。

![圖 52：SAP ASCS/SCS 共用磁碟的 DataKeeper 同步鏡像處於作用中狀態][sap-ha-guide-figure-3044]

**圖 52：**SAP ASCS/SCS 共用磁碟的 DataKeeper 同步鏡像處於作用中狀態

如此一來，磁碟現在在「Windows 容錯移轉叢集管理員」中就可被視為「DataKeeper 磁碟」，如以下所示。

![圖 53：DataKeeper 所複寫的磁碟已顯示在「容錯移轉叢集管理員」中][sap-ha-guide-figure-3045]

**圖 53：**DataKeeper 所複寫的磁碟已顯示在「容錯移轉叢集管理員」中


## <a name="a06f0b49-8a7a-42bf-8b0d-c12026c5746b"></a> SAP NetWeaver 系統安裝

我們將不會說明 DBMS 的設定，因為設定會因所使用的 DBMS 系統而異。不過，我們會假設 DBMS 在「高可用性」方面的疑慮已藉由不同 DBMS 廠商為 Azure 提供的功能支援而獲得解決。例如適用於 SQL Server 的 AlwaysOn 或「資料庫鏡像」，以及適用於 Oracle 的 Oracle Data Guard。在我們針對此文件使用的範例案例中，我們並沒有為 DBMS 提供額外保護。

在不同 DBMS 與這類 Azure 上叢集 SAP ASCS/SCS 組態的互動方面，也沒有任何特殊考量。

> [AZURE.NOTE]  
SAP NetWeaver ABAP 系統、Java 系統及 ABAP+Java 系統的安裝程序幾乎完全相同。最大的差異在於 SAP ABAP 系統有一個 ASCS 執行個體。SAP Java 系統有一個 SCS 執行個體，而 SAP ABAP+Java 系統則有一個 ASCS 和一個 SCS 執行個體在相同 Microsoft 容錯移轉叢集群組中執行。我們將明確說明每個 SAP NetWeaver 安裝堆疊的所有安裝差異。所有其他部分則假設為相同。

### <a name="31c6bd4f-51df-4057-9fdf-3fcbc619c170"></a> 具有高可用性 ASCS/SCS 執行個體的 SAP 安裝

> [AZURE.NOTE]  
請「勿」將您的分頁檔放在 DataKeeper 鏡像磁碟區上，因為 DataKeeper 不支援分頁檔。您可以將分頁檔留在 Azure VM 的 D:\\ 暫存磁碟機中，此處是在 Azure 中部署 VM 時就已用來放置分頁檔的位置。如果情況並非如此，請加以修正，然後將 Windows 分頁檔放在您 Azure VM 的 D:\\ 磁碟機上。

#### <a name="a97ad604-9094-44fe-a364-f89cb39bf097"></a> 建立叢集 SAP ASCS/SCS 的虛擬主機名稱

第一個步驟是為「ASCS/SCS 執行個體」的「虛擬主機名稱」建立必要的 DNS 項目。用來執行這項操作的工具是「Windows DNS 管理員」。除了「虛擬主機名稱」之外，也必須定義指派給「虛擬主機名稱」的 IP 位址。

> [AZURE.NOTE]  
**請記住，我們指派給 ASCS/SCS 執行個體之「虛擬主機名稱」的 IP 位址必須與我們已指派給 Azure Load Balancer (`<sid>-lb-ascs`) 的 IP 位址相同。虛擬 SAP ASCS/SCS 主機名稱 `(pr1-ascs-sap)` 的 IP 位址 = Azure Load Balancer`(pr1-lb-ascs)` 的 IP 位址**

這也意謂著在 Azure 上，一個「Windows Server 容錯移轉叢集」中只能執行一個「SAP 容錯移轉叢集」角色，例如就 ABAP 系統而言，是一個 ASCS 執行個體，就 Java 系統而言，是一個 SCS 執行個體，以及就 ABAP+Java 而言，是一個 ASCS 和一個 SCS 執行個體。

> [AZURE.NOTE] 《SAP 安裝指南》(請參閱 [SAP 安裝指南][sap-installation-guides]) 中所述的多 SID 叢集目前在 Azure 中無法運作。

![圖 54：定義 SAP ASCS/SCS 叢集虛擬名稱和 TCP/IP 位址的 DNS 項目][sap-ha-guide-figure-3046]

**圖 54：**定義 SAP ASCS/SCS 叢集虛擬名稱和 TCP/IP 位址的 DNS 項目

該項目會顯示在 DNS 管理員的網域底下，如下一張圖所示。

![圖 55：針對 SAP ASCS/SCS 叢集組態列出的新虛擬名稱和 TCP/IP 位址][sap-ha-guide-figure-3047]

**圖 55：**針對 SAP ASCS/SCS 叢集組態列出的新虛擬名稱和 TCP/IP 位址

#### <a name="eb5af918-b42f-4803-bb50-eff41f84b0b0"></a> 安裝 SAP 的第一個叢集節點

第一個 ASCS/SCS 叢集節點的安裝方式與 SAP 安裝文件中所記載的方式沒有任何不同，同樣是︰
- 在叢集節點 A 上 (例如在我們的範例中是在 _**pr1-ascs-0**_ 主機上) 執行「第一個叢集節點」選項。

如果您想要為 Azure Internal Load Balancer 保留預設連接埠，則請選擇︰

- 針對 **ABAP 系統** - **ASCS** 執行個體號碼 **00**
- 針對 **Java 系統** - **SCS** 執行個體號碼 **01**
- 針對 **ABAP + JAVA 系統** - **ASCS** 執行個體號碼 **00** 和 **SCS** 執行個體號碼 **01**

如果您想要針對 ABAP ASCS 執行個體使用 00 以外的號碼，以及針對 Java SCS 執行個體使用 01 以外的號碼，您必須先變更 Azure ILB 預設負載平衡規則，如**[變更 Azure Internal Load Balancer (ILB) 的 ASCS/SCS 預設負載平衡規則][sap-ha-guide-8.9]**所述。

完成此步驟之後，您必須執行一般 SAP 安裝文件中未描述的幾個步驟。

#### <a name="e4caaab2-e90f-4f2c-bc84-2cd2e12a9556"></a> 修改 ASCS/SCS 執行個體的 SAP 設定檔

必須新增一個新的設定檔參數。此設定檔參數可避免 SAP 工作程序與加入佇列伺服器之間的連線在閒置時間太長時關閉。我們在本文件的**[在用於 SAP ASCS/SCS 執行個體的兩個叢集節點上都新增登錄項目][sap-ha-guide-8.11]**一章中已提到問題案例。在該部分，我們也介紹了對一些基本 TCP/IP 連線參數所做的兩項變更。在第二個步驟中，我們必須設定讓加入佇列伺服器傳送 **keep\_alive** 訊號，如此連線才不會達到 Azure ILB 的閒置臨界值。基於此目的，請將此設定檔參數︰

```
enque/encni/set_so_keepalive = true
```

新增到 SAP ASCS/SCS 執行個體設定檔。在我們的範例中，路徑為：

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_ASCS00_pr1-ascs-sap`

例如，新增到 SAP SCS 執行個體設定檔和對應的路徑

`<ShareDisk>:\usr\sap\PR1\SYS\profile\PR1_SCS01_pr1-ascs-sap`


#### <a name="10822f4f-32e7-4871-b63a-9b86c76ce761"></a> 新增探查連接埠

為了讓整個叢集組態能夠與 Azure Load Balancer 搭配運作，我們必須利用「內部負載平衡器」的探查功能。Azure Internal Load Balancer 通常會在參與的虛擬機器之間，平衡及平均分配內送的工作負載。不過，在這樣的叢集組態中，該方式並無法運作，因為只有其中一個執行個體會處於作用中狀態，另一個則為被動狀態而無法接受工作負載。為了啟用讓 Azure Internal Load Balancer 只將工作指派給作用中執行個體的組態，於是建立了探查功能。透過該功能，「內部負載平衡器」便能夠檢查哪個執行個體處於作用中狀態，然後只以該執行個體作為工作負載的目標。首先，讓我們在參與叢集組態的其中一個 VM 內執行下列 PowerShell 命令，以檢查目前的 _**ProbePort**_ 設定︰

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter 
```

![圖 56：叢集組態的探查連接埠預設為 0][sap-ha-guide-figure-3048]

**圖 56：**叢集組態的探查連接埠預設為 0

探查連接埠號碼預設是設定為 0。為了讓組態能夠運作，必須定義連接埠。在我們的案例中，我們必須使用探查連接埠 _**62300**_，因為 SAP Azure Resource Manager 範本中已定義這個連接埠號碼。若要指派該連接埠號碼，可以透過下面兩個命令：

首先，取得 SAP 虛擬主機名稱叢集資源 _**SAP WAC IP**_

```PowerShell
$var = Get-ClusterResource | Where-Object {  $_.name -eq "SAP PR1 IP"  } 
```

接著，將探查連接埠設定為 62300

```PowerShell
$var | Set-ClusterParameter -Multiple @{"Address"="10.0.0.43";"ProbePort"=62300;"Subnetmask"="255.255.255.0";"Network"="Cluster Network 1";"OverrideAddressMatch"=1;"EnableDhcp"=0}  
```

您必須將 _**SAP PR1**_ 叢集角色停止後再啟動，才能啟用變更。

讓 _**SAP PR1**_ 叢集角色上線之後，請確認 _**ProbePort**_ 已設定為新值︰

```PowerShell
Get-ClusterResource „SAP PR1 IP" | Get-ClusterParameter 
```

![圖 57：變更後的叢集探查連接埠][sap-ha-guide-figure-3049]

**圖 57：**變更後的叢集探查連接埠

您可以看到 _**ProbePort**_ 現在已設定為 _**62300**_。現在您可以從其他主機 (例如 _**ascsha-dbas**_) 存取檔案共用 _**\\\ascsha-clsap\\sapmnt**_。

### <a name="85d78414-b21d-4097-92b6-34d8bcb724b7"></a> 安裝資料庫執行個體

安裝資料庫執行個體的方式與 SAP 安裝文件中所述的程序沒有任何不同。因此，此處並未記載。

### <a name="8a276e16-f507-4071-b829-cdc0a4d36748"></a> 安裝第二個叢集節點

同樣地，第二個叢集節點的安裝方式與 SAP 安裝文件所述的方式沒有什麼不同。因此，請依照安裝指南中的步驟來安裝第二個叢集節點。

### <a name="094bc895-31d4-4471-91cc-1513b64e406a"></a> 變更 SAP ERS 執行個體的 Windows 服務啟動類型

將兩個叢集節點上 SAP ERS Windows 服務的啟動類型都變更為 [自動 (延遲啟動)]。

![圖 58：將 SAP ERS 執行個體的服務類型變更為延遲的自動類型][sap-ha-guide-figure-3050]

**圖 58：**將 SAP ERS 執行個體的服務類型變更為延遲的自動類型

### <a name="2477e58f-c5a7-4a5d-9ae3-7b91022cafb5"></a> SAP 主要應用程式伺服器 (PAS) 安裝

在被指定來裝載 PAS 的 VM 上執行「主要應用程式伺服器執行個體」`<sid>-di-0` 安裝。不需要提供任何 Azure 或 DataKeeper 詳細資料。

### <a name="0ba4a6c1-cc37-4bcf-a8dc-025de4263772"></a> SAP 其他應用程式伺服器 (AAS) 安裝

在所有被指定來裝載「SAP 應用程式伺服器」的 VM (例如從 `<sid>-di-1` 直到 `<sid>-di-<n>`) 上執行額外「SAP 應用程式伺服器」的安裝。

## <a name="18aa2b9d-92d2-4c0e-8ddd-5acaabda99e9"></a> 測試 SAP ASCS/SCS 執行個體容錯移轉和 SIOS 複寫

您可以使用「容錯移轉叢集管理員」 和 SIOS DataKeeper UI，輕鬆地測試及監視 SAP ASCS/SCS 執行個體容錯移轉和 SIOS 磁碟複寫。

### <a name="65fdef0f-9f94-41f9-b314-ea45bbfea445"></a> 起點 – SAP ASCS/SCS 執行個體在執行叢集節點 A 上執行

_**SAP WAC**_ 叢集群組在叢集節點 A 上執行，例如在 _**ascsha-clna**_ 上。屬於 _**SAP WAC**_ 叢集群組並由 ASCS/SCS 執行個體使用的共用磁碟 S: 被指派給叢集節點 A。

![圖 59：容錯移轉叢集管理員︰SAP <SID> 叢集群組在叢集節點 A 上執行][sap-ha-guide-figure-5000]

**圖 59：**容錯移轉叢集管理員︰SAP <SID> 叢集群組在叢集節點 A 上執行

藉由使用 SIOS DataKeeper UI，您可以看到共用磁碟資料以同步方式從叢集節點 A (例如 _**ascsha-clna [10.0.0.41]**_) 上的來源磁碟區 S: 被複寫到叢集節點 B (例如 _**ascsha-clnb [10.0.0.42]**_) 上的目標磁碟區 _**S:**_

![圖 60：SIOS DataKeeper︰將本機磁碟區從叢集節點 A 複寫到叢集節點 B][sap-ha-guide-figure-5001]

**圖 60：**SIOS DataKeeper︰將本機磁碟區從叢集節點 A 複寫到叢集節點 B


### <a name="5e959fa9-8fcd-49e5-a12c-37f6ba07b916"></a> 從節點 A 到節點 B 的容錯移轉程序

您可以起始將 SAP <SID> 叢集群組從叢集節點 A 容錯移轉到叢集節點 B 的容錯移轉：

- 使用「容錯移轉叢集管理員」

- 使用「容錯移轉叢集 PowerShell」

  ```powershell
  Move-ClusterGroup -Name "SAP WAC" 
  ```

- 在 Windows 客體 OS 內重新啟動叢集節點 A (這會起始將 SAP <SID> 叢集群組從節點 A 容錯移轉到節點 B 的自動容錯移轉)

- 從 Azure 入口網站重新啟動叢集節點 A (這會起始將 SAP <SID> 叢集群組從節點 A 容錯移轉到節點 B 的自動容錯移轉)

- 使用 Azure PowerShell 來重新啟動叢集節點 A (這會起始將 SAP <SID> 叢集群組從節點 A 容錯移轉到節點 B 的自動容錯移轉)

  ```powershell
  Restart-AzureVM -Name ascsha-clna -ServiceName ascsha-cluster
  ```

### <a name="755a6b93-0099-4533-9f6d-5c9a613878b5"></a> 最終結果 – SAP ASCS/SCS 執行個體在叢集節點 B 上執行

在容錯移轉之後，`SAP <SID>` 叢集群組在叢集節點 B 上執行，例如在 _**ascsha-clnb**_ 上。

![圖 61：容錯移轉叢集管理員︰SAP <SID> 叢集群組在叢集節點 B 上執行][sap-ha-guide-figure-5002]

**圖 61：**容錯移轉叢集管理員︰SAP <SID> 叢集群組在叢集節點 B 上執行

共用磁碟現在已掛接在叢集節點 B 上。SIOS DataKeeper 正在將資料從叢集節點 B (例如 _**ascsha-clnb [10.0.0.42]**_) 上的來源磁碟區 S: 複寫到叢集節點 A (例如 _**ascsha-clna [10.0.0.41]**_) 上的目標磁碟區 S:。

![圖 62：SIOS DataKeeper︰將本機磁碟區從叢集節點 B 複寫到叢集節點 A][sap-ha-guide-figure-5003]

**圖 62：**SIOS DataKeeper︰將本機磁碟區從叢集節點 B 複寫到叢集節點 A

<!---HONumber=AcomDC_0907_2016-->