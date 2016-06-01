<properties
   pageTitle="Linux 上的 Azure Service Fabric | Microsoft Azure"
   description="Service Fabric 叢集支援 Linux 和 Java，這表示您能夠在 Linux 上部署和裝載以 Java 撰寫的 Service Fabric 應用程式。"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="05/13/2016"
   ms.author="SubramaR"/>

# Linux 上的 Service Fabric

Service Fabric 目前在 Linux 上可供有限預覽，讓您如同在 Windows 上一樣，在該環境中建置、部署和管理高可用性、高可調整性應用程式。此外，高階的 Service Fabric 架構 (Reliable Services 和 Reliable Actors) 現在可在 Java 中建置。

## 支援的作業系統和程式設計語言

有限預覽支援建立一整體開發叢集，以及在 Azure 中支援執行 Ubuntu Server 15.10 的多部電腦的叢集。

您可以使用任何語言或架構來建置[客體可執行服務](service-fabric-deploy-existing-app.md)。您也可以使用 Java 來建置以 Reliable Services 和 Reliable Actor 架構為基礎的服務。

>[AZURE.NOTE] Java 尚未支援可靠的集合。

## 參與預覽

如果您有興趣參與有限預覽方案，[請填寫問卷調查](http://aka.ms/sflinuxsurvey)，讓我們更加瞭解您的案例和需求。預覽起初非常少量，但會隨著時間擴展。

請注意，Linux 上的 Service Fabric 在概念上相當於 Windows 上可用的 Service Fabric (OS 詳細規格和程式設計語言支援除外)，因此大部分的[現有文件](http://aka.ms/servicefabricdocs)都適用並將協助您熟悉這項技術。

## 後續步驟

請熟悉 [Reliable Actors](service-fabric-reliable-actors-introduction.md) 和 [Reliable Services](service-fabric-reliable-services-introduction.md) 程式設計架構。

<!---HONumber=AcomDC_0518_2016-->