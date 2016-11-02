<properties
   pageTitle="針對事件追蹤進行疑難排解| Microsoft Azure"
   description="在 Microsoft Azure Service Fabric 上部署服務時最常遇到的問題。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/31/2016"
   ms.author="mattrow"/>


# 針對您在 Azure Service Fabric 上部署服務時的常見問題進行疑難排解

當您在開發人員電腦上執行服務時，很方便就能使用 [Visual Studio 的偵錯工具](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)。對於遠端叢集而言，建議一律從[健康情況報表](service-fabric-view-entities-aggregated-health.md)開始。存取這些報表最簡單的方式，是透過 PowerShell 或 [SFX](service-fabric-visualizing-your-cluster.md)。本文假設您要對遠端叢集執行偵錯，而且對於如何使用這些工具已有基本的認識。

##應用程式損毀
「分割低於目標複本或執行個體數目」報告是指出您服務將要損毀的最佳指標。若要找出您服務發生損毀的位置，需要進行深入一點的調查。當大規模執行服務時，最好有一套詳實的追蹤資料。我們建議您試著使用 [Azure 診斷](service-fabric-diagnostics-how-to-setup-wad.md)來收集這些追蹤，並使用 [Elastic Search](service-fabric-diagnostic-how-to-use-elasticsearch.md) 等解決方案來檢視和搜尋追蹤。

![SFX 資料分割健康情況](./media/service-fabric-diagnostics-troubleshoot-common-scenarios/crashNewApp.png)

###在服務或動作項目初始化期間
在服務類型初始化之前發生的任何例外狀況都將導致程序損毀。對於這些類型的損毀，應用程式事件記錄檔會顯示您服務中的錯誤。這些是服務初始化之前，最常見的例外狀況。

***System.IO.FileNotFoundException***

這些通常是因為缺少組件相依性所致。請檢查 Visual Studio 中的 CopyLocal 屬性，或節點的全域組件快取。

在 System.Fabric.Interop.NativeRuntime+IFabricRuntime.RegisterStatefulServiceFactory(IntPtr, IFabricStatefulServiceFactory) 發生的 ***System.Runtime.InteropServices.COMException***
 
 這表示註冊的服務類型名稱，與服務資訊清單不符。

[Azure 診斷](service-fabric-diagnostics-how-to-setup-wad.md) 可以設定成自動上傳您所有節點的應用程式事件記錄。

###RunAsync() 或 OnActivateAsync()
若損毀發生在已註冊之服務類型或動作項目的初始化或執行期間，Azure Service Fabric 將會捕捉到該例外狀況。您可以在＜後續步驟＞一節所述的 EventSource 提供者中，檢視這些例外狀況。

## 後續步驟

深入了解 Service Fabric 所提供的現有診斷：

* [Reliable Actors 項目診斷](service-fabric-reliable-actors-diagnostics.md)
* [Reliable Services 診斷](service-fabric-reliable-services-diagnostics.md)

<!---HONumber=AcomDC_0406_2016-->