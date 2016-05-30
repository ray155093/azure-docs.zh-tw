<properties
   pageTitle="使用 Azure 入口網站建立 DNS 區域的記錄集和記錄 | Microsoft Azure"
   description="如何使用 Azure 入口網站建立 Azure DNS 的主機記錄並建立記錄集和記錄"
   services="dns"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="dns"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/06/2016"
   ms.author="cherylmc"/>



# 使用 Azure 入口網站建立 DNS 記錄集和記錄


> [AZURE.SELECTOR]
- [Azure 入口網站](dns-getstarted-create-recordset-portal.md)
- [PowerShell](dns-getstarted-create-recordset.md)
- [Azure CLI](dns-getstarted-create-recordset-cli.md)


本文將逐步引導您使用 Azure 入口網站建立記錄和記錄集。建立 DNS 區域之後，您必須加入網域的 DNS 記錄。若要這樣做，您必須先了解 DNS 記錄和記錄集。

[AZURE.INCLUDE [dns-about-records-include](../../includes/dns-about-records-include.md)]


## 建立記錄集和記錄

下列範例會逐步解說如何使用 Azure 入口網站建立記錄集和記錄。我們將使用 DNS 'A' 記錄類型。

1. 登入 Azure 管理入口網站。

2. 瀏覽至要在其中建立記錄集的 [DNS 區域] 刀鋒視窗。

3. 在您的 [DNS 區域] 刀鋒視窗頂端按一下 [記錄集] 以開啟 [新增記錄集] 刀鋒視窗。
 
	![新的記錄集](./media/dns-getstarted-create-recordset-portal/newrecordset500.png)

4. 在 [新增記錄集] 刀鋒視窗中，為您的記錄集命名。例如，您可以將記錄集命名為 "**www**"。
  
	![新增記錄集](./media/dns-getstarted-create-recordset-portal/addrecordset500.png)

5. 選取您想要建立的記錄類型。例如，**A**。

6. 設定 **TTL**。入口網站中的預設值是 1 小時。

7. 新增 IP 位址，每行一個 IP 位址。使用上述的建議記錄集名稱和記錄類型，這會新增 IPv4 IP 位址到 www 記錄集的 A 記錄。

8. 當您新增好 IP 位址，按一下刀鋒視窗底部的 [確定]。隨即會建立 DNS 記錄集。


## 後續步驟

若要管理您的記錄集和記錄，請參閱[使用 Azure 入口網站管理 DNS 記錄和記錄集](dns-operations-recordsets-portal.md)。

如需 Azure DNS 的詳細資訊，請參閱 [Azure DNS 概觀](dns-overview.md)。

<!---HONumber=AcomDC_0518_2016-->