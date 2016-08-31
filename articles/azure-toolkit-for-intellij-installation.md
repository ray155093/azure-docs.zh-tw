<properties
	pageTitle="安裝 Azure Toolkit for IntelliJ | Microsoft Azure"
	description="了解如何安裝 Azure Toolkit for the IntelliJ IDEA。"
	services=""
	documentationCenter="java"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="multiple"
	ms.workload="na"
	ms.tgt_pltfrm="multiple"
	ms.devlang="Java"
	ms.topic="article"
	ms.date="08/11/2016" 
	ms.author="robmcm"/>

# 安裝 Azure Toolkit for IntelliJ

Azure Toolkit for IntelliJ 提供範本和功能，可讓您輕鬆地使用 IntelliJ IDEA 開發環境來建立、開發、測試及部署 Azure 應用程式。Azure Toolkit for IntelliJ 是開放原始碼專案，其來源程式碼可從 GitHub 上該專案網站的 MIT License 下取得，URL 如下：

<https://github.com/microsoft/azure-tools-for-java>

安裝 Azure Toolkit for IntelliJ 的方法有兩種，一種是從 [設定] 對話方塊，另一種是從啟動畫面上的 [設定] 功能表；下列步驟中將會示範這兩種安裝方法。

[AZURE.INCLUDE [azure-toolkit-for-IntelliJ-prerequisites](../includes/azure-toolkit-for-intellij-prerequisites.md)]

## 從 [設定] 對話方塊安裝 Azure Toolkit for IntelliJ

1. 啟動 IntelliJ IDEA。

1. IntelliJ IDEA 開啟時，按一下 [檔案]，然後按一下 [設定]。

    ![開啟 IntelliJ IDEA 的 [設定] 對話方塊][01a]

1. 在 [設定] 對話方塊中，按一下 [外掛程式]，然後按一下 [瀏覽儲存機制]。

    ![IntelliJ IDEA [設定] 對話方塊][02a]

1. 在 [瀏覽儲存機制] 對話方塊中，於 [搜尋] 方塊中輸入"Azure"。反白顯示 **Azure Toolkit for IntelliJ**，然後按一下 [安裝]。

    ![搜尋 Azure Toolkit for IntelliJ][03]

    IntelliJ IDEA 會在對話方塊中顯示安裝進度。

    ![安裝進度][04]

1. 安裝完成後，按一下 [重新啟動 IntelliJ IDEA]。

    ![重新啟動 IntelliJ IDEA][05]

1. 按一下 [確定] 以關閉 [設定] 對話方塊。

    ![關閉 IntelliJ IDEA [設定] 對話方塊][06]

1. 當系統提示您重新啟動 IntelliJ IDEA 或延後，請按一下 [重新啟動]。

    ![重新啟動 IntelliJ IDEA][07]

## 從啟動畫面安裝 Azure Toolkit for IntelliJ

1. 啟動 IntelliJ IDEA。

1. IntelliJ IDEA 啟動畫面出現時，按一下 [設定]，然後按一下 [外掛程式]。

    ![安裝 IntelliJ IDEA 外掛程式][01b]

1. 在 [外掛程式] 對話方塊中，按一下 [瀏覽儲存機制]。

    ![瀏覽 IntelliJ IDEA 外掛程式儲存機制][02b]

1. 在 [瀏覽儲存機制] 對話方塊中，於 [搜尋] 方塊中輸入"Azure"。反白顯示 **Azure Toolkit for IntelliJ**，然後按一下 [安裝]。

    ![搜尋 Azure Toolkit for IntelliJ][03]

    IntelliJ IDEA 會在對話方塊中顯示安裝進度。

    ![安裝進度][04]

1. 安裝完成後，按一下 [重新啟動 IntelliJ IDEA]。

    ![重新啟動 IntelliJ IDEA][05]

1. 當系統提示您重新啟動 IntelliJ IDEA 或延後，請按一下 [重新啟動]。

    ![重新啟動 IntelliJ IDEA][07]

## 另請參閱

如需適用於 Java IDE 的 Azure 套件組的詳細資訊，請參閱下列連結：

- [適用於 Eclipse 的 Azure 工具組]
  - [安裝 Azure Toolkit for Eclipse]
  - [Create a Hello World Web App for Azure in Eclipse (在 Eclipse 中建立 Azure Hello World Web 應用程式)]
  - [適用於 Eclipse 的 Azure 工具組的新功能]
- [Azure Toolkit for IntelliJ]
  - 安裝 Azure Toolkit for IntelliJ (本文)
  - [在 IntelliJ 中建立 Azure Hello World Web 應用程式]
  - [適用於 IntelliJ 的 Azure 工具組新增功能]

如需如何搭配使用 Azure 與 Java 的詳細資訊，請參閱 [Azure Java 開發人員中心]。

<!-- URL List -->

[適用於 Eclipse 的 Azure 工具組]: ./azure-toolkit-for-eclipse.md
[Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij.md
[Create a Hello World Web App for Azure in Eclipse (在 Eclipse 中建立 Azure Hello World Web 應用程式)]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[在 IntelliJ 中建立 Azure Hello World Web 應用程式]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[安裝 Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Installing the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[適用於 Eclipse 的 Azure 工具組的新功能]: ./azure-toolkit-for-eclipse-whats-new.md
[適用於 IntelliJ 的 Azure 工具組新增功能]: ./azure-toolkit-for-intellij-whats-new.md

[Azure Java 開發人員中心]: https://azure.microsoft.com/develop/java/

<!-- IMG List -->

[01a]: ./media/azure-toolkit-for-intellij-installation/01-intellij-file-settings.png
[01b]: ./media/azure-toolkit-for-intellij-installation/01-intellij-configure-dropdown.png
[02a]: ./media/azure-toolkit-for-intellij-installation/02-intellij-settings-dialog.png
[02b]: ./media/azure-toolkit-for-intellij-installation/02-intellij-plugins-dialog.png
[03]: ./media/azure-toolkit-for-intellij-installation/03-intellij-browse-repositories.png
[04]: ./media/azure-toolkit-for-intellij-installation/04-install-progress.png
[05]: ./media/azure-toolkit-for-intellij-installation/05-restart-intellij.png
[06]: ./media/azure-toolkit-for-intellij-installation/06-intellij-settings-dialog.png
[07]: ./media/azure-toolkit-for-intellij-installation/07-restart-intellij.png

<!---HONumber=AcomDC_0817_2016-->