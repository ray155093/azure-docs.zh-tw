---
title: "在 Azure Linux VM 上搭配 Django 的 Python Web 應用程式 | Microsoft Docs"
description: "了解如何使用 Linux VM，在 Azure 中裝載以 Django 作為基礎的 Web 應用程式。"
services: virtual-machines-linux
documentationcenter: python
author: huguesv
manager: wpickett
editor: 
tags: azure-resource-manager
ms.assetid: 00ad4c2c-4316-4f9a-913f-f7f49b158db7
ms.service: virtual-machines-linux
ms.workload: web
ms.tgt_pltfrm: vm-linux
ms.devlang: python
ms.topic: article
ms.date: 05/31/2017
ms.author: huvalo
ms.translationtype: Human Translation
ms.sourcegitcommit: 6adaf7026d455210db4d7ce6e7111d13c2b75374
ms.openlocfilehash: 6e2ab8c7da7496d0e2b567a4bdc9341adcf01552
ms.contentlocale: zh-tw
ms.lasthandoff: 06/22/2017


---
# <a name="django-hello-world-web-app-on-a-linux-vm"></a>Linux VM 上的 Django Hello World Web 應用程式
> [!div class="op_single_selector"]
> * [Windows](../windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [Mac/Linux](../windows/classic/python-django-web-app.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

<br>

本教學課程會示範如何在 Azure 虛擬機器中裝載 Linux 中以 Django 作為基礎的網站。 在教學課程中，我們假設沒有 Azure 的使用經驗。 當您完成教學課程時，將可在雲端啟動並執行以 Django 作為基礎的應用程式。

了解如何：

* 設定 Azure 虛擬機器以裝載 Django。 雖然本教學課程說明如何針對 **Linux** 執行這項操作，您也可以針對 Azure 中裝載的 Windows Server VM 執行相同操作。 
* 在 Linux 中建立新的 Django 應用程式。

本教學課程會示範如何建立基本的 Hello World Web 應用程式。 該應用程式是裝載於 Azure 虛擬機器中。

下列螢幕擷取畫面會顯示完成的應用程式：

![瀏覽器視窗會顯示 Azure 中的 Hello World 頁面](./media/python-django-web-app/mac-linux-django-helloworld-browser.png)

[!INCLUDE [create-account-and-vms-note](../../../includes/create-account-and-vms-note.md)]

## <a name="create-and-set-up-an-azure-virtual-machine-to-host-django"></a>建立並設定 Azure 虛擬機器以裝載 Django

1. 若要使用 Ubuntu Server 14.04 LTS 發佈來建立 Azure 虛擬機器，請參閱[在 Azure 入口網站中建立 Linux 虛擬機器](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 您也可以選擇密碼驗證來取代使用 SSH 公開金鑰。
2. 若要編輯允許內送至通訊埠 80 的 HTTP 流量之網路安全性群組，請參閱[在 Azure 入口網站中建立網路安全性群組](../../virtual-network/virtual-networks-create-nsg-arm-pportal.md)。
3. (選擇性) 根據預設，新的虛擬機器沒有完整網域名稱 (FQDN)。  若要使用 FQDN 建立 VM，請參閱[在 Azure 入口網站建立適用於 Windows VM 的 FQDN](../windows/portal-create-fqdn.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 完成本教學課程不需要這個步驟。

## <a id="setup"> </a>設定開發環境
> [!NOTE]
> 如果您需要安裝 Python 或想要使用用戶端程式庫，請參閱 [Python 安裝指南](../../python-how-to-install.md)。

Ubuntu Linux VM 已預先安裝 Python 2.7，但它並未隨附 Apache 或 Django。 完成下列步驟可連線至您的 VM，並安裝 Apache 和 Django：

1. 開啟新的終端機視窗。
2. 若要連線至 Azure VM，請輸入下列命令。 如果您尚未建立 FQDN，您可以使用 Azure 入口網站之虛擬機器摘要所顯示的公用 IP 位址來進行連線。
   
       $ ssh yourusername@yourVmUrl
3. 若要安裝 Django，請輸入下列命令：
   
       $ sudo apt-get install python-setuptools python-pip
       $ sudo pip install django
4. 若要安裝含 mod-wsgi 的 Apache，請輸入下列命令：
   
       $ sudo apt-get install apache2 libapache2-mod-wsgi

## <a name="create-a-new-django-app"></a>建立新的 Django 應用程式
1. 若要使用 SSH 存取您的 VM，請開啟您在上一節中使用的終端機視窗。
2. 若要建立新的 Django 專案，請輸入下列命令：
   
       $ cd /var/www
       $ sudo django-admin.py startproject helloworld
   
   `django-admin.py` 指令碼會為以 Django 作為基礎的網站產生一個基本結構：
   
   * `helloworld/manage.py` 可協助您開始裝載及停止裝載以 Django 作為基礎的網站。
   * `helloworld/helloworld/settings.py` 具有您應用程式的 Django 設定。
   * `helloworld/helloworld/urls.py` 具有每個 URL 及其檢視之間的對應程式碼。
3. 在 /var/www/helloworld/helloworld 目錄中，建立名為 views.py 的新檔案。 這個檔案具有轉譯 "hello world" 頁面的檢視。 在程式碼編輯器中，輸入下列命令：
   
       from django.http import HttpResponse
       def home(request):
           html = "<html><body>Hello World!</body></html>"
           return HttpResponse(html)
4. 使用下列命令取代 urls.py 檔案的內容：
   
       from django.conf.urls import patterns, url
       urlpatterns = patterns('',
           url(r'^$', 'helloworld.views.home', name='home'),
       )

## <a name="set-up-apache"></a>設定 Apache
1. 在 /etc/apache2/sites-available/helloworld.conf 資料夾中，建立 Apache 虛擬主機組態檔。 將內容設為下列值。 以您所使用之電腦的實際名稱取代 yourVmName(例如 pyubuntu)。
   
       <VirtualHost *:80>
       ServerName yourVmName
       </VirtualHost>
       WSGIScriptAlias / /var/www/helloworld/helloworld/wsgi.py
       WSGIPythonPath /var/www/helloworld
2. 若要啟動站台，請使用下列命令：
   
       $ sudo a2ensite helloworld
3. 若要重新啟動 Apache，請使用下列命令：
   
       $ sudo service apache2 reload
4. 在您的瀏覽器中載入網頁：
   
   ![瀏覽器視窗會顯示 Azure 中的 Hello World 頁面](./media/python-django-web-app/mac-linux-django-helloworld-browser.png)

## <a name="shut-down-your-azure-virtual-machine"></a>關閉 Azure 虛擬機器
當您完成本教學課程時，建議您將為本教學課程建立的 Azure VM 關閉或移除。 這樣可為其他教學課程釋放資源，您也可以避免產生 Azure 使用量費用。


