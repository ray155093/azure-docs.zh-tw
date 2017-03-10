---
title: "排定的事件與 Azure 的中繼資料服務 | Microsoft Docs"
description: "在具影響力的事件發生之前，對虛擬機器上的這類事件做出回應。"
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/10/2016
ms.author: zivr
translationtype: Human Translation
ms.sourcegitcommit: bb4f7c4977de290e6e148bbb1ae8b28791360f96
ms.openlocfilehash: 1a385de3c00b9288d9e1245f04969a9099bf5b45
ms.lasthandoff: 03/01/2017


---
# <a name="azure-metadata-service---scheduled-events-preview"></a>Azure 中繼資料服務 - 排定的事件 (預覽)

> [!NOTE] 
> 若您同意使用規定即可取得預覽。 如需詳細資訊，請參閱 [Microsoft Azure 預覽版增補使用條款。] (https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/)
>

排定的事件是 Azure 中繼資料服務下的其中一個子服務，會呈現即將發生的事件 (例如，重新開機) 相關資訊，您的應用程式才能做好準備以及限制中斷。 它適用於所有 Azure 虛擬機器類型 (包括 PaaS 和 IaaS)。 排定的事件讓虛擬機器有時間執行預防性工作並將事件的影響降至最低。 


## <a name="introduction---why-scheduled-events"></a>簡介 - 為何使用排定的事件？

有了排定的事件，您可以採取措施限制對您服務的影響。 多重執行個體的工作負載 (其使用複寫技術來維護狀態) 可能易受發生於多個執行個體的經常中斷所影響。 例如中斷可能會導致耗費資源的工作 (例如，重建索引) 或甚至是複本遺失。 在其他許多情況下，使用正常關機順序可改善整體服務可用性。 例如，完成 (或取消) 進行中的交易、將其他工作重新指派給叢集中的其他 VM (手動容錯移轉)，即可從負載平衡器集區中移除虛擬機器。 在某些情況下，通知系統管理員有關即將發生的事件，或甚至只是記錄這類事件都有助於改善雲端裝載的應用程式服務性。
Azure 中繼資料服務會在下列使用案例中呈現排定的事件︰
-    平台起始的維護 (例如，主機作業系統推出)
-    使用者起始的呼叫 (例如，使用者重新啟動或重新部署 VM)


## <a name="scheduled-events---the-basics"></a>排定的事件 - 基本概念  

Azure 中繼資料服務會公開有關使用 VM 內的 REST 端點執行虛擬機器的資訊。 此資訊可透過不可路由傳送的 IP 取得，因此會會在 VM 之外公開。

### <a name="scope"></a>Scope
排定的事件會對雲端服務中的所有虛擬機器或可用性設定組中的所有虛擬機器呈現。 因此，您應該檢查事件中的 [資源] 欄位以找出哪些 VM 即將受到影響。 

### <a name="discover-the-endpoint"></a>探索端點
在虛擬機器建立在虛擬網路 (VNet) 之中的情況下，可從非路由傳送的 IP：169.254.169.254 提供中繼資料服務。否則雲端服務和傳統 VM 在預設的情況下，需要額外的邏輯來探索要使用的端點。 請參閱此範例以了解如何 [探索主機端點] (https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)

### <a name="versioning"></a>版本控制 
中繼資料服務會使用下列格式的版本控制 API︰http://{ip}/metadata/{version}/scheduledevents。建議您的服務在下列位置取用最新版本：http://{ip}/metadata/latest/scheduledevents

### <a name="using-headers"></a>使用標頭
當您查詢中繼資料服務時，您必須提供下列標頭「中繼資料︰true」。 

### <a name="enable-scheduled-events"></a>啟用排定的事件
第一次呼叫排定的事件時，Azure 會在您的虛擬機器上以隱含方式啟用此功能。 因此，您可能會在第一次呼叫中遇到長達兩分鐘的延遲回應。

### <a name="testing-your-logic-with-user-initiated-operations"></a>透過使用者起始的作業測試您的邏輯
若要測試您的邏輯，您可以使用 Azure 入口網站、API、CLI 或 PowerShell 來起始會導致排定事件的作業。 重新啟動虛擬機器會導致事件類型相當於重新開機的排定事件。 重新部署虛擬機器會導致事件類型相當於重新部署的排定事件。
在這兩種情況下，使用者起始的作業會花費較長時間來完成，因為排定事件需要更多時間讓應用程式順利關閉。 

## <a name="using-the-api"></a>使用 API

### <a name="query-for-events"></a>查詢事件
您只要進行下列呼叫，即可查詢排定的事件

    curl -H Metadata:true http://169.254.169.254/metadata/latest/scheduledevents

回應包含排定的事件陣列。 空白陣列表示目前沒有任何排定的事件。
在有排定事件的情況下，回應會包含事件陣列︰ 

    {
     "DocumentIncarnation":{IncarnationID},
     "Events":[
          {
                "EventId":{eventID},
                "EventType":"Reboot" | "Redeploy" | "Freeze",
                "ResourceType":"VirtualMachine",
                "Resources":[{resourceName}],
                "EventStatus":"Scheduled" | "Started",
                "NotBefore":{timeInUTC},              
         }
     ]
    }

EventType 會擷取虛擬機器上預期的影響，其中︰
- 凍結︰虛擬機器已排定會暫停幾秒鐘。 這不會影響記憶體、開啟的檔案或網路連線
- 重新開機︰虛擬機器已排定要重新開機 (已抹除記憶體)。
- 重新部署︰虛擬機器已排定要移至另一個節點 (暫時磁碟會遺失)。 

排定事件 (狀態 = 已排定) 後，Azure 會共用多久後可啟動事件的時間 (指定於 NotBefore 欄位中)。

### <a name="starting-an-event-expedite"></a>啟動事件 (加速)

得知即將發生的事件並完成您的正常關機邏輯之後，您可以進行 **POST** 呼叫來指示 Azure 加速移動 (可能的話) 
 

## <a name="powershell-sample"></a>PowerShell 範例 

下列範例會讀取中繼資料伺服器中排定的事件，並在認可之前將其記錄在應用程式事件記錄檔中。

```PowerShell
$localHostIP = "169.254.169.254"
$ScheduledEventURI = "http://"+$localHostIP+"/metadata/latest/scheduledevents"

# Call Azure Metadata Service - Scheduled Events 
$scheduledEventsResponse =  Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $ScheduledEventURI -Method get 

if ($json.Events.Count -eq 0 )
{
    Write-Output "++No scheduled events were found"
}

for ($eventIdx=0; $eventIdx -lt $scheduledEventsResponse.Events.Length ; $eventIdx++)
{
    if ($scheduledEventsResponse.Events[$eventIdx].Resources[0].ToLower().substring(1) -eq $env:COMPUTERNAME.ToLower())
    {    
        # YOUR LOGIC HERE 
         pause "This Virtual Machine is scheduled for to "+ $scheduledEventsResponse.Events[$eventIdx].EventType

        # Acknoledge the event to expedite
        $jsonResp = "{""StartRequests"" : [{ ""EventId"": """+$scheduledEventsResponse.events[$eventIdx].EventId +"""}]}"
        $respbody = convertto-JSon $jsonResp
       
        Invoke-RestMethod -Uri $ScheduledEventURI  -Headers @{"Metadata"="true"} -Method POST -Body $jsonResp 
    }
}


``` 


## <a name="c-sample"></a>C\# 範例 
下列範例是用來與中繼資料服務進行通訊的用戶端呈現 API
```csharp
   public class ScheduledEventsClient
    {
        private readonly string scheduledEventsEndpoint;
        private readonly string defaultIpAddress = "169.254.169.254"; 

        public ScheduledEventsClient()
        {
            scheduledEventsEndpoint = string.Format("http://{0}/metadata/latest/scheduledevents", defaultIpAddress);
        }
        /// Retrieve Scheduled Events 
        public string GetDocument()
        {
            Uri cloudControlUri = new Uri(scheduledEventsEndpoint);
            using (var webClient = new WebClient())
            {
                webClient.Headers.Add("Metadata", "true");
                return webClient.DownloadString(cloudControlUri);
            }   
        }

        /// Issues a post request to the scheduled events endpoint with the given json string
        public void PostResponse(string jsonPost)
        {
            using (var webClient = new WebClient())
            {
                webClient.Headers.Add("Content-Type", "application/json");
                webClient.UploadString(scheduledEventsEndpoint, jsonPost);
            }
        }
    }

```
使用下列資料結構可以剖析排定的事件 

```csharp
    public class ScheduledEventsDocument
    {
        public List<CloudControlEvent> Events { get; set; }
    }

    public class CloudControlEvent
    {
        public string EventId { get; set; }
        public string EventStatus { get; set; }
        public string EventType { get; set; }
        public string ResourceType { get; set; }
        public List<string> Resources { get; set; }
        public DateTime NoteBefore { get; set; }
    }

    public class ScheduledEventsApproval
    {
        public List<StartRequest> StartRequests = new List<StartRequest>();
    }

    public class StartRequest
    {
        [JsonProperty("EventId")]
        private string eventId;

        public StartRequest(string eventId)
        {
            this.eventId = eventId;
        }
    }

```

使用用戶端來擷取、處理及認可事件的範例程式︰   

```csharp
public class Program
    {
    static ScheduledEventsClient client;
    static void Main(string[] args)
    {
        while (true)
        {
            client = new ScheduledEventsClient();
            string json = client.GetDocument();
            ScheduledEventsDocument scheduledEventsDocument = JsonConvert.DeserializeObject<ScheduledEventsDocument>(json);

            HandleEvents(scheduledEventsDocument.Events);

            // Wait for user response
            Console.WriteLine("Press Enter to approve executing events\n");
            Console.ReadLine();

            // Approve events
            ScheduledEventsApproval scheduledEventsApprovalDocument = new ScheduledEventsApproval();
            foreach (CloudControlEvent ccevent in scheduledEventsDocument.Events)
            {
                scheduledEventsApprovalDocument.StartRequests.Add(new StartRequest(ccevent.EventId));
            }
            if (scheduledEventsApprovalDocument.StartRequests.Count > 0)
            {
                // Serialize using Newtonsoft.Json
                string approveEventsJsonDocument =
                    JsonConvert.SerializeObject(scheduledEventsApprovalDocument);

                Console.WriteLine($"Approving events with json: {approveEventsJsonDocument}\n");
                client.PostResponse(approveEventsJsonDocument);
            }

            Console.WriteLine("Complete. Press enter to repeat\n\n");
            Console.ReadLine();
            Console.Clear();
        }
    }

    private static void HandleEvents(List<CloudControlEvent> events)
    {
        // Add logic for handling events here
    }
}

```

## <a name="python-sample"></a>Python 範例 

```python


#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url="http://169.254.169.254/metadata/latest/scheduledevents"
headers="{Metadata:true}"
this_host=socket.gethostname()

def get_scheduled_events():
   req=urllib2.Request(metadata_url)
   req.add_header('Metadata','true')
   resp=urllib2.urlopen(req)
   data=json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid=evt['EventId']
        status=evt['EventStatus']
        resources=evt['Resources'][0]
        eventype=evt['EventType']
        restype=evt['ResourceType']
        notbefore=evt['NotBefore'].replace(" ","_")
        if this_host in evt['Resources'][0]:
            print "+ Scheduled Event. This host is scheduled for " + eventype + " not before " + notbefore
            print "++ Add you logic here"

def main():
   data=get_scheduled_events()
   handle_scheduled_events(data)
   

if __name__ == '__main__':
  main()
  sys.exit(0)


```
## <a name="next-steps"></a>後續步驟 
[Azure 中虛擬機器預定進行的維修](./virtual-machines-linux-planned-maintenance.md)

