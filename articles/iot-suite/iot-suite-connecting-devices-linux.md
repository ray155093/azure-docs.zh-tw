<properties
   pageTitle="在 Linux 上使用 C 連接裝置| Microsoft Azure"
   description="描述如何在 Linux 上使用已寫入 C 的應用程式，將裝置連接至 Azure IoT Suite 預先設定遠端監視方案。"
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
   ms.date="04/26/2016"
   ms.author="dobett"/>


# 將裝置連接至遠端監視預先設定方案 (Linux)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## 建置並執行範例 C 用戶端 Linux

下列程序示範如何使用在 Ubuntu Linux 裝置上建置並執行的 C 程式，建立簡單的用戶端應用程式來與遠端監視預先設定的方案通訊。若要完成這些步驟，您需要執行 Ubuntu 版本 15.04 或 15.10 的裝置。繼續之前，使用下列命令在 Ubuntu 裝置上安裝的必要條件封裝：

```
sudo apt-get install cmake gcc g++
```

## 在裝置上安裝用戶端程式庫

Azure IoT 中樞用戶端程式庫可當成封裝來使用，而您可以使用 **apt get** 命令在 Ubuntu 裝置上安裝這類封裝。完成下列步驟來安裝封裝，其中包含您 Ubuntu 電腦上的程式庫和標頭檔：

1. 將 AzureIoT 儲存機制新增至電腦：

    ```
    sudo add-apt-repository ppa:aziotsdklinux/ppa-azureiot
    sudo apt-get update
    ```

2. 安裝 azure-iot-sdk-c-dev 封裝

    ```
    sudo apt-get install -y azure-iot-sdk-c-dev
    ```

## 新增程式碼以指定裝置的行為

在 Ubuntu 電腦上，建立名為 **remote\_monitoring** 的資料夾。在 **remote\_monitoring** 資料夾中建立四個檔案︰**main.c**、**remote\_monitoring.c**、**remote\_monitoring.h** 和 **CMakeLists.txt**。

IoT 中樞序列化程式用戶端程式庫使用模型，來指定裝置傳送到 IoT 中樞的訊息格式，或裝置所回應的 IoT 中樞命令格式。

1. 在文字編輯器中，開啟 **remote\_monitoring.c** 檔案。新增下列 `#include` 陳述式：

    ```
    #include "iothubtransportamqp.h"
    #include "schemalib.h"
    #include "iothub_client.h"
    #include "serializer.h"
    #include "schemaserializer.h"
    #include "azure_c_shared_utility/threadapi.h"
    #include "azure_c_shared_utility/platform.h"
    ```

2. 在 `#include` 陳述式之後新增下列變數宣告。從遠端監視方案的儀表板將 [Device Id] 和 [Device Key] 這兩個預留位置值取代為裝置的值。使用儀表板中的 IoT 中樞主機名稱取代 [IoTHub Name]。例如，若您的 IoT 中樞主機名稱是 **contoso.azure-devices.net**，請使用 contoso 取代 [IoTHub Name]：

    ```
    static const char* deviceId = "[Device Id]";
    static const char* deviceKey = "[Device Key]";
    static const char* hubName = "IoTHub Name]";
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

### 新增程式碼以實作裝置的行為

新增裝置收到中樞傳來的命令時要執行的函式，以及用來將模擬遙測傳送到中樞的程式碼。

1. 新增下列會在裝置收到模型中所定義的 **SetTemperature** 和 **SetHumidity** 命令時執行的函式：

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

4. 新增下列函式以連線到 IoT 中樞、傳送和接收訊息，以及與中樞中斷連線。請注意，裝置一旦連線就會將與本身有關的中繼資料 (包括其支援的命令) 傳送到 IoT 中樞；這可讓方案將儀表板上的裝置狀態更新為 [執行中]：

    ```
    void remote_monitoring_run(void)
    {
      if (platform_init() != 0)
      {
        printf("Failed to initialize the platform.\r\n");
      }
      else
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
          config.deviceSasToken = NULL;
          config.iotHubName = hubName;
          config.iotHubSuffix = hubSuffix;
          config.protocol = AMQP_Protocol;
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
        platform_deinit();
      }
    }
    ```
    
    做為參考，以下是啟動時會傳送到 IoT 中樞的範例 **DeviceInfo** 訊息：

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
    
    做為參考，以下是傳送到 IoT 中樞的範例 **Telemetry** 訊息：

    ```
    {"DeviceId":"mydevice01", "Temperature":50, "Humidity":50, "ExternalTemperature":55}
    ```
    
    做為參考，以下是從 IoT 中樞收到的範例 **Command**：
    
    ```
    {
      "Name":"SetHumidity",
      "MessageId":"2f3d3c75-3b77-4832-80ed-a5bb3e233391",
      "CreatedTime":"2016-03-11T15:09:44.2231295Z",
      "Parameters":{"humidity":23}
    }
    ```

### 新增程式碼來叫用 remote\_monitoring\_run 函式

在文字編輯器中，開啟 **remote\_monitoring.h** 檔案。新增下列程式碼：

```
void remote_monitoring_run(void);
```

在文字編輯器中，開啟 **main.c** 檔案。新增下列程式碼：

```
#include "remote_monitoring.h"

int main(void)
{
    remote_monitoring_run();

    return 0;
}
```

## 使用 CMake 建置用戶端應用程式

下列步驟說明如何使用 CMake 建置用戶端應用程式。

1. 在文字編輯器中，開啟 **remote\_monitoring** 資料夾中的 **CMakeLists.txt** 檔案。

2. 新增下列指示來定義如何建置用戶端應用程式：

    ```
    cmake_minimum_required(VERSION 2.8.11)

    set(AZUREIOT_INC_FOLDER ".." "/usr/include/azureiot")

    include_directories(${AZUREIOT_INC_FOLDER})

    set(sample_application_c_files
        ./remote_monitoring.c
        ./main.c
    )

    set(sample_application_h_files
        ./remote_monitoring.h
    )

    add_executable(sample_app ${sample_application_c_files} ${sample_application_h_files})

    target_link_libraries(sample_app
        serializer
        iothub_client
        iothub_client_amqp_transport
        aziotsharedutil
        uamqp
        pthread
        curl
        ssl
        crypto
    )
    ```

3. 在 **remote\_monitoring** 資料夾中，建立資料夾來儲存 CMake 產生的 make 檔案，然後執行 **cmake** 和 **make** 命令，如下所示：

    ```
    mkdir cmake
    cd cmake
    cmake ../
    make
    ```

4. 執行用戶端應用程式，並將遙測傳送至 IoT 中樞：

    ```
    ./sample_app
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-setup-linux]: https://github.com/azure/azure-iot-sdks/blob/develop/c/doc/devbox_setup.md#linux

<!---HONumber=AcomDC_0622_2016-->