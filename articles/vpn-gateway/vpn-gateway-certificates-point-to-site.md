<properties 
   pageTitle="使用 makecert 建立點對站虛擬網路交叉部署連線的自我簽署憑證 |Microsoft Azure"
   description="本文包含使用 makecert 在 Windows 10 上建立自我簽署憑證的步驟。"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/15/2016"
   ms.author="cherylmc" />

# 使用點對站連線的自我簽署憑證

本文將協助您使用 makecert 建立自我簽署憑證，然後再從它產生用戶端憑證。下列步驟專為 Windows 10 上的 makecert 所撰寫。Makecert 已經驗證以建立與 P2S 連線相容的憑證。

對於 P2S 連線，針對憑證的慣用方法是使用您的企業憑證解決方案，確保使用具有通用名稱值格式「name@yourdomain.com」核發用戶端憑證，而非使用「NetBIOS 網域名稱\\使用者名稱」格式。

如果您沒有企業解決方案，則需要自我簽署憑證以允許 P2S 用戶端連接到虛擬網路。我們知道 makecert 已經過時，但它仍是建立與 P2S 連線相容的自我簽署憑證的有效方法。

## 建立自我簽署憑證

Makecert 是建立自我簽署憑證的其中一種方式。下列步驟將逐步引導您使用 makecert 建立自我簽署憑證。這些並非部署模型特定的步驟。它們同樣適用於資源管理員和傳統部署模型。

### 建立自我簽署憑證

1. 從執行 Windows 10 的電腦上下載並安裝[適用於 Windows 10 的 Windows 軟體開發套件 (SDK)](https://dev.windows.com/zh-TW/downloads/windows-10-sdk)。

2. 安裝之後，您可以在下列路徑中找到 makecert.exe 公用程式：C:\\Program Files (x86)\\Windows Kits\\10\\bin<arch>。
		
	範例：
	
		C:\Program Files (x86)\Windows Kits\10\bin\x64\makecert.exe

3. 接下來，在您電腦上的個人憑證存放區中建立並安裝憑證。下列範例會建立對應的 *.cer* 檔案，您在設定 P2S 時會將此檔案上傳至 Azure。以系統管理員身分執行下列命令，其中 *CertificateName* 是您要用於憑證的名稱。<br><br>如果您執行下列範例而不進行任何變更，將會產生憑證與對應的檔案 *CertificateName.cer*。您可以在執行命令所在的目錄中找到 .cer 檔案。憑證會位於您的憑證 - 目前使用者\\個人\\憑證。

    	makecert -sky exchange -r -n "CN=CertificateName" -pe -a sha1 -len 2048 -ss My "CertificateName.cer"

4. 自我簽署憑證是用來建立用戶端憑證。當您針對自我簽署憑證上傳 .cer 檔案做為 P2S 設定的一部分時，您即告知 Azure 去信任用戶端電腦正在使用的憑證。<br><br>任何安裝用戶端憑證並已設定適當 VPN 用戶端設定的電腦，將可以透過 P2S 連接到您的虛擬網路。基於這個原因，您必須確定用戶端憑證只有在需要時才會產生並安裝，而且此自我簽署憑證已安全地備份和儲存。
 

## 建立並安裝用戶端憑證

您並不會將自我簽署憑證安裝在用戶端電腦上。您需要從自我簽署憑證產生用戶端憑證。您接著會將用戶端憑證匯出並安裝到用戶端電腦。下列步驟並非針對特定部署模型。它們同樣適用於資源管理員和傳統部署模型。

### 第 1 部分 - 從自我簽署憑證產生用戶端憑證

下列步驟將逐步引導您完成從自我簽署憑證產生用戶端憑證的其中一種方法。您也可以從相同憑證產生多個用戶端憑證。然後每個用戶端憑證可以匯出及安裝在用戶端電腦上。

1. 在用來建立自我簽署憑證的相同電腦上，以系統管理員身分開啟命令提示字元。

2. 將目錄切換至您要儲存用戶端憑證檔案的位置。*CertificateName* 是指您產生的自我簽署憑證。如果您執行下列範例 (但將 CertificateName 變更為您的憑證名稱)，就會在個人憑證存放區中產生一個名為 "ClientCertificateName" 的用戶端憑證。

3. 輸入以下命令：

    	makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "CertificateName" -is my -a sha1

4. 所有憑證都會儲存在電腦上的憑證 - 目前使用者\\個人\\憑證存放區中。您可以視需要根據這個程序來產生許多用戶端憑證。

### 第 2 部分 - 匯出用戶端憑證

1. 若要匯出用戶端憑證，請開啟 **certmgr.msc**。以滑鼠右鍵按一下要匯出的用戶端憑證，然後依序按一下 [所有工作] 和 [匯出]。這會開啟 [憑證匯出精靈]。

2. 在精靈中，按一下 [下一步]，接著選取 [是，匯出私密金鑰]，然後按 [下一步]。

3. 在 [匯出檔案格式] 頁面上，您可以保留選取預設值。然後按 [下一步]。
 
4. 在 [安全性] 頁面上，您必須保護私密金鑰。如果您選取要使用密碼，請務必記錄或牢記您為此憑證設定的密碼。然後按 [下一步]。

5. 在 [要匯出的檔案] 中，[瀏覽] 到您要匯出憑證的位置。針對 [檔案名稱]，請為憑證檔案命名。然後按 [下一步]。

6. 按一下 [完成] 以匯出憑證。

### 第 3 部分 - 安裝用戶端憑證

您想要使用點對站連線與虛擬網路連線的每個用戶端，都必須安裝用戶端憑證。此憑證是必要的 VPN 設定封裝以外的項目。下列步驟將逐步引導您手動安裝用戶端憑證。

1. 找出 *.pfx* 檔案並複製到用戶端電腦。在用戶端電腦上，按兩下 *.pfx* 檔案以安裝。將 [存放區位置] 保留為 [目前使用者]，然後按 [下一步]。

2. 在 [要匯入的檔案] 頁面上，請勿進行任何變更。按 [下一步]。

3. 在 [私密金鑰保護] 頁面上，如果您已使用密碼，請輸入憑證的密碼，或確認安裝憑證的安全性主體是否正確，然後按 [下一步]。

4. 在 [憑證存放區] 頁面上，保留預設的位置，然後按 [下一步]。

5. 按一下 [完成]。在憑證安裝的 [安全性警告] 上，按一下 [是]。現在已成功匯入憑證。

## 後續步驟

繼續使用您的點對站設定。

- 針對 **Resource Manager** 部署模型步驟，請參閱[使用 PowerShell 設定虛擬網路的點對站連線](vpn-gateway-howto-point-to-site-rm-ps.md)。
- 針對「傳統」部署模型步驟，請參閱[設定 VNet 的點對站 VPN 連線](vpn-gateway-point-to-site-create.md)。

<!---HONumber=AcomDC_0817_2016-->