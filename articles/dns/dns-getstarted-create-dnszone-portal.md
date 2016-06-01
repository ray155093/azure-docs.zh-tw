<properties
   pageTitle="如何在 Azure 入口網站中建立和管理 DNS 區域 | Microsoft Azure"
   description="了解如何建立 Azure DNS 的 DNS 區域。這是使用 Azure 入口網站建立和管理您的第一個 DNS 並開始裝載 DNS 網域的逐步指南。"
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
   ms.date="05/09/2016"
   ms.author="cherylmc"/>

# 在 Azure 入口網站中建立 DNS 區域


> [AZURE.SELECTOR]
- [Azure 入口網站](dns-getstarted-create-dnszone-portal.md)
- [PowerShell](dns-getstarted-create-dnszone.md)
- [Azure CLI](dns-getstarted-create-dnszone-cli.md)



本文將逐步引導您完成使用 Azure 入口網站建立 DNS 區域的步驟。您也可以使用 PowerShell 或 CLI 建立 DNS 區域。

[AZURE.INCLUDE [dns-create-zone-about](../../includes/dns-create-zone-about-include.md)]


### 關於 Azure DNS 的標記


標記是名稱-值組的清單，由 Azure Resource Manager 在計費或分群用途上用來標示資源。如需標記的詳細資訊，請參閱[使用標記來組織您的 Azure 資源](../resource-group-using-tags.md)一文。

您可以在 Azure 入口網站中使用 DNS 區域的 [設定] 刀鋒視窗新增標記。


## 建立 DNS 區域

1. 登入 Azure 入口網站

2. 在 [中樞] 功能表上，按一下 [新增] > [網路] > [DNS 區域] 以開啟 [DNS 區域] 刀鋒視窗。
 
	![DNS 區域](./media/dns-getstarted-create-dnszone-portal/openzone650.png)

3. 在 [DNS 區域] 刀鋒視窗中，按一下位於底部的 [建立]。這會開啟 [建立 DNS 區域] 刀鋒視窗。

	![建立區域](./media/dns-getstarted-create-dnszone-portal/newzone250.png)

4. 在 [建立 DNS 區域] 刀鋒視窗中，提供 DNS 區域的 [名稱]。例如 *contoso.com*。請參閱上面的[關於 DNS 區域名稱](#names)一節。

5. 接下來，指定您想要使用的資源群組。您可以建立新的資源群組，或選取已經存在的資源群組。

6. 在 [位置] 下拉式清單中指定資源群組的位置。請注意，這項設定是指資源群組的位置，而非 DNS 區域的位置。實際的 DNS 區域資源會自動遍布「全域」，因此不是您可以 (或必須) 在入口網站中指定的項目。

7. 如果您想要輕鬆地在儀表板上找到您的新區域，您可以讓 [釘選到儀表板] 核取方塊保持選取狀態。然後按一下 [**建立**]。

	![釘選到儀表板](./media/dns-getstarted-create-dnszone-portal/pindashboard150.png)

8. 按一下 [建立] 之後，您會看到您的新區域已設定到儀表板上。

	![建立中](./media/dns-getstarted-create-dnszone-portal/creating150.png)

9. 新區域建立好之後，新區域的刀鋒視窗就會在儀表板上開啟。


## 檢視記錄

建立 DNS 區域也會建立下列記錄：

- 「起始點授權」(SOA) 記錄。SOA 出現在每個 DNS 區域的根。
- 授權名稱伺服器 (NS) 記錄。這些顯示哪些名稱伺服器裝載該區域。Azure DNS 使用名稱伺服器集區，因此，不同的名稱伺服器可能會指派至 Azure DNS 中的不同區域。如需詳細資訊，請參閱[將網域委派給 Azure DNS](dns-domain-delegation.md)。

您可以從 Azure 入口網站檢視記錄

1. 在 [DNS 區域] 刀鋒視窗上，按一下 [所有設定] 以開啟 DNS 區域的 [設定] 刀鋒視窗。 

	![區域](./media/dns-getstarted-create-dnszone-portal/viewzonens500.png)


2. 在 [基本資訊] 窗格的下半部，您可以看到 DNS 區域的記錄集。


	![區域](./media/dns-getstarted-create-dnszone-portal/viewzone500.png)

## 測試

您可以使用 nslookup、dig 或 [Resolve-DnsName PowerShell Cmdlet](https://technet.microsoft.com/library/jj590781.aspx) 等 DNS 工具測試您的 DNS 區域。

如果您還沒有將網域委派給 Azure DNS 中的新區域，您必須將 DNS 查詢直接導向您的區域的其中一個名稱伺服器。如上面的 `Get-AzureRmDnsRecordSet` 所列，NS 記錄提供您區域的名稱伺服器。請務必在下列命令中用正確的值取代您的區域。

	nslookup
	> set type=SOA
	> server ns1-01.azure-dns.com
	> contoso.com

	Server: ns1-01.azure-dns.com
	Address:  208.76.47.1

	contoso.com
        	primary name server = ns1-01.azure-dns.com
        	responsible mail addr = msnhst.microsoft.com
        	serial  = 1
        	refresh = 900 (15 mins)
        	retry   = 300 (5 mins)
        	expire  = 604800 (7 days)
        	default TTL = 300 (5 mins)



## 刪除 DNS 區域

您可以直接從入口網站中刪除 DNS 區域。在 Azure DNS 中刪除 DNS 區域之前，您必須刪除所有記錄集，但建立區域時在區域的根自動建立的 NS 和 SOA 記錄除外。

1. 找到您想要刪除之區域的 [DNS 區域] 刀鋒視窗，然後按一下刀鋒視窗頂端的 [刪除]。
 
2. 隨即會出現一則訊息讓您知道您必須刪除所有記錄集，但自動建立的 NS 和 SOA 記錄除外。如果您已刪除記錄集，則按一下 [是]。請注意，當您從入口網站中刪除 DNS 區域時，將不會刪除 DNS 區域相關聯的資源群組。


## 後續步驟

建立 DNS 區域之後，請建立[記錄集和記錄](dns-getstarted-create-recordset-portal.md)，以開始解析您的網際網路網域名稱。

<!---HONumber=AcomDC_0518_2016-->