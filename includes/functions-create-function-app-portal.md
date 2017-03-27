
1. 移至 [Azure 入口網站](https://portal.azure.com) ，然後以您的 Azure 帳戶登入。

2. 按一下 [+新增]  >  [計算]  >  [函式應用程式]，選取您的 [訂用帳戶]，輸入可識別您函式應用程式的唯一 [應用程式名稱]，然後指定下列設定：
   
   * **[資源群組](../articles/azure-resource-manager/resource-group-overview.md)**：選取 [新建] 並為您的新資源群組輸入名稱。 
   * **[主控方案](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)**可以是下列其中一個方案： 
     * **取用方案**：Azure Functions 的預設方案類型。 當您選擇取用方案時，您也必須選擇 [位置] 並設定 [記憶體配置] (以 MB 為單位)。 如需記憶體配置如何影響成本的詳細資訊，請參閱 [Azure Functions 定價](https://azure.microsoft.com/pricing/details/functions/)。 
     * **App Service 方案**：傳統 App Service 方案需要您建立一個 (或選取現有的)「App Service 方案/位置」。 這些設定決定與您的應用程式相關聯的[位置、功能、成本和計算資源](https://azure.microsoft.com/pricing/details/app-service/)。  
   * **儲存體帳戶**：每個函數應用程式都需要一個儲存體帳戶。 您可以選擇現有的儲存體帳戶或是[建立儲存體帳戶](../articles/storage/storage-create-storage-account.md#create-a-storage-account)。 
     
    ![在 Azure 入口網站中建立函式應用程式](./media/functions-create-function-app-portal/function-app-create-flow.png)

3. 按一下 [建立] 以佈建並部署新的函數應用程式。  