<properties
   pageTitle="設定負載平衡器 TCP 閒置逾時 |Microsoft Azure"
   description="設定負載平衡器 TCP 閒置逾時"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/03/2016"
   ms.author="sewhee" />

# 變更負載平衡器的 TCP 閒置逾時設定

在預設組態中，Azure Load Balancer 的閒置逾時設定是 4 分鐘。

這意謂著如果閒置期間超過逾時值，即無法保證用戶端與雲端服務之間的 TCP 或 HTTP 工作階段仍然存在。

當連線關閉時，用戶端應用程式將收到如下的錯誤訊息：「基礎連線已關閉：應該保持運作的連接卻被伺服器關閉」。

若要讓連線能夠更長時間保持作用中，一個常見的做法是使用 TCP Keep-Alive。(您可以[找到 .NET 範例](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx))。 若未在此連線上偵測到任何活動，則會傳送封包。此網路活動可確保永遠不會達到閒置逾時值，而會長期維持連線。

若要避免連線中斷，您必須使用比閒置逾時設定短的間隔來設定 TCP Keep-Alive，或增加閒置逾時值。

儘管 TCP Keep-Alive 相當適合電池不是條件約束的情況，但通常不建議用於行動裝置應用程式。從行動裝置應用程式使用 TCP Keep-Alive 可能會更快耗盡裝置電池電力。

為了支援這類情況，我們新增了可設定閒置逾時的支援。您現在可以設定 4 至 30 分鐘的持續時間。此設定僅適用於輸入連線。

![TCP 逾時](./media/load-balancer-tcp-idle-timeout/image1.png)

下列各節說明如何在虛擬機器和雲端服務中變更閒置逾時設定。

>[AZURE.NOTE] 若要支援設定這些設定，請確定您已安裝最新的 Azure PowerShell 套件。

## 將執行個體層級公用 IP 的 TCP 逾時值設定為 15 分鐘

    Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15

`IdleTimeoutInMinutes` 為選擇性。若未設定，則預設的逾時為 4 分鐘。

>[AZURE.NOTE] 可接受的逾時範圍為 4 到 30 分鐘。

## 在虛擬機器上建立 Azure 端點時設定閒置逾時

變更端點的逾時設定：

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM

檢閱您的閒置逾時組態：

    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15

## 在負載平衡端點集上設定 TCP 逾時

如果端點是負載平衡端點集的一部分，就必須在負載平衡端點集上設定 TCP 逾時：

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15

## 變更雲端服務的逾時設定

您可以使用 Azure SDK for .NET 2.4 來更新雲端服務。

您需在 .csdef 檔案中進行雲端服務的端點設定。若要更新雲端服務部署的 TCP 逾時，就必須進行部署升級。如果只針對公用 IP 指定 TCP 逾時，則為例外狀況。公用 IP 設定是在 .cscfg 中，而您可以透過更新和升級部署來更新這些設定。

端點設定的 .csdef 變更如下：

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
      </Endpoints>
    </WorkerRole>

公用 IP 上逾時設定的 .cscfg 變更如下：

    <NetworkConfiguration>
      <VirtualNetworkSite name="VNet"/>
      <AddressAssignments>
    <InstanceAddress roleName="VMRolePersisted">
      <PublicIPs>
        <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
      </PublicIPs>
    </InstanceAddress>
      </AddressAssignments>
    </NetworkConfiguration>

## REST API 範例

您可以使用服務管理 API 來設定 TCP 閒置逾時。請確定 x-ms-version 標頭是設定為 2014-06-01 或更新版本。

在部署中的所有虛擬機器上，更新指定負載平衡輸入端點的組態。

    Request:

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>
<BR>

    Response:

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
    <InputEndpoint>
    <LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
    <LocalPort>local-port-number</LocalPort>
    <Port>external-port-number</Port>
    <LoadBalancerProbe>
    <Path>path-of-probe</Path>
    <Port>port-assigned-to-probe</Port>
    <Protocol>probe-protocol</Protocol>
    <IntervalInSeconds>interval-of-probe</IntervalInSeconds>
    <TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
    </LoadBalancerProbe>
    <LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
    <Protocol>endpoint-protocol</Protocol>
    <IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
    <EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
    <EndpointACL>
    <Rules>
    <Rule>
    <Order>priority-of-the-rule</Order>
    <Action>permit-rule</Action>
    <RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
    <Description>description-of-the-rule</Description>
    </Rule>
    </Rules>
    </EndpointACL>
    </InputEndpoint>
    </LoadBalancedEndpointList>

## 後續步驟

[內部負載平衡器概觀](load-balancer-internal-overview.md)

[開始設定網際網路對向負載平衡器](load-balancer-get-started-internet-arm-ps.md)

[設定負載平衡器分配模式](load-balancer-distribution-mode.md)

<!---HONumber=AcomDC_0914_2016-->