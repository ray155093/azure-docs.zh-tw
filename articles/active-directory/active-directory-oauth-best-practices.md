<properties
	pageTitle="Azure AD 中 OAuth 2.0 的最佳作法 | Microsoft Azure"
	description="此文章說明在 Azure Active Directory 中開發使用 OAuth 2.0 之應用程式的最佳作法。"
	services="active-directory"
	documentationCenter=".net"
	authors="priyamohanram"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="priyamo"/>


# Azure Active Directory 中 OAuth 2.0 的最佳作法

[AZURE.INCLUDE [active-directory-protocols](../../includes/active-directory-protocols.md)]

## 使用 State 參數

`state` 參數是用戶端在要求中傳送之隨機產生的非可重複使用值 (通常是 GUID)。這是選擇性參數，但強烈建議在取得授權碼的要求中使用。回應也包含相同的 `state` 值，而應用程式必須在使用回應之前驗證狀態值完全相同。

`state` 參數可協助偵測對用戶端的跨網站要求偽造 (CSRF) 攻擊。如需 CSRF 攻擊的詳細資訊，請參閱 OAuth 2.0 授權架構中的[跨網站偽造要求](https://tools.ietf.org/html/rfc6749#section-10.12)。

## 快取存取權杖

為了減少來自用戶端應用程式和與其相關延遲的網路呼叫，用戶端應用程式應該快取存取權杖達 OAuth 2.0 回應中所指定的權杖存留期。若要判斷權杖存留期，請使用 `expires_in` 或 `expires_on` 參數值。

如果 Web API 資源傳回 `invalid_token` 錯誤碼，這可能表示資源判定權杖已過期。如果用戶端和資源的時鐘時間不同 (稱為「時間偏差」)，在從用戶端快取中清除權杖之前，資源可能會將權杖視為已過期。如果發生這種情況，請從快取中清除權杖，即使它仍在其計算的存留期內，也是如此。

## 處理重新整理權杖

重新整理權杖並沒有指定的存留期。一般而言，重新整理權杖的存留期相當長。不過，在某些情況下，重新整理權杖會過期、遭到撤銷或對要執行的動作缺乏足夠的權限。用戶端應用程式必須預期並正確處理權杖發行端點所傳回的錯誤。

當您收到具有重新整理權杖錯誤的回應時，請捨棄目前的重新整理權杖並要求新的授權碼或存取權杖。特別是，在授權碼授與流程中使用重新整理權杖時，如果您收到的回應含有 `interaction_required` 或 `invalid_grant` 錯誤代碼，請捨棄重新整理權杖並要求新的授權碼。

## 確認資源識別碼參數

用戶端應用程式必須確認回應中 `resource_id` 參數的值。否則，惡意服務可能會引發提升權限攻擊。

 防止攻擊的建議策略是確認 resource\_id 符合要存取的 Web API URL 的基礎。例如，如果要存取 https://service.contoso.com/data，`resource_id` 可以是 https://service.contoso.com/。除非有可確認識別碼的可靠替代方法，否則用戶端應用程式必須拒絕開頭不是基礎 URL 的 `resource_id`。

## 權杖發行與重試指引

Azure AD 支援用戶端可以查詢的數個權杖發行端點。使用下列指導方針，對這些端點實作重試邏輯，以處理未預期的網路或伺服器失敗。

回應重試的失敗通常會對 Azure AD 端點的要求傳回 HTTP 500 系列錯誤代碼。在某些情況下，用戶端是應用程式或服務，會對 Azure AD 自動提出要求。在其他情況下，例如使用 WS 同盟通訊協定的 Web 型同盟，用戶端是 Web 瀏覽器，而使用者必須手動重試。

### 根據 HTTP 500 系列錯誤回應實作重試邏輯

Active Directory 存取控制服務 (ACS) 傳回 HTTP 500 系列錯誤時，強烈建議使用重試邏輯。其中包括：

- HTTP 錯誤 500 - 內部伺服器錯誤
- HTTP 錯誤 502 - 錯誤的閘道
- HTTP 錯誤 503 - 服務無法使用
- HTTP 錯誤 504 - 閘道逾時

雖然可以在重試邏輯中明確列出 HTTP 代碼，在傳回任何 HTTP 500 系列錯誤時叫用重試邏輯即已足夠。

通常，傳回 HTTP 400 系列錯誤碼時不建議使用重試邏輯。來自 ACS 的 400 系列 HTTP 錯誤回應碼表示要求無效，而且需要修訂。

應該依 HTTP 錯誤碼，例如 HTTP 504 (外部伺服器逾時) 或 HTTP 500 錯誤碼系列，而不是依 ACS 錯誤碼，例如 ACS90005 來觸發重試邏輯。ACS 錯誤碼可供參考，而且可能隨時變更。

### 重試應該使用倒數計時器以獲得最佳流量控制

當用戶端收到 HTTP 500 系列錯誤時，用戶端應該在重試要求之前先行等待。為了獲得最佳結果，建議對每個後續的重試增加此期間。這個方法可讓您快速地解決暫時性錯誤，同時最佳化需要較長時間才能解決的暫時性網路或伺服器問題的要求率。

例如，使用指數型倒數計時器，其中，重試會隨著每個執行個體以指數方式增加，例如重試 1：1 秒，重試 2：2 秒、重試 3：4 秒等等。

根據您的使用者經驗需求調整重試次數和每次重試之間的時間。不過，我們建議在五分鐘的時段中最多五個重試。逾時所造成的失敗需要較長時間才能解決。

## 後續步驟

[Active Directory 驗證程式庫 (ADAL)](active-directory-authentication-libraries.md)

<!---HONumber=AcomDC_0601_2016-->