<properties
	pageTitle="Azure IoT 中樞裝置管理快速入門 | Microsoft Azure"
	description="採用 C# 的 Azure IoT 中樞裝置管理快速入門教學課程。使用 Azure IoT 中樞與 C# 搭配 Microsoft Azure IoT SDK 來實作裝置管理。"
	services="iot-hub"
	documentationCenter=".net"
	authors="ellenfosborne"
	manager="timlt"
	editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="dotnet"
 ms.topic="hero-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="elfarber"/>

# 利用 node.js 開始使用 Azure IoT 中樞裝置管理 (預覽)

[AZURE.INCLUDE [iot-hub-device-management-get-started-selector](../../includes/iot-hub-device-management-get-started-selector.md)]

## 簡介
若要開始使用 Azure IoT 中樞裝置管理，您必須建立 Azure IoT 中樞、在 IoT 中樞佈建裝置，並啟動多個模擬的裝置。本教學課程將逐步引導您完成下列步驟。

> [AZURE.NOTE]  即使您有現有的 IoT 中樞，還是需要建立新的 IoT 中樞，才能啟用裝置管理功能，因為現有的 IoT 中樞尚未有裝置管理功能。裝置管理公開上市後，所有現有的 IoT 中樞都會升級以取得裝置管理功能。

## 必要條件

您必須先安裝下列項目才能完成步驟︰

- Git
- node
- npm
- CMake (2.8 版或更新版本)。從 <https://cmake.org/download/> 安裝 CMake。務必勾選此方塊，將 CMake 新增至目前的使用者 PATH 變數。
- 有效的 Azure 訂用帳戶。

	如果您沒有帳戶，只需要幾分鐘的時間就可以建立免費試用帳戶。如需詳細資訊，請參閱 [Azure 免費試用][lnk-free-trial]。

## 建立已啟用裝置管理的 IoT 中樞

您需要為您的模擬裝置建立已啟用裝置管理的 IoT 中樞以供連接。下列步驟顯示如何使用 Azure 入口網站完成這項工作。

1.  登入 [Azure 入口網站]。
2.  在 Jumpbar 中，按一下 [新增]，然後按一下 [物聯網]，再按一下 [Azure IoT 中樞]。

	![][img-new-hub]

3.  在 [IoT 中樞] 刀鋒視窗中，選擇 IoT 中樞的組態。

	![][img-configure-hub]

  -   在 [名稱] 方塊中，輸入 IoT 中樞的名稱。如果**名稱**有效且可用，[名稱] 方塊中會出現綠色的核取記號。
  -   選取一個**價格和級別層**。本教學課程不需要特定層。
  -   在**資源群組**中，建立新的資源群組，或選取現有的資源群組。如需詳細資訊，請參閱[使用資源群組來管理您的 Azure 資源]。
  -   勾選 [啟用裝置管理] 方塊。
  -   在 [位置] 中，選取要裝載您的 IoT 中樞的位置。在公開預覽期間，IoT 中樞裝置管理功能僅適用於美國東部、北歐和東亞。未來它可在所有區域使用。

  > [AZURE.NOTE]  如果您未勾選 [啟用裝置管理] 方塊，則範例無法運作。

4.  選擇 IoT 中樞組態選項時，請按一下 [建立]。Azure 可能需要幾分鐘的時間來建立您的 IoT 中樞。若要檢查狀態，您可以在「開始面板」或 [通知] 面板中監視進度。

	![][img-monitor]

5.  成功建立 IoT 中樞時，請開啟新 IoT 中樞的刀鋒視窗，記下 **Hostname**，然後按一下 [金鑰] 圖示。

	![][img-keys]

6.  按一下 [iothubowner] 原則，然後複製並記下 **iothubowner** 刀鋒視窗中的連接字串。將連接字串複製到您可稍後存取的位置，因為您需要它才能完成本教學課程的其餘部分。

 	> [AZURE.NOTE] 在生產案例中，請務必避免使用 **iothubowner** 認證。

	![][img-connection]

您現在已建立啟用裝置管理的 IoT 中樞。您需要有連接字串，才能繼續完成本教學課程的其餘部分。

## 在 IoT 中樞建立範例和佈建裝置

在本節中，您將執行一個指令碼來建立模擬裝置和範例，以及在 IoT 中樞的裝置登錄中佈建一組新的裝置識別。裝置無法連線到 IoT 中樞，除非它在裝置登錄中具有項目。

若要在 IoT 中樞建立範例和佈建裝置，請遵循下列步驟：

1.  開啟 [終端機]。

2.  複製 github 儲存機制。**務必在沒有任何空格的目錄中複製。**

	  ```
	  git clone --recursive --branch dmpreview https://github.com/Azure/azure-iot-sdks.git
	  ```

3.  從您複製 **azure-iot-sdks** 儲存機制的根資料夾，瀏覽至 **azure-iot-sdks/node/service/samples** 目錄並執行，同時使用前一節的連接字串取代預留位置值︰

	  ```
	  setup.bat <IoT Hub Connection String>
	  ```

此指令碼會執行下列動作︰

1.  執行 **cmake** 以建立必要的檔案，建置模擬裝置。此執行檔位於 **azure-iot-sdks/node/service/samples/cmake/iotdm\_client/samples/iotdm\_simple\_sample**。注意，原始程式檔位於資料夾 **azure-iot-sdks/c/iotdm\_client/samples/iotdm\_simple\_sample** 中。

2.  建立模擬的裝置可執行檔 **iotdm\_simple\_sample**。

3.  執行 ``` npm install ``` 以安裝必要的封裝。

4.  執行 ```node generate_devices.js``` 以在您的 IoT 中樞佈建裝置識別。裝置如 **sampledevices.json** 描述。在裝置佈建後，認證會儲存於 **devicecreds.txt** 檔案 (位於 **azure-iot-sdks/node/service/samples** 目錄)。

## 啟動您的模擬裝置

裝置現已加入到裝置登錄，您可以啟用受管理的模擬裝置。模擬裝置必須針對 Azure IoT 中樞佈建的每個裝置身分識別啟動。

使用終端機，在 **azure-iot-sdks/node/service/samples** 目錄中，執行︰

  ```
  simulate.sh
  ```

此指令碼會輸出命令，您必執行以針對 **devicecreds.txt** 檔案中所列的每個裝置，啟動 **iotdm\_simple\_sample**。請從個別的終端機視窗針對每個模擬的裝置執行命令。模擬裝置會繼續執行，直到您關閉命令視窗為止。

**iotdm\_simple\_sample** 應用程式是使用適用於 C 的 Azure IoT 中樞裝置管理用戶端程式庫來建置，該程式庫可讓您建立可由 Azure IoT 中樞管理的 IoT 裝置。裝置製造商可以使用此程式庫來報告裝置屬性，並實作裝置作業所需的執行動作。此程式庫是當作開放原始碼 Azure IoT 中樞 SDK 的一部分傳送的元件。

當您執行 **simulate.sh** 時，您會在輸出視窗中看到資料串流。此輸出會顯示傳入和傳出流量，以及應用程式特定回呼函數中的 **printf** 陳述式。這可讓您查看傳入和傳出流量，以及範例應用程式如何處理已解碼的封包。當裝置連接到 IoT 中樞時，服務會自動開始觀察裝置上的資源。然後，IoT 中樞 DM 用戶端程式庫會叫用裝置回呼，以便從裝置擷取最新的值。

以下是 **iotdm\_simple\_sample** 範例應用程式的輸出。在最上方，您會看成功的 **REGISTERED** 訊息，顯示識別碼為 **Device11-7ce4a850** 的裝置已連接到 IoT 中樞。

> [AZURE.NOTE]  若要有較為精簡的輸出，請建立並執行零售組態。

![][img-output]

確定在您完成教學課程的「後續步驟」時，所有的模擬裝置都在執行中。

## 後續步驟

若要深入了解 Azure IoT 中樞裝置管理功能，您可以瀏覽下列教學課程︰

- [如何使用裝置對應項][lnk-tutorial-twin]

- [如何使用查詢找出裝置對應項][lnk-tutorial-queries]

- [如何使用裝置作業更新裝置韌體][lnk-tutorial-jobs]

<!-- images and links -->
[img-new-hub]: media/iot-hub-device-management-get-started/image1.png
[img-configure-hub]: media/iot-hub-device-management-get-started/image2.png
[img-monitor]: media/iot-hub-device-management-get-started/image3.png
[img-keys]: media/iot-hub-device-management-get-started/image4.png
[img-connection]: media/iot-hub-device-management-get-started/image5.png
[img-output]: media/iot-hub-device-management-get-started/image6.png

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure 入口網站]: https://portal.azure.com/
[使用資源群組來管理您的 Azure 資源]: ../azure-portal/resource-group-portal.md
[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-tutorial-queries]: iot-hub-device-management-device-query.md
[lnk-tutorial-jobs]: iot-hub-device-management-device-jobs.md

<!---HONumber=AcomDC_0511_2016-->