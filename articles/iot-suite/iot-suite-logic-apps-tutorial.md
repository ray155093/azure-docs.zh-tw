<properties
  pageTitle="Azure IoT 套件和 Logic Apps | Microsoft Azure"
  description="如何將 Logic Apps 連結至 Azure IoT 套件以執行商務程序的教學課程。"
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="05/20/2016"
  ms.author="araguila"/>
  
# 教學課程：將邏輯應用程式連接至 Azure IoT 套件遠端監視預先設定解決方案

[Microsoft Azure IoT 套件][lnk-internetofthings] 遠端監視預先設定解決方案以一套端對端功能集示範 IoT 方案，是快速入門的好工具。本教學課程逐步引導您將邏輯應用程式連接至 Microsoft Azure IoT 套件遠端監視預先設定解決方案。其中示範如何將 IoT 解決方案連接至商務程序，以進一步發展此 IoT 解決方案。

_如果您要尋找有關如何佈建遠端監視預先設定解決方案的逐步解說，請參閱[教學課程：IoT 預先設定解決方案入門][lnk-getstarted]。_

開始本教學課程之前，除了在 Azure 訂用帳戶中佈建遠端監視預先設定解決方案，您還需要建立 SendGrid 帳戶，以便能夠傳送電子郵件來觸發商務程序。您可以在 [SendGrid](https://sendgrid.com/) 按一下 [免費試用]，註冊免費試用帳戶。註冊免費試用帳戶後，您必須在 SendGrid 中建立 [API 金鑰](https://sendgrid.com/docs/User_Guide/Settings/api_keys.html)來授權傳送郵件。稍後在教學課程中需要此 API 金鑰。

假設您已佈建遠端監視預先設定解決方案，請在 [Azure 入口網站][lnk-azureportal]中瀏覽至該解決方案的資源群組。資源群組的名稱與您在佈建遠端監視解決方案時所選擇的解決方案名稱相同。在資源群組中，您可以看到解決方案已預先佈建的所有 Azure 資源 (在 Azure 傳統入口網站中可找到的 Azure Active Directory 應用程式除外)。下列螢幕擷取畫面顯示遠端監視預先設定解決方案的 [資源群組] 刀鋒視窗範例︰

![](media/iot-suite-logic-apps-tutorial/resourcegroup.png)

若要開始，請設定邏輯應用程式來使用預先設定的解決方案。

## 設定邏輯應用程式

1. 在 Azure 入口網站中，按一下資源群組刀鋒視窗頂端的 [加入]。

2. 搜尋 [邏輯應用程式]，選取它，然後按一下 [建立]。

3. 填寫 [名稱]，並使用您佈建遠端監視解決方案時使用的相同 [訂用帳戶]、[資源群組] 和 [App Service 方案]。按一下 [建立]。

    ![](media/iot-suite-logic-apps-tutorial/createlogicapp.png)

4. 部署完成時，您會看到邏輯應用程式現在列為資源群組中的資源。

5. 按一下邏輯應用程式來瀏覽至 [邏輯應用程式] 刀鋒視窗，這會立即開啟 [Logic Apps 設計工具]。

    ![](media/iot-suite-logic-apps-tutorial/logicappsdesigner.png)

6. 選取 [手動 - 收到 HTTP 要求時]。這指定以內送 HTTP 要求加上特定 JSON 格式化承載做為觸發程序。

7. 將下列內容貼到 [要求本文 JSON 結構描述] 中：

    ```
    {
      "$schema": "http://json-schema.org/draft-04/schema#",
      "id": "/",
      "properties": {
        "DeviceId": {
          "id": "DeviceId",
          "type": "string"
        },
        "measuredValue": {
          "id": "measuredValue",
          "type": "integer"
        },
        "measurementName": {
          "id": "measurementName",
          "type": "string"
        }
      },
      "required": [
        "DeviceId",
        "measurementName",
        "measuredValue"
      ],
      "type": "object"
    }
    ```
    
    注意︰您可以在儲存邏輯應用程式之後複製 HTTP post 要求的 URL，但必須先加入動作。

8. 按一下手動觸發程序下的 __(+)__。然後按一下 [加入動作]。

    ![](media/iot-suite-logic-apps-tutorial/logicappcode.png)

9. 搜尋 [SendGrid - 傳送電子郵件] 並按一下它。

    ![](media/iot-suite-logic-apps-tutorial/logicappaction.png)

10. 輸入連線名稱，例如 **SendGridConnection**，輸入您設定 SendGrid 帳戶時設定的 [SendGrid API 金鑰]，然後按一下 [建立連線]。

    ![](media/iot-suite-logic-apps-tutorial/sendgridconnection.png)

11. 將您擁有的電子郵件地址加入至 [寄件者] 和 [收件者] 欄位。將**遠端監視警示 [DeviceId]** 加入至 [主旨] 欄位。在 [電子郵件本文] 欄位中加入 **裝置 [DeviceId] 已報告 [measurementName] 與 [measuredValue] 值**。 您可以按一下 [您可以從先前步驟中插入資料] 區段，以加入 **[DeviceId]**、**[measurementName]** 和 **[measuredValue]**。

    ![](media/iot-suite-logic-apps-tutorial/sendgridaction.png)

12. 按一下頂端功能表中的 [儲存]。

13. 按一下 [收到 HTTP 要求時] 觸發程序，複製 [此 URL 的 Http Post] 值。稍後在本教學課程中需要此 URL。

> [AZURE.NOTE] Logic Apps 可讓您執行[許多不同類型的動作][lnk-logic-apps-actions]，包括 Office 365 中的動作。

## 設定 EventProcessor Web 作業

本節中，您要將用於觸發邏輯應用程式的 URL，加入至當裝置感應器值超過臨界值時引發的動作，以便將預先設定的解決方案連接至您建立的邏輯應用程式。

1. 使用您的 git 用戶端複製最新版的 [azure-iot-remote-monitoring github 儲存機制][lnk-rmgithub]。例如：

    ```
    git clone https://github.com/Azure/azure-iot-remote-monitoring.git
    ```

2. 在 Visual Studio 中，從儲存機制的本機複本開啟 __RemoteMonitoring.sln__。

3. 開啟 **Infrastructure\\Repository** 資料夾中的 __ActionRepository.cs__ 檔案。

4. 使用您從邏輯應用程式記下的 [此 URL 的 Http Post] 更新 **actionIds** 字典，如下所示︰

    ```
    private Dictionary<string,string> actionIds = new Dictionary<string, string>()
    {
        { "Send Message", "<Http Post to this UR>" },
        { "Raise Alarm", "<Http Post to this UR> }
    };
    ```

5. 在方案中儲存所做的變更並結束 Visual Studio。

## 從命令列部署

本節中，您要部署已更新的遠端監視解決方案，取代目前在 Azure 中執行的版本。

1. 遵循 [開發設定][lnk-devsetup]指示，設定您的環境準備部署。

2.  若要在本機部署，請遵循[本機部署][lnk-localdeploy]指示。

3.  若要部署到雲端並更新現有的雲端部署，請遵循[雲端部署][lnk-clouddeploy]指示。使用原始部署的名稱做為部署名稱。例如，如果原始部署稱為 **demologicapp**，請使用下列命令︰

    ``
    build.cmd cloud release demologicapp
    ``
    
    當建置指令碼執行時，請務必使用您第一次佈建方案時所使用的相同 Azure 帳戶、訂用帳戶、區域和 Active Directory 執行個體。

## 了解邏輯應用程式的實際運作

當您第一次佈建解決方案時，根據預設，遠端監視預先設定解決方案會設定兩個規則。這兩個規則都在 **SampleDevice001** 裝置上︰

* 溫度 > 38.00
* 溼度 > 48.00

溫度規則會觸發 **Raise Alarm** 動作，溼度規則會觸發 **SendMessage** 動作。假設您在 **ActionRepository** 類別中對這兩個動作使用相同的 URL，邏輯應用程式將會觸發任何一項規則，並使用 SendGrid 將警示的詳細資料透過電子郵件傳送至 [收件者] 位址。

> [AZURE.NOTE] 邏輯應用程式會在每次符合臨界值時持續觸發，若要避免不必要的電子郵件，您可以在方案入口網站中停用規則，或在 [Azure 入口網站][lnk-azureportal]中停用邏輯應用程式。

除了接收電子郵件，您也可以查看邏輯應用程式在入口網站中運作情形︰

![](media/iot-suite-logic-apps-tutorial/logicapprun.png)

## 後續步驟

既然您已使用邏輯應用程式將預先設定的解決方案連接到商務程序，您可以深入了解自訂預先設定的解決方案的選項。

- [搭配使用動態遙測與遠端監視預先設定解決方案][lnk-dynamic]
- [遠端監視預先設定方案中的裝置資訊中繼資料][lnk-devinfo]

[lnk-dynamic]: iot-suite-dynamic-telemetry.md
[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[lnk-internetofthings]: https://azure.microsoft.com/documentation/suites/iot-suite/
[lnk-getstarted]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-azureportal]: https://portal.azure.com
[lnk-logic-apps-actions]: ../connectors/apis-list.md
[lnk-rmgithub]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-devsetup]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/dev-setup.md
[lnk-localdeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/local-deployment.md
[lnk-clouddeploy]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/Docs/cloud-deployment.md

<!---HONumber=AcomDC_0727_2016-->