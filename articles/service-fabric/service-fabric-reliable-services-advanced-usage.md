<properties
   pageTitle="Reliable Services 的進階用法 | Microsoft Azure"
   description="深入了解 Service Fabric 的 Reliable Services 的進階用法，以在服務中增加彈性。"
   services="Service-Fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="masnider"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/25/2016"
   ms.author="vturecek"/>

# 可靠服務程式設計模型的進階用法
Azure Service Fabric 可簡化撰寫和管理可靠的無狀態與具狀態服務。本指南會討論到 Reliable Services 的進階用法，以在您的服務中取得更多控制權和彈性。在閱讀這份指南之前，請您先熟悉＜[可靠的服務程式設計模型](service-fabric-reliable-services-introduction.md)＞。

可設定狀態與無狀態服務有使用者程式碼的兩個主要進入點：

 - `RunAsync` 是服務程式碼的一般用途進入點。
 - `CreateServiceReplicaListeners` 和 `CreateServiceInstanceListeners` 適用於開啟用戶端要求的通訊接聽程式。
 
對於大部分服務而言，這些兩個進入點已足夠。在少數情況下，需要更充分掌控服務的命週期時，可以使用其他生命週期事件。

## 無狀態服務執行個體生命週期

無狀態服務的生命週期非常簡單。無狀態服務只能開啟、關閉或中止。執行無狀態服務中的 `RunAsync` 會在服務執行個體開啟時執行，而在服務執行個體關閉或中止時取消。

雖然 `RunAsync` 應該足以應付幾乎所有情況，但也可使用無狀態服務中的開啟、關閉和中止事件︰

- 準備使用無狀態服務執行個體時，會呼叫 `Task OnOpenAsync(IStatelessServicePartition, CancellationToken)` OnOpenAsync。這個階段可以啟動擴充服務的初始化作業。

- 無狀態服務執行個體準備正常關閉時，會呼叫 `Task OnCloseAsync(CancellationToken)` OnCloseAsync。這可能在升級服務程式碼、因負載平衡而移動服務執行個體或偵測到暫時性失敗的時侯發生。OnCloseAsync 可以用來安全地關閉任何資源、停止任何背景處理、完成外部狀態儲存或關閉現有的連接。

- 強制關閉無狀態服務執行個體時，會呼叫 `void OnAbort()` OnAbort。這個一般會在於節點上偵測到永久錯誤，或因內部失敗而 Service Fabric 無法可靠地管理服務執行個體生命週期時呼叫。

## 具狀態服務複本生命週期

具狀態服務複本的生命週期比無狀態服務執行個體更為複雜。除了開啟、關閉和中止事件，具狀態服務複本會在其生命週期內經歷角色變更。當具狀態服務複本變更角色時，會觸發 `OnChangeRoleAsync` 事件︰

- 具狀態服務變更角色 (例如變更為主要或次要角色) 時，會呼叫 `Task OnChangeRoleAsync(ReplicaRole, CancellationToken)` OnChangeRoleAsync。主要複本會獲得寫入狀態 (可建立並寫入可靠的集合)。次要複本則會獲得讀取狀態 (只能從現有的可靠集合讀取)。您可以啟動或更新背景作業以回應角色變更，例如在次要複本上執行唯讀驗證、產生報告或資料採礦。

在具狀態服務中，只有主要複本具有狀態的寫入權限，因此通常在服務正在執行實際工作時。只有在具狀態服務複本為主要複本時，才會執行具狀態服務中的 `RunAsync` 方法。主要複本的角色變成非主要時，以及在關閉和中止事件期間會取消 `RunAsync` 方法。

使用 `OnChangeRoleAsync` 事件可讓您視複本角色執行工作，以及回應角色變更。

具狀態服務也會提供與無狀態服務相同的四個生命週期事件，並具有相同的語意和使用案例：

- `Task OnOpenAsync(IStatefulServicePartition, CancellationToken)`
- `Task OnCloseAsync(CancellationToken)`
- `void OnAbort()`



## 後續步驟
如需更多與 Service Fabric 相關的進階主題，請參閱下列文章：

- [設定具狀態的 Reliable Services](service-fabric-reliable-services-configuration.md)

- [Service Fabric 健康狀態簡介](service-fabric-health-introduction.md)

- [使用系統健康狀態報告進行疑難排解](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

- [使用 Service Fabric 叢集資源管理員設定服務](service-fabric-cluster-resource-manager-configure-services.md)

<!---HONumber=AcomDC_0406_2016-->