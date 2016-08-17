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

# 使用 Windows VM 的自訂指令碼擴充功能搭配 Azure Resource Manager 範本

[AZURE.INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## Windows VM 的範本範例

在範本的資源區段中定義下列資源

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

在上述範例中，以您自己的設定取代檔案的 URL 和檔案名稱。編寫範本之後，您可以使用 Azure PowerShell 部署它。

在許多情況下，客戶會想要將指令碼 URL 和參數保持為私用。若要達到此目的，只要將指令碼 URL 保持為私用，使得只有使用儲存體帳戶名稱和金鑰 (以受保護的設定形式傳送) 才能存取它即可。此外，提供指令碼參數時，也可以使用 1.7 版或更新版本的 Windows 自訂指令碼擴充功能，以受保護的設定形式提供。

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
如需有關最新版自訂指令碼擴充功能之結構描述的資訊，請參閱[這裡](virtual-machines-windows-extensions-configuration-samples.md)的文件

如需在 VM 上使用自訂指令碼擴充功能來設定應用程式的完整範例，請參閱以下範例。

* [Windows VM 上的自訂指令碼擴充功能](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

<!---HONumber=AcomDC_0803_2016-->