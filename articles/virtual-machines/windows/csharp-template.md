---
title: "使用 C# 和 Resource Manager 範本部署 VM | Microsoft Docs"
description: "了解如何使用 C# 和 Resource Manager 範本來部署 Azure VM。"
services: virtual-machines-windows
documentationcenter: 
author: davidmu1
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: bfba66e8-c923-4df2-900a-0c2643b81240
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: davidmu
ms.translationtype: Human Translation
ms.sourcegitcommit: 61fd58063063d69e891d294e627ae40cb878d65b
ms.openlocfilehash: daff6ab4c0eaf17d1cb488f1c16aa111b6ed9a88
ms.contentlocale: zh-tw
ms.lasthandoff: 06/22/2017

---
<a id="deploy-an-azure-virtual-machine-using-c-and-a-resource-manager-template" class="xliff"></a>

# 利用 C# 和 Resource Manager 範本來部署 Azure 虛擬機器
本文說明如何使用 C# 來部署 Azure Resource Manager 範本。 此[範本](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json)會在具有單一子網路的新虛擬網路中，部署執行 Windows Server 的單一虛擬機器。

如需虛擬機器資源的詳細說明，請參閱 [Azure Resource Manager 範本中的虛擬機器 (英文)](template-description.md)。 如需有關範本中所有資源的詳細資訊，請參閱 [Azure Resource Manager 範本逐步解說](../../azure-resource-manager/resource-manager-template-walkthrough.md)。

執行這些步驟大約需要 10 分鐘的時間。

<a id="step-1-create-a-visual-studio-project" class="xliff"></a>

## 步驟 1：建立 Visual Studio 專案

在此步驟中，您會確定是否已安裝 Visual Studio，並建立用來部署此範本的主控台應用程式。

1. 如果您尚未安裝 [Visual Studio](https://www.visualstudio.com/)，請進行安裝。
2. 在 Visual Studio 中，按一下 [檔案] > [新增] > [專案]。
3. 在 [範本] > [Visual C#] 中，選取 [主控台應用程式 (.NET Framework)]，輸入專案的名稱和位置，然後按一下 [確定]。

<a id="step-2-install-libraries" class="xliff"></a>

## 步驟 2：安裝程式庫

NuGet 套件是安裝完成這些步驟所需之程式庫的最簡單方式。 您必須要有「Azure Resource Manager 程式庫」和 Azure Active Directory Authentication Library，才能建立資源。 若要在 Visual Studio 中取得這些程式庫，請執行下列步驟：

1. 在 [方案總管] 中，於專案名稱上按一下滑鼠右鍵、按一下 [管理解決方案的 NuGet 封裝...]，然後按一下 [瀏覽]。
2. 在搜尋方塊中輸入「Microsoft.IdentityModel.Clients.ActiveDirectory」，選取您的專案，按一下 [安裝]，然後依照指示安裝封裝。
3. 在頁面的頂端，選取 [包含發行前版本] 。 在搜尋方塊中輸入 *Microsoft.Azure.Management.ResourceManager*，按一下 [安裝]，然後依照指示操作來安裝套件。

您現在已經準備就緒，可以開始使用程式庫建立您的應用程式。

<a id="step-3-create-credentials-used-to-authenticate-requests" class="xliff"></a>

## 步驟 3：建立用來驗證要求的認證

在您開始此步驟之前，請確定您可以存取 [Active Directory 服務主體](../../resource-group-authenticate-service-principal.md)。 從服務主體中，您會取得向 Azure Resource Manager 驗證要求的權杖。

1. 開啟您建立之專案的 Program.cs 檔案，然後將這些 using 陳述式新增至檔案頂端的現有陳述式：

    ```
    using Microsoft.Azure;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.ResourceManager.Models;
    using Microsoft.Rest;
    using System.IO;
    ```

2. 將下列方法新增至 Program 類別，以取得建立認證所需的權杖：

    ```
    private static async Task<AuthenticationResult> GetAccessTokenAsync()
    {
      var cc = new ClientCredential("client-id", "client-secret");
      var context = new AuthenticationContext("https://login.microsoftonline.com/tenant-id");
      var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
      if (token == null)
      {
        throw new InvalidOperationException("Could not get the token.");
      } 
      return token;
    }
    ```

    取代下列值：
    
    - 將 *client-id* 取代成 Azure Active Directory 應用程式的識別碼。 您可以在 AD 應用程式的 [屬性] 刀鋒視窗上找到此識別碼。 若要在 Azure 入口網站中尋找您的 AD 應用程式，請按一下資源功能表中的 [Azure Active Directory]，然後按一下 [應用程式註冊]。
    - 將 *client-secret* 取代成 AD 應用程式的存取金鑰。 您可以在 AD 應用程式的 [屬性] 刀鋒視窗上找到此識別碼。
    - 將 *tenant-id* 取代成訂用帳戶的租用戶識別碼。 您可以在 Azure 入口網站中 Azure Active Directory 的 [屬性] 刀鋒視窗上找到租用戶識別碼。 其標示為 [目錄識別碼]。

3. 若要呼叫您剛才新增的方法，請將下列程式碼新增至 Main 方法：

    ```
    var token = GetAccessTokenAsync();
    var credential = new TokenCredentials(token.Result.AccessToken);
    ```

4. 儲存 Program.cs 檔案。

<a id="step-4-create-a-resource-group" class="xliff"></a>

## 步驟 4：建立資源群組

雖然您可以從範本建立資源群組，但是您從資源庫使用的範本並不會建立資源群組。 在此步驟中，您會新增程式碼來建立資源群組。

1. 若要指定應用程式的值，請將變數新增至 Program 類別的 Main 方法：

    ```
    var groupName = "myResourceGroup";
    var subscriptionId = "subsciptionId";
    var deploymentName = "deploymentName";
    var location = "location";
    ```

    取代下列值：
    
    - 將 *myResourceGroup* 取代成所要建立之資源群組的名稱。
    - 將 *subscriptionId* 取代成您的訂用帳戶 ID。 您可以在 Azure 入口網站的 [訂用帳戶] 刀鋒視窗上找到訂用帳戶 ID。
    - 將 *deploymentName* 取代成部署名稱。
    - 將 *location* 取代成您要建立資源的 [Azure 區域](https://azure.microsoft.com/regions/)。

2. 若要建立資源群組，請將下列方法新增至 Program 類別：

    ```
    public static async Task<ResourceGroup> CreateResourceGroupAsync(
      TokenCredentials credential,
      string groupName,
      string subscriptionId,
      string location)
    {
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };

      Console.WriteLine("Creating the resource group...");
      var resourceGroup = new ResourceGroup { Location = location };
      return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(
        groupName, 
        resourceGroup);
    }
    ```

3. 若要呼叫您剛才新增的方法，請將下列程式碼新增至 Main 方法：

    ```
    var rgResult = CreateResourceGroupAsync(
      credential,
      groupName,
      subscriptionId,
      location);
    Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

<a id="step-5-create-a-parameters-file" class="xliff"></a>

## 步驟 5︰建立參數檔案

若要為範本中所定義的資源參數指定值，您需建立包含這些值的參數檔案。 當您部署此範本時，就會使用這些參數檔案。 您從資源庫使用的範本會預期 *adminUserName*、*adminPassword* 及 *dnsLabelPrefix* 參數要有值。

在 Visual Studio 中，執行下列操作：

1. 在 [方案總管] 中，以滑鼠右鍵按一下專案名稱，然後按一下 [新增] > [新增項目]。
2. 按一下 [Web]、選取 [JSON 檔案]、輸入 *Parameters.json* 作為名稱，然後按一下 [新增]。
3. 開啟 Parameters.json 檔案，然後新增以下 JSON 內容：

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adminUserName": { "value": "mytestacct1" },
        "adminPassword": { "value": "mytestpass1" },
        "dnsLabelPrefix": { "value": "mydns1" }
      }
    }
    ```

    以可在您的環境中運作的值取代參數值。

4. 儲存 Parameters.json 檔案。

<a id="step-6-deploy-a-template" class="xliff"></a>

## 步驟 6：部署範本

在此範例中，您會從 Azure 範本庫部署範本，然後從您建立的本機檔案提供參數值給該範本。 

1. 若要部署範本，請將下列方法新增至 Program 類別：

    ```
    public static async Task<DeploymentExtended> CreateTemplateDeploymentAsync(
      TokenCredentials credential,
      string groupName,
      string deploymentName,
      string subscriptionId)
    {
    
      var resourceManagementClient = new ResourceManagementClient(credential)
        { SubscriptionId = subscriptionId };

      Console.WriteLine("Creating the template deployment...");
      var deployment = new Deployment();
      deployment.Properties = new DeploymentProperties
        {
          Mode = DeploymentMode.Incremental,
          TemplateLink = new TemplateLink("https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json"),
          Parameters = File.ReadAllText("..\\..\\Parameters.json")
        };
      
      return await resourceManagementClient.Deployments.CreateOrUpdateAsync(
        groupName,
        deploymentName,
        deployment
      );
    }
    ```

    您也可以從本機資料夾部署範本，方法是使用 Template 屬性，而不使用 TemplateLink 屬性。

2. 若要呼叫您剛才新增的方法，請將下列程式碼新增至 Main 方法：

    ```
    var dpResult = CreateTemplateDeploymentAsync(
      credential,
      groupName,
      deploymentName,
      subscriptionId);
    Console.WriteLine(dpResult.Result.Properties.ProvisioningState);
    Console.ReadLine();
    ```

<a id="step-7-delete-the-resources" class="xliff"></a>

## 步驟 7：刪除資源

由於您需要為在 Azure 中使用的資源付費，因此刪除不再需要的資源一律是理想的做法。 您不需要從資源群組個別刪除每個資源。 刪除資源群組，其所有資源都將會自動刪除。

1. 若要刪除資源群組，請將下列方法新增至 Program 類別：

   ```
   public static async void DeleteResourceGroupAsync(
     TokenCredentials credential,
     string groupName,
     string subscriptionId)
   {
     Console.WriteLine("Deleting resource group...");
     var resourceManagementClient = new ResourceManagementClient(credential)
       { SubscriptionId = subscriptionId };
     await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
   }
   ```

2. 若要呼叫您剛才新增的方法，請將下列程式碼新增至 Main 方法：

   ```
   DeleteResourceGroupAsync(
     credential,
     groupName,
     subscriptionId);
   Console.ReadLine();
   ```

<a id="step-8-run-the-console-application" class="xliff"></a>

## 步驟 8：執行主控台應用程式

此主控台應用程式從開始到完成的完整執行應該需要五分鐘左右。 

1. 若要執行主控台應用程式，按一下 Visual Studio 中的 [啟動]  ，然後以您用於訂用帳戶的同一個認證，登入 Azure AD。

2. 在出現 [成功] 狀態之後，按 **Enter**。 

    在 Azure 入口網站中您資源群組的 [概觀] 刀鋒視窗上，您應該也會在 [部署] 底下看到 [1 個成功]。

3. 在您按 **Enter** 以開始刪除資源之前，可以先花幾分鐘的時間來確認 Azure 入口網站中的資源建立情況。 請按一下部署狀態來查看該項部署的相關資訊。

<a id="next-steps" class="xliff"></a>

## 後續步驟
* 如果部署有問題，下一個步驟就是查看[使用 Azure Resource Manager 針對常見的 Azure 部署錯誤進行疑難排解](../../resource-manager-common-deployment-errors.md)。
* 檢閱[使用 C# 來部署 Azure 虛擬機器](csharp.md)，以了解如何部署虛擬機器及支援它的資源。

