## <a name="create-an-iot-hub"></a>建立 IoT 中樞

1. 在 [Azure 入口網站](https://portal.azure.com/)中，按一下 [新增] > [物聯網] >  [IoT 中樞]。

   ![在 Azure 入口網站中建立 IoT 中樞](../articles/iot-hub/media/iot-hub-create-hub-and-device/1_create-azure-iot-hub-portal.png)
1. 在 [IoT 中樞] 窗格中，輸入 IoT 中樞的下列資訊︰

   **名稱**：它是 IoT 中樞的名稱。 如果您輸入的名稱有效，則會出現綠色核取記號。

   **定價與級別層**︰選取 F1 免費層。 對此範例而言，此選項就已足夠。 請參閱[定價與級別層](https://azure.microsoft.com/pricing/details/iot-hub/)。

   **資源群組**：建立用以裝載 IoT 中樞的資源群組，或使用現有資源群組。 請參閱[使用資源群組來管理您的 Azure 資源](../articles/azure-resource-manager/resource-group-portal.md)。

   **位置**︰選取與您最接近的位置，以在其中建立 IoT 中樞。

   **釘選儀表板**︰核取此選項可讓您從儀表板輕鬆地存取 IoT 中樞。

   ![填寫用來建立 Azure IoT 中樞的欄位](../articles/iot-hub/media/iot-hub-create-hub-and-device/2_fill-in-fields-for-azure-iot-hub-portal.png)

1. 按一下 [建立] 。 可能需要幾分鐘的時間才能建立 IoT 中樞。 您可以在 [通知] 窗格中看到進度。

   ![請參閱 IoT 中樞建立進度的通知](../articles/iot-hub/media/iot-hub-create-hub-and-device/3_notification-azure-iot-hub-creation-progress-portal.png)

1. IoT 中樞建立好之後，從儀表板對它按一下。 請記下**主機名稱**，然後按一下 [共用存取原則]。

   ![取得 IoT 中樞的主機名稱](../articles/iot-hub/media/iot-hub-create-hub-and-device/4_get-azure-iot-hub-hostname-portal.png)

1. 在 [共用存取原則] 窗格中，按一下 [iothubowner] 原則，然後複製並記下 IoT 中樞的**連接字串**。 如需詳細資訊，請參閱[控制 IoT 中樞的存取權](../articles/iot-hub/iot-hub-devguide-security.md)。

   ![取得 IoT 中樞連接字串](../articles/iot-hub/media/iot-hub-create-hub-and-device/5_get-azure-iot-hub-connection-string-portal.png)

## <a name="register-a-device-in-the-iot-hub-for-the-your-device"></a>在裝置的 IoT 中樞註冊裝置

1. 在 [Azure 入口網站](https://portal.azure.com/)中，開啟 IoT 中樞。
1. 按一下 [裝置總管]。
1. 在 [裝置總管] 窗格中，按一下 [新增] 將裝置新增到 IoT 中樞。

   **裝置識別碼**︰新裝置的識別碼。 裝置識別碼會區分大小寫。

   **驗證類型**：選取 [對稱金鑰]。

   **自動產生金鑰**︰檢查此欄位。

   **將裝置連線至 IoT 中樞**：按一下 [啟用]。

   ![在 IoT 中樞的裝置總管中新增裝置](../articles/iot-hub/media/iot-hub-create-hub-and-device/6_add-device-in-azure-iot-hub-device-explorer-portal.png)

1. 按一下 [儲存] 。
1. 建立裝置之後，請在 [裝置總管] 窗格中開啟裝置。
1. 記下連接字串的主索引鍵。

   ![取得裝置連接字串](../articles/iot-hub/media/iot-hub-create-hub-and-device/7_get-device-connection-string-in-device-explorer-portal.png)