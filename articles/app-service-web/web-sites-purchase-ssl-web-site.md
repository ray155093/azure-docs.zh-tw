<properties
	pageTitle="購買並設定您的 Azure App Service 的 SSL 憑證"
	description="了解如何購買並設定您的 Azure App Service 的 SSL 憑證。"
	services="app-service"
	documentationCenter=".net"
	authors="apurvajo"
	manager="stefsch"
	editor="cephalin"
	tags="buy-ssl-certificates"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/01/2016"
	ms.author="apurvajo"/>

#購買並設定您的 Azure App Service 的 SSL 憑證

> [AZURE.SELECTOR]
- [在 Azure 中購買 SSL 憑證](web-sites-purchase-ssl-web-site.md)
- [使用來自其他位置的 SSL 憑證](web-sites-configure-ssl-certificate.md)

根據預設，**[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)** 已使用 *.azurewebsites.net 網域的萬用字元憑證來啟用您 Web 應用程式的 HTTPS。如果您不打算設定自訂網域，您可以直接利用預設的 HTTPS 憑證。但是，就像*[所有萬用字元網域](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates)一樣，這並不如使用自訂網域搭配自己的憑證那麼安全。Azure App Service 現在提供購買及管理 SSL 憑證真正簡易的方法，就是從 Azure 入口網站，完全不用離開入口網站。本文說明如何使用 3 個簡單的步驟，來購買並設定 **[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)** 的 SSL 憑證。

> [AZURE.NOTE]
自訂網域名稱的 SSL 憑證無法搭配免費和共用的 Web 應用程式使用。您必須將 Web 應用程式設定為 [基本]、[標準] 或 [進階] 模式，但這可能會變更您的訂用帳戶費用。如需詳細資訊，請參閱 **[Web Apps 定價詳細資料](https://azure.microsoft.com/pricing/details/web-sites/)**。



##<a name="bkmk_Overview"></a>概觀
> [AZURE.NOTE]
請不要嘗試使用沒有與作用中信用卡相關聯的訂用帳戶購買 SSL 憑證。這可能會導致您的訂用帳戶被停用。

##<a> 購買、儲存及指派您自訂網域的 SSL 憑證 </a>
若要為自訂網域 (如 contoso.com) 啟用 HTTPS，首先必須**[在 Azure App Service 中設定自訂網域名稱](web-sites-custom-domain-name.md)**。

在要求 SSL 憑證之前，您必須先決定有哪些網域名稱要由該憑證保護。這將決定您必須取得的憑證類型。如果您只需要保護單一網域名稱 (如 contoso.com 或 www.contoso.com)，則標準 (基本) 憑證就已足夠。如果您需要保護多個網域名稱 (如 contoso.com、www.contoso.com 與 mail.contoso.com)，則您可以取得**[萬用字元憑證](http://en.wikipedia.org/wiki/Wildcard_certificate)**

##<a name="bkmk_purchasecert"></a>步驟 0：訂購 SSL 憑證

在此步驟中，您將學習如何訂購您選擇的 SSL 憑證。

1.	在 **[Azure 入口網站](https://portal.azure.com/)**中按一下 [瀏覽]、在搜尋列中輸入「App Service 憑證」、從結果中選取 [App Service 憑證]，然後按一下 [新增]。

    ![插入使用瀏覽建立的影像](./media/app-service-web-purchase-ssl-web-site/browse.jpg)

    ![插入使用瀏覽建立的影像](./media/app-service-web-purchase-ssl-web-site/add.jpg)

2.	為 SSL 憑證提供**易記名稱**。

3.	輸入**主機名稱**
> [AZURE.NOTE]
    這是購買程序中其中一個最重要的部分。請務必輸入您想要使用此憑證保護的正確主機名稱 (自訂網域)。**請勿**在主機名稱上附加 WWW。例如，如果您的自訂網域名稱為 www.contoso.com，則只要在 [主機名稱] 欄位中輸入 contoso.com，憑證會保護 www 和根網域。
    
4.	選取您的**訂用帳戶**。

    如果您有多個訂用帳戶，請務必在您用於自訂網域或 Web 應用程式的相同訂用帳戶中建立 SSL 憑證。
       
5.	選取或建立**資源群組**。

    資源群組可讓您以單位的形式管理相關的 Azure 資源，並可在您為應用程式建立角色型存取控制 (RBAC) 規則時發揮效用。如需詳細資訊，請參閱「管理 Azure 資源」。
     
6.	選取 [憑證 SKU]。

    最後，選取符合您需求的憑證 SKU，然後按一下 [建立]。現在，Azure App Service 可讓您購買兩個不同 SKU • S1 – 1 年有效期和自動續約的標準憑證 • W1 – 1 年有效期和自動續約的萬用字元憑證。如需詳細資訊，請參閱 **[Web Apps 定價詳細資料](https://azure.microsoft.com/pricing/details/web-sites/)**。

![插入憑證 SKU 的影像](./media/app-service-web-purchase-ssl-web-site/SKU.jpg)

> [AZURE.NOTE]
建立 SSL 憑證需要 1 - 10 分鐘。此程序會在背景中執行多個步驟，若手動執行這些步驟有些繁複。

##<a name="bkmk_StoreKeyVault"></a>步驟 1：將憑證儲存至 Azure 金鑰保存庫

在此步驟中，您將學習如何訂購 SSL 憑證，並將其儲存至您選擇的 Azure 金鑰保存庫。

1.	完成 SSL 憑證購買程序之後，您必須手動開啟 [App Service 憑證] 資源刀鋒視窗，方法是再瀏覽一次 (請參閱上述「步驟 1」)

    ![插入準備在 KV 中儲存的影像](./media/app-service-web-purchase-ssl-web-site/ReadyKV.jpg)

    您會注意到憑證狀態是「暫止發行」，因為您必須先完成一些其他的步驟，才能開始使用此憑證。
 
2. 按一下 [憑證內容] 刀鋒視窗內的 [憑證設定]，然後按一下 [步驟 1：儲存]，將此憑證儲存至 Azure 金鑰保存庫。

3.	從 [金鑰保存庫狀態] 刀鋒視窗中按一下 [金鑰保存庫儲存機制]，選擇要儲存此憑證的現有金鑰保存庫，或者選擇 [建立新的金鑰保存庫]，在相同訂用帳戶和資源群組內建立新的金鑰保存庫。
 
    ![插入建立新 KV 的影像](./media/app-service-web-purchase-ssl-web-site/NewKV.jpg)
 
    > [AZURE.NOTE]
    Azure 金鑰保存庫儲存此憑證會產生少許費用。如需詳細資訊，請參閱 **[Azure 金鑰保存庫定價詳細資料](https://azure.microsoft.com/pricing/details/key-vault/)**。

4. 當您選取要儲存此憑證的金鑰保存庫儲存機制之後，請繼續進行並加以儲存，方法是按一下 [金鑰保存庫狀態] 刀鋒視窗頂端的 [儲存] 按鈕。

    這樣應該就完成使用您選擇的 Azure 金鑰保存庫儲存您購買的憑證的步驟。在重新整理刀鋒視窗時，您應該會看到此步驟也標示綠色勾號。
    
##<a name="bkmk_VerifyOwnership"></a>步驟 2︰確認網域擁有權

在此步驟中，您將學習如何對您剛剛訂購的 SSL 憑證執行網域擁有權驗證。

1.	從 [憑證設定] 刀鋒視窗按一下 [步驟 2：驗證] 步驟。App Service 憑證支援 4 種類型的網域驗證。

    * **App Service 驗證**
    
        * 如果您已經有**指派給 App Service 應用程式的自訂網域**，則這是最方便的程序。 這個方法將會列出符合此準則的所有 App Service 應用程式。例如，在此案例中，**contosocertdemo.com** 是一個自訂網域，且已將其指派給名為 **"ContosoCertDemo"** 的 App Service 應用程式，所以它是這裡唯一列出的 App Service 應用程式。如果有多重區域部署，則它會列出跨區域的所有項目。
        
           驗證方法僅適用於標準 (基本) 憑證購買。對於萬用字元憑證，請略過並移至以下的選項 B、C 或 D。
        * 按一下 [驗證] 按鈕來完成這個步驟。
        * 按一下 [重新整理]，在完成驗證之後更新憑證狀態。驗證可能需要數分鐘才能完成。
        
        ![插入 App Service 驗證的影像](./media/app-service-web-purchase-ssl-web-site/AppVerify.jpg)

    * **網域驗證**

        * **只有在**您已經**[從 Azure App Service 購買自訂網域](custom-dns-web-site-buydomains-web-app.md)**時，這才是最方便的程序。
        
        * 按一下 [驗證] 按鈕來完成這個步驟。
        
        * 按一下 [重新整理]，在完成驗證之後更新憑證狀態。驗證可能需要數分鐘才能完成。

    * **郵件驗證**
        
        * 驗證電子郵件已傳送到與此自訂網域相關聯的電子郵件地址。
         
        * 開啟電子郵件，然後按一下驗證連結，以完成電子郵件驗證步驟。
        
        * 如果您需要重新傳送驗證電子郵件，請按一下 [重新傳送電子郵件] 按鈕。
         
    * **手動驗證**
                 
        1. **HTML 網頁驗證**
        
            * 建立名為 **{網域驗證權杖}**.html 的 HTML 檔案 (您可以從 [網域驗證狀態] 刀鋒視窗複製權杖)
            
            * 此檔案的名稱應該與 [網域驗證權杖] 的名稱完全相同。
            
            * 在主控您的網域的 Web 伺服器的根目錄上傳此檔案。
            
            * 按一下 [重新整理]，在完成驗證之後更新憑證狀態。驗證可能需要數分鐘才能完成。
            
            例如，如果您為具有網域驗證權杖 **‘cAGgQrKc’** 的 contosocertdemo.com 購買標準憑證，則對 **‘http://contosocertdemo.com/cAGgQrKc.html’** 所做的 Web 要求應該會傳回 **cAGgQrKc**。
        2. **DNS TXT 記錄驗證**

            * 使用 DNS 管理員，在具有與 [網域驗證權杖] 相同值的 [DZC] 子網域上建立 TXT 記錄。
            
            * 按一下 [重新整理]，在完成驗證之後更新憑證狀態。驗證可能需要數分鐘才能完成。
                              
            例如，若要對具有主機名稱 ***.contosocertdemo.com** 或 ***.subdomain.contosocertdemo.com** 且網域驗證權杖為 **cAGgQrKc** 的萬用字元憑證執行驗證，您必須在值為 **cAGgQrKc** 的 dzc.contosocertdemo.com 上建立 TXT 記錄。


##<a name="bkmk_AssignCertificate"></a>步驟 3︰將憑證指派給 App Service 應用程式

在此步驟中，您將學習如何將這個新購買的憑證指派給您的 App Service 應用程式。

> [AZURE.NOTE]
在執行本節中的步驟之前，您必須先建立自訂網域名稱與應用程式的關聯。如需詳細資訊，請參閱**[設定 Web 應用程式的自訂網域名稱](web-sites-custom-domain-name.md)**。

1.	在瀏覽器中，開啟 **[Azure 入口網站](https://portal.azure.com/)**。
2.	按一下頁面左側的 [App Service] 選項。
3.	按一下您要指派此憑證的應用程式的名稱。
4.	在 [設定] 中，按一下 [SSL 憑證]。
5.	按一下 [匯入 App Service 憑證] 並選取您剛剛購買的憑證

    ![插入匯入憑證的影像](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

6. 在 [SSL 繫結] 區段按一下 [新增繫結]
7. 在 [新增 SSL 繫結] 刀鋒視窗中，使用下拉式清單選取要以 SSL 保護的網域名稱，以及要使用的憑證。您也可以選擇使用**[伺服器名稱指示 (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** 還是 IP SSL。

    ![插入 SSL 繫結的影像](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)

       IP SSL 會將伺服器的專用公用 IP 位址對應至網域名稱，以建立憑證與網域名稱的關聯。這需要與您服務相關聯的每個網域名稱 (contoso.com、fabricam.com 等) 都有專用 IP 位址。這是傳統用來建立 SSL 憑證與網頁伺服器之關聯的方法。• SNI SSL 是 SSL 和**[傳輸層安全性](http://en.wikipedia.org/wiki/Transport_Layer_Security)** (TLS) 的延伸，可讓多個網域共用相同的 IP 位址，而每個網域都有個別的安全性憑證。現今大部分的瀏覽器 (包括 Internet Explorer、Chrome、Firefox 和 Opera) 都支援 SNI，不過，較舊的瀏覽器可能不支援 SNI。如需 SNI 的詳細資訊，請參閱 Wikipedia 上的**[伺服器名稱指示](http://en.wikipedia.org/wiki/Server_Name_Indication)**一文。
       
7. 按一下 [新增繫結]，儲存變更並啟用 SSL。



如果您已選取 [**IP SSL**]，而且您的自訂網域是以 A 記錄設定，則必須執行下列額外步驟：

* 設定 IP SSL 繫結之後，您的應用程式將會獲派專用的 IP 位址。您可以在應用程式設定下的 [自訂網域] 頁面上找到此 IP 位址，正好位於 [主機名稱] 區段上面。它將會列出成為 [外部 IP 位址]：
    
    ![插入 IP SSL 的影像](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)

    請注意，此 IP 位址與先前用來設定您網域之 A 記錄的虛擬 IP 位址不同。如果您設定成使用 SNI SSL，或未設定成使用 SSL，則不會列出此項目的位址。
    
2. 使用網域名稱註冊機構所提供的工具，修改自訂網域名稱的 A 記錄，使其指向上一個步驟的 IP 位址。現在，您應該可以使用 HTTPS:// 而非 HTTP:// 造訪您的應用程式，確認已正確設定憑證。


##<a name="bkmk_Rekey"></a>重設金鑰和同步處理憑證

1. 基於安全性理由，如果您需要重設憑證的金鑰，只要選取 [憑證內容] 刀鋒視窗的 [重設金鑰和同步處理] 選項。

2. 按一下 [重設金鑰] 按鈕來啟動處理程序。此程序需要 1 - 10 分鐘才能完成。

    ![插入重設 SSL 金鑰的影像](./media/app-service-web-purchase-ssl-web-site/Rekey.jpg)

3. 重設您的憑證的金鑰，會以憑證授權單位發行的新憑證變更憑證。
4. 在憑證的存留期期間，不會向您收取重設金鑰的費用。
5. 重設您的憑證的金鑰會經歷 [暫止發行] 狀態。
6. 憑證準備就緒後，請確定使用此憑證同步處理您的資元，以避免服務中斷。
7. 同步處理選項不適用於尚未指派到 Web 應用程式的憑證。

## 其他資源 ##
- [針對 Azure App Service 中的 App 啟用 HTTPS](web-sites-configure-ssl-certificate.md)
- [在 Azure App Service 中購買並設定自訂網域名稱](custom-dns-web-site-buydomains-web-app.md)
- [Microsoft Azure 信任中心](/support/trust-center/security/)
- [Azure 網站中解除鎖定的設定選項](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [Azure 管理入口網站](https://manage.windowsazure.com)

>[AZURE.NOTE] 如果您想在註冊 Azure 帳戶前開始使用 Azure App Service，請移至[試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751)，即可在 App Service 中立即建立短期入門 Web 應用程式。不需要信用卡；沒有承諾。

<!---HONumber=AcomDC_0824_2016-->