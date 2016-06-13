<properties
	pageTitle="建立 Oracle WebLogic Server 12c VM | Microsoft Azure"
	description="在 Microsoft Azure 中使用資源管理員部署模型建立執行 Windows Server 2012 的 Oracle WebLogic Server 12c 虛擬機器。"
	services="virtual-machines-windows"
	authors="rickstercdn"
	manager="timlt"
	documentationCenter=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="05/17/2016"
	ms.author="rclaus" />

#在 Azure 中建立 Oracle WebLogic Server 12c 虛擬機器

[AZURE.INCLUDE [virtual-machines-common-oracle-support](../../includes/virtual-machines-common-oracle-support.md)]

下列範例示範如何在 Azure 中建立在 VM 上執行的 WebLogic Server 12c，而您先前已在此 VM 上建立並安裝在 Windows Server 2012 上執行的 WebLogic Server 12c。

##若要在 Azure 中設定您的 Oracle WebLogic Server 12c 虛擬機器

1. 登入 [Azure 入口網站](https://ms.portal.azure.com/)。

2.	按一下 [虛擬機器]。

3.	按一下要登入之虛擬機器的名稱。

4.	按一下 [連接]。

5.	視需要回應提示以連接虛擬機器。當要求提供系統管理員名稱和密碼的提示出現時，請使用在建立虛擬機器時提供的值。

6.	在 [WebLogic 平台快速入門] 對話方塊中，按一下 [開始使用 WebLogic Server]。(若尚未開啟 [WebLogic 平台快速入門] 對話方塊，請按一下 [Windows 開始]，輸入**啟動 WebLogic Server 網域的管理伺服器**，然後按一下 [啟動 WebLogic Server 網域的管理伺服器] 圖示。)

7.	在 [歡迎] 對話方塊中，選取 [建立新的 WebLogic 網域]，然後按 [下一步]。

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image10.png)

8.	在 [選取網域來源] 對話方塊中，接受預設值，然後按 [下一步]。

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image11.png)

9.	在 [指定網域名稱與位置] 對話方塊中，接受預設值，然後按 [下一步]。

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image12.png)

10.	在 [設定系統管理員使用者名稱與密碼] 對話方塊中：

	1.	將使用者名稱從 **weblogic** 變更為您選擇的值 (選擇性)。

	2.	指定並確認 WebLogic Server 系統管理員密碼。

	3.	按 [下一步]。

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image13.png)

11.	在 [設定伺服器開始模式與 JDK] 對話方塊中，選取 [生產模式]，選取可用的 JDK (或瀏覽至想要的 JDK)，然後按 [下一步]。

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image14.png)

12.	在 [選取選用設定] 對話方塊中，不要選取任何選項，然後按 [下一步]。

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image15.png)

13.	在 [設定摘要] 對話方塊中，按一下 [建立]。

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image16.png)

14.	在 [建立網域] 對話方塊中，勾選 [啟動管理伺服器]，然後按一下 [完成]。

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image17.png)

15.	就會啟動 **startWebLogic.cmd** 的命令提示字元。出現提示時，請提供您的 WebLogic 使用者名稱與密碼。

##在 Azure 中於 Oracle WebLogic Server 12c 虛擬機器上安裝應用程式
1.	仍然登入您的虛擬機器，複製在 http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war 本機可用的 shoppingcart.war 範例。例如，建立名為 **c:\\mywar** 的資料夾，然後將位於 http://www.oracle.com/webfolder/technetwork/tutorials/obe/fmw/wls/12c/12-ManageSessions--4478/files/shoppingcart.war 的 WAR 儲存至 **c:\\mywar**。

2.	開啟 [WebLogic Server 管理主控台] (http://localhost:7001/console)。出現提示時，請提供您的 WebLogic 使用者名稱與密碼。

3.	在 [WebLogic Server 管理主控台] 中，依序按一下 [鎖定與編輯]、[部署] 及 [安裝]。

4.	對於 [路徑]，請輸入 **c:\\mywar\\shoppingcart.war**。

	![](media/virtual-machines-windows-create-oracle-weblogic-server-12c/image18.png)

	按 [下一步]。

5.	選取 [將此部署安裝為應用程式]，然後按 [下一步]。

6.	按一下 [完成]。

7.	在 [WebLogic Server 管理主控台] 中，按一下 [儲存]，然後按一下 [啟用變更]。

8.	按一下 [部署]，選取 [購物車]，然後依序按一下 [開始] 和 [服務所有要求]。在系統提示您確認時，按一下 [Yes] (是)。

9.	若要查看在本機執行的購物車應用程式，請開啟瀏覽器並瀏覽 <http://localhost:7001/shoppingcart>

10.	為虛擬機器建立端點：

	1. 登入 [Azure 入口網站](https://ms.portal.azure.com/)。

	2.	按一下 [瀏覽]

	3.	按一下 [虛擬機器]

	4.	選取虛擬機器

	5.	按一下 [設定]

	6.	按一下 [端點]。

	7.	按一下 [新增]。

	8.	指定端點的名稱

		1. 使用 **TCP** 做為通訊協定

		2. 使用 **80** 做為公用連接埠

		3. 使用 **7001** 做為私人連接埠。

	9.	維持其餘選項不變

	10. 按一下 [確定]。

11.	允許透過防火牆輸入連線至連接埠 7001。

	1.	登入虛擬機器。

	2.	按一下 [Windows 開始]，輸入「具有進階安全性的 Windows 防火牆」，然後按一下 [具有進階安全性的 Windows 防火牆] 圖示。這會開啟 [具有進階安全性的 Windows 防火牆] 管理主控台。

	3.	在防火牆管理主控台中，按一下左邊窗格內的 [輸入規則] (如果您沒有看到 [輸入規則]，請展開左邊窗格內的最上層節點)，然後按一下右邊窗格內的 [新增規則]。

	4.	對於 [規則類型]，請選取 [連接埠]，然後按 [下一步]。

	5.	對於 [通訊協定與連接埠]，請選取 [TCP]，選取 [特定本機連接埠]，輸入 **7001** 做為連接埠，然後按 [下一步]。

	6.	選取 [允許連線] 並按一下 [下一步]。

	7.	接受套用規則之設定檔的預設值，然後按 [下一步]。

	8.	指定規則的名稱並選擇性指定描述，然後按一下 [完成]。

12.	若要查看在網際網路上執行的購物車應用程式，請開啟瀏覽器並連線至格式為 `http://<<unique_domain_name>>/shoppingcart` 的 URL。(您可以在 [Azure 入口網站](https://ms.portal.azure.com/)中按一下 [虛擬機器]，然後選取用來執行 Oracle WebLogic Server 的虛擬機器，以決定 <<unique\_domain\_name>> 的值。)


##其他資源
現在您已經設定好執行 Oracle WebLogic Server 的虛擬機器，請查看下列主題以了解其他資訊。

-	[Oracle 虛擬機器映像 - 其他考量](virtual-machines-windows-classic-oracle-considerations.md)

-	[Oracle WebLogic Server 產品文件 (英文)](http://www.oracle.com/technetwork/middleware/weblogic/documentation/index.html)

-	[Microsoft Azure 上使用 Linux 的 Oracle WebLogic Server 12c (英文)](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

-	[適用於 Azure 的 Oracle 虛擬機器映像](virtual-machines-linux-classic-oracle-images.md)

<!---HONumber=AcomDC_0601_2016-->