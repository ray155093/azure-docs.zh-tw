### <a name="app-service-plan"></a>App Service 方案
建立主控 Web 應用程式的服務方案。 您可以透過 **hostingPlanName** 參數提供方案名稱。 方案的位置與用於資源群組的位置相同。 定價層和背景工作大小指定於 **sku** 和 **workerSize** 參數

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('hostingPlanName')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "[parameters('sku')]",
        "capacity": "[parameters('workerSize')]"
      },
      "properties": {
        "name": "[parameters('hostingPlanName')]"
      }
    },



<!--HONumber=Nov16_HO3-->


