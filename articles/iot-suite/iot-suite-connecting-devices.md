<properties
   pageTitle="在 Windows 上使用 C 連接裝置 | Microsoft Azure"
   description="描述如何在 Windows 上使用已寫入 C 的應用程式，將裝置連接至 Azure IoT Suite 預先設定遠端監視方案。"
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/14/2016"
   ms.author="dobett"/>


# 將裝置連接至遠端監視預先設定方案 (Windows)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## 在 Windows 上建立 C 範例方案

下列步驟示範如何使用 Visual Studio 建立以 C 撰寫的簡單用戶端應用程式，來與遠端監視預先設定的方案進行通訊。

在 Visual Studio 2015 中建立入門專案，並新增 IoT 中樞的裝置用戶端 NuGet 封裝︰

1. 在 Visual Studio 2015 中使用 Visual C++ **Win32 主控台應用程式**範本建立新的 C 主控台應用程式。將專案命名為 **RMDevice**。

2. 在 [Win32 應用程式精靈] 的 [應用程式設定] 頁面中，確定已選取 [主控台應用程式]，並取消核取 [預先編譯的標頭] 和 [安全性開發生命週期 (SDL) 檢查]。

3. 在 [方案總管] 中刪除檔案 stdafx.h、targetver.h 和 stdafx.cpp。

4. 在 [方案總管] 中將檔案 RMDevice.cpp 重新命名為 RMDevice.c。

5. 在 [方案總管] 中以滑鼠右鍵按一下 [RMDevice] 專案，然後按一下 [管理 NuGet 封裝]。按一下 [瀏覽]，然後搜尋下列 NuGet 封裝並將其安裝到專案中︰

    - Microsoft.Azure.IoTHub.Serializer
    - Microsoft.Azure.IoTHub.IoTHubClient
    - Microsoft.Azure.IoTHub.HttpTransport

6. 在 [方案總管] 中，以滑鼠右鍵按一下 [RMDevice] 專案，然後按一下 [屬性] 開啟專案的 [屬性頁] 對話方塊。如需詳細資訊，請參閱[設定 Visual C++ 專案屬性][lnk-c-project-properties]。

7. 按一下 [連結器] 資料夾，然後按一下 [輸入] 屬性頁。

8. 將 **crypt32.lib** 新增至 [其他相依性] 屬性。按一下 [確定]，然後再按一下 [確定] 以儲存專案屬性值。

## 指定 IoT 中樞裝置的行為

IoT 中樞用戶端程式庫使用模型來指定裝置傳送至 IoT 中樞之訊息，或裝置所回應之 IoT 中樞命令的格式。

1. 在 Visual Studio 中，開啟 RMDevice.c 檔案。以下列程式碼取代現有 `#include` 陳述式：

    ```
    #include "iothubtransporthttp.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    ```

2. 在 `#include` 陳述式之後新增下列變數宣告。從遠端監視方案的儀表板將 [Device Id] 和 [Device Key] 這兩個預留位置值取代為裝置的值。使用儀表板中的 IoT 中樞主機名稱取代 [IoTHub Name]。例如，若您的 IoT 中樞主機名稱是 **contoso.azure-devices.net**，請使用 **contoso** 取代 [IoTHub Name]：

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "[IoTHub Name]";
    static const char* hubSuffix = "azure-devices.net";
    ```

3. 新增下列程式碼以定義可讓裝置與 IoT 中樞通訊的模型。此模型指定裝置會傳送溫度、外部溫度、濕度和裝置識別碼做為遙測。裝置也會傳送與該裝置有關的中繼資料到 IoT 中樞，包括裝置支援的命令清單。這個裝置會回應 **SetTemperature** 和 **SetHumidity** 命令：

    ```
    // Define the Model
    BEGIN_NAMESPACE(Contoso);

    DECLARE_STRUCT(SystemProperties,
    ascii_char_ptr, DeviceID,
    _Bool, Enabled
    );

    DECLARE_STRUCT(DeviceProperties,
    ascii_char_ptr, DeviceID,
    _Bool, HubEnabledState
    );

    DECLARE_MODEL(Thermostat,

    /* Event data (temperature, external temperature and humidity) */
    WITH_DATA(int, Temperature),
    WITH_DATA(int, ExternalTemperature),
    WITH_DATA(int, Humidity),
    WITH_DATA(ascii_char_ptr, DeviceId),

    /* Device Info - This is command metadata + some extra fields */
    WITH_DATA(ascii_char_ptr, ObjectType),
    WITH_DATA(_Bool, IsSimulatedDevice),
    WITH_DATA(ascii_char_ptr, Version),
    WITH_DATA(DeviceProperties, DeviceProperties),
    WITH_DATA(ascii_char_ptr_no_quotes, Commands),

    /* Commands implemented by the device */
    WITH_ACTION(SetTemperature, int, temperature),
    WITH_ACTION(SetHumidity, int, humidity)
    );

    END_NAMESPACE(Contoso);
    ```

## 實作裝置的行為

您現在必須新增程式碼來實作模型中所定義的行為。

1. 新增下列當裝置收到來自 IoT 中樞的 **SetTemperature** 和 **SetHumidity** 命令時執行的函式：

    ```
    EXECUTE_COMMAND_RESULT SetTemperature(Thermostat* thermostat, int temperature)
    {
      (void)printf("Received temperature %d\r\n", temperature);
      thermostat->Temperature = temperature;
      return EXECUTE_COMMAND_SUCCESS;
    }

    EXECUTE_COMMAND_RESULT SetHumidity(Thermostat* thermostat, int humidity)
    {
      (void)printf("Received humidity %d\r\n", humidity);
      thermostat->Humidity = humidity;
      return EXECUTE_COMMAND_SUCCESS;
    }
    ```

2. 新增下列會傳送訊息到 IoT 中樞的函式︰

    ```
    static void sendMessage(IOTHUB_CLIENT_HANDLE iotHubClientHandle, const unsigned char* buffer, size_t size)
    {
      IOTHUB_MESSAGE_HANDLE messageHandle = IoTHubMessage_CreateFromByteArray(buffer, size);
      if (messageHandle == NULL)
      {
        printf("unable to create a new IoTHubMessage\r\n");
      }
      else
      {
        if (IoTHubClient_SendEventAsync(iotHubClientHandle, messageHandle, NULL, NULL) != IOTHUB_CLIENT_OK)
        {
          printf("failed to hand over the message to IoTHubClient");
        }
        else
        {
          printf("IoTHubClient accepted the message for delivery\r\n");
        }

        IoTHubMessage_Destroy(messageHandle);
      }
    free((void*)buffer);
    }
    ```

3. 新增下列會連結到 SDK 中序列化程式庫的函式︰

    ```
    static IOTHUBMESSAGE_DISPOSITION_RESULT IoTHubMessage(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
    {
      IOTHUBMESSAGE_DISPOSITION_RESULT result;
      const unsigned char* buffer;
      size_t size;
      if (IoTHubMessage_GetByteArray(message, &buffer, &size) != IOTHUB_MESSAGE_OK)
      {
        printf("unable to IoTHubMessage_GetByteArray\r\n");
        result = EXECUTE_COMMAND_ERROR;
      }
      else
      {
        /*buffer is not zero terminated*/
        char* temp = malloc(size + 1);
        if (temp == NULL)
        {
          printf("failed to malloc\r\n");
          result = EXECUTE_COMMAND_ERROR;
        }
        else
        {
          memcpy(temp, buffer, size);
          temp[size] = '\0';
          EXECUTE_COMMAND_RESULT executeCommandResult = EXECUTE_COMMAND(userContextCallback, temp);
          result =
            (executeCommandResult == EXECUTE_COMMAND_ERROR) ? IOTHUBMESSAGE_ABANDONED :
            (executeCommandResult == EXECUTE_COMMAND_SUCCESS) ? IOTHUBMESSAGE_ACCEPTED :
            IOTHUBMESSAGE_REJECTED;
          free(temp);
        }
      }
      return result;
    }
    ```

4. 新增下列函式以連線到 IoT 中樞、傳送和接收訊息，以及與中樞中斷連線。請注意，裝置一旦連線就會將與本身有關的中繼資料 (包括其支援的命令) 傳送到 IoT 中樞 - 這可讓方案將儀表板上的裝置狀態更新為 [執行中]：

    ```
    void remote_monitoring_run(void)
    {
      if (serializer_init(NULL) != SERIALIZER_OK)
      {
        printf("Failed on serializer_init\r\n");
      }
      else
      {
        IOTHUB_CLIENT_CONFIG config;
        IOTHUB_CLIENT_HANDLE iotHubClientHandle;

        config.deviceId = deviceId;
        config.deviceKey = deviceKey;
        config.iotHubName = hubName;
        config.iotHubSuffix = hubSuffix;
        config.protocol = HTTP_Protocol;
        config.deviceSasToken = NULL;
        config.protocolGatewayHostName = NULL;
        iotHubClientHandle = IoTHubClient_Create(&config);
        if (iotHubClientHandle == NULL)
        {
          (void)printf("Failed on IoTHubClient_CreateFromConnectionString\r\n");
        }
        else
        {
          Thermostat* thermostat = CREATE_MODEL_INSTANCE(Contoso, Thermostat);
          if (thermostat == NULL)
          {
            (void)printf("Failed on CREATE_MODEL_INSTANCE\r\n");
          }
          else
          {
            STRING_HANDLE commandsMetadata;

            if (IoTHubClient_SetMessageCallback(iotHubClientHandle, IoTHubMessage, thermostat) != IOTHUB_CLIENT_OK)
            {
              printf("unable to IoTHubClient_SetMessageCallback\r\n");
            }
            else
            {

              /* send the device info upon startup so that the cloud app knows
              what commands are available and the fact that the device is up */
              thermostat->ObjectType = "DeviceInfo";
              thermostat->IsSimulatedDevice = false;
              thermostat->Version = "1.0";
              thermostat->DeviceProperties.HubEnabledState = true;
              thermostat->DeviceProperties.DeviceID = (char*)deviceId;

              commandsMetadata = STRING_new();
              if (commandsMetadata == NULL)
              {
                (void)printf("Failed on creating string for commands metadata\r\n");
              }
              else
              {
                /* Serialize the commands metadata as a JSON string before sending */
                if (SchemaSerializer_SerializeCommandMetadata(GET_MODEL_HANDLE(Contoso, Thermostat), commandsMetadata) != SCHEMA_SERIALIZER_OK)
                {
                  (void)printf("Failed serializing commands metadata\r\n");
                }
                else
                {
                  unsigned char* buffer;
                  size_t bufferSize;
                  thermostat->Commands = (char*)STRING_c_str(commandsMetadata);

                  /* Here is the actual send of the Device Info */
                  if (SERIALIZE(&buffer, &bufferSize, thermostat->ObjectType, thermostat->Version, thermostat->IsSimulatedDevice, thermostat->DeviceProperties, thermostat->Commands) != IOT_AGENT_OK)
                  {
                    (void)printf("Failed serializing\r\n");
                  }
                  else
                  {
                    sendMessage(iotHubClientHandle, buffer, bufferSize);
                  }

                }

                STRING_delete(commandsMetadata);
              }

              thermostat->Temperature = 50;
              thermostat->ExternalTemperature = 55;
              thermostat->Humidity = 50;
              thermostat->DeviceId = (char*)deviceId;

              while (1)
              {
                unsigned char*buffer;
                size_t bufferSize;

                (void)printf("Sending sensor value Temperature = %d, Humidity = %d\r\n", thermostat->Temperature, thermostat->Humidity);

                if (SERIALIZE(&buffer, &bufferSize, thermostat->DeviceId, thermostat->Temperature, thermostat->Humidity, thermostat->ExternalTemperature) != IOT_AGENT_OK)
                {
                  (void)printf("Failed sending sensor value\r\n");
                }
                else
                {
                  sendMessage(iotHubClientHandle, buffer, bufferSize);
                }

                ThreadAPI_Sleep(1000);
              }
            }

            DESTROY_MODEL_INSTANCE(thermostat);
          }
          IoTHubClient_Destroy(iotHubClientHandle);
        }
        serializer_deinit();
      }
    }
    ```
    
    以下是啟動時會傳送到 IoT 中樞的範例 **DeviceInfo** 訊息做為參考：

    ```
    {
      "ObjectType":"DeviceInfo",
      "Version":"1.0",
      "IsSimulatedDevice":false,
      "DeviceProperties":
      {
        "DeviceID":"mydevice01", "HubEnabledState":true
      }, 
      "Commands":
      [
        {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
        { "Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
      ]
    }
    ```
    
    以下是傳送到 IoT 中樞的範例 **Telemetry** 訊息做為參考：

    ```
    {"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
    ```
    
    以下是從 IoT 中樞收到的範例 **Command** 做為參考：
    
    ```
    {
      "Name":"SetHumidity",
      "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
      "CreatedTime":"2016-03-11T15:09:44.2231295Z",
      "Parameters":{"humidity":23}
    }
    ```

5. 將 **main** 函式取代為下列程式碼以叫用 **remote\_monitoring\_run** 函式︰

    ```
    int main()
    {
      remote_monitoring_run();
      return 0;
    }
    ```

6. 依序按一下 [建置] 和 [建置方案] 以建置裝置應用程式。

7. 在 [方案總管] 中，以滑鼠右鍵按一下 **RMDevice** 專案，按一下 [偵錯]，然後按一下 [開始新執行個體] 來執行範例。當應用程式將範例遙測傳送到 IoT 中樞並收到命令時，主控台會顯示訊息。

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]


[lnk-c-project-properties]: https://msdn.microsoft.com/library/669zx6zc.aspx

<!---HONumber=AcomDC_0720_2016-->