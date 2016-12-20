> [!div class="op_single_selector"]
> * [Node.js](../articles/iot-hub/iot-hub-node-node-direct-methods.md)
> * [C#](../articles/iot-hub/iot-hub-csharp-node-direct-methods.md)
> 
> 

## <a name="introduction"></a>簡介
Azure IoT 中樞是一項完全受管理的服務，可在數百萬個裝置和一個應用程式後端之間進行可靠且安全的雙向通訊。 先前的教學課程 ([IoT 中樞入門]和[使用 IoT 中樞傳送雲端到裝置訊息]) 說明如何使用 IoT 中樞的裝置到雲端和雲端到裝置的基本傳訊功能。 IoT 中樞也能讓您從雲端的裝置上叫用非持續性方法。 方法代表與裝置的要求-回覆互動，類似於 HTTP 呼叫，因為會立即成功或失敗 (在使用者指定的逾時之後)，讓使用者知道呼叫的狀態。 [在裝置上叫用直接方法][lnk-devguide-methods]描述更詳細的方法，並提供在使用方法與雲端到裝置訊息時的相關指導方針。

本教學課程說明如何：

* 使用 Azure 入口網站來建立 IoT 中樞，並且在 IoT 中樞建立裝置身分識別。
* 建立模擬裝置應用程式，其中具有可由雲端呼叫的直接方法。
* 建立主控台應用程式，可透過您的 IoT 中樞在模擬裝置應用程式中呼叫直接方法。

> [!NOTE]
> 目前，直接方法只能從使用 MQTT 通訊協定連接至 IoT 中樞的裝置存取。 請參閱 [MQTT 支援][lnk-devguide-mqtt]文章，以取得如何將現有裝置應用程式轉換為使用 MQTT 的指示。
> 
> 




[lnk-devguide-methods]: ../articles/iot-hub/iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: ../articles/iot-hub/iot-hub-mqtt-support.md

[使用 IoT 中樞傳送雲端到裝置訊息]: ../articles/iot-hub/iot-hub-csharp-csharp-c2d.md
[IoT 中樞入門]: ../articles/iot-hub/iot-hub-node-node-getstarted.md

<!--HONumber=Nov16_HO5-->


