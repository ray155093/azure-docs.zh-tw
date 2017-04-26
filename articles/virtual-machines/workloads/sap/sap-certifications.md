---
title: "適用於 SAP 的 Microsoft Azure 認證 | Microsoft Docs"
description: "目前 Azure 平台上 SAP 設定和認證的更新清單。"
services: virtual-machines-linux
documentationcenter: 
author: RicksterCDN
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/29/2017
ms.author: rclaus
ms.custom: 
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 8925de446b9dc7e17a2f2033426eb380329c8249
ms.lasthandoff: 04/03/2017


---
# <a name="sap-certifications-and-configurations-running-on-microsoft-azure"></a>於 Microsoft Azure 上執行的 SAP 認證和設定

SAP 和 Microsoft 是已合作多年的穩固合作夥伴，能互相為彼此的客戶帶來許多好處。 Microsoft 一直持續更新其平台，並提交新的認證詳細資料給 SAP，以確保 Microsoft Azure 是執行 SAP 工作負載的最佳平台。 下表概述我們所支援的設定，以及持續增加的認證清單。 

## <a name="sap-hana-certifications"></a>SAP HANA 認證

| SAP 產品 | 支援的 OS | Azure 供應項目 |
| --- | --- | --- |
| SAP HANA Developer Edition (包括由 SQLODBC、Windows 專用版的 ODBO、ODBC、JDBC 驅動程式、HANA Studio 及 HANA 資料庫組成的 HANA 用戶端軟體) |Red Hat Enterprise Linux、SUSE Linux Enterprise | D 系列 VM 系列 |
| HANA One |Red Hat Enterprise Linux、SUSE Linux Enterprise |DS14_v2 (正式運作後) |
| SAP S/4 HANA |Red Hat Enterprise Linux、SUSE Linux Enterprise |GS5 管制性運作、SAP HANA on Azure (大型執行個體) |
| Suite on HANA (OLTP) |Red Hat Enterprise Linux、SUSE Linux Enterprise |適用於單一節點部署的 GS5 (針對非實際執行案例)、SAP HANA on Azure (大型執行個體) |
| HANA Enterprise for BW (OLAP) |Red Hat Enterprise Linux、SUSE Linux Enterprise |適用於單一節點部署的 GS5、SAP HANA on Azure (大型執行個體) |
| SAP BW/4 HANA |Red Hat Enterprise Linux、SUSE Linux Enterprise |適用於單一節點部署的 GS5、SAP HANA on Azure (大型執行個體) |

## <a name="sap-netweaver-certifications"></a>SAP NetWeaver 認證
Microsoft Azure 已獲認證可用於下列 SAP 產品，因此具備 Microsoft 和 SAP 的完整支援。

| SAP 產品 | 客體作業系統 | RDBMS | 虛擬機器類型 |
| --- | --- | --- | --- |
| SAP Business Suite 軟體 |Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux |SQL Server、Oracle (僅限 Windows)、DB2、SAP ASE |A5 至 A11、D11 至 D14、DS11 至 DS14、DS11_v2 至 DS15_v2、GS1 至 GS5 |
| SAP Business All-in-One |Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux |SQL Server、Oracle (僅限 Windows)、DB2、SAP ASE |A5 至 A11、D11 至 D14、DS11 至 DS14、DS11_v2 至 DS15_v2、GS1 至 GS5 |
| SAP BusinessObjects BI |Windows |N/A |A5 至 A11、D11 至 D14、DS11 至 DS14、DS11_v2 至 DS15_v2、GS1 至 GS5 |
| SAP NetWeaver |Windows、SUSE Linux Enterprise、Red Hat Enterprise Linux |SQL Server、Oracle (僅限 Windows)、DB2、SAP ASE |A5 至 A11、D11 至 D14、DS11 至 DS14、DS11_v2 至 DS15_v2、GS1 至 GS5 |

