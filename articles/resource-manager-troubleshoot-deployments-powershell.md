<properties
   pageTitle="使用 PowerShell 來檢視部署作業 | Microsoft Azure"
   description="描述如何使用 Azure PowerShell 來偵測源自「資源管理員」部署的問題。"
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/14/2016"
   ms.author="tomfitz"/>

# 使用 Azure PowerShell 來檢視部署作業

> [AZURE.SELECTOR]
- [入口網站](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure CLI](resource-manager-troubleshoot-deployments-cli.md)
- [REST API](resource-manager-troubleshoot-deployments-rest.md)

您可以透過 Azure PowerShell 檢視部署的作業。當您在部署期間收到錯誤時，您可能對於檢視作業最感興趣，所以本文著重於檢視失敗的作業。PowerShell 提供 Cmdlet，可讓您輕鬆地找到錯誤並判斷可能的修正方法。

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

部署之前先驗證您的範本和基礎結構，即可避免發生一些錯誤。您也可以記錄部署期間的要求和回應資訊，這在後續進行疑難排解時可能會有幫助。如需了解驗證，以及記錄要求和回應資訊，請參閱[使用 Azure Resource Manager 範本部署資源群組](resource-group-template-deploy.md)。

## 使用部署作業進行疑難排解

1. 若要取得部署的整體狀態，請使用 **Get-AzureRmResourceGroupDeployment** 命令。您可以篩選結果，只取得失敗的部署。

        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
    以下列格式傳回失敗的部署︰
        
        DeploymentName          : Microsoft.Template
        ResourceGroupName       : ExampleGroup
        ProvisioningState       : Failed
        Timestamp               : 6/14/2016 9:55:21 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                    Name                Type                 Value
                    ===============     ===================  ==========
                    storageAccountName  String               tfstorage9855
                    adminUsername       String               tfadmin
                    adminPassword       SecureString
                    dnsNameforLBIP      String               dns
                    vmNamePrefix        String               myVM
                    imagePublisher      String               MicrosoftWindowsServer
                    imageOffer          String               WindowsServer
                    imageSKU            String               2012-R2-Datacenter
                    lbName              String               myLB
                    nicNamePrefix       String               nic
                    publicIPAddressName String               myPublicIP
                    vnetName            String               myVNET
                    vmSize              String               Standard_D1

        Outputs                 :
        DeploymentDebugLogLevel :

2. 每個部署通常由多個作業所組成，而每個作業代表部署程序中的一個步驟。若要探索部署有何問題，您通常需要查看有關部署作業的詳細資訊。您可以利用 **Get-AzureRmResourceGroupDeploymentOperation** 查看作業的狀態。

        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName Microsoft.Template
        
    以下列格式傳回多項作業︰
        
        Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
        OperationId    : A3EB2DA598E0A780
        Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                         duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                         serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
        PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                         serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}

3. 若要取得有關失敗作業的詳細資訊，請擷取具有 [失敗] 狀態的作業屬性。

        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
        
    以下列格式傳回所有失敗的作業︰
        
        provisioningOperation : Create
        provisioningState     : Failed
        timestamp             : 2016-06-14T21:54:55.1468068Z
        duration              : PT3.1449887S
        trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
        serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
        statusCode            : BadRequest
        statusMessage         : @{error=}
        targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                                Microsoft.Network/publicIPAddresses/myPublicIP;
                                resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}

    請注意此作業的追蹤識別碼。您將在下一個步驟中使用該識別碼，以將重點放在特定的作業。

4. 若要取得特定失敗作業的狀態訊息，請使用下列命令︰

        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
        
    它會傳回：
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}

## 使用稽核記錄檔進行疑難排解

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

若要查看部署相關錯誤，請使用下列步驟 ︰

1. 若要擷取記錄檔項目，請執行 **Get-AzureRmLog** 命令。您可以使用 **ResourceGroup** 和 **Status** 參數，只傳回單一資源群組失敗的事件。如果未指定開始和結束時間，則會傳回最後一個小時的項目。例如，若要擷取過去一小時失敗的作業，請執行︰

        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    您可以指定特定的時間範圍。在下一個範例中，我們將尋找最後 1 天失敗的動作。

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -Status Failed
      
    或者，您可以針對失敗的動作設定確切的開始和結束時間︰

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. 如果這個命令傳回太多項目和屬性，您可以擷取 **Properties** 屬性以專注於進行稽核。我們也將加入 **DetailedOutput** 參數，以顯示錯誤訊息。

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -DetailedOutput).Properties
        
    以下列格式傳回記錄檔項目的內容︰
        
        Content
        -------
        {} 
        {[statusCode, BadRequest], [statusMessage, {"error":{"code":"DnsRecordInUse","message":"DNS record dns.westus.clouda...
        {[statusCode, BadRequest], [serviceRequestId, a426f689-5d5a-448d-a2f0-9784d14c900a], [statusMessage, {"error":{"code...

3. 根據這些結果，讓我們將焦點放在第二個元素。您可藉由查看該項目的狀態訊息，進一步精簡結果。

        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput -StartTime (Get-Date).AddDays(-1)).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
        
    它會傳回：
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}



## 後續步驟

- 如需解決特定部署錯誤的說明，請參閱[針對使用 Azure Resource Manager 將資源部署至 Azure 時常見的錯誤進行疑難排解](resource-manager-common-deployment-errors.md)。
- 若要了解如何使用稽核記錄檔來監視其他類型的動作，請參閱[使用 Resource Manager 來稽核作業](resource-group-audit.md)。
- 若要在執行之前驗證您的部署，請參閱[使用 Azure Resource Manager 範本部署資源群組](resource-group-template-deploy.md)。

<!---HONumber=AcomDC_0622_2016-->