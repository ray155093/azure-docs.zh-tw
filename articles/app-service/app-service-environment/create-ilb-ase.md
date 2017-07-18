---
title: "使用 Azure App Service Environment 建立及使用內部負載平衡器"
description: "建立及使用網際網路隔離 Azure App Service Environment 的詳細資料"
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: 0f4c1fa4-e344-46e7-8d24-a25e247ae138
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2017
ms.author: ccompy
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 8bc82a297ef8e3f1beac773a6a3b0c4a9b80334f
ms.contentlocale: zh-tw
ms.lasthandoff: 06/26/2017

---
# <a name="create-and-use-an-internal-load-balancer-with-an-app-service-environment"></a>使用 App Service Environment 建立及使用內部負載平衡器 #

App Service Environment (ASE) 是 Azure App Service 到 Azure 虛擬網路 (VNet) 中之子網路的部署。 部署 ASE 的做法有二種： 

- 使用外部 IP 位址上的 VIP，通常稱為_外部 ASE_。
- 使用內部 IP 位址上的 VIP，通常稱為 _ILB ASE_，因為內部端點是內部負載平衡器 (ILB)。 

本文說明如何建立 _ILB ASE_。  如需 ASE 的概觀，您可以從 [App Service Environment 簡介][Intro] 開始，如果您想要了解如何建立外部 ASE，請從[建立外部 ASE][MakeExternalASE] 開始。

## <a name="overview"></a>概觀 ##

ASE 可以使用網際網路可存取的端點或您虛擬網路中的 IP 位址加以部署。 為了將 IP 位址設定為虛擬網路位址，ASE 必須與 ILB 一起部署。 在部署 ASE 與 ILB 時，您必須提供：

-   用來建立應用程式之您自己的網域
-   針對 HTTPS 使用的憑證
-   您網域的 DNS 管理

相對的，您可以執行以下動作：

-   在您可以透過「端對端」或 ExpressRoute VPN 存取的雲端，安全地裝載內部網路應用程式
-   在雲端裝載未在公用 DNS 伺服器中列出的 app
-   建立與網際網路隔離，且您的前端 app 可以安全地與之整合的後端應用程式

已停用的功能

當您使用 ILB ASE 時，有一些動作您無法執行，包括：

-   使用以 IP 為主的 SSL
-   將 IP 位址指派給特定 app
-   透過入口網站購買憑證並搭配 app 使用。 您可以直接透過「憑證授權單位」取得憑證並搭配您的 app 使用，只是無法透過 Azure 入口網站這樣做。

## <a name="create-an-ilb-ase"></a>建立 ILB ASE ##

若要建立 ILB ASE：

1.  在 Azure 入口網站中，選取 [新增] -&gt; [Web + 行動] -&gt; [App Service Environment]。
2.  選取您的訂用帳戶。
3.  選取或建立資源群組。
4.  選取或建立虛擬網路。
5.  建立子網路 (如果選取虛擬網路)。 確定將子網路的大小設為足以容納 ASE 的任何未來成長。 建議的大小是 `/25`，其中具有 128 個位址，而且可以處理最大大小的 ASE。 您無法使用 `/29` 或較小的子網路大小。  基礎結構需要用盡至少 5 個位址。  即使使用 `/28`，您在 `/28` 子網路中擁有最大 11 個執行個體的規模。 如果您認為假以時日在 App Service 方案中需要超過 100 個執行個體的預設最大值，或者需要調整接近 100 但是具有更快速的前端調整，則使用具有 256 個位址的 /24。
6.  選取 [虛擬網路/位置] -&gt; [虛擬網路設定]，並將 [VIP 類型] 設定為 [內部]。
7.  提供網域名稱。 這會成為在此 ASE 中建立之 app 所使用的網域。 有某些限制。 它不能是：
    - net
    - azurewebsites.net
    - p.azurewebsites.net
    - &lt;asename&gt;.p.azurewebsites.net

    如果您也想要針對使用此 ASE 裝載的 app 使用自訂網域名稱，在自訂網域名稱與 ASE 所使用的網域名稱之間不能有重疊。 針對具有網域名稱為 _contoso.com_ 的 ILB ASE，您無法針對 app 使用如下所示的自訂網域名稱：
    - www.contoso.com
    - abcd.def.contoso.com
    - abcd.contoso.com

    如果您知道想要用於部署到 ILB ASE 之 app 的自訂網域名稱，則挑選在 ILB ASE 建立期間不會有衝突的 ILB ASE 網域。 在此範例中，它可能類似 _contoso-internal.com_。
8.  選取 [確定]，然後選取 [建立]。

    ![][1]

在 [虛擬網路] 刀鋒視窗中，會有一個 [虛擬網路設定] 選項。 這個選項可讓您在 [外部 VIP] 或 [內部 VIP] 之間選擇。 預設為「外部」。 如果您選取 [外部]，您的 ASE 會使用一個網際網路可存取的 VIP。 如果您選取 [內部]，您的 ASE 會使用您虛擬網路內 IP 位址搭配 ILB 進行設定。

選取 [內部] 之後，將會移除把更多 IP 位址新增至您 ASE 的功能，取而代之的是您必須提供 ASE 的網域。 在使用外部 VIP 的 ASE 中，ASE 的名稱會在網域中用於在該 ASE 中建立的 app。

如果您將 [VIP 類型] 設定為 [內部]，您的 ASE 名稱不會在 ASE 的網域中使用。 您可以明確地指定網域。 如果您的網域是 contoso.corp.net 而您在該 ASE 中建立一個名為 timereporting 的 app，該 app 的 URL 會是 timereporting.contoso.corp.net。

## <a name="apps-in-an-ilb-ase"></a>ILB ASE 中的 App ##

在 ILB ASE 中建立 app，通常與在 ASE 中建立 app 相同。

1.  在 Azure 入口網站選取 [新增] -&gt; [Web + 行動] -&gt; [Web] 或 [行動]，或者 [API 應用程式]。
2.  輸入 app 的名稱。
3.  選取訂用帳戶。
4.  選取或建立資源群組。
5.  選取或建立 App Service 方案。 如果您想要建立新的 App Service 方案，請選取您的 ASE 作為位置並選取您希望在其中建立 App Service 方案的背景工作角色集區。 當您建立 App Service 方案時，可以選取您的 ASE 作為位置與背景工作角色集區。 當您指定 app 的名稱時，您會看見您 app 名稱底下的網域會由您 ASE 的網域取代。
6.  選取 [ **建立**]。 如果您希望 app 顯示在儀表板上，您應該選取 [釘選到儀表板]  核取方塊。

    ![][2]

在 app 名稱底下，網域名稱會更新，以反映您的 ASE 網域。

## <a name="post-ilb-ase-creation-validation"></a>ILB ASE 建立後驗證 ##

ILB ASE 與非 ILB ASE 稍微有些不同。 如先前所述，您必須管理您自己的 DNS，而且您也必須提供您自己的 HTTPS 連線憑證。

建立您的 ASE 之後，您會注意到網域名稱顯示您所指定的網域，且 [設定] 功能表中會有一個稱為 [ILB 憑證] 的新項目。 ASE 使用憑證建立，該憑證未指定 ILB ASE 網域。 如果您使用 ASE 與該憑證，您的瀏覽器會告訴您它是無效的。 此憑證可讓您更輕鬆地測試 HTTPS，但是預期情況下，您必須上傳您自己的憑證 (繫結至您的 ILB ASE 網域)，無論是自我簽署或是從憑證授權單位 (CA) 取得。

![][3]

有各種不同的方式可取得有效的 SSL 憑證，包括內部 CA、向外部簽發者購買憑證，以及使用自我簽署的憑證。 無論 SSL 憑證的來源，都需要正確設定下列憑證屬性︰

-   _主體_︰此屬性必須設為 _\*.your-root-domain-here_
-   _主體替代名稱_︰此屬性必須同時包含 _\*.your-root-domain-here_ 和 _\*.scm.your-root-domain-here_。 第二個項目的原因是將使用 _your-app-name.scm.your-root-domain-here_形式的位址，進行與每個應用程式相關聯的 SCM/Kudu 網站的 SSL 連線

備妥有效的 SSL 憑證，還需要兩個額外的準備步驟。 SSL 憑證必須能夠轉換/另存為 .pfx 檔案。 請記住，.pfx 檔案必須包含所有中繼和根憑證，而且也必須使用密碼保護。

如果您想要使用 PowerShell 建立您自己的憑證，您可以使用如下所示的命令。  務必使用您的 ILB ASE 網域名稱，而不是 internal.contoso.com。 

    $certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "\*.internal-contoso.com","\*.scm.internal-contoso.com"
    
    $certThumbprint = "cert:\localMachine\my\" +$certificate.Thumbprint
    $password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText
    
    $fileName = "exportedcert.pfx" 
    Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password

這些 PowerShell 命令產生的憑證仍然會由瀏覽器標示，因為它不是由瀏覽器使用之信任鏈結中的 CA 所建立。 取得您的瀏覽器不會發生問題之憑證的唯一方式是從瀏覽器信任鏈結中的商業 CA 購買它。  

![][4]

若要上傳您自己的憑證並測試存取：

1.  在建立 ASE 之後移至 ASE UI ([ASE] > [設定] > [ILB 憑證])。
2.  選取憑證 pfx 檔案並提供密碼，來設定 ILB 憑證。 這個步驟需要一些時間來處理，且會顯示上傳作業正在進行中的訊息。
3.  取得您 ASE 的 ILB 位址 ([ASE] > [屬性] > [虛擬 IP 位址])。
4.  建立後，在 ASE 中建立 Web 應用程式。
5.  如果您在該虛擬網路中還沒有 VM，則建立 VM。

    > [!NOTE] 
    > 請勿在與 ASE 相同的子網路中建立此 VM。 否則，它會中斷您的設定。
    >
    >

6.  設定 ASE 網域的 DNS。 您可以在您 DNS 中使用萬用字元搭配您的網域，或者如果您想要執行一些簡單測試，請編輯您 VM 上的主機檔案來將 Web 應用程式名稱設定為 VIP IP 位址。 如果 ASE 有網域名稱 _.ilbase.com_，且您建立名稱為 _mytestapp_ 的 Web 應用程式，則它將會在 _mytestapp.ilbase.com_ 定址。 然後，您設定 _mytestapp.ilbase.com_ 以解析 ILB 位址。 (在 Windows 上，主機檔案位於 _C:\Windows\System32\drivers\etc\_。)如果您想要測試 Web 部署發佈或存取進階主控台，則您也需要建立 _mytestapp.scm.ilbase.com_ 的記錄。
7.  在該 VM 上使用瀏覽器並移至 http://mytestapp.ilbase.com (或者您的 Web 應用程式名稱與您的網域)。
8.  在該 VM 上使用瀏覽器並移至 https://mytestapp.ilbase.com。 如果您使用自我簽署憑證，您必須接受安全性不足。

您的 ILB IP 位址列在 [IP 位址] 底下。 也會有外部 VIP 使用以及用於輸入管理流量的 IP 位址。

![][5]

### <a name="functions-and-the-ilb-ase"></a>函式和 ILB ASE

在 ILB ASE 使用函式時，您可能會遇到錯誤，指出「我們無法立即擷取您的函式。*請稍後再試一次。」*  原因是函式 UI 透過 HTTPS 運用 scm 網站。  如果您針對沒有瀏覽器中根憑證的 ASE 使用 HTTP 憑證，就會遇到這個錯誤。  此外，IE\Edge 瀏覽器不會在索引標籤之間共用 accept-invalid-cert。 因此您可以：

- 將憑證新增至您的信任憑證存放區 
- 或使用 Chrome，但是您必須先前往 scm 網站，接受不受信任的憑證，然後前往入口網站

## <a name="dns-configuration"></a>DNS 組態 ##

使用外部 VIP 時，DNS 是由 Azure 管理。 在您 ASE 中建立的任何 app 都會自動新增至 Azure DNS，這是一個公用 DNS。 在 ILB ASE 中，您必須管理您自己的 DNS。 針對指定的網域 (例如 _contoso.net_)，您必須為下列項目，在 DNS 中建立指向您 ILB 位址的 DNS A 記錄︰

- *.contoso.net
- *.scm.contoso.net

如果 ILB ASE 網域是用於此 ASE 之外的多項作業，您可能需要在每個 app 名稱的基礎上管理 DNS。 這更具有挑戰性，因為當您建立名稱時，必須將每個新的 app 名稱新增至您的 DNS。 基於這個理由，建議使用專用網域。

## <a name="publishing-with-an-ilb-ase"></a>使用 ILB ASE 發佈 ##

對於建立的每個 app，有兩個端點。 在 ILB ASE 中，您有 &lt;app name>.&lt;ILB ASE Domain> 以及 &lt;app name>.scm.&lt;ILB ASE Domain>。 

SCM 網站名稱會帶您前往 Kudu 主控台，稱為 Azure 入口網站中的**進階入口網站**。 Kudu 主控台可讓您執行許多作業，包括檢視環境變數、探索磁碟、使用主控台等等。 如需詳細資訊，請參閱 [Azure App Service 的 Kudu 主控台][Kudu]。 

在多租用戶 App Service 和外部 ASE 中，Azure 入口網站與 Kudu 主控台之間有單一登入。 不過，對於 ILB ASE，您必須改為使用發佈認證來登入 Kudu 主控台。

以網際網路為基礎的 CI 系統，例如 Github 和 VSTS，不會與 ILB ASE 搭配運作，因為發佈端點不是網際網路可存取。 相反地，您需要使用會使用提取模型的 CI 系統，例如 Dropbox。

ILB ASE 中應用程式的發佈端點會使用用來建立 ILB ASE 的網域。 這可以在 app 的發行設定檔，以及 app 的入口網站刀鋒視窗中看到 (在 [概觀]  >  [基本資訊]，以及 [屬性])。 如果您有具有子網域 contoso.net 的 ILB ASE，且 app 名為 mytest，則您會 FTP 至 mytest.contoso.net，並且執行到 mytest.scm.contoso.net 的 Web 部署。

## <a name="coupling-an-ilb-ase-with-a-waf-device"></a>將 ILB ASE 與 WAF 裝置耦合 ##

Azure App Service 提供許多安全性措施，保護您的系統，並且協助您判斷 app 是否遭到駭客入侵。 Web 應用程式的最佳保護是結合裝載平台，例如 Azure App Service 與 Web 應用程式防火牆 (WAF)。 由於 ILB ASE 具有與網路隔離的應用程式端點，因此很適合這樣的使用方式。  

若要深入了解設定 ILB ASE 與 WAF 裝置，請參閱[使用 App Service Environment 設定 Web 應用程式防火牆][ASEWAF]。 該文章會示範如何搭配使用您的 ASE 與 Barracuda 虛擬應用裝置。 另一個選項是使用 Azure 應用程式閘道。 應用程式閘道會使用 OWASP 核心規則來保護後面的任何應用程式。 如需有關 Azure 應用程式閘道的詳細資訊，請參閱 [Azure Web 應用程式防火牆簡介][AppGW]。

### <a name="getting-started"></a>開始使用 ###

您可以在[應用程式服務環境的讀我檔案][ASEReadme]中取得 App Service Environment 的所有相關文章與做法。

若要開始使用 App Service Environment，請參閱 [App Service Environment 簡介][Intro]。

如需有關 Azure App Service 平台的詳細資訊，請參閱 [Azure App Service](http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/)。
 
<!--Image references-->
[1]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-network.png
[2]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-webapp.png
[3]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate.png
[4]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-certificate2.png
[5]: ./media/creating_and_using_an_internal_load_balancer_with_app_service_environment/createilbase-ipaddresses.png

<!--Links-->
[Intro]: ./intro.md
[MakeExternalASE]: ./create-external-ase.md
[MakeASEfromTemplate]: ./create-from-template.md
[MakeILBASE]: ./create-ilb-ase.md
[ASENetwork]: ./network-info.md
[ASEReadme]: ./readme.md
[UsingASE]: ./using-an-ase.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/virtual-networks-nsg.md
[ConfigureASEv1]: ../../app-service-web/app-service-web-configure-an-app-service-environment.md
[ASEv1Intro]: ../../app-service-web/app-service-app-service-environment-intro.md
[webapps]: ../../app-service-web/app-service-web-overview.md
[mobileapps]: ../../app-service-mobile/app-service-mobile-value-prop.md
[APIapps]: ../../app-service-api/app-service-api-apps-why-best-platform.md
[Functions]: ../../azure-functions/index.yml
[Pricing]: http://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/resource-group-overview.md
[ConfigureSSL]: ../../app-service-web/web-sites-purchase-ssl-web-site.md
[Kudu]: http://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../../app-service-web/web-sites-deploy.md
[ASEWAF]: ../../app-service-web/app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../application-gateway/application-gateway-web-application-firewall-overview.md

