<properties
	pageTitle="Azure AD Connect︰同步處理服務執行個體 | Microsoft Azure"
	description="本頁記載 Azure AD 執行個體的特殊考量。"
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/10/2016"
	ms.author="andkjell"/>

# Azure AD Connect：執行個體的特殊考量
Azure AD Connect 最常搭配 Azure AD 和 Office 365 的全球執行個體。但還有其他執行個體，而它們有不同的 URL 需求和其他特殊考量。

## Microsoft Cloud Germany
[Microsoft Cloud Germany](http://www.microsoft.de/cloud-deutschland) 是由德國資料信託所運作的最高雲端。

此雲端目前為預覽狀態。許多您通常可以自行完成的案例 (例如驗證網域) 必須由操作人員完成。如需如何加入預覽的詳細資訊，請連絡您當地的 Microsoft 代表。

要在 Proxy 伺服器中開啟的 URL |
--- |
*.microsoftonline.de |
*.windows.net |
\+憑證撤銷清單 |

當您登入 Azure AD 目錄時，您必須使用 onmicrosoft.de 網域中的帳戶。

Microsoft Cloud Germany 目前沒有的功能︰

- 無法使用 Azure AD Connect Health。
- 無法使用自動更新。
- 無法使用密碼回寫。

## Microsoft Azure Government 雲端
[Microsoft Azure Government 雲端](https://azure.microsoft.com/features/gov/)是用於美國政府的雲端。

此雲端是以舊版的 DirSync 提供支援。從 Azure AD Connect 的組建 1.1.180 起，支援新一代的雲端。這一代使用僅限美國的端點，而且有不同的 URL 清單要在您的 Proxy 伺服器中開啟。

要在 Proxy 伺服器中開啟的 URL |
--- |
*.microsoftonline.com |
*.gov.us.microsoftonline.com |
\+憑證撤銷清單 |

Azure AD Connect 將無法自動偵測您的 Azure AD 目錄位於 Government 雲端。而您在安裝 Azure AD Connect 時需要採取下列動作。

1. 開始 Azure AD Connect 安裝。
2. 當您看見您應在其中接受 EULA 的第一頁時，請勿繼續，但讓安裝精靈保持執行中。
3. 啟動 regedit 並將登錄機碼 `HKLM\SOFTWARE\Microsoft\Azure AD Connect\AzureInstance` 變更為 `2` 值。
4. 回到 Azure AD Connect 安裝精靈，接受 EULA 並繼續。在安裝期間，務必使用**自訂組態**安裝路徑 (而非快速安裝)。然後如往常一樣繼續安裝。

Microsoft Azure Government 雲端目前沒有的功能︰

- 無法使用 Azure AD Connect Health。
- 無法使用自動更新。
- 無法使用密碼回寫。

## 後續步驟
深入了解[整合內部部署身分識別與 Azure Active Directory](active-directory-aadconnect.md)。

<!---HONumber=AcomDC_0518_2016-->