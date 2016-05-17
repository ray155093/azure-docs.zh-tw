<properties
   pageTitle="Azure 自動化安全性"
   description="本文概述 Azure 自動化中的自動化安全性和自動化帳戶可用的不同驗證方法。"
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="自動化安全性, 安全的自動化" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/10/2016"
   ms.author="magoedte" />

# Azure 自動化安全性
Azure 自動化可讓您針對 Azure、內部部署以及其他雲端提供者 (例如 Amazon Web Services (AWS)) 的資源自動執行工作。為了讓 Runbook 執行其必要動作，其必須有權能以訂用帳戶內的最少必要權限，安全地存取資源。本文將介紹 Azure 自動化支援的各種驗證案例，並說明如何根據您要管理的一或多個環境來開始使用。

## 自動化帳戶概觀
當您第一次啟動 Azure 自動化時，您必須建立至少一個自動化帳戶。自動化帳戶可讓您將您的自動化資源 (Runbook、資產、組態) 與其他自動化帳戶中包含的資源區隔開來。您可以使用自動化帳戶將資源分成個別的邏輯環境。例如，您可能會針對開發、生產和內部部署環境各自使用一個帳戶。Azure 自動化帳戶與 Microsoft 帳戶或您在 Azure 訂用帳戶中建立的帳戶不同。

每個自動化帳戶的自動化資源都會與單一 Azure 區域相關聯，但自動化帳戶可管理任何區域中的資源。在不同區域建立自動化帳戶的主要原因會是，若您擁有需要區隔資料和資源到特定區域的原則時。

>[AZURE.NOTE]使用 Azure 入口網站所建立的自動化帳戶以及其所包含的資源，無法在 Azure 傳統入口網站中存取。如果您想使用 Windows PowerShell 來管理這些帳戶或它們的資源，您必須使用 「Azure 資源管理員」模組。

在 Azure 自動化中使用 Azure Resource Manager (ARM) 和 Azure Cmdlet 針對資源所執行的工作，皆必須使用 Azure Active Directory 組織身分識別的認證型驗證向 Azure 進行驗證。憑證型驗證是 Azure 服務管理 (ASM) 模式的原始驗證方法，但其設定步驟很複雜。我們在 2014 年重新引進使用 Azure AD 使用者向 Azure 進行驗證的方法，不僅是為了簡化驗證帳戶的設定程序，也是為了能夠以同時適用於 ASM 和 ARM 模式的單一使用者帳戶向 Azure 進行非互動式驗證。

我們最近發行了另一項更新，現在我們會在建立自動化帳戶時自動建立 Azure AD 服務主體物件。這稱為「Azure 執行身分帳戶」，而且是使用 Azure Resource Manager 自動執行 Runbook 時的預設驗證方法。

ARM 模式提供了角色存取控制來對 Azure AD 使用者帳戶和服務主體授與允許的動作，並驗證該服務主體。若要取得有助於開發自動化權限管理模型的進一步資訊，請閱讀 [Azure 自動化中的角色型存取控制](../automation/automation-role-based-access-control.md)一文。

在資料中心的混合式 Runbook 背景工作角色上或針對 AWS 中的運算服務所執行的 Runbook，不能使用向 Azure 資源進行驗證的 Runbook 通常會使用的相同方法。這是因為這些資源是在 Azure 外執行，因此將需要在自動化中定義自己的安全性認證才能向它們要在本機存取的資源進行驗證。

## 驗證方法

下表摘要說明 Azure 自動化支援的每個環境所適用的不同驗證方法，以及將會說明如何設定 Runbook 驗證的文章。

方法 | Environment | 文章
----------|----------|----------
Azure AD 使用者帳戶 | Azure 資源管理員和 Azure 服務管理 | [使用 Azure AD 使用者帳戶驗證 Runbook](../automation/automation-sec-configure-aduser-account.md)
Azure AD 服務主體物件 | Azure 資源管理員 | [使用 Azure 執行身分帳戶驗證 Runbook](../automation/automation-sec-configure-azure-runas-account.md)
Windows 驗證 | 內部部署資料中心 | [驗證混合式 Runbook 背景工作角色的 Runbook](../automation/automation-hybrid-runbook-worker.md)
AWS 認證 | Amazon Web Services | [使用 Amazon Web Services (AWS) 驗證 Runbook](../automation/automation-sec-configure-aws-account.md)

<!---HONumber=AcomDC_0511_2016-->