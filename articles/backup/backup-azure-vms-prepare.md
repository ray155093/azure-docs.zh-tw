---
title: "準備環境以備份 Azure 虛擬機器 | Microsoft Docs"
description: "確認在 Azure 中備份虛擬機器的環境已準備就緒"
services: backup
documentationcenter: 
author: markgalioto
manager: cfreeman
editor: 
keywords: "備份；備份；"
ms.assetid: 238ab93b-8acc-4262-81b7-ce930f76a662
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2016
ms.author: trinadhk; jimpark; markgal;
translationtype: Human Translation
ms.sourcegitcommit: cbd7e36c5ef5c78b38a2cc7791b442cac1711b95
ms.openlocfilehash: 92e8e25abc047811fc0ff45f424bd1b97a045e1f


---
# <a name="prepare-your-environment-to-back-up-azure-virtual-machines"></a>準備環境以備份 Azure 虛擬機器
> [!div class="op_single_selector"]
> * [Resource Manager 模型](backup-azure-arm-vms-prepare.md)
> * [傳統模型](backup-azure-vms-prepare.md)
>
>

在您可以備份 Azure 虛擬機器 (VM) 之前，有三個條件必須存在。

* 您需要在「與 VM 的相同區域中」 建立備份保存庫，或識別現有的備份保存庫。
* 在 Azure 公用網際網路位址和 Azure 儲存體端點之間建立網路連線。
* 在 VM 上安裝 VM 代理程式。

如果您知道環境滿足這些條件，請繼續依 [備份 VM 文章](backup-azure-vms.md)中的指示進行。 否則，請繼續閱讀，這篇文章會引導您逐步完成備妥環境來備份 Azure VM。

##<a name="supported-operating-system-for-backup"></a>支援的備份作業系統
 * **Linux**：Azure 備份支援 [Azure 所背書的散發套件清單](../virtual-machines/virtual-machines-linux-endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ，但核心作業系統 Linux 除外。 _只要虛擬機器上有 VM 代理程式並且可支援 Python，其他「攜帶您自己的 Linux」散發套件可能也可以運作。不過，我們並不為這些備份散發套件背書。_
 * **Windows Server**：不支援比 Windows Server 2008 R2 更舊的版本。


## <a name="limitations-when-backing-up-and-restoring-a-vm"></a>備份和還原 VM 時的限制
> [!NOTE]
> Azure 有兩種用來建立和使用資源的部署模型： [Resource Manager 和傳統](../azure-resource-manager/resource-manager-deployment-model.md)。 下列清單提供在傳統的模型中部署時的限制。
>
>

* 不支援備份具有 16 個以上資料磁碟的虛擬機器。
* 不支援備份具有保留的 IP 且沒有已定義之端點的虛擬機器。
* 備份資料不包含連接至 VM 的網路掛接磁碟機。
* 不支援在還原期間取代現有的虛擬機器。 先刪除現有的虛擬機器及任何相關聯的磁碟，然後從備份還原資料。
* 不支援跨區域備份和還原。
* Azure 的所有公用區域皆支援使用「Azure 備份」服務來備份虛擬機器 (請參閱支援的區域 [檢查清單](https://azure.microsoft.com/regions/#services) )。 如果您尋找的區域目前不受支援，在建立保存庫期間，該區域就不會顯示在下拉式清單中。
* 只有特定的作業系統版本才支援使用「Azure 備份」服務來備份虛擬機器：
* 只有透過 PowerShell 才支援還原屬於多網域控制站 (DC) 組態的 DC VM。 進一步了解 [還原多 DC 網域控制站](backup-azure-restore-vms.md#restoring-domain-controller-vms)。
* 僅支援透過 PowerShell 還原具有以下特殊網路組態的虛擬機器。 藉由使用 UI 中的還原工作流程來建立的 VM 在還原作業完成之後，將不會具有這些網路組態。 若要深入了解，請參閱 [還原具有特殊網路組態的 VM](backup-azure-restore-vms.md#restoring-vms-with-special-network-configurations)。
  * 負載平衡器組態下的虛擬機器 (內部與外部)
  * 具有多個保留的 IP 位址的虛擬機器
  * 具有多個網路介面卡的虛擬機器

## <a name="create-a-backup-vault-for-a-vm"></a>為 VM 建立備份保存庫
備份保存庫是一個實體，儲存隨著時間建立的所有備份和復原點。 備份保存庫也包含備份虛擬機器時將套用的備份原則。

此影像顯示各種 Azure 備份實體之間的關係：    ![Azure 備份實體和關聯性](./media/backup-azure-vms-prepare/vault-policy-vm.png)

若要建立備份保存庫：

1. 登入 [Azure 入口網站](http://manage.windowsazure.com/)。
2. 在 Azure 入口網站，按一下 [新增] > [混合式整合] > [備份]。 當您按一下 [備份] 時，您會自動切換至傳統入口網站 (顯示於 [注意] 之後)。

    ![Ibiza 入口網站](./media/backup-azure-vms-prepare/Ibiza-portal-backup01.png)

   > [!NOTE]
   > 如果您上次是在傳統入口網站中使用訂用帳戶，則您的訂用帳戶可能會在傳統入口網站中開啟。 在此情況下，若要建立備份保存庫，請按一下 [新增] > [資料服務] > [復原服務] > [備份保存庫] > [快速建立] (請參閱下圖)。
   >
   >

    ![建立備份保存庫](./media/backup-azure-vms-prepare/backup_vaultcreate.png)
3. 針對 [名稱] ，輸入保存庫的易記識別名稱。 必須是 Azure 訂用帳戶中唯一的名稱。 輸入包含 2 到 50 個字元的名稱。 該名稱必須以字母開頭，而且只可以包含字母、數字和連字號。
4. 在 [ **區域**] 中，選取保存庫的地理區域。 保存庫必須與您想要保護的虛擬機器位於相同區域。 如果您在多個區域中有虛擬機器，您必須在每個區域中建立備份保存庫。 儲存備份資料時，不需要指定儲存體帳戶，備份保存庫和「Azure 備份」服務會自動處理此作業。
5. 在 [訂用帳戶]  中，選取您希望與備份保存庫相關聯的訂用帳戶。 只有在您的組織帳戶與多個 Azure 訂用帳戶相關聯時，才會有多個選擇。
6. 按一下 [建立保存庫] 。 要等備份保存庫建立好，可能需要一些時間。 監視位於入口網站底部的狀態通知。

    ![建立保存庫快顯通知](./media/backup-azure-vms-prepare/creating-vault.png)
7. 將有則訊息確認已成功建立保存庫。 該保存庫將會在 [復原服務] 頁面中以 [使用中] 狀態列出。 建立保存庫之後，請務必立即選擇適當的儲存體備援選項。 進一步了解[在備份保存庫中設定儲存體備援選項](backup-configure-vault.md#step-1-create-a-recovery-services-vault)。

    ![備份保存庫的清單](./media/backup-azure-vms-prepare/backup_vaultslist.png)
8. 按一下備份保存庫以前往 [快速入門]  頁面，此頁面會顯示備份 Azure 虛擬機器的相關指示。

    ![「儀表板」頁面上的虛擬機器備份指示](./media/backup-azure-vms-prepare/vmbackup-instructions.png)

## <a name="network-connectivity"></a>網路連線
為了管理 VM 快照，備份擴充功能需要連接 Azure 公用 IP 位址。 若無適當的網際網路連線，虛擬機器的 HTTP 要求將會逾時，而備份作業將會失敗。 如果您的部署有存取限制 (如透過網路安全性群組 (NSG))，請選擇其中一個選項來為備份流量提供明確的路徑︰

* [將 Azure 資料中心 IP 範圍列入允許清單](http://www.microsoft.com/en-us/download/details.aspx?id=41653) - 請參閱文章以取得將 IP 位址列入白名單的指示。
* 部署 HTTP Proxy 伺服器來路由傳送流量。

在決定該使用哪個選項時，要取捨的不外乎是可管理性、精確控制及成本等要素。

| 選項 | 優點 | 缺點 |
| --- | --- | --- |
| 將 IP 範圍列入允許清單 |沒有額外的成本。<br><br>如需在某個 NSG 中開啟存取權，請使用 <i>Set-AzureNetworkSecurityRule</i> Cmdlet。 |由於受影響的 IP 範圍會隨著時間改變，因此難以管理。<br><br>提供整個 Azure 的存取權，而不只是「儲存體」的存取權。 |
| HTTP Proxy |在 proxy 中精確控制允許的儲存體 URL。<br>VM 的單一網際網路存取點。<br>不會隨著 Azure IP 位址變更。 |使用 Proxy 軟體執行 VM 時的額外成本。 |

### <a name="whitelist-the-azure-datacenter-ip-ranges"></a>將 Azure 資料中心 IP 範圍列入允許清單
若要將 Azure 資料中心 IP 範圍列入允許清單，請參閱 [Azure 網站](http://www.microsoft.com/en-us/download/details.aspx?id=41653) 以取得 IP 範圍的詳細資料和指示。

### <a name="using-an-http-proxy-for-vm-backups"></a>使用 HTTP Proxy 進行 VM 備份
備份 VM 時，VM 上的備份擴充功能會使用 HTTPS API 將快照管理命令傳送到 Azure 儲存體。 透過 HTTP Proxy 路由傳送擴充功能流量，因為它是唯一為了要存取公用網際網路而設定的元件。

> [!NOTE]
> 對於應該使用什麼 Proxy 軟體，並無任何建議。 請務必挑選與下面設定步驟相容的 Proxy。
>
>

以下範例影像示範使用 HTTP Proxy 所需的三個組態步驟︰

* 應用程式 VM 會透過 Proxy VM 路由傳送所有連往公用網際網路的 HTTP 流量。
* Proxy VM 允許從虛擬網路之 VM 傳輸的傳入流量。
* 名為 NSF-lockdown 的網路安全性群組 (NSG) 需要允許從 Proxy VM 傳輸之輸出網際網路流量的安全性規則。

![包含 HTTP Proxy 部署圖表的 NSG](./media/backup-azure-vms-prepare/nsg-with-http-proxy.png)

若要使用 HTTP Proxy 來與公用網際網路通訊，請依照下列步驟執行︰

#### <a name="step-1-configure-outgoing-network-connections"></a>步驟 1. 設定連出網路連線
###### <a name="for-windows-machines"></a>Windows 電腦
這會設定本機系統帳戶的 Proxy 伺服器組態。

1. 下載 [PsExec](https://technet.microsoft.com/sysinternals/bb897553)
2. 從提升權限的提示字元執行下列命令。

     ```
     psexec -i -s "c:\Program Files\Internet Explorer\iexplore.exe"
     ```
     它會開啟 Internet Explorer 視窗。
3. 移至 [工具]-> [網際網路選項]-> [連線]-> [區域網路設定]。
4. 確認系統帳戶的 Proxy 設定。 設定 Proxy IP 和連接埠。
5. 關閉 Internet Explorer。

這會設定一個整部機器的 Proxy 設定，並用於任何連出 HTTP/HTTPS 流量。

如果您已在目前的使用者帳戶 (非本機系統帳戶) 上設定 Proxy 伺服器，請使用下列指令碼將它們套用至 SYSTEMACCOUNT︰

```
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name DefaultConnectionSettings -Value $obj.DefaultConnectionSettings
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings\Connections" -Name SavedLegacySettings -Value $obj.SavedLegacySettings
   $obj = Get-ItemProperty -Path Registry::”HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Internet Settings"
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name ProxyEnable -Value $obj.ProxyEnable
   Set-ItemProperty -Path Registry::”HKEY_USERS\S-1-5-18\Software\Microsoft\Windows\CurrentVersion\Internet Settings" -Name Proxyserver -Value $obj.Proxyserver
```

> [!NOTE]
> 如果您在 Proxy 伺服器記錄檔中發現「(407) 需要 Proxy 驗證」，請檢查驗證設定是否正確。
>
>

###### <a name="for-linux-machines"></a>Linux 電腦
在 ```/etc/environment``` 檔案中新增以下文字行：

```
http_proxy=http://<proxy IP>:<proxy port>
```

將下列幾行新增至 ```/etc/waagent.conf``` 檔案：

```
HttpProxy.Host=<proxy IP>
HttpProxy.Port=<proxy port>
```

#### <a name="step-2-allow-incoming-connections-on-the-proxy-server"></a>步驟 2. 在 Proxy 伺服器上允許連入連線：
1. 在 Proxy 伺服器上，開啟 [Windows 防火牆]。 存取防火牆最簡單的方式搜尋「具有進階安全性的 Windows 防火牆」。

    ![開啟防火牆](./media/backup-azure-vms-prepare/firewall-01.png)
2. 在 [Windows 防火牆] 對話方塊中，以滑鼠右鍵按一下 [輸入規則] 並按一下 [新增規則...]。

    ![建立新的規則](./media/backup-azure-vms-prepare/firewall-02.png)
3. 在 [新增輸入規則精靈] 中，針對 [規則類型] 選擇 [自訂] 選項，然後按 [下一步]。
4. 在選取 [程式] 的頁面上，選擇 [所有程式]，然後按 [下一步]。
5. 在 [通訊協定和連接埠] 頁面上，輸入下列資訊，然後按 [下一步]：

    ![建立新的規則](./media/backup-azure-vms-prepare/firewall-03.png)

   * 針對 [通訊協定類型]，請選擇 [TCP]
   * 針對 [本機連接埠]，請選擇 [特定連接埠]，在下方欄位中指定已經設定的 ```<Proxy Port>```。
   * 針對 [遠端連接埠]，請選取 [所有連接埠]

     在精靈的其餘部分，按一下直到結束為止並指定此規則的名稱。

#### <a name="step-3-add-an-exception-rule-to-the-nsg"></a>步驟 3. 新增 NSG 例外規則：
在 Azure PowerShell 命令提示字元中，輸入下列命令：

下列命令會新增 NSG 例外狀況。 此例外狀況允許從 10.0.0.5 上任何連接埠傳輸至 80 (HTTP) 或 443 (HTTPS) 連接埠上任何網際網路位址的 TCP 流量。 如果您需要公用網際網路中的特定連接埠，請務必將該連接埠一併新增至 ```-DestinationPortRange``` 。

```
Get-AzureNetworkSecurityGroup -Name "NSG-lockdown" |
Set-AzureNetworkSecurityRule -Name "allow-proxy " -Action Allow -Protocol TCP -Type Outbound -Priority 200 -SourceAddressPrefix "10.0.0.5/32" -SourcePortRange "*" -DestinationAddressPrefix Internet -DestinationPortRange "80-443"
```

*務必以適合您的部署的詳細資料取代範例中的名稱。*

## <a name="vm-agent"></a>VM 代理程式
備份 Azure 虛擬機器之前，您應該先確定虛擬機器上已正確安裝 Azure VM 代理程式。 由於 VM 代理程式在虛擬機器建立時為選擇性元件，因此佈建虛擬機器之前，請先確定已選取 VM 代理程式的核取方塊。

### <a name="manual-installation-and-update"></a>手動安裝和更新
從 Azure 資源庫建立的 VM 中已經有 VM 代理程式。 不過，從內部部署資料中心移轉的虛擬機器不會安裝 VM 代理程式。 對於這類 VM，必須明確安裝 VM 代理程式。 深入了解 [在現有 VM 上安裝 VM 代理程式](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx)。

| **作業** | **Windows** | **Linux** |
| --- | --- | --- |
| 安裝 VM 代理程式 |<li>下載並安裝 [代理程式 MSI](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)。 您需要有系統管理員權限，才能完成安裝。 <li>[更新 VM 屬性](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) 以表示已安裝代理程式。 |<li> 從 GitHub 安裝最新的 [Linux 代理程式](https://github.com/Azure/WALinuxAgent) 。 您需要有系統管理員權限，才能完成安裝。 <li> [更新 VM 屬性](http://blogs.msdn.com/b/mast/archive/2014/04/08/install-the-vm-agent-on-an-existing-azure-vm.aspx) 以表示已安裝代理程式。 |
| 更新 VM 代理程式 |更新 VM 代理程式與重新安裝 [VM 代理程式二進位檔](http://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409)一樣簡單。 <br><br>確定在更新 VM 代理程式時，沒有任何執行中的備份作業。 |請遵循[更新 Linux VM 代理程式](../virtual-machines/virtual-machines-linux-update-agent.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)上的指示。 <br><br>確定在更新 VM 代理程式時，沒有任何執行中的備份作業。 |
| 驗證 VM 代理程式安裝 |<li>瀏覽至 Azure VM 中的 C:\WindowsAzure\Packages 資料夾。 <li>您應該會發現 WaAppAgent.exe 檔案已存在。<li> 在該檔案上按一下滑鼠右鍵，前往 [屬性]，然後選取 [詳細資料] 索引標籤。 [產品版本] 欄位應為 2.6.1198.718 或更高版本。 |N/A |

深入了解 [VM 代理程式](https://go.microsoft.com/fwLink/?LinkID=390493&clcid=0x409)和[如何安裝](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/)。

### <a name="backup-extension"></a>備份擴充功能
為了備份虛擬機器，「Azure 備份」服務會安裝 VM 代理程式的擴充功能。 Azure 備份服務無需使用者介入，即可順暢地升級和修補備份擴充功能。

如果 VM 正在執行，表示已安裝備份擴充功能。 執行中的 VM 也提供了取得應用程式一致復原點的絕佳機會。 不過，即使 VM 已關閉而無法安裝擴充功能 (亦稱為離線 VM)，「Azure 備份」服務仍會繼續備份 VM。 在此情況下，復原點將如前面所述為「當機時保持一致」  。

## <a name="questions"></a>有疑問嗎？
如果您有問題，或希望我們加入任何功能，請 [傳送意見反應給我們](http://aka.ms/azurebackup_feedback)。

## <a name="next-steps"></a>後續步驟
您現在已經備妥環境來備份您的 VM，您的下一個邏輯步驟是建立備份。 規劃文章會提供備份 VM 的詳細資訊。

* [備份虛擬機器](backup-azure-vms.md)
* [規劃 VM 備份基礎結構](backup-azure-vms-introduction.md)
* [管理虛擬機器備份](backup-azure-manage-vms.md)



<!--HONumber=Dec16_HO3-->


