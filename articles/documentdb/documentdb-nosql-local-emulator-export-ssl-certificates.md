---
title: "匯出 DocumentDB 模擬器憑證 | Microsoft Docs"
description: "以不使用 Windows 憑證存放區的語言和執行階段進行開發時，您必須匯出和管理 SSL 憑證。 這篇文章提供逐步指示。"
services: documentdb
documentationcenter: 
keywords: "DocumentDB 模擬器"
author: voellm
manager: jhubbard
editor: 
ms.assetid: ef43deda-c2e9-4193-99e2-7f6a88a0319f
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/16/2016
ms.author: tvoellm
translationtype: Human Translation
ms.sourcegitcommit: 638c6c5ac9ffac7e726d86a979e2c5dc7fab41f4
ms.openlocfilehash: 32bebcbd724d577f78672cb623748fcf77cee20a


---

# <a name="export-the-documentdb-emulator-certificates"></a>匯出 DocumentDB 模擬器憑證

[**下載模擬器**](https://aka.ms/documentdb-emulator)

Azure DocumentDB 模擬器提供一個模擬 Azure DocumentDB 服務的本機環境做為開發之用，當中也能使用 SSL 連線。 這篇文章示範如何匯出 SSL 憑證以用於未整合 Windows 憑證存放區的語言和執行階段，例如使用它自己的[憑證存放區](https://docs.oracle.com/cd/E19830-01/819-4712/ablqw/index.html) (英文) 的 Java，以及使用[通訊端包裝函式](https://docs.python.org/2/library/ssl.html) (英文) 的 Python。 您可以閱讀[使用 Azure DocumentDB 模擬器進行開發和測試](./documentdb-nosql-local-emulator.md)，瞭解更多有關模擬器的資訊。

## <a name="certification-rotation"></a>憑證旋轉

DocumentDB 本機模擬器中的憑證會在模擬器第一次執行時產生。 有兩個憑證。 一個用來連線到本機模擬器，一個用來管理模擬器中的機密資料。 您要匯出的憑證是有 "DocumentDBEmulatorCertificate" 易記名稱的連線憑證。

您可以從 Windows 系統匣中執行的 DocumentDB 本機模擬器按一下 [重設資料] 來重新產生這兩個憑證，如下所示。 如果您重新產生憑證並將這些憑證安裝到 Java 憑證存放區或在其他地方使用它們，則必須更新這些憑證，否則您的應用程式將無法再連線到本機模擬器。

![DocumentDB 本機模擬器的重設資料](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-reset-data.png)

## <a name="how-to-export-the-documentdb-ssl-certificate"></a>如何匯出 DocumentDB SSL 憑證

1. 執行 certlm.msc 啟動 Windows 憑證管理員，瀏覽至 [個人 -> 憑證] 資料夾並開啟有 **DocumentDBEmulatorCertificate** 易記名稱的憑證。

    ![DocumentDB 本機模擬器的匯出步驟 1](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-1.png)

2. 按一下 [詳細資料]，然後按一下 [確定]。

    ![DocumentDB 本機模擬器的匯出步驟 2](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-2.png)

3. 按一下 [複製到檔案...]。

    ![DocumentDB 本機模擬器的匯出步驟 3](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-3.png)

4. 按 [下一步] 。

    ![DocumentDB 本機模擬器的匯出步驟 4](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-4.png)

5. 按一下 [否，不要匯出私密金鑰 ]，然後按 [下一步]。

    ![DocumentDB 本機模擬器的匯出步驟 5](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-5.png)

6. 按一下 [Base-64 編碼的 X.509 (.CER)]，然後按 [下一步]。

    ![DocumentDB 本機模擬器的匯出步驟 6](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-6.png)

7. 指定憑證的名稱。 此案例中為 **documentdbemulatorcert**，然後按 [下一步]。

    ![DocumentDB 本機模擬器的匯出步驟 7](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-7.png)

8. 按一下 [完成] 。

    ![DocumentDB 本機模擬器的匯出步驟 8](./media/documentdb-nosql-local-emulator/azure-documentdb-database-local-emulator-export-step-8.png)

## <a name="how-to-use-the-certificate-in-java"></a>如何在 Java 中使用憑證

執行 Java 應用程式或執行使用 Java 用戶端的 MongoDB 應用程式時，將憑證安裝到 Java 預設憑證存放區比傳遞 "-Djavax.net.ssl.trustStore=<keystore> -Djavax.net.ssl.trustStorePassword="<password>" 旗標更簡單。 例如包含的 [Java 示範應用程式](https://localhost:8081/_explorer/index.html)會根據預設的憑證存放區。

遵循[新增憑證至 Java CA 憑證存放區](https://docs.microsoft.com/en-us/azure/java-add-certificate-ca-store)中的指示，將 X.509 憑證匯入預設的 Java 憑證存放區。 請記住，執行 keytool 時您會在 %JAVA_HOME% 目錄中工作。

"DocumentDBEmulatorCertificate" SSL 憑證安裝好之後，應用程式應該就能夠連線，並使用本機 DocumentDB 模擬器。 如果您仍然遇到問題，可以遵循[為 SSL/TLS 連線進行偵錯](http://docs.oracle.com/javase/7/docs/technotes/guides/security/jsse/ReadDebug.html) (英文) 文章。 很有可能是憑證未安裝至 %JAVA_HOME%/jre/lib/security/cacerts 存放區。 例如，如果您安裝多個 Java 版本，您的應用程式使用的 cacerts 存放區可能與您更新的不同。

## <a name="how-to-use-the-certificate-in-python"></a>如何在 Python 中使用憑證

適用於 DocumentDB 的 Python SDK 在連線到本機模擬器時，預設不會嘗試和使用 SSL 憑證。 不過如果您想要使用 SSL 驗證，可以依照 [Python 通訊端包裝函式](https://docs.python.org/2/library/ssl.html) (英文) 文件中的範例。

## <a name="next-steps"></a>後續步驟
* 若要深入了解 DocumentDB，請參閱 [Azure DocumentDB 簡介](documentdb-introduction.md)
* 若要開始針對 DocumentDB 模擬器進行開發，請下載其中一個[支援的 DocumentDB SDK](documentdb-sdk-dotnet.md)。



<!--HONumber=Dec16_HO3-->


