---
title: "移除伺服器與停用保護 | Microsoft Docs"
description: "本文說明如何取消註冊 Site Recovery 保存庫中的伺服器，並停用虛擬機器和實體伺服器的保護。"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: ef1f31d5-285b-4a0f-89b5-0123cd422d80
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 03/27/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: f57c88cbace41af233f542880c6199b3e278700e
ms.openlocfilehash: c8d893dbac1a4f6cb3f05f857e186bca155e5865
ms.lasthandoff: 01/05/2017


---

# <a name="remove-servers-and-disable-protection"></a>移除伺服器並停用保護

Azure Site Recovery 是一項有助於建立商務持續性和災害復原 (BCDR) 策略的服務。 該服務可協調虛擬機器和實體伺服器的複寫、容錯移轉和復原。 機器可以複寫至 Azure，或次要的內部部署資料中心。 如需快速概觀，請參閱 [什麼是 Azure Site Recovery？](site-recovery-overview.md)

本文說明如何在 Azure 入口網站中，將「復原服務」保存庫中的伺服器取消註冊，以及如何針對由 Site Recovery 保護的機器停用保護。

在這篇文章下方或 [Azure Recovery Services Forum (Azure 復原服務論壇)](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)中張貼意見或問題。

## <a name="unregister-a-connected-configuration-server"></a>將已連線的組態伺服器取消註冊

如果您將 VMware VM 或 Windows/Linux 實體伺服器複寫到 Azure，您可以將已連線的組態伺服器從保存庫取消註冊，方法如下：

1. 停用機器保護。 在 [受保護的項目] > [已複寫的項目] 中，以滑鼠右鍵按一下機器 > [刪除]。
2. 取消關聯任何原則。 在 [Site Recovery 基礎結構] > [適用於 VMWare 與實體機器] > [複寫原則] 中，按兩下相關聯的原則。 以滑鼠右鍵按一下組態伺服器 > [取消關聯]。
3. 移除任何額外的內部部署處理伺服器或主要目標伺服器。 在 [Site Recovery 基礎結構] > [適用於 VMWare 與 Physical 機器] > [組態伺服器] 中，以滑鼠右鍵按一下伺服器 > [刪除]。
4. 刪除組態伺服器。
5. 將在主要目標伺服器 (個別的伺服器，或在組態伺服器上執行的伺服器) 上執行的行動服務手動解除安裝。
6. 將任何額外的處理伺服器解除安裝。
7. 將組態伺服器解除安裝。
8. 在組態伺服器上，將 Site Recovery 安裝的 MySQL 執行個體解除安裝。
9. 在組態伺服器的登錄中，刪除金鑰 ``HKEY_LOCAL_MACHINE\Software\Microsoft\Azure Site Recovery``。

## <a name="unregister-a-unconnected-configuration-server"></a>將未連線的組態伺服器取消註冊

如果您將 VMware VM 或 Windows/Linux 實體伺服器複寫到 Azure，您可以將未連線的組態伺服器從保存庫取消註冊，方法如下：

1. 停用機器保護。 在 [受保護的項目] > [已複寫的項目] 中，以滑鼠右鍵按一下機器 > [刪除]。 選取 [停止管理機器]。
2. 移除任何額外的內部部署處理伺服器或主要目標伺服器。 在 [Site Recovery 基礎結構] > [適用於 VMWare 與 Physical 機器] > [組態伺服器] 中，以滑鼠右鍵按一下伺服器 > [刪除]。
3. 刪除組態伺服器。
4. 將在主要目標伺服器 (個別的伺服器，或在組態伺服器上執行的伺服器) 上執行的行動服務手動解除安裝。
5. 將任何額外的處理伺服器解除安裝。
6. 將組態伺服器解除安裝。
7. 在組態伺服器上，將 Site Recovery 安裝的 MySQL 執行個體解除安裝。
8. 在組態伺服器的登錄中，刪除金鑰 ``HKEY_LOCAL_MACHINE\Software\Microsoft\Azure Site Recovery``。

## <a name="unregister-a-connected-vmm-server"></a>取消註冊已連線的 VMM 伺服器

根據最佳做法，建議您在連線到 Azure 時，取消註冊 VMM 伺服器。 這麼做可以確保適當地清除 VMM 伺服器 (以及其他已和雲端配對的 VMM 伺服器) 上的設定。 您只應在連線發生永久性問題時，才移除未連線的伺服器。 如果 VMM 伺服器未連線，您將需要手動執行指令碼來清除設定。

1. 在您想要移除的 VMM 伺服器上，停止在雲端複寫 VM。
2. 在您想要刪除的 VMM 伺服器上，刪除任何由雲端使用的網路對應。 在 [Site Recovery 基礎結構] > [適用於 System Center VMM] > [網路對應] 中，以滑鼠右鍵按一下網路對應 > [刪除]。
3. 將您想要移除的 VMM 伺服器上取消雲端和複寫原則的關聯。  在 [Site Recovery 基礎結構] > [適用於 System Center VMM] >  [複寫原則] 中，按兩下相關聯的原則。 以滑鼠右鍵按一下雲端 > [取消關聯]。
4. 刪除 VMM 伺服器或作用中的 VMM 節點。 在 [Site Recovery 基礎結構] > [適用於 System Center VMM] > [VMM 伺服器] 中，以滑鼠右鍵按一下伺服器 > [刪除]。
5. 在 VMM 伺服器上手動將提供者解除安裝。 如果您有叢集，請將它從所有節點中移除。
6. 如果您是複寫到 Azure，請將 Microsoft 復原服務代理程式從已刪除之雲端中的 Hyper-V 主機上手動移除。



### <a name="unregister-an-unconnected-vmm-server"></a>取消註冊未連線的 VMM 伺服器

1. 在您想要移除的 VMM 伺服器上，停止在雲端複寫 VM。
2. 在您想要刪除的 VMM 伺服器上，刪除任何由雲端使用的網路對應。 在 [Site Recovery 基礎結構] > [適用於 System Center VMM] > [網路對應] 中，以滑鼠右鍵按一下網路對應 > [刪除]。
3. 記下 VMM 伺服器的識別碼。
4. 將您想要移除的 VMM 伺服器上取消雲端和複寫原則的關聯。  在 [Site Recovery 基礎結構] > [適用於 System Center VMM] >  [複寫原則] 中，按兩下相關聯的原則。 以滑鼠右鍵按一下雲端 > [取消關聯]。
5. 刪除 VMM 伺服器或作用中的節點。 在 [Site Recovery 基礎結構] > [適用於 System Center VMM] > [VMM 伺服器] 中，以滑鼠右鍵按一下伺服器 > [刪除]。
6. 在 VMM 伺服器上下載並執行[清除指令碼](http://aka.ms/asr-cleanup-script-vmm)。 使用 [以系統管理員身分執行] 選項開啟 PowerShell，以變更預設 (LocalMachine) 範圍的執行原則。 在指令碼中，指定您想要移除的 VMM 伺服器的識別碼。 該指令碼會從伺服器移除註冊及雲端配對資訊。
5. 在包含已和您想要刪除之 VMM 伺服器上的雲端配對之雲端的任何其他 VMM 伺服器上執行清除指令碼。
6. 在已安裝提供者的任何其他被動 VMM 叢集節點上執行清除指令碼。
7. 在 VMM 伺服器上手動將提供者解除安裝。 如果您有叢集，請將它從所有節點中移除。
8. 如果您是複寫到 Azure，您可以將 Microsoft 復原服務代理程式從已刪除之雲端中的 Hyper-V 主機上移除。

## <a name="unregister-a-hyper-v-host-in-a-hyper-v-site"></a>取消註冊 Hyper-V 站台中的 Hyper-V 主機

未受 VMM 管理的 Hyper-V 主機會集合成 Hyper-V 站台。 移除 Hyper-V 站台中的主機，方法如下：

1. 針對主機上的 Hyper-V VM 停用複寫。
2. 取消關聯 Hyper-V 站台的原則。 在 [Site Recovery 基礎結構] > [適用於 Hyper-V 站台] >  [複寫原則] 中，按兩下相關聯的原則。 以滑鼠右鍵按一下站台 > [取消關聯]。
3. 刪除 Hyper-V 主機。 在 [Site Recovery 基礎結構] > [適用於 System Center VMM] > [Hyper-V 主機] 中，以滑鼠右鍵按一下伺服器 [刪除]。
4. 從 Hyper-V 站台移除所有主機之後，將 Hyper-V 站台刪除。 在 [Site Recovery 基礎結構] > [適用於 System Center VMM] > [Hyper-V 站台] 中，以滑鼠右鍵按一下站台 [刪除]。
5. 在每個移除的 Hyper-V 主機上執行下列指令碼。 該指令碼會清除伺服器上的設定，並從保存庫取消註冊該伺服器。


        `` pushd .
        try
        {
             $windowsIdentity=[System.Security.Principal.WindowsIdentity]::GetCurrent()
             $principal=new-object System.Security.Principal.WindowsPrincipal($windowsIdentity)
             $administrators=[System.Security.Principal.WindowsBuiltInRole]::Administrator
             $isAdmin=$principal.IsInRole($administrators)
             if (!$isAdmin)
             {
                "Please run the script as an administrator in elevated mode."
                $choice = Read-Host
                return;       
             }

            $error.Clear()    
            "This script will remove the old Azure Site Recovery Provider related properties. Do you want to continue (Y/N) ?"
            $choice =  Read-Host

            if (!($choice -eq 'Y' -or $choice -eq 'y'))
            {
            "Stopping cleanup."
            return;
            }

            $serviceName = "dra"
            $service = Get-Service -Name $serviceName
            if ($service.Status -eq "Running")
            {
                "Stopping the Azure Site Recovery service..."
                net stop $serviceName
            }

            $asrHivePath = "HKLM:\SOFTWARE\Microsoft\Azure Site Recovery"
            $registrationPath = $asrHivePath + '\Registration'
            $proxySettingsPath = $asrHivePath + '\ProxySettings'
            $draIdvalue = 'DraID'

            if (Test-Path $asrHivePath)
            {
                if (Test-Path $registrationPath)
                {
                    "Removing registration related registry keys."    
                    Remove-Item -Recurse -Path $registrationPath
                }

                if (Test-Path $proxySettingsPath)
            {
                    "Removing proxy settings"
                    Remove-Item -Recurse -Path $proxySettingsPath
                }

                $regNode = Get-ItemProperty -Path $asrHivePath
                if($regNode.DraID -ne $null)
                {            
                    "Removing DraId"
                    Remove-ItemProperty -Path $asrHivePath -Name $draIdValue
                }
                "Registry keys removed."
            }

            # First retrive all the certificates to be deleted
            $ASRcerts = Get-ChildItem -Path cert:\localmachine\my | where-object {$_.friendlyname.startswith('ASR_SRSAUTH_CERT_KEY_CONTAINER') -or $_.friendlyname.startswith('ASR_HYPER_V_HOST_CERT_KEY_CONTAINER')}
            # Open a cert store object
            $store = New-Object System.Security.Cryptography.X509Certificates.X509Store("My","LocalMachine")
            $store.Open('ReadWrite')
            # Delete the certs
            "Removing all related certificates"
            foreach ($cert in $ASRcerts)
            {
                $store.Remove($cert)
            }
        }catch
        {    
            [system.exception]
            Write-Host "Error occured" -ForegroundColor "Red"
            $error[0]
            Write-Host "FAILED" -ForegroundColor "Red"
        }
        popd``



## <a name="disable-protection-for-a-vmware-vm-or-physical-server"></a>停用 VMware VM 或實體伺服器的保護

1. 在 [受保護的項目] > [已複寫的項目] 中，以滑鼠右鍵按一下機器 > [刪除]。
2. 在 [移除機器] 中，選取下列其中一個選項：
    - **停用對機器的保護 (建議)**。 使用此選項以停止複寫機器。 系統會自動清除 Site Recovery 設定。 您只會在以下情況看到此選項：
        - **您已調整虛擬機器磁碟區的大小**—當您調整磁碟區大小時，虛擬機器會進入重大狀態。 選取此選項以停用保護，同時保留在 Azure 中的復原點。 當您再次啟用機器的保護時，調整過大小的磁碟區資料會傳輸至 Azure。
        - **您最近已執行容錯移轉**—在您執行容錯移轉進行環境測試之後，請選取此選項以再次開始保護內部部署機器。 它會停用每個虛擬機器，您接著必須再次啟用對它們的保護。 使用這項設定停用機器，不會影響 Azure 中的複本虛擬機器。 請勿從機器解除安裝行動服務。
    - [停止管理機器]。 如果選取此選項，則只會將機器從保存庫移除。 機器的內部部署保護設定不受影響。 若要移除機器上的設定並從 Azure 訂用帳戶移除機器，您需要將行動服務解除安裝以清除設定。

## <a name="disable-protection-for-a-hyper-v-vm-in-a-vmm-cloud"></a>停用 VMM 雲端中 Hyper-V VM 的保護

1. 在 [受保護的項目] > [已複寫的項目] 中，以滑鼠右鍵按一下機器 > [刪除]。
2. 在 [移除機器] 中，選取下列其中一個選項：

    - **停用對機器的保護 (建議)**。 使用此選項以停止複寫機器。 系統會自動清除 Site Recovery 設定。
    - [停止管理機器]。 如果選取此選項，則只會將機器從保存庫移除。 機器的內部部署保護設定不受影響。 若要移除機器上的設定並從 Azure 訂用帳戶移除機器，您需要按照以下指示手動清除設定。 請注意，如果您選擇刪除虛擬機器及其硬碟，將會從目標位置移除它們。

### <a name="clean-up-protection-settings---replication-to-a-secondary-vmm-site"></a>清除保護設定 - 複寫至次要 VMM 站台

如果您選取 [停止管理機器] 且複寫至次要站台，請在主要伺服器上執行此指令碼，以清除主要虛擬機器的設定。 在 VMM 主控台中，按一下 [PowerShell] 按鈕來開啟 VMM PowerShell 主控台。 將 SQLVM1 取代成您的虛擬機器的名稱。

         ``$vm = get-scvirtualmachine -Name "SQLVM1"
         Set-SCVirtualMachine -VM $vm -ClearDRProtection``
2. 在次要 VMM 伺服器上，執行此指令碼來清除次要虛擬機器的設定：

        ``$vm = get-scvirtualmachine -Name "SQLVM1"
        Remove-SCVirtualMachine -VM $vm -Force``
3. 在次要 VMM 伺服器上，重新整理 Hyper-V 主機伺服器上的虛擬機器，以便再次於 VMM 主控台中偵測次要 VM。
4. 上述步驟會清除 VMM 伺服器上的複寫設定。 如果您想要停止複寫虛擬機器，請在主要和次要 VM 上執行以下指令碼。 將 SQLVM1 取代成您的虛擬機器的名稱。

        ``Remove-VMReplication –VMName “SQLVM1”``

### <a name="clean-up-protection-settings---replication-to-azure"></a>清除保護設定 - 複寫至 Azure

1. 如果您選取 [停止管理機器] 且複寫到 Azure，請從 VMM 主控台使用 PowerShell，在來源 VMM 伺服器上執行此指令碼。
        ``$vm = get-scvirtualmachine -Name "SQLVM1"
        Set-SCVirtualMachine -VM $vm -ClearDRProtection``
2. 上述步驟會清除 VMM 伺服器上的複寫設定。 若要停止複寫在 Hyper-V 主機伺服器上執行的虛擬機器，請執行此指令碼。 將 SQLVM1 取代成您的虛擬機器的名稱，並將 host01.contoso.com 取代成 Hyper-V 主機伺服器的名稱。

        ``$vmName = "SQLVM1"
        $hostName  = "host01.contoso.com"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'" -computername $hostName
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"  -computername $hostName
        $replicationService.RemoveReplicationRelationship($vm.__PATH)``


## <a name="disable-protection-for-a-hyper-v-vm-in-a-hyper-v-site"></a>停用對 Hyper-V 站台中 Hyper-V VM 的保護

如果您是在沒有 VMM 伺服器的情況下將 Hyper-V VM 複寫到 Azure，請使用此程序。

1. 在 [受保護的項目] > [已複寫的項目] 中，以滑鼠右鍵按一下機器 > [刪除]。
2. 在 [移除機器] 中，您可以選取下列選項：

   - **停用對機器的保護 (建議)**。 使用此選項以停止複寫機器。 系統會自動清除 Site Recovery 設定。
   - [停止管理機器]。 如果選取此選項，則只會將機器從保存庫移除。 機器的內部部署保護設定不受影響。 若要移除機器上的設定並從 Azure 訂用帳戶移除虛擬機器，您需要手動清除設定。 如果您選擇刪除虛擬機器及其硬碟，將會從目標位置移除它們。
3. 如果您選取 [停止管理機器]，請在來源 Hyper-V 主機伺服器上執行此指令碼，以移除虛擬機器的複寫。 將 SQLVM1 取代成您的虛擬機器的名稱。

        $vmName = "SQLVM1"
        $vm = Get-WmiObject -Namespace "root\virtualization\v2" -Query "Select * From Msvm_ComputerSystem Where ElementName = '$vmName'"
        $replicationService = Get-WmiObject -Namespace "root\virtualization\v2"  -Query "Select * From Msvm_ReplicationService"
        $replicationService.RemoveReplicationRelationship($vm.__PATH)

