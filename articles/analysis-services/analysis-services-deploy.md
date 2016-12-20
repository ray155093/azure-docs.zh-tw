---
title: "部署至 Azure Analysis Services | Microsoft Docs"
description: "了解如何將表格式模型部署至 Azure Analysis Services 伺服器。"
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
tags: 
ms.assetid: 5f1f0ae7-11de-4923-a3da-888b13a3638c
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 11/28/2016
ms.author: owend
translationtype: Human Translation
ms.sourcegitcommit: 193c939065979dc48243d31e7f97cd87d96bf9a8
ms.openlocfilehash: 18d64f1ad4ef0dd41ae9302d08e02e94d1c608f5


---
# <a name="deploy-to-azure-analysis-services"></a>部署至 Azure Analysis Services
以您的 Azure 訂用帳戶建立伺服器後，您即可將表格式模型資料庫部署至該伺服器。 您可以使用 SQL Server Data Tools (SSDT) 建置與部署您正在使用的表格式模型專案。 或者，您可以使用 SQL Server Management Studio (SSMS) 從 Analysis Services 執行個體部署現有的表格式模型資料庫。

## <a name="before-you-begin"></a>開始之前
若要開始，您需要：

* Azure 中的 **Analysis Services 伺服器**。 若要深入了解，請參閱 [Create an Analysis Services in Azure](analysis-services-create-server.md) (在 Azure 中建立 Analysis Services)。
* SSDT 中的**表格式模型專案**，或 Analysis Services 執行個體上 1200 相容性層級的現有表格式模型。 未曾建立過？ 請嘗試[表格式模型化 (Adventure Works 教學課程)](https://msdn.microsoft.com/library/hh231691.aspx)。
* **內部部署閘道** - 如果您組織的網路中有一或多個資料來源為內部部署，您必須安裝[內部部署資料閘道](analysis-services-gateway.md)。 您在雲端中的伺服器必須有閘道，才能連線至您的內部部署資料來源，以處理和重新整理模型中的資料。

## <a name="to-deploy-a-tabular-model-from-ssdt"></a>從 SSDT 部署表格式模型
為了從 SSDT 部署，請確定您使用的是於 2016 年 9 月 30 日或以後更新的[最新版本](https://msdn.microsoft.com/library/mt204009.aspx)。

> [!TIP]
> 部署之前，請確定您可以在資料表中處理資料。 在 SSDT 中，按一下 [模型]  >  [程序]  >  **Process All** (全部處理)。 如果處理失敗，部署將失敗。
> 
> 

1. 部署之前，您必須先取得伺服器名稱。 在 [Azure 入口網站] > 伺服器 > [概觀]  >  [伺服器名稱] 中，複製伺服器名稱。
   
    ![在 Azure 中取得伺服器名稱](./media/analysis-services-deploy/aas-deploy-get-server-name.png)
2. 在 SSDT > [方案總管] 中，以滑鼠右鍵按一下專案 > [屬性]。 接著在 [部署]  >  [伺服器] 中，貼上伺服器名稱。   
   
    ![將伺服器名稱貼到部署伺服器屬性](./media/analysis-services-deploy/aas-deploy-deployment-server-property.png)
3. 在 [方案總管] 中，以滑鼠右鍵按一下 [屬性]，然後按一下 [部署]。 系統會提示您登入 Azure。
   
    ![部署至伺服器](./media/analysis-services-deploy/aas-deploy-deploy.png)
   
    部署狀態會出現在 [輸出] 視窗和 [部署] 中。
   
    ![部署狀態](./media/analysis-services-deploy/aas-deploy-status.png)

就是這麼簡單！

## <a name="to-deploy-using-xmla-script"></a>使用 XMLA 指令碼部署
1. 在 SSMS 中，以滑鼠右鍵按一下您要部署的表格式模型資料庫，按一下 [指令碼]  >  [編寫資料庫的指令碼為]  >  **CREATE to** (建立至)，然後選擇一個位置。
2. 在您想要部署查詢的伺服器執行個體上執行查詢。 如果您正部署至相同的伺服器，您必須至少變更 XMLA 指令碼中的 **name** 屬性。  

## <a name="but-something-went-wrong"></a>但發生錯誤
如果在部署中繼資料時部署失敗，則可能是因為 SSDT 無法連線至您的伺服器。 請確定您可以使用 SSMS 連線至您的伺服器。 接著確定專案的 [部署伺服器] 屬性正確。

如果在資料表上部署失敗，則可能是因為您的伺服器無法連線至資料來源。 如果您的組織來源在您組織的網路中為內部部署，請務必安裝[內部部署資料閘道](analysis-services-gateway.md)。

## <a name="next-steps"></a>後續步驟
現在您的伺服器上已部署了表格式模型，您即可連線至該伺服器。 您可以[使用 SSMS 連線至該伺服器](analysis-services-manage.md)以進行管理。 此外，您可以[使用用戶端工具連線至該伺服器 ](analysis-services-connect.md) (如 Power BI、Power BI Desktop 或 Excel 等工具)，並開始建立報告。




<!--HONumber=Nov16_HO3-->


