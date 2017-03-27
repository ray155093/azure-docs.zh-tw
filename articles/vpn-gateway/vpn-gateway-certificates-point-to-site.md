---
title: "建立點對站的自我簽署憑證：PowerShell: Azure | Microsoft Docs"
description: "本文中的步驟用來建立自我簽署的根憑證、匯出公開金鑰，以及使用 PowerShell 在 Windows 10 上產生用戶端憑證。"
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 27b99f7c-50dc-4f88-8a6e-d60080819a43
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: cb66edd0c0ff1f0b78232233719dd44329584c78
ms.lasthandoff: 03/15/2017


---
# <a name="create-a-self-signed-root-certificate-for-point-to-site-connections-using-powershell"></a>使用 PowerShell 建立點對站連線的自我簽署根憑證

點對站連線使用憑證進行驗證。 當您設定點對站連線時時，您需要將根憑證的公開金鑰 (.cer 檔案) 上傳至 Azure。 本文協助您建立自我簽署的根憑證、匯出公開金鑰，以及產生和安裝用戶端憑證。

> [!NOTE]
> 先前，makecert 是建立自我簽署根憑證，並產生用於點對站連線之用戶端憑證的建議方法。 您現在可以使用 PowerShell 來建立這些憑證。 使用 PowerShell 的一個優點是能夠建立 SHA-2 憑證。 
>
>

## <a name="rootcert"></a>建立自我簽署根憑證

下列步驟將逐步引導您使用 PowerShell 建立自我簽署根憑證。 需要有 Windows 10，才能完成下列步驟。 這些步驟中所用的 Cmdlet 和參數屬於 Windows 10 作業系統，不屬於 PowerShell 版本。

1. 從執行 Windows 10 的電腦，以提高的權限開啟 Windows PowerShell 主控台。
2. 使用下列範例建立自我簽署的根憑證。 下列範例會建立名為 'P2SRootCert' 的自我簽署的根憑證，其自動安裝在 'Certificates-Current User\Personal\Certificates' 中。 開啟 *certmgr.msc* 即可檢視憑證。

        $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
        -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
        -HashAlgorithm sha256 -KeyLength 2048 `
        -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign

### <a name="cer"></a>取得公開金鑰

點對站連線需要公開金鑰 (.cer) 上傳至 Azure。 下列步驟可協助您匯出自我簽署根憑證的 .cer 檔案。

1. 若要取得憑證的 .cer 檔案，請開啟 [certmgr.msc] 。 找出自我簽署的根憑證，通常位於 '[憑證 - 目前的使用者]\[個人]\[憑證]' 中，然後按一下滑鼠右鍵。 按一下 [所有工作]，然後按一下 [匯出]。 這會開啟 [憑證匯出精靈] 。
2. 在精靈中，按 [下一步]。 選取 [否，不要匯出私密金鑰]，然後按 [下一步]。
3. 在 [匯出檔案格式] 頁面上，選取 [Base-64 編碼 X.509 (.CER)]，然後按 [下一步]。 
4. 在 [要匯出的檔案] 中，[瀏覽] 到您要匯出憑證的位置。 針對 [檔案名稱] ，請為憑證檔案命名。 然後按 [下一步] 。
5. 按一下 [完成]  以匯出憑證。 您將會看到 [匯出成功]。 按一下 [確定] 以關閉精靈。

### <a name="to-export-a-self-signed-root-certificate-optional"></a>匯出自我簽署根憑證 (選用)
您可能想要匯出自我簽署的根憑證，並將它安全地儲存。 如有需要，您可以稍後在另一部電腦上安裝這個自我簽署憑證，然後產生更多用戶端憑證，或匯出另一個 .cer 檔案。

若要將自我簽署的根憑證匯出為 .pfx，請選取根憑證，並使用 [匯出用戶端憑證](#clientexport) 中所述的步驟匯出。

## <a name="clientcert"></a>產生用戶端憑證 

每個使用點對站連線至 VNet 的用戶端電腦都必須安裝用戶端憑證。 您可以從自我簽署根憑證產生用戶端憑證，然後匯出及安裝用戶端憑證。 如果未安裝用戶端憑證，則驗證會失敗。 

下列步驟將逐步引導您完成從自我簽署的根憑證產生用戶端憑證。 您可以從相同根憑證產生多個用戶端憑證。 當您使用下列步驟產生用戶端憑證時，用戶端憑證會自動安裝在您用來產生憑證的電腦上。 如果您想要在另一部用戶端電腦上安裝用戶端憑證，您可以匯出憑證。

需要有 Windows 10，才能完成下列步驟。 這些步驟中所用的 Cmdlet 和參數屬於 Windows 10 作業系統，不屬於 PowerShell 版本。

### <a name="example-1"></a>範例 1

此範例會使用從上一節宣告的 '$cert' 變數。 如果您在建立自我簽署根憑證之後關閉 PowerShell 主控台，或是在新的 PowerShell 主控台工作階段中建立其他用戶端憑證，請使用範例 2 中的步驟。

修改並執行範例以產生用戶端憑證。 如果您執行下列範例，但未加以修改，結果會是名為 'P2SChildCert' 的用戶端憑證。  如果您要將子憑證命名為其他名稱，請修改 CN 值。 執行這個範例時，請勿變更 TextExtension。 您產生的用戶端憑證會自動安裝在您電腦的 'Certificates - Current User\Personal\Certificates' 中。

    New-SelfSignedCertificate -Type Custom -KeySpec Signature `
    -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 -KeyLength 2048 `
    -CertStoreLocation "Cert:\CurrentUser\My" `
    -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

### <a name="example-2"></a>範例 2

如果您要建立其他用戶端憑證，或者不是使用您用來建立自我簽署根憑證的相同 PowerShell 工作階段，請使用下列步驟︰

1. 識別安裝在電腦上的自我簽署根憑證。 此 Cmdlet 會傳回安裝於電腦上的憑證清單。

        Get-ChildItem -Path “Cert:\CurrentUser\My”

2. 從傳回的清單尋找主體名稱，然後將其旁邊的指紋複製到文字檔。 在下列範例中，有兩個憑證。 CN 名稱是您要從中產生子憑證之自我簽署根憑證的名稱。 在此例中為 'P2SRootCert'。

        Thumbprint                                Subject
        ----------                                -------
        AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
        7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert


3. 使用上一個步驟中的指紋，為根憑證宣告一個變數。 將 THUMBPRINT 替換為您要從中產生子憑證之根憑證的指紋。

        $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"

    例如，使用上一個步驟中 P2SRootCert 的指紋，變數會如下所示︰

        $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
        

4.  修改並執行範例以產生用戶端憑證。 如果您執行下列範例，但未加以修改，結果會是名為 'P2SChildCert' 的用戶端憑證。 如果您要將子憑證命名為其他名稱，請修改 CN 值。 執行這個範例時，請勿變更 TextExtension。 您產生的用戶端憑證會自動安裝在您電腦的 'Certificates - Current User\Personal\Certificates' 中。

        New-SelfSignedCertificate -Type Custom -KeySpec Signature `
        -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
        -HashAlgorithm sha256 -KeyLength 2048 `
        -CertStoreLocation "Cert:\CurrentUser\My" `
        -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

## <a name="clientexport"></a>匯出用戶端憑證   

當您產生用戶端憑證時，它會自動安裝於您用來產生它的電腦上。 如果您想要在另一部用戶端電腦上安裝用戶端憑證，您必須匯出您所產生的用戶端憑證。                              

1. 若要匯出用戶端憑證，請開啟 **certmgr.msc**。 根據預設，您產生的用戶端憑證位於 'Certificates - Current User\Personal\Certificates'。 以滑鼠右鍵按一下要匯出的用戶端憑證，然後依序按一下 [所有工作] 和 [匯出]。 這會開啟 [憑證匯出精靈] 。
2. 在精靈中，按一下 [下一步]，接著選取 [是，匯出私密金鑰]，然後按 [下一步]。
3. 在 [匯出檔案格式]  頁面上，您可以保留選取預設值。 然後按 [下一步] 。 
4. 在 [安全性]  頁面上，您必須保護私密金鑰。 如果您選取要使用密碼，請務必記錄或牢記您為此憑證設定的密碼。 然後按 [下一步] 。
5. 在 [要匯出的檔案] 中，[瀏覽] 到您要匯出憑證的位置。 針對 [檔案名稱] ，請為憑證檔案命名。 然後按 [下一步] 。
6. 按一下 [完成]  以匯出憑證。    

## <a name="install"></a>安裝匯出的用戶端憑證

如果您想要從不同於用來產生用戶端憑證的用戶端電腦建立 P2S 連線，您需要安裝用戶端憑證。 安裝用戶端憑證時，您需要匯出用戶端憑證時所建立的密碼。

1. 找出 *.pfx* 檔案並複製到用戶端電腦。 在用戶端電腦上，按兩下 *.pfx* 檔案以安裝。 [存放區位置] 保留 [目前使用者]，然後按 [下一步]。
2. 在 [要匯入的檔案]  頁面上，請勿進行任何變更。 按 [下一步] 。
3. 在 [私密金鑰保護] 頁面上，如果您已使用密碼，請輸入憑證的密碼，或確認安裝憑證的安全性主體是否正確，然後按 [下一步]。
4. 在 [憑證存放區] 頁面上，保留預設的位置，然後按 [下一步]。
5. 按一下 [完成] 。 在憑證安裝的 [安全性警告] 上，按一下 [是]。 您可以安心地按一下 [是]，因為您已經產生憑證。 現在已成功匯入憑證。

## <a name="next-steps"></a>後續步驟
繼續使用您的點對站設定。 

* 如需 **Resource Manager** 部署模型步驟，請參閱[設定 VNet 的點對站連線](vpn-gateway-howto-point-to-site-resource-manager-portal.md)。 
* 如需**傳統**部署模型步驟，請參閱[設定 VNet 的點對站 VPN 連線 (傳統)](vpn-gateway-howto-point-to-site-classic-azure-portal.md)。


