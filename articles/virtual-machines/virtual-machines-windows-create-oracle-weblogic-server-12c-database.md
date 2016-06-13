<properties
	pageTitle="Oracle WebLogic Server 和 Database VM |Microsoft Azure"
	description="使用資源管理員部署模型，建立在 Windows Server 2012 上執行的 Oracle WebLogic Server 12c 和 Oracle Database 12c Azure 映像。"
	services="virtual-machines-windows"
	authors="rickstercdn"
	manager="timlt"
	documentationCenter=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="infrastructure-services"
	ms.date="05/17/2016"
	ms.author="rickstercdn" />

#在 Azure 中建立 Oracle WebLogic Server 12c 與 Oracle Database 12c 虛擬機器

[AZURE.INCLUDE [virtual-machines-common-oracle-support](../../includes/virtual-machines-common-oracle-support.md)]

這篇文章說明如何在 Azure 中，於您先前使用安裝在 Windows Server 2012 上之 Oracle 軟體所建立的映像上，建立 Oracle WebLogic Server 12c 和 Oracle Database 12c 資料庫。

##建立裝載在此虛擬機器中的資料庫

遵循[在 Azure 中建立 Oracle Database 12c 虛擬機器](virtual-machines-windows-classic-create-oracle-database.md)中的指示，從**在 Azure 中使用 Oracle Database 12c 虛擬機器建立您的資料庫**一節開始。

##設定裝載在此虛擬機器中的 Oracle WebLogic Server 12c
遵循[在 Azure 中建立 Oracle WebLogic Server 12c 虛擬機器](virtual-machines-windows-create-oracle-weblogic-server-12c.md)中的指示，從**在 Azrue 中設定 Oracle WebLogic Server 12c 虛擬機器**一節開始。

##其他資源
[針對 Oracle 虛擬機器映像的其他考量](virtual-machines-windows-classic-oracle-considerations.md)

[Oracle 虛擬機器映像清單](virtual-machines-linux-classic-oracle-images.md)

[從 Java 應用程式連線到 Oracle 資料庫](http://docs.oracle.com/cd/E11882_01/appdev.112/e12137/getconn.htm#TDPJD136)

[Microsoft Azure 上使用 Linux 的 Oracle WebLogic Server 12c](http://www.oracle.com/technetwork/middleware/weblogic/learnmore/oracle-weblogic-on-azure-wp-2020930.pdf)

[Oracle Database 2 Day DBA 12c Release 1](http://docs.oracle.com/cd/E16655_01/server.121/e17643/toc.htm)

<!---HONumber=AcomDC_0601_2016-->