# 概觀
## [什麼是 Resource Manager？](resource-group-overview.md)
## [支援的服務](resource-manager-supported-services.md)
## [Resource Manager 與傳統部署](resource-manager-deployment-model.md)
## [訂用帳戶治理](resource-manager-subscription-governance.md)
## [受管理的應用程式](managed-application-overview.md)

# 開始使用
## [匯出範本](resource-manager-export-template.md)
## [建立您的第一個範本](resource-manager-create-first-template.md)
## [搭配 Resource Manager 使用 Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

# 範例
## PowerShell
### [部署範本](resource-manager-samples-powershell-deploy.md)

## Azure CLI
### [部署範本](resource-manager-samples-cli-deploy.md)

# 作法
## 建立範本
### [範本的最佳做法](resource-manager-template-best-practices.md)
### [範本區段](resource-group-authoring-templates.md)
### [連結至其他範本](resource-group-linked-templates.md)
### [定義資源間的相依性](resource-group-define-dependencies.md)
### [建立多個執行個體](resource-group-create-multiple.md)
### [設定位置](resource-manager-template-location.md)
### [指派標記](resource-manager-template-tags.md)
### [設定子資源名稱與類型](resource-manager-template-child-resource.md)
### [更新資源](resource-manager-update.md)
### [使用參數的物件](resource-manager-objects-as-parameters.md)
### [在連結的範本間共用狀態](best-practices-resource-manager-state.md)
### [設計範本的模式](best-practices-resource-manager-design-templates.md)

## 部署
### PowerShell
#### [部署範本](resource-group-template-deploy.md)
#### [使用 SAS 權杖部署私人範本](resource-manager-powershell-sas-token.md)
#### [匯出範本並重新部署](resource-manager-export-template-powershell.md)
### Azure CLI
#### [部署範本](resource-group-template-deploy-cli.md)
#### [使用 SAS 權杖部署私人範本](resource-manager-cli-sas-token.md)
#### [匯出範本並重新部署](resource-manager-export-template-cli.md)
### [入口網站](resource-group-template-deploy-portal.md)
### [REST API](resource-group-template-deploy-rest.md)
### [跨資源群組部署](resource-manager-cross-resource-group-deployment.md)
### [持續與 Visual Studio Team Services 整合](../vs-azure-tools-resource-groups-ci-in-vsts.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [在部署期間傳遞安全值](resource-manager-keyvault-parameter.md)

## 管理
### [PowerShell](powershell-azure-resource-manager.md)
### [Azure CLI](xplat-cli-azure-resource-manager.md)
### [入口網站](resource-group-portal.md)
### [REST API](resource-manager-rest-api.md)
### [使用標籤來整理資源](resource-group-using-tags.md)
### [將資源移至新群組或訂用帳戶](resource-group-move-resources.md)
### [治理範例](resource-manager-subscription-examples.md)

## 控制存取權
### 建立服務主體
#### [PowerShell](resource-group-authenticate-service-principal.md)
#### [Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
#### [Azure CLI 1.0](resource-group-authenticate-service-principal-cli.md)
#### [入口網站](resource-group-create-service-principal-portal.md)
### [驗證 API 以存取訂閱帳戶](resource-manager-api-authentication.md)
### [鎖定資源](resource-group-lock-resources.md)

## 設定資源原則
### [資源原則是什麼？](resource-manager-policy.md)
### [入口網站原則指派](resource-manager-policy-portal.md)
### [指令碼原則指派](resource-manager-policy-create-assign.md)
### [資源標籤原則](resource-manager-policy-tags.md)
### [儲存體原則](resource-manager-policy-storage.md)
### [Linux VM 原則](../virtual-machines/linux/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)
### [Windows VM 原則](../virtual-machines/windows/policy.md?toc=%2fazure%2fazure-resource-manager%2ftoc.json)

## 使用受管理的應用程式
### [發佈受管理的應用程式](managed-application-publishing.md)
### [使用受管理的應用程式](managed-application-consumption.md)
### [建立 UI 定義](managed-application-createuidefinition-overview.md)

## 稽核
### [檢視活動記錄](resource-group-audit.md)
### [檢視部署作業](resource-manager-deployment-operations.md)

## 疑難排解
### [一般部署錯誤](resource-manager-common-deployment-errors.md)

# 參考
## [範本格式](/azure/templates/)
## [範本函式](resource-group-template-functions.md)
### [陣列和物件函式](resource-group-template-functions-array.md)
### [比較函式](resource-group-template-functions-comparison.md)
### [部署函式](resource-group-template-functions-deployment.md)
### [數值函式](resource-group-template-functions-numeric.md)
### [資源函式](resource-group-template-functions-resource.md)
### [字串函式](resource-group-template-functions-string.md)
## [UI 定義函式](managed-application-createuidefinition-functions.md)
## [UI 定義元素](managed-application-createuidefinition-elements.md)
### [Microsoft.Common.DropDown](managed-application-microsoft-common-dropdown.md)
### [Microsoft.Common.FileUpload](managed-application-microsoft-common-fileupload.md)
### [Microsoft.Common.OptionsGroup](managed-application-microsoft-common-optionsgroup.md)
### [Microsoft.Common.PasswordBox](managed-application-microsoft-common-passwordbox.md)
### [Microsoft.Common.Section](managed-application-microsoft-common-section.md)
### [Microsoft.Common.TextBox](managed-application-microsoft-common-textbox.md)
### [Microsoft.Compute.CredentialsCombo](managed-application-microsoft-compute-credentialscombo.md)
### [Microsoft.Compute.SizeSelector](managed-application-microsoft-compute-sizeselector.md)
### [Microsoft.Compute.UserNameTextBox](managed-application-microsoft-compute-usernametextbox.md)
### [Microsoft.Network.PublicIpAddressCombo](managed-application-microsoft-network-publicipaddresscombo.md)
### [Microsoft.Network.VirtualNetworkCombo](managed-application-microsoft-network-virtualnetworkcombo.md)
### [Microsoft.Storage.MultiStorageAccountCombo](managed-application-microsoft-storage-multistorageaccountcombo.md)
### [Microsoft.Storage.StorageAccountSelector](managed-application-microsoft-storage-storageaccountselector.md)
## [PowerShell](/powershell/module/azurerm.resources)
## [Azure CLI](/cli/azure/resource)
## [.NET](/dotnet/api/microsoft.azure.management.resourcemanager)
## [Java](/java/api/com.microsoft.azure.management.resources)
## [Python](http://azure-sdk-for-python.readthedocs.io/en/latest/resourcemanagement.html)
## [REST](/rest/api/resources/)

# 資源
## [節流要求](resource-manager-request-limits.md)
## [追蹤非同步作業](resource-manager-async-operations.md)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-resource-manager)
## [影片](https://azure.microsoft.com/documentation/videos/index/?services=azure-resource-manager)
## [服務更新](https://azure.microsoft.com/updates/?product=azure-resource-manager)
