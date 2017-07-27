## <a name="create-a-device-identity"></a>建立裝置識別

在本節中，您可以使用名為 [iothub-explorer][iot-hub-explorer] 的 Node.js 工具，針對本教學課程建立裝置身分識別。 裝置識別碼會區分大小寫。

1. 在命令列環境中執行下列命令：

    `npm install -g iothub-explorer@latest`

1. 接著，執行下列命令來登入您的中樞。 將 `{iot hub connection string}` 替換成您先前複製的 IoT 中樞連接字串：

    `iothub-explorer login "{iot hub connection string}"`

1. 最後，使用以下命令建立名為 `myDeviceId` 的新裝置身分識別：

    `iothub-explorer create myDeviceId --connection-string`

   [!INCLUDE [iot-hub-pii-note-naming-device](iot-hub-pii-note-naming-device.md)]

記下結果中的裝置連接字串。 裝置應用程式會使用此裝置連接字串，以裝置的形式連接到您的 IoT 中樞。

![][img-identity]

請參閱[開始使用 IoT 中樞][lnk-getstarted]，以程式設計方式建立裝置身分識別。

<!-- images and links -->
[img-identity]: media/iot-hub-get-started-create-device-identity/devidentity.png

[iot-hub-explorer]: https://github.com/Azure/iothub-explorer/blob/master/readme.md

[lnk-getstarted]: ../articles/iot-hub/iot-hub-csharp-csharp-getstarted.md
