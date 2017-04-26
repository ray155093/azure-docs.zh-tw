---
title: "將 SSL 憑證新增至 Azure App Service 應用程式 | Microsoft Docs"
description: "了解如何將 SSL 憑證新增至您的 App Service 應用程式。"
services: app-service
documentationcenter: .net
author: ahmedelnably
manager: stefsch
editor: cephalin
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: apurvajo;aelnably
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 00e252e249dbd1a38a4649e435071685860722e4
ms.lasthandoff: 04/07/2017


---

# <a name="add-an-ssl-certificate-to-your-app-service-app"></a>將 SSL 憑證新增至您的 App Service 應用程式
> [!div class="op_single_selector"]
> * [購買 Azure 中的 SSL 憑證](web-sites-purchase-ssl-web-site.md)
> * [使用來自其他地方的 SSL 憑證](web-sites-configure-ssl-certificate.md)
> 
> 

[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 預設會使用 \*.azurewebsites.net 網域的萬用字元憑證來為您的 Web 應用程式啟用 HTTPS。 如果您不打算設定自訂網域，可以直接使用預設的 HTTPS 憑證。 不過，就像所有 [萬用字元網域](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates)一樣，Azure 萬用字元憑證並不如使用自訂網域搭配您自己的憑證那樣安全。

App Service 可讓您在 Azure 入口網站中以簡單的方法購買及管理 SSL 憑證。 

本文說明如何購買並設定 [App Service](http://go.microsoft.com/fwlink/?LinkId=529714) 的 SSL 憑證。 

> [!NOTE]
> 您不能在免費或共用 App Service 方案中裝載的應用程式使用自訂網域名稱的 SSL 憑證。 若要使用 SSL 憑證，您的 Web 應用程式必須裝載於基本、標準或高階版的 App Service 方案。 變更訂用帳戶類型可能會改變您的訂用帳戶費用。 如需詳細資訊，請參閱 [App Service 價格](https://azure.microsoft.com/pricing/details/web-sites/)。
> 
> 

> [!WARNING]
> 請勿使用尚未聯結有效信用卡的訂用帳戶購買 SSL 憑證。 這可能會導致您的訂用帳戶被停用。 
> 
> 

## <a name="prerequisites"></a>必要條件
若要為自訂網域啟用 HTTPS，請先[將自訂網域名稱對應至您的 Azure 應用程式](web-sites-custom-domain-name.md)。

在您要求 SSL 憑證之前，先決定該憑證要保護哪些網域名稱。 這決定了您需要的憑證類型。 如果您只要保護一個網域名稱，例如 contoso.com *或* www.contoso.com，可以使用標準 (基本) 憑證。 如果您需要保護多個網域名稱，例如 contoso.com、www.contoso.com *和* mail.contoso.com，則可以使用[萬用字元憑證](http://en.wikipedia.org/wiki/Wildcard_certificate)。

## <a name="bkmk_purchasecert"></a>購買 SSL 憑證

1. 在 [Azure 入口網站](https://portal.azure.com/)，選取功能表中的 [瀏覽]。 在 [搜尋] 方塊中，輸入 **App Service 憑證**。 在 [搜尋]結果中，選取 **App Service 憑證**。 

   ![使用瀏覽建立憑證](./media/app-service-web-purchase-ssl-web-site/browse.jpg)
   
2. 在 [App Service 憑證] 頁面中，選取 [新增]。 

   ![新增憑證](./media/app-service-web-purchase-ssl-web-site/add.jpg)

3. 輸入 SSL 憑證的 [名稱]。
4. 輸入 [主機名稱]。
   
   > [!WARNING]
   > 這是購買程序中其中一個最重要的部分。 請務必輸入您想要使用此憑證保護的正確主機名稱 (自訂網域名稱)。 請「不要」在主機名稱的開頭加上 "www"。 例如，如果您的自訂網域名稱為 www.contoso.com，則只要在 [主機名稱] 欄位中輸入 **contoso.com**。 憑證會保護 www 和根網域。 
   > 

5. 選取您的 **訂用帳戶**。 
   
   如果您有多個訂用帳戶，在您用於自訂網域或 Web 應用程式的相同訂用帳戶中建立 SSL 憑證。

6. 選取或建立 **資源群組**。
   
   您可以使用資源群組來管理相關的一組 Azure 資源。 當您為應用程式建立角色型存取控制 (RBAC) 規則時，資源群組十分實用。 如需詳細資訊，請參閱「管理 Azure 資源」。

7. 選取 [憑證 SKU]。 
   
   選取符合需求的憑證 SKU，然後按一下 [建立]。 
   
   您可以選擇 App Service 中的兩個 SKU：
   * **S1**︰標準憑證，有效期一年，會自動續訂  
   * **W1**︰萬用字元憑證，有效期一年，會自動續訂       
  
    ![憑證 SKU](./media/app-service-web-purchase-ssl-web-site/SKU.jpg)

    如需詳細資訊，請參閱 [App Service 價格](https://azure.microsoft.com/pricing/details/web-sites/)。

> [!NOTE]
> 建立 SSL 憑證可能需要長達 10 分鐘。 程序包含多個在背景中進行的步驟。  
> 
> 

## <a name="bkmk_StoreKeyVault"></a>將憑證儲存至 Azure Key Vault

1. 當您完成 SSL 憑證的購買，在 Azure 入口網站中，移至 [App Service 憑證] 刀鋒視窗。

   ![憑證已備妥可儲存於 Key Vault](./media/app-service-web-purchase-ssl-web-site/ReadyKV.jpg)
   
   請注意，憑證的狀態是 [暫止發行]。 您必須完成幾個步驟，才能開始使用這個憑證。

2. 在 [憑證內容] 刀鋒視窗中，選取 [憑證設定]。 若要將此憑證儲存在 Key Vault，請選取 [步驟 1：儲存]。
3. 在 [Key Vault 狀態] 刀鋒視窗中，若要選取現有的 Key Vault 來儲存此憑證，請選取 [Key Vault 存放庫]。  若要在相同訂用帳戶和資源群組中建立新的 Key Vault，則選取 [建立新的Key Vault]。

   ![建立新的 Key Vault](./media/app-service-web-purchase-ssl-web-site/NewKV.jpg)
   
   > [!NOTE]
   > 以 Azure Key Vault 儲存此憑證而支付少許費用。 如需詳細資訊，請參閱 [Azure Key Vault 價格](https://azure.microsoft.com/pricing/details/key-vault/)。
   > 
   > 

4. 選取要儲存此憑證的 Key Vault 存放庫之後，選取 [Key Vault 狀態] 刀鋒視窗頂端的 [儲存] 按鈕。  
   
若要確認您的選擇，可以按一下瀏覽器的 [重新整理] 按鈕。 綠色核取記號表示此步驟完成。

## <a name="bkmk_VerifyOwnership"></a>確認網域擁有權

1. 在 [憑證設定] 刀鋒視窗中，選取 [步驟 2：驗證]。
2. 利用下列資訊選取驗證選項。 

App Service 憑證支援三種類型的網域驗證：

   * 網域驗證
   * 郵件驗證
   * 手動驗證

### <a name="domain-verification"></a>網域驗證 
     
「只有」在您已經[從 Azure App Service 購買自訂網域](custom-dns-web-site-buydomains-web-app.md)時，網域驗證才是最方便的程序。

1. 選取 [確認] 以完成這個步驟。
2. 驗證完成之後，若要更新憑證狀態請選取 [重新整理]。 驗證可能需要數分鐘才能完成。

### <a name="mail-verification"></a>郵件驗證
     
使用自訂網域時，系統會傳送一封驗證電子郵件到與此自訂網域相關聯的電子郵件地址。 

1. 開啟電子郵件，並按一下驗證連結，以完成電子郵件驗證步驟。 
2. 如果您需要重新傳送驗證電子郵件，按一下 [重新傳送電子郵件] 按鈕。

### <a name="manual-verification"></a>手動驗證    
     
**HTML 網頁驗證 (僅適用於標準憑證 SKU)**

1. 建立名為 starfield.html 的 HTML 檔案。 檔案的內容應該與網域驗證權杖的名稱完全相同。 (您可以從 [網域驗證狀態] 刀鋒視窗中複製權杖。)
2. 將此檔案上傳至裝載網域的 Web 伺服器的根目錄。 例如，/.well-known/pki-validation/starfield.html。
3.  驗證完成後，若要更新憑證狀態請選取 [重新整理]。 驗證可能需要數分鐘才能完成。

    例如，如果您為具有網域驗證權杖 **tgjgthq8d11ttaeah97s3fr2sh** 的 **contosocertdemo.com** 購買標準憑證，對 **http://contosocertdemo.com/.well-known/pki-validation/starfield.html** 所做的 Web 要求應該會傳回 **tgjgthq8d11ttaeah97s3fr2sh**。

**DNS TXT 記錄驗證**
        
1. 使用 DNS 管理員，在具有與**域驗證權杖**相同值的 **@** 子網域上建立 TXT 記錄。
2. 驗證完成後，若要更新憑證狀態請選取 [重新整理]。 驗證可能需要數分鐘才能完成。
 
   例如，若要對具有主機名稱 **\*.contosocertdemo.com** 或 **\*.subdomain.contosocertdemo.com** 且網域驗證權杖為 **tgjgthq8d11ttaeah97s3fr2sh** 的萬用字元憑證執行驗證，您必須在值為 **tgjgthq8d11ttaeah97s3fr2sh** 的 **contosocertdemo.com** 上建立 TXT 記錄。     

## <a name="bkmk_AssignCertificate"></a>將憑證指派給 App Service 應用程式

> [!NOTE]
> 在完成本節的步驟之前，您必須先將您的自訂網域名稱與應用程式建立關聯。 如需詳細資訊，請參閱[設定 Web 應用程式的自訂網域名稱](web-sites-custom-domain-name.md)。
> 
> 

1. 在 [Azure 入口網站](https://portal.azure.com/)，選取功能表中的 [App Service]。
2. 選取要指派此憑證的應用程式名稱。 
3. 移至 [設定]  >  [SSL 憑證]  >  [匯入 App Service 憑證]，然後選取憑證。

   ![匯入憑證](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

4. 在 [SSL 繫結] 區段按一下 [新增繫結]。
5. 在 [新增 SSL 繫結] 刀鋒視窗中，選取要以 SSL 憑證保護的網域名稱。 選取您要使用的憑證。 您可能也要選擇使用以[伺服器名稱指示 (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication) 為主或以 IP 為主的 SSL。

   ![SSL 繫結](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)
   
    * 以 IP 為主的 SSL 會將伺服器的專用公用 IP 位址對應至網域名稱，以建立憑證與網域名稱的關聯。 當您使用以 IP 為主的 SSL 時，與您的服務相關聯的每個網域名稱 (contoso.com、fabricam.com 等) 都有專用 IP 位址。 這是傳統用來將 SSL 憑證與網頁伺服器建立關聯的方法。
    * 以 SNI 為主的 SSL，是 SSL 和[傳輸層安全性](http://en.wikipedia.org/wiki/Transport_Layer_Security) (TLS) 的擴充。 當您使用以 SNI 為主的 SSL 時，可以多個網域共用相同的 IP 位址。 每個網域都有個別的安全性憑證。 現今大部分的瀏覽器 (包括 Internet Explorer、Chrome、Firefox 和 Opera) 都支援 SNI。 較舊的瀏覽器可能不支援 SNI。 如需 SNI 的詳細資訊，請參閱 Wikipedia 上的[伺服器名稱指示](http://en.wikipedia.org/wiki/Server_Name_Indication) (英文)。

6. 選取 [新增繫結]  以儲存變更並啟用 SSL。

如果您已選取 [以 IP 為主的 SSL]，而且您是使用 A 記錄設定自訂網域，則必須執行下列額外步驟。

1.  設定以 IP 為主的 SSL 繫結之後，您的應用程式將會獲派專用的 IP 位址。 若要找出此 IP 位址，移至 [設定]  >  [自訂網域]。 在 [主機名稱] 區段的上面，您的 IP 位址會列在 [外部 IP 位址]。

   ![以 IP 為主的 SSL](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)
    
  請注意，此 IP 位址與先前用來設定您網域之 A 記錄的虛擬 IP 位址不同。 如果您的應用程式設定為使用以 SNI 為主的 SSL，或如果它未設定為使用 SSL，則此處不會列出任何 IP 位址。

2.  使用網域名稱註冊機構所提供的工具，修改自訂網域名稱的 A 記錄，使其指向上一個步驟中使用的 IP 位址。

3.  若要確認已正確設定憑證，請使用 HTTPS:// 而非 HTTP:// 造訪您的應用程式。

## <a name="bkmk_Export"></a>匯出 App Service 憑證
您可以建立 App Service 憑證的本機 PFX 複本。 當有本機複本時，可使用該憑證來搭配其他 Azure 服務。 如需詳細資訊，請參閱我們的部落格文章[建立 App Service 憑證的本機 PFX 複本](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/) (英文)。

## <a name="bkmk_Renew"></a>自動續訂 App Service 憑證
若要為您的憑證設定「自動續訂」或手動續訂憑證，在 [憑證內容] 刀鋒視窗中選取 [自動續訂設定]。 

![自動續訂設定](./media/app-service-web-purchase-ssl-web-site/autorenew.png)

若要在憑證到期前自動續訂，將 [自動續訂] 設為 [開啟]。 這是預設選項。 如果開啟自動續訂，我們就會在憑證到期前 90 天開始嘗試續訂您的憑證。 如果您已使用 Azure 入口網站在您的 App Service 應用程式上建立 SSL 繫結，則在新憑證備妥之後，也會一併更新這些繫結 (就像「重設金鑰」和「同步處理」案例一樣)。 

如果您想要手動處理續訂，請將 [自動續訂] 設為 [關閉]。 只有當「App Service 憑證」將在 90 天內到期的情況下，您才能手動更新該憑證。

## <a name="bkmk_Rekey"></a>重設金鑰和同步處理憑證

1. 如果您需要重設憑證的金鑰 (為了安全性)，只要選取 [憑證內容] 刀鋒視窗中的 [重設金鑰和同步處理]。 
2. 選取 [重設金鑰]。 此程序最多可能需要 10 分鐘才能完成。 

   ![重設金鑰 SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.jpg)

以下是一些關於重設金鑰的資訊：

* 重設憑證的金鑰，會以新憑證變更憑證。 新憑證是由憑證授權單位發行。
* 在憑證的存留期期間，不會向您收取重設金鑰的費用。 
* 重設憑證的金鑰會使憑證處於**暫止發行**狀態。 
* 憑證備妥後，為了避免服務中斷，請確定使用此憑證同步處理您的資源。
* 同步處理選項不適用於尚未指派到 Web 應用程式的憑證。 

## <a name="next-steps"></a>後續步驟

* [使用 HTTPS 保護應用程式的自訂網域](web-sites-configure-ssl-certificate.md)
* [在 Azure App Service 中購買並設定自訂網域名稱](custom-dns-web-site-buydomains-web-app.md)
* [Microsoft Azure 信任中心](https://azure.microsoft.com/en-us/support/trust-center/)

> [!NOTE]
> 若要在註冊 Azure 帳戶之前先開始使用 Azure App Service，請前往[試用 App Service](https://azure.microsoft.com/try/app-service/)。 您可以在 App Service 中建立短期的入門 Web 應用程式。 不需要信用卡，無需承諾。
> 
> 

