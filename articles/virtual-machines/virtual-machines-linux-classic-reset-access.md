---
title: 從 CLI 重設 Linux VM 密碼和 SSH 金鑰 | Microsoft Docs
description: 如何從 Azure 命令列介面 (CLI) 使用 VMAccess 擴充功能來重設 Linux VM 密碼或 SSH 金鑰、修正 SSH 組態及檢查磁碟一致性
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: timlt
editor: ''
tags: azure-service-management

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 06/14/2016
ms.author: cynthn

---
# 如何使用 VMAccess 擴充功能重設 Linux VM 密碼或 SSH 金鑰、修正 SSH 組態，和檢查磁碟一致性
如果您因為忘記密碼、安全殼層 (SSH) 金鑰不正確或 SSH 組態有問題而無法連線到 Linux 虛擬機器，請使用 VMAccessForLinux 擴充功能搭配 Azure CLI 來重設密碼或 SSH 金鑰、修正 SSH 組態和檢查磁碟一致性。

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

了解如何[使用 Resource Manager 模型執行這些步驟](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)。

使用 Azure CLI，您就能從命令列介面 (Bash、終端機、命令提示字元) 中使用 **azure vm extension set** 命令來存取命令。如需詳細的擴充功能使用方式，請執行 **azure help vm extension set**。

使用 Azure CLI，您可以執行下列工作：

* [重設密碼](#pwresetcli)
* [重設 SSH 金鑰](#sshkeyresetcli)
* [重設密碼和 SSH 金鑰](#resetbothcli)
* [建立新的 sudo 使用者帳戶](#createnewsudocli)
* [重設 SSH 組態](#sshconfigresetcli)
* [刪除使用者](#deletecli)
* [顯示 VMAccess 延伸模組的狀態](#statuscli)
* [檢查新增的磁碟的一致性](#checkdisk)
* [修復 Linux VM 上新增的磁碟](#repairdisk)

## 必要條件
您將需要執行下列動作：

* 您需要[安裝 Azure CLI](../xplat-cli-install.md) 並[連接訂用帳戶](../xplat-cli-connect.md)，才能使用與帳戶相關的 Azure 資源。
* 針對傳統部署模型設定正確的模式，方法是在命令提示字元中輸入以下命令：
  
        azure config mode asm
* 具備新的密碼或一組 SSH 金鑰 (如果要重設其中一項)。如果您想要重設 SSH 組態，則不需要這些。

## <a name="pwresetcli"></a>重設密碼
1. 使用下列程式行建立名為 PrivateConf.json 的檔案。使用您自己的資訊取代括號和 &#60;placeholder&#62; 的值。
   
        {
        "username":"<currentusername>",
        "password":"<newpassword>",
        "expiration":"<2016-01-01>"
        }
2. 執行這個命令，以您虛擬機器的名稱來取代 &#60;vm-name&#62;。
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* –-private-config-path PrivateConf.json

## <a name="sshkeyresetcli"></a>重設 SSH 金鑰
1. 使用下列內容建立名為 PrivateConf.json 的檔案。使用您自己的資訊取代括號和 &#60;placeholder&#62; 的值。
   
        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>"
        }
2. 執行這個命令，以您虛擬機器的名稱來取代 &#60;vm-name&#62;。
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="resetbothcli"></a>同時重設密碼和 SSH 金鑰
1. 使用下列內容建立名為 PrivateConf.json 的檔案。使用您自己的資訊取代括號和 &#60;placeholder&#62; 的值。
   
        {
        "username":"<currentusername>",
        "ssh_key":"<contentofsshkey>",
        "password":"<newpassword>"
        }
2. 執行這個命令，以您虛擬機器的名稱來取代 &#60;vm-name&#62;。
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="createnewsudocli"></a>建立新的 sudo 使用者帳戶
如果您忘記使用者名稱，可以使用 VMAccess，利用 sudo 授權單位來建立一個新的帳戶。在此案例中，將不會修改現有的使用者名稱和密碼。

若要建立新的 sudo 使用者及密碼存取權限，請使用[重設密碼](#pwresetcli)中的指令碼，並指定新的使用者名稱。

若要建立新的 sudo 使用者及 SSH 金鑰存取權限，請使用[重設 SSH 金鑰](#sshkeyresetcli)中的指令碼，並指定新的使用者名稱。

您也可以使用[重設密碼和 SSH 金鑰](#resetbothcli)，來建立新的使用者以及密碼和 SSH 金鑰存取權限。

## <a name="sshconfigresetcli"></a>重設 SSH 組態
如果 SSH 組態處於不良的狀態，您可能也會無法存取 VM。您可以使用 VMAccess 延伸模組，將組態重設為其預設狀態。若要這樣做，只需將 "reset\_ssh" 金鑰設為 "True"。延伸模組將會重新啟動 SSH 伺服器、開啟 VM 上的 SSH 連接埠，然後將 SSH 組態重設為預設值。使用者帳戶 (名稱、密碼或 SSH 金鑰) 將不會變更。

> [!NOTE]
> 要重設的 SSH 組態檔位於 /etc/ssh/sshd\_config。
> 
> 

1. 使用此內容建立名為 PrivateConf.json 的檔案。
   
        {
        "reset_ssh":"True"
        }
2. 執行這個命令，以您虛擬機器的名稱來取代 &#60;vm-name&#62;。
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="deletecli"></a>刪除使用者
如果您想要刪除使用者帳戶而不直接登入 VM，可以使用這個指令碼。

1. 使用此內容建立名為 PrivateConf.json 的檔案，以要移除的使用者名稱來取代 &#60;usernametoremove&#62;。
   
        {
        "remove_user":"<usernametoremove>"
        }
2. 執行這個命令，以您虛擬機器的名稱來取代 &#60;vm-name&#62;。
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --private-config-path PrivateConf.json

## <a name="statuscli"></a>顯示 VMAccess 延伸模組的狀態
若要顯示 VMAccess 延伸模組的狀態，請執行下列命令。

        azure vm extension get

## <a name='checkdisk'<</a>檢查新增磁碟的一致性
若要在 Linux 虛擬機器中的所有磁碟上執行 fsck，您必須執行下列操作：

1. 使用此內容建立名為 PublicConf.json 的檔案。檢查磁碟以布林值決定是否要檢查附加至您的虛擬機器的磁碟。
   
        {   
        "check_disk": "true"
        }
2. 執行這個執行命令，以您的虛擬機器名稱來取代 &#60;vm-name&#62;。
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json 

## <a name='repairdisk'></a>修復磁碟
若要修復未掛接或發生掛接設定錯誤的磁碟，請使用 VMAccess 擴充功能來重設 Linux 虛擬機器上的掛接設定。使用您的磁碟名稱取代 &#60;yourdisk&#62;。

1. 使用此內容建立名為 PublicConf.json 的檔案。
   
        {
        "repair_disk":"true",
        "disk_name":"<yourdisk>"
        }
2. 執行這個執行命令，以您的虛擬機器名稱來取代 &#60;vm-name&#62;。
   
        azure vm extension set <vm-name> VMAccessForLinux Microsoft.OSTCExtensions 1.* --public-config-path PublicConf.json

## 後續步驟
* 如果您想要使用 Azure PowerShell Cmdlet 或 Azure Resource Manager 範本來重設密碼或 SSH 金鑰、修正 SSH 組態及檢查磁碟一致性，請參閱 [GitHub 上的 VMAccess 擴充功能文件](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess)。
* 您也可以使用 [Azure 入口網站](https://portal.azure.com)來重設傳統部署模型中部署的 Linux VM 密碼或 SSH 金鑰。目前您無法使用入口網站來針對部署在 Resource Manager 部署模型內的 Linux VM 執行上述操作。
* 如需使用 Azure 虛擬機器 VM 擴充功能的詳細資訊，請參閱[有關虛擬機器擴充功能和功能](virtual-machines-linux-extensions-features.md)。

<!---HONumber=AcomDC_0629_2016-->