<properties
	pageTitle="Azure 事件中樞封存逐步解說 | Microsoft Azure"
	description="此範例使用 Azure Python SDK 來示範如何使用事件中樞封存功能。"
	services="event-hubs"
	documentationCenter=""
	authors="djrosanova"
	manager="timlt"
	editor=""/>

<tags
	ms.service="event-hubs"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/13/2016"
	ms.author="darosa;sethm"/>

# 事件中樞封存逐步解說︰Python

事件中樞封存是事件中樞的新功能，可讓您自動將事件中樞內的串流資料傳遞到您所選擇的 Azure Blob 儲存體帳戶。這可讓您輕鬆地對即時串流資料執行批次處理。本文說明如何搭配使用事件中樞封存與 Python。如需事件中樞封存的詳細資訊，請參閱[概觀文章](event-hubs-archive-overview.md)。

此範例使用 Azure Python SDK 來示範如何使用封存功能。sender.py 會以 JSON 格式將模擬的環境遙測傳送至事件中樞。事件中樞會設定為使用封存功能將此資料批次寫入至 Blob 儲存體。archivereader.py 接著會讀取這些 Blob、為每個裝置建立附加檔案，並將資料寫入至 .csv 檔案。

將會完成的工作

1.  使用 Azure 入口網站建立 Azure Blob 儲存體帳戶和其中所含的 Blob 容器

2.  使用 Azure 入口網站建立事件中樞命名空間

3.  使用 Azure 入口網站建立啟用了封存功能的事件中樞

4.  使用 Python 指令碼將資料傳送至事件中樞

5.  使用另一個 Python 指令碼讀取封存的檔案並加以處理

必要條件

1.  Python 2.7.x

2.  Azure 訂用帳戶

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## 建立 Azure 儲存體帳戶

1.  登入 [Azure 入口網站][]。

2.  在入口網站的左方瀏覽窗格中，依序按一下 [新增]、[資料 + 儲存體] 及 [儲存體帳戶]。

3.  完成儲存體帳戶刀鋒視窗中的欄位，然後按一下 [建立]。

    ![][1]

4.  在看到**部署成功**訊息之後，按一下新的儲存體帳戶，並在 [基本功能] 刀鋒視窗中按一下 [Blob]。當 [Blob 服務] 刀鋒視窗開啟時，按一下頂端的 [+ 容器]。將容器命名為**封存**，然後關閉 [Blob 服務] 刀鋒視窗。

5.  按一下左側刀鋒視窗的 [存取金鑰]，然後複製儲存體帳戶名稱和 **key1** 的值。將這些值儲存到記事本或一些其他暫存位置。

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

## 建立 Python 指令碼以將事件傳送至事件中樞

1.  開啟您慣用的 Python 編輯器，例如 [Visual Studio 程式碼][]。

2.  建立稱為 **sender.py** 的指令碼。此指令碼會傳送 200 個事件至事件中樞。這些事件是以 JSON 格式傳送的簡單環境數據。

3.  將下列程式碼貼到 sender.py：

	```
	import uuid
	import datetime
	import random
	import json
	from azure.servicebus import ServiceBusService
	
	sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
	devices = []
	for x in range(0, 10):
	    devices.append(str(uuid.uuid4()))
	
	for y in range(0,20):
	    for dev in devices:
	        reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
	        s = json.dumps(reading)
	        sbs.send\_event('myhub', s)
	    print y
	```
4.  更新上述程式碼，以使用您在建立事件中樞命名空間時取得的命名空間名稱和金鑰值。

## 建立 Python 指令碼來讀取封存檔案

1.  填寫刀鋒視窗，然後按一下 [建立]。

2.  建立稱為 **archivereader.py** 的指令碼。此指令碼會讀取封存檔案，並為每個裝置建立檔案以便只寫入該裝置的資料。

3.  將下列程式碼貼到 archivereader.py：

	```
    import os
	import string
	import json
	import avro.schema
	from avro.datafile import DataFileReader, DataFileWriter
	from avro.io import DatumReader, DatumWriter
	from azure.storage.blob import BlockBlobService
	
	def processBlob(filename):
	    reader = DataFileReader(open(filename, 'rb'), DatumReader())
	    dict = {}
	    for reading in reader:
	        parsed\_json = json.loads(reading["Body"])
	        if not 'id' in parsed\_json:
	            return
	        if not dict.has\_key(parsed\_json['id']):
	        list = []
	        dict[parsed\_json['id']] = list
	    else:
	        list = dict[parsed\_json['id']]
	        list.append(parsed\_json)
	    reader.close()
	    for device in dict.keys():
	        deviceFile = open(device + '.csv', "a")
	        for r in dict[device]:
	            deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\\n')

	def startProcessing(accountName, key, container):
	    print 'Processor started using path: ' + os.getcwd()
	    block\_blob\_service = BlockBlobService(account\_name=accountName, account\_key=key)
	    generator = block\_blob\_service.list\_blobs(container)
	    for blob in generator:
	        if blob.properties.content\_length != 0:
	            print('Downloaded a non empty blob: ' + blob.name)
	            cleanName = string.replace(blob.name, '/', '\_')
	            block\_blob\_service.get\_blob\_to\_path(container, blob.name, cleanName)
	            processBlob(cleanName)
	            os.remove(cleanName)
	        block\_blob\_service.delete\_blob(container, blob.name)
	startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'archive')
    ```

4.  請務必在 `startProcessing` 的呼叫中貼上儲存體帳戶名稱和金鑰的適當值。

## 執行指令碼

1.  開啟在其路徑中具有 Python 的命令提示字元，並執行下列命令來安裝 Python 必要條件封裝︰

	```
    pip install azure-storage
	pip install azure-servicebus
	pip install avro
    ```
  
    如果您有舊版的 Azure 儲存體或 Azure，您可能需要使用 **--upgrade** 選項

    您可能也需要執行下列命令 (在大部分系統上並不需要)︰

    ```
    pip install cryptography
    ```

2.  將目錄變更為儲存了 sender.py 和 archivereader.py 的任何位置，並執行此命令︰

    ```
    start python sender.py
    ```
    
    這會啟動新的 Python 程序來執行傳送器。

3. 現在等候封存執行幾分鐘的時間。然後在原始命令視窗中輸入下列命令︰

    ```
    python archivereader.py
    ```

此封存處理器會使用本機目錄從儲存體帳戶/容器下載所有 Blob。它會處理任何非空白的 Blob，並將結果以 .csv 檔案的形式寫入到本機目錄。

## 後續步驟

您可以造訪下列連結以深入了解事件中樞︰

- [事件中樞封存概觀][]
- [使用事件中樞的完整範例應用程式][]。
- [使用「事件中樞」相應放大事件處理][]範例。
- [事件中心概觀][]
 

[Azure 入口網站]: https://portal.azure.com/
[事件中樞封存概觀]: event-hubs-archive-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]: https://azure.microsoft.com/zh-TW/documentation/articles/storage-create-storage-account/
[Visual Studio 程式碼]: https://code.visualstudio.com/
[事件中心概觀]: event-hubs-overview.md
[使用事件中樞的完整範例應用程式]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[使用「事件中樞」相應放大事件處理]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3

<!---HONumber=AcomDC_0914_2016-->