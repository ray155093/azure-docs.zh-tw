---
title: "如何使用 Python 中的 Azure 檔案儲存體 | Microsoft Docs"
description: "了解如何使用 Python 的 Azure 檔案儲存體來上傳、列出、下載及刪除檔案。"
services: storage
documentationcenter: python
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 12/08/2016
ms.author: robinsh
ms.translationtype: Human Translation
ms.sourcegitcommit: fefebeae665ccd14f15b0197241b30d33830fd09
ms.openlocfilehash: 9973da827ea5a9311904d7d6d4c22d59b5e2d0ce
ms.contentlocale: zh-tw
ms.lasthandoff: 11/17/2016


---
<a id="how-to-use-azure-file-storage-from-python" class="xliff"></a>

# 如何使用 Python 的 Azure 檔案儲存體
[!INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]

<a id="overview" class="xliff"></a>

## Overview
本文將示範如何使用檔案儲存體執行一般案例。 這些範例是以 Python 所撰寫，並使用 [Microsoft Azure Storage SDK for Python (適用於 Python 的 Microsoft Azure 儲存體 SDK)]。 所涵蓋的案例包括上傳、列出、下載及刪除檔案。

[!INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

<a id="create-a-share" class="xliff"></a>

## 建立共用
**FileService** 物件可讓您使用共用、目錄和檔案。 下列程式碼會建立 **FileService** 物件。 將下列內容新增至您想要在其中以程式設計方式存取 Azure 儲存體之任何 Python 檔案內的頂端附近。

```python
from azure.storage.file import FileService
```

下列程式碼會使用儲存體帳戶名稱和帳戶金鑰來建立 **FileService** 物件。  將 'myaccount' 和 'mykey' 取代為您的帳戶名稱和金鑰。

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

在下列的程式碼範例中，如果共用不存在，您可以使用 **FileService** 物件建立共用。

```python
file_service.create_share('myshare')
```

<a id="upload-a-file-into-a-share" class="xliff"></a>

## 將檔案上傳到共用中
Azure 檔案儲存體共用至少包含根目錄，檔案可以放置其中。 在本節中，您將學習如何從本機儲存體將檔案上傳至共用的根目錄。

若要建立檔案和上傳資料，請使用 **create\_file\_from\_path**、**create\_file\_from\_stream**、**create\_file\_from\_bytes** 或 **create\_file\_from\_text** 方法。 這些是高階方法，可在資料大小超過 64 MB 時執行必要的區塊化動作。

**create\_file\_from\_path** 會從指定的路徑上傳檔案的內容，**create\_file\_from\_stream** 會從已開啟的檔案/串流上傳內容。 **create\_file\_from\_bytes** 會上傳位元組陣列，**create\_file\_from\_text** 會使用指定的編碼 (預設為 UTF-8) 上傳指定的文字值。

下列範例會將 **sunset.png** 檔案的內容上傳至 **myfile** 檔案中。

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None, # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

<a id="how-to-create-a-directory" class="xliff"></a>

## 如何：建立目錄
您也可以組織儲存體，方法是將檔案放在子目錄中，而不是將所有檔案都放在根目錄中。 Azure 檔案儲存體服務可讓您建立您的帳戶允許數量的目錄。 下列程式碼會在根目錄底下建立名為 **sampledir** 的子目錄。

```python
file_service.create_directory('myshare', 'sampledir')
```

<a id="how-to-list-files-and-directories-in-a-share" class="xliff"></a>

## 如何：列出共用中的檔案和目錄
若要列出共用中的檔案和目錄，請使用 **list\_directories\_and\_files** 方法。 這個方法會傳回產生器。 下列程式碼會將共用中每個檔案和目錄的 **name** 輸出到主控台。

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

<a id="download-files" class="xliff"></a>

## 下載檔案
若要從檔案下載資料，請使用 **get\_file\_to\_path**、**get\_file\_to\_stream**、**get\_file\_to\_bytes** 或 **get\_file\_to\_text**。 這些是高階方法，可在資料大小超過 64 MB 時執行必要的區塊化動作。

下列範例示範如何使用 **get\_file\_to\_path** 下載 **myfile** 檔案的內容，並將其儲存至 **out-sunset.png** 檔案。

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

<a id="delete-a-file" class="xliff"></a>

## 刪除檔案
最後，若要刪除檔案，請呼叫 **delete_file**。

```python
file_service.delete_file('myshare', None, 'myfile')
```

<a id="next-steps" class="xliff"></a>

## 後續步驟
了解檔案儲存體的基礎概念之後，請使用下列連結深入了解。

* [Python 開發人員中心](/develop/python/)
* [Azure 儲存體服務 REST API](http://msdn.microsoft.com/library/azure/dd179355)
* [Azure 儲存體團隊部落格]
* [Microsoft Azure Storage SDK for Python (適用於 Python 的 Microsoft Azure 儲存體 SDK)]

[Azure 儲存體團隊部落格]: http://blogs.msdn.com/b/windowsazurestorage/
[Microsoft Azure Storage SDK for Python (適用於 Python 的 Microsoft Azure 儲存體 SDK)]: https://github.com/Azure/azure-storage-python

