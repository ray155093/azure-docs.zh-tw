<properties
   pageTitle="Windows VM 上使用範本的自訂指令碼 | Microsoft Azure"
   description="使用自訂指令碼擴充功能搭配資源管理員範本，將 Windows VM 組態工作自動化"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="03/29/2016"
   ms.author="kundanap"/>

# 與 Azure Resource Manager 範本搭配的 Windows VM 自訂指令碼擴充功能

[AZURE.INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## Windows VM 的範本範例

在範本的「資源」區段中定義下列資源。

       {
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "MyCustomScriptExtension",
       "apiVersion": "2015-05-01-preview",
       "location": "[parameters('location')]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.Compute",
           "type": "CustomScriptExtension",
           "typeHandlerVersion": "1.7",
           "autoUpgradeMinorVersion":true,
           "settings": {
               "fileUris": [
               "http://Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
           ],
           "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
         }
       }
     }

在上述範例中，請以您自己的設定取代檔案 URL 和檔案名稱。編寫範本之後，您可以使用 Azure PowerShell 來部署它。

如果您想要將指令碼 URL 和參數保持為私用，您可以將指令碼 URL 設定為 **private**。如果將指令碼 URL 設定為 **private**，將只有使用儲存體帳戶名稱和金鑰 (以受保護的設定形式傳送) 才能存取它。使用 1.7 版或更新版的「自訂指令碼」擴充功能時，也可以用受保護的設定形式來提供指令碼參數。

## 搭配受保護設定的 Windows VM 範本範例

        {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "settings": {
        "fileUris": [
        "http: //Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
        ]
        },
        "protectedSettings": {
        "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -start.ps1",
        "storageAccountName": "yourStorageAccountName",
        "storageAccountKey": "yourStorageAccountKey"
        }
        }
如需有關最新版「自訂指令碼」擴充功能之結構描述的資訊，請參閱 [Azure Windows VM 擴充功能組態範例](virtual-machines-windows-extensions-configuration-samples.md)。

如需有關使用「自訂指令碼」擴充功能的 VM 上應用程式組態範例，請參閱 [Windows VM 上的自訂指令碼擴充功能](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)。

<!---HONumber=AcomDC_0824_2016-->