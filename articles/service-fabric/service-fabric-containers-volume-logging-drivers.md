---
title: "Azure Service Fabric Docker Compose 預覽 | Microsoft Docs"
description: "Azure Service Fabric 接受 Docker Compose 格式，讓您能更輕鬆地使用 Service Fabric 協調現有容器。 這項支援目前只能預覽。"
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: 
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 6/28/2017
ms.author: subramar
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 868c3051f60c27f15bfd99f66e50b65595951a00
ms.contentlocale: zh-tw
ms.lasthandoff: 05/25/2017

---

# <a name="specifying-volume-plugins-and-logging-drivers-for-your-container"></a>指定容器的磁碟區外掛程式和記錄驅動程式

Service Fabric 支援為容器服務指定 [Docker 磁碟區外掛程式](https://docs.docker.com/engine/extend/plugins_volume/)和 [Docker 記錄驅動程式](https://docs.docker.com/engine/admin/logging/overview/)。 外掛程式是在應用程式資訊清單中指定，如下列資訊清單中所示：


```xml
?xml version="1.0" encoding="UTF-8"?>
<ApplicationManifest ApplicationTypeName="WinNodeJsApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
    <Description>Calculator Application</Description>
    <Parameters>
        <Parameter Name="ServiceInstanceCount" DefaultValue="3"></Parameter>
      <Parameter Name="MyCpuShares" DefaultValue="3"></Parameter>
    </Parameters>
    <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
     <Policies>
       <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="hyperv"> 
        <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        <RepositoryCredentials PasswordEncrypted="false" Password="****" AccountName="test"/>
        <LogConfig Driver="etwlogs" >
          <DriverOption Name="test" Value="vale"/>
        </LogConfig>
        <Volume Source="c:\workspace" Destination="c:\testmountlocation1" IsReadOnly="false"></Volume>
        <Volume Source="d:\myfolder" Destination="c:\testmountlocation2" IsReadOnly="true"> </Volume>
        <Volume Source="myexternalvolume" Destination="c:\testmountlocation3" Driver="sf" IsReadOnly="true"></Volume>
       </ContainerHostPolicies>
   </Policies>
    </ServiceManifestImport>
    <ServiceTemplates>
        <StatelessService ServiceTypeName="StatelessNodeService" InstanceCount="5">
            <SingletonPartition></SingletonPartition>
        </StatelessService>
    </ServiceTemplates>
</ApplicationManifest>
```

在上述範例中，`Volume` 的 `Source` 標記指的是來源資料夾。 來源資料夾可以是裝載容器或持續性遠端存放區的 VM 中的資料夾。 `Destination` 標記是 `Source` 在執行容器內對應的位置。 使用磁碟區外掛程式時，會指定外掛程式的名稱 (`Driver` 標記)，如上述範例所示。  如果指定了 Docker 記錄驅動程式，就必須在叢集中部署代理程式 (或容器) 來處理記錄檔。 

請參閱下列文章將容器部署至 Service Fabric 叢集：

[將 Windows 容器部署至 Windows Server 2016 上的 Service Fabric](service-fabric-deploy-container.md)

[將 Docker 容器部署至 Linux 上的 Service Fabric](service-fabric-deploy-container-linux.md)


