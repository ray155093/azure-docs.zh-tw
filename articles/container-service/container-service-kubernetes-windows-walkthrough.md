---
title: "適用於 Windows 的 Azure Kubernetes 叢集 | Microsoft Docs"
description: "在 Azure Container Service 中部署並開始使用適用於 Windows 的 Kubernetes 叢集"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/03/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: ef1e790edc4cd329245331bf1178ed1f610e914c
ms.lasthandoff: 03/06/2017


---

# <a name="get-started-with-kubernetes-and-windows-containers-in-container-service"></a>在 Container Service 中開始使用 Kubernetes 和 Windows 容器


本文說明如何在包含 Windows 節點的 Azure Container Service 中建立 Kubernetes 叢集。以執行 Windows 容器。 

> [!NOTE]
> 支援 Windows 容器使用 Azure Container Service 中的 Kubernetes 處於預覽階段。 使用 Azure 入口網站或 Resource Manager 範本，建立 Kubernetes 叢集搭配使用 Windows 節點。 Azure CLI 2.0 目前不支援此功能。



下圖顯示 Azure Container Service 中 Kubernetes 叢集的架構，搭配使用一個 Linux 主要節點與兩個 Windows 代理程式節點。 

* Linux 主要節點提供 Kubernetes REST API，並可由連接埠 22 上的 SSH 或連接埠 443 上的 `kubectl` 存取。 
* Windows 代理程式節點則是以群組方式存在於 Azure 可用性設定組並執行您的容器。 Windows 節點可以透過主要節點經由 RDP SSH 通道來存取。 Azure Load Balancer 規則會根據公開的服務而動態地新增至叢集。


![azure 上 Kubernetes 叢集的影像](media/container-service-kubernetes-windows-walkthrough/kubernetes-windows.png)

所有 VM 都位於相同的私人虛擬網路，可完全存取彼此。 所有 VM 都會執行 kubelet、Docker 和 Proxy。

## <a name="prerequisites"></a>必要條件


* **SSH RSA 公開金鑰**︰透過入口網站或其中一個 Azure 快速入門範本部署時，您必須提供 SSH RSA 公開金鑰，以便對 Azure Container Service 虛擬機器進行驗證。 若要建立安全殼層 (SSH) RSA 金鑰，請參閱 [OS X 和 Linux](../virtual-machines/virtual-machines-linux-mac-create-ssh-keys.md) 和 [Windows](../virtual-machines/virtual-machines-linux-ssh-from-windows.md)指引。 

* **服務主體用戶端識別碼和密碼**：如需詳細資訊和指引，請參閱[關於 Kubernetes 叢集的服務主體](container-service-kubernetes-service-principal.md)。




## <a name="create-the-cluster"></a>建立叢集

您可以使用 Azure 入口網站以[建立 Kubernetes 叢集](container-service-deployment.md#create-a-cluster-by-using-the-azure-portal)搭配使用 Windows 代理程式節點。 建立叢集時，請注意下列設定︰

* 在 [基本] 刀鋒視窗的 [Orchestrator] 中，選取 [Kubernetes]。 

  ![選取 Kubernetes Orchestrator](media/container-service-kubernetes-windows-walkthrough/portal-select-kubernetes.png)

* 在 [主要組態] 刀鋒視窗中，輸入 Linux 的主要節點的使用者認證和服務主體認證。 選擇 1、3 或 5 個 master。

* 在 [代理程式組態] 刀鋒視窗的 [作業系統] 中，選取 [Windows (預覽)]。 輸 Windows 代理程式節點的系統管理員認證。

  ![選取 Windows 代理程式](media/container-service-kubernetes-windows-walkthrough/portal-select-windows.png)

如需詳細資料，請參閱[部署 Azure Container Service 叢集](container-service-deployment.md)。

## <a name="connect-to-the-cluster"></a>連接到叢集

使用 `kubectl` 命令列工具以從您的本機電腦連接到 Kubernetes 叢集的主要節點。 如需安裝和設定 `kubectl` 的步驟，請參閱[連接到 Azure Container Service 叢集](container-service-connect.md#connect-to-a-kubernetes-cluster)。 您可以使用 `kubectl` 命令來存取 Kubernetes Web UI，也可以建立和管理 Windows 容器工作負載。

## <a name="create-your-first-kubernetes-service"></a>建立第一個 Kubernetes 服務

建立叢集並與 `kubectl` 連接之後，您可以嘗試啟動基本 Windows Web 應用程式，並對網際網路公開。 在此範例中，您使用 YAML 檔案來指定容器資源，然後再使用 `kubctl apply` 來建立。

1. 若要查看您的節點清單，請輸入 `kubectl get nodes`。 如果您想要取得節點的完整詳細資料，請輸入：  

  ```
  kubectl get nodes -o yaml
  ```

2. 建立名為 `simpleweb.yaml` 的檔案，並複製下列內容。 此檔案會使用 [Docker 中樞 (Docker Hub)](https://hub.docker.com/r/microsoft/windowsservercore/) 中的 Windows Server 2016 伺服器核心基本 OS 映像來設定 Web 應用程式。  

  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: win-webserver
    labels:
      app: win-webserver
  spec:
    ports:
      # the port that this service should serve on
    - port: 80
      targetPort: 80
    selector:
      app: win-webserver
    type: LoadBalancer
  ---
  apiVersion: extensions/v1beta1
  kind: Deployment
  metadata:
    labels:
      app: win-webserver
    name: win-webserver
  spec:
    replicas: 1
    template:
      metadata:
        labels:
          app: win-webserver
        name: win-webserver
      spec:
        containers:
        - name: windowswebserver
          image: microsoft/windowsservercore
          command:
          - powershell.exe
          - -command
          - "<#code used from https://gist.github.com/wagnerandrade/5424431#> ; $$ip = (Get-NetIPAddress | where {$$_.IPAddress -Like '*.*.*.*'})[0].IPAddress ; $$url = 'http://'+$$ip+':80/' ; $$listener = New-Object System.Net.HttpListener ; $$listener.Prefixes.Add($$url) ; $$listener.Start() ; $$callerCounts = @{} ; Write-Host('Listening at {0}...' -f $$url) ; while ($$listener.IsListening) { ;$$context = $$listener.GetContext() ;$$requestUrl = $$context.Request.Url ;$$clientIP = $$context.Request.RemoteEndPoint.Address ;$$response = $$context.Response ;Write-Host '' ;Write-Host('> {0}' -f $$requestUrl) ;  ;$$count = 1 ;$$k=$$callerCounts.Get_Item($$clientIP) ;if ($$k -ne $$null) { $$count += $$k } ;$$callerCounts.Set_Item($$clientIP, $$count) ;$$header='<html><body><H1>Windows Container Web Server</H1>' ;$$callerCountsString='' ;$$callerCounts.Keys | % { $$callerCountsString+='<p>IP {0} callerCount {1} ' -f $$_,$$callerCounts.Item($$_) } ;$$footer='</body></html>' ;$$content='{0}{1}{2}' -f $$header,$$callerCountsString,$$footer ;Write-Output $$content ;$$buffer = [System.Text.Encoding]::UTF8.GetBytes($$content) ;$$response.ContentLength64 = $$buffer.Length ;$$response.OutputStream.Write($$buffer, 0, $$buffer.Length) ;$$response.Close() ;$$responseStatus = $$response.StatusCode ;Write-Host('< {0}' -f $$responseStatus)  } ; "
        nodeSelector:
          beta.kubernetes.io/os: windows
  ```

3. 若要啟動應用程式，請輸入：

  ```
  kubectl apply -f simpleweb.yaml
  ```
  
  > [!NOTE] 
  > 組態包括 `type: LoadBalancer`。 此設定會使得服務透過 Azure Load Balancer 而對網際網路公開。 如需詳細資訊，請參閱[在 Azure Container Service 中針對 Kubernetes 叢集內的容器進行負載平衡](container-service-kubernetes-load-balancing.md)。
  
4. 若要驗證服務部署情況 (這大約需要 30 秒)，請輸入︰

  ```
  kubectl get pods
  ```

5. 服務執行之後，若要查看服務的內部和外部 IP 位址，請輸入︰

  ```
  kubectl get svc
  ``` 

  ![Windows 服務的 IP 位址](media/container-service-kubernetes-windows-walkthrough/externalipa.png)

  新增外部 IP 位址需要數分鐘的時間。 在負載平衡器設定外部位址之前，它會顯示為 `<pending>`。


6. 可以使用外部 IP 位址之後，您可以在網頁瀏覽器中取用服務。

  ![瀏覽器中的 Windows 伺服器應用程式](media/container-service-kubernetes-windows-walkthrough/wincontainerwebserver.png)


## <a name="access-the-windows-nodes"></a>存取 Windows 節點
Windows 節點可以透過遠端桌面連線從本機 Windows 電腦來存取。 建議您透過主要節點使用 RDP SSH 通道。 

在 Windows 上建立 SSH 通道有很多選項。 本節說明如何使用 PuTTY 建立通道。

1. 將 [PuTTY 下載](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)到 Windows 系統。

2. 執行應用程式。

3. 輸入叢集中第一個節點的主機名稱，由叢集系統管理員使用者名稱和公用 DNS 名稱所組成。 [主機名稱] 看起來類似 `adminuser@PublicDNSName`。 輸入 22 作為 [連接埠]。

    ![PuTTY 組態 1](media/container-service-kubernetes-windows-walkthrough/putty1.png)

4. 選取 [SSH] > [Auth]。 新增私密金鑰檔 (.ppk 格式) 的路徑以供驗證。 您可以使用 [PuTTYgen](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) 等工具，從用來建立叢集的 SSH 金鑰產生此檔案。

    ![PuTTY 組態 2](media/container-service-kubernetes-windows-walkthrough/putty2.png)

5. 選取 [SSH] > [通道] 並設定已轉送的連接埠。 因為您的本機 Windows 電腦已使用連接埠 3389，所以建議您使用下列設定來連接 Windows 節點 0 和 Windows 節點 1。 (其他 Windows 節點也繼續採用此模式)。

  **Windows 節點 0**

  * **來源連接埠：**3390
  * **目的地：**10.240.245.5:3389

  **Windows 節點 1**

  * **來源連接埠：**3391
  * **目的地︰**10.240.245.6:3389

  ![Windows RDP 通道的影像](media/container-service-kubernetes-windows-walkthrough/rdptunnels.png)

6. 完成時，按一下 [工作階段] > [儲存] 以儲存連線組態。

7. 若要連接到 PuTTY 工作階段，請按一下 [開啟]。 完成主要節點連線。

8. 啟動遠端桌面連線。 若要連接到第一個 Windows 節點，請對 [電腦] 指定 `localhost:3390`，然後按一下 [連接]。 (若要連接到第二個，請指定 `localhost:3390`，依此類推)。若要完成您的連線，請提供您在部署期間設定的本機 Windows 系統管理員密碼。








## <a name="next-steps"></a>後續步驟

建議使用下列連結以深入了解 Kubernetes：

* [Kubernetes 訓練營](https://kubernetesbootcamp.github.io/kubernetes-bootcamp/index.html) - 說明如何部署、調整、更新和偵錯容器化應用程式。
* [Kubernetes 使用者指南](http://kubernetes.io/docs/user-guide/) - 提供在現有 Kubernetes 叢集中執行程式的相關資訊。
* [Kubernetes 範例](https://github.com/kubernetes/kubernetes/tree/master/examples) - 提供如何使用 Kubernetes 執行實際應用程式的範例。
