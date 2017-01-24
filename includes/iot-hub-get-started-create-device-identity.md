## <a name="create-a-device-identity"></a>建立裝置識別
在本節中，您可以使用名為 [IoT Hub Explorer][iot-hub-explorer] 的 Node.js 工具，針對本教學課程建立裝置身分識別。

1. 在命令列環境中執行下列命令：
   
    npm install -g iothub-explorer@latest
2. 然後，執行下列命令來登入中樞，記得以您先前複製的 IoT 中樞連接字串取代 `{iot hub connection string}`：
   
    iothub-explorer login "{iot hub connection string}"
3. 最後，使用以下命令建立名為 `myDeviceId` 的新裝置身分識別：
   
    iothub-explorer create myDeviceId --connection-string

記下結果中的裝置連接字串。 裝置應用程式會使用此裝置連接字串，以裝置的形式連接到您的 IoT 中樞。

![][img-identity]

請參閱[開始使用 IoT 中樞][lnk-getstarted]，取得以程式設計方式建立裝置身分識別的方式。

<!-- images and links -->
[img-identity]: media/iot-hub-get-started-create-device-identity/devidentity.png

[iot-hub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md


<!--HONumber=Dec16_HO1-->


