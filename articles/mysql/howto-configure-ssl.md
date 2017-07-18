---
title: "設定 SSL 連線能力，以安全地連線到適用於 MySQL 的 Azure 資料庫 | Microsoft Docs"
description: "有關如何適當設定「適用於 MySQL 的 Azure 資料庫」及相關聯應用程式以適當使用 SSL 連線的指示"
services: mysql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 06/01/2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 1303e6cf6f6fdd10a7310d770127828276d5056e
ms.contentlocale: zh-tw
ms.lasthandoff: 06/30/2017

---
# <a name="configure-ssl-connectivity-in-your-application-to-securely-connect-to-azure-database-for-mysql"></a>在您的應用程式中設定 SSL 連線能力，以安全地連線至適用於 MySQL 的 Azure 資料庫
適用於 MySQL 的 Azure 資料庫支援使用安全通訊端層 (SSL)，將適用於 MySQL 的 Azure 資料庫伺服器連線至用戶端應用程式。 在您的資料庫伺服器和用戶端應用程式之間強制使用 SSL 連線，可將伺服器與應用程式之間的資料流加密，有助於抵禦「中間人」攻擊。

根據預設，資料庫服務應該會設定為在連線至「適用於 MySQL 的 Azure 資料庫」伺服器時需要 SSL 連線。  請盡可能避免停用 SSL 選項。

## <a name="enforcing-ssl-connections"></a>強制使用 SSL 連線
透過 Azure 入口網站和 CLI 佈建新的適用於 MySQL 伺服器的 Azure 資料庫時，預設會強制啟用 SSL 連線。

同樣地，在 Azure 入口網站中，您的伺服器下方的 [連接字串] 設定中所預先定義的連接字串，也包含通用語言要求 SSL 所需的參數。 SSL 參數會根據連接器而有所不同，例如，"ssl=true" 或 "sslmode=require" 或 "sslmode=required" 及其他變化。

## <a name="configure-enforcement-of-ssl"></a>設定強制使用 SSL
您可以停用或啟用「強制使用 SSL」。 Microsoft Azure 建議一律啟用 [強制使用 SSL 連線] 設定，以增強安全性。

### <a name="using-azure-portal"></a>使用 Azure 入口網站
使用 Azure 入口網站，瀏覽適用於 MySQL 的 Azure 資料庫伺服器，然後按一下 [連線安全性]。 使用切換按鈕來啟用或停用 [強制使用 SSL 連線] 設定。 然後按一下 [儲存] 。 Microsoft 建議一律啟用 [強制使用 SSL 連線] 設定，以增強安全性。
![啟用 ssl](./media/howto-configure-ssl/enable-ssl.png)

您可以檢視 [概觀] 頁面來查看 [SSL 強制執行狀態] 指標，以確認此設定。

### <a name="using-azure-cli"></a>使用 Azure CLI
您可以在 Azure CLI 中分別使用 Enabled 或 Disabled 值，以啟用或停用 **ssl-enforcement** 參數。
```azurecli-interactive
az mysql server update --resource-group myresource --name mysqlserver4demo --ssl-enforcement Enabled
```
## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>確定您的應用程式或架構支援 SSL 連線
許多使用 MySQL 取得資料庫服務的常見應用程式 (例如 Wordpress、Drupal 和 Magento)，在安裝期間預設為不啟用 SSL。 在安裝之後，或透過應用程式特定的 CLI 命令，啟用那些應用程式中的 SSL 連線能力。 如果您的 MySQL 伺服器強制使用 SSL 連線，若相關聯的應用程式未正確設定，則可能無法連線到您的資料庫伺服器。 請參閱您的應用程式文件，以了解如何啟用 SSL 連線。

## <a name="applications-that-require-a-local-certificate-for-ssl-connectivity"></a>需要憑證才能啟用 SSL 連線能力的應用程式
在某些情況下，應用程式需要有從憑證授權單位 (CA) 憑證檔案 (.cer) 所產生的本機憑證檔 (.pem)，才能安全地連線。  請參閱下列步驟來取得 .cer 檔案、產生本機 .pem 檔案，並將它繫結到您的應用程式。

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>從憑證授權單位 (CA) 下載憑證檔
您可以在[這裡](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt)找到透過 SSL 與「適用於 MySQL 的 Azure 資料庫」伺服器通訊所需的憑證。  將憑證檔案下載至本機磁碟機 (本教學課程中，我們使用 **c:\ssl**)。

### <a name="download-and-install-openssl-on-your-pc"></a>在您的電腦下載並安裝 OpenSSL
若要產生必要的本機 **.pem** 檔案，以便應用程式能夠安全地連線至資料庫伺服器，您需要在本機電腦上安裝 OpenSSL。  

下列各節說明您可以使用的方法。 根據您慣用的作業系統而定，您可以使用 Linux 或 Windows 電腦。 您只需要依照一個方法執行。

### <a name="linux-users---download-and-install-openssl-using-a-linux-pc"></a>Linux 使用者 - 使用 Linux 電腦下載並安裝 OpenSSL
[OpenSSL Software Foundation](http://www.openssl.org) 直接以原始程式碼形式提供 OpenSSL 程式庫。  下列指示會引導您逐步完成在 Linux 電腦上安裝 OpenSSL 的必要步驟。  在本指南中，我們會示範適用於 Ubuntu 12.04 和更新版本的命令。

開啟終端機工作階段並安裝 OpenSSL
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
```  
從下載套件中將檔案解壓縮
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
進入已解壓縮檔案的目錄。  預設應該如下所示。

```bash
cd openssl-1.1.0e
```
執行下列命令來設定 OpenSSL：如果您想要的檔案不是在 /usr/local/openssl 資料夾中，請務必適當地變更下列命令。

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
既然已正確設定 OpenSSL，則您需要編譯它來轉換憑證。  若要進行編譯，請執行下列命令：

```bash
make
```
編譯完成之後，您就可以執行下列命令，以可執行檔形式安裝 OpenSSL：
```bash
make install
```
若要確認您已在系統上成功安裝 OpenSSL，請執行下列命令。 檢查以確定您得到相同的輸出。

```bash
/usr/local/openssl/bin/openssl version
```
如果成功，您應該會看到下列訊息：
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

### <a name="windows-pc-users---download-and-install-openssl-using-a-windows-pc"></a>Windows 電腦使用者 - 使用 Windows 電腦下載並安裝 OpenSSL
若要產生必要的本機 **.pem** 檔案，以便應用程式能夠安全地連線至資料庫伺服器，您需要在本機電腦上安裝 OpenSSL。

[OpenSSL Software Foundation](http://www.openssl.org) 直接以原始程式碼形式提供 OpenSSL 程式庫。 下列指示會引導您逐步完成在 Linux 電腦上安裝 OpenSSL 的必要步驟。

在 Windows 電腦上可使用下列方式安裝 OpenSSL：

1. **(建議)** 在 Window 10 和更新版本中使用內建的 Bash for Windows 功能，預設會安裝 OpenSSL。  您可以在[這裡](https://msdn.microsoft.com/commandline/wsl/install_guide)找到如何在 Window 10 啟用 Bash for Windows 功能的指示。

2. 透過下載社群所提供的 Win32/64 應用程式。 雖然 OpenSSL Software Foundation 不提供或建議任何特定的 Windows 安裝程式，但他們在[這裡](https://wiki.openssl.org/index.php/Binaries)提供可用的安裝程式清單

### <a name="convert-your-cer-certificate-to-a-local-pem"></a>將 .cer 憑證轉換成本機 .pem
下載的根 CA 檔案是 **.cer** 格式。 使用 OpenSSL 將憑證檔案轉換成 **.pem**。  若要這樣做，請執行 openssl.exe 命令列工具，並執行下列命令︰

```dos
OpenSSL>x509 -inform DER -in BaltimoreCyberTrustRoot.crt -out MyServerCACert.pem
```
既然您已成功建立憑證檔案 (MyServerCACert.pem)，您現在可以透過 SSL 安全地連線至資料庫伺服器。

下列範例示範如何透過 MySQL 命令列介面和透過使用 MySQL Workbench，利用 **MyServerCACert.pem** 檔案來連線至您的 MySQL 伺服器。

### <a name="connecting-to-server-using-the-mysql-cli-over-ssl"></a>使用 MySQL CLI 透過 SSL 連線至伺服器
使用 MySQL 命令列介面，執行下列命令：

```dos
mysql.exe -h mysqlserver4demo.mysql.database.azure.com -uUsername@mysqlserver4demo -pYourPassword --ssl-ca=c:\ssl\MyServerCACert.pem
```
執行 mysql **status** 命令，確認您已使用 SSL 連線至 MySQL 伺服器︰

```dos
mysql> status
--------------
mysql.exe  Ver 14.14 Distrib 5.7.18, for Win64 (x86_64)

Connection id:          65535
Current database:
Current user:           jason@66.235.55.141
SSL:                    Cipher in use is AES256-SHA
Using delimiter:        ;
Server version:         5.6.26.0 MySQL Community Server (GPL)
Protocol version:       10
Connection:             janderserver.mysql.sqltest-eg1.mscds.com via TCP/IP
Server characterset:    latin1
Db     characterset:    latin1
Client characterset:    cp850
Conn.  characterset:    cp850
TCP port:               3306
Uptime:                 1 day 19 hours 9 min 43 sec

Threads: 4  Questions: 26082  Slow queries: 0  Opens: 112  Flush tables: 1  Open tables: 105  Queries per second avg: 0.167
--------------
```

> [!NOTE]
> 目前，您在對服務的 mysql.exe 連線中使用 "--ssl-mode=VERIFY_IDENTITY" 選項時發生已知的問題，連線會失敗並出現下列錯誤︰_錯誤 2026 (HY000): SSL 連線錯誤: SSL 憑證驗證失敗_。請降級至 "--ssl-mode=VERIFY_CA" 或較低的 [SSL 模式](https://dev.mysql.com/doc/refman/5.7/en/secure-connection-options.html#option_general_ssl-mode) \(英文\)。 如果您需要使用 "--ssl-mode=VERIFY_IDENTITY"，請改為使用區域伺服器名稱，直到解決此問題為止。 偵測您的伺服器名稱以決定區域伺服器，例如 `westeurope1-a.control.database.windows.net`，並在連線中使用該區域伺服器名稱。 我們計劃在未來移除此限制。

### <a name="connecting-to-server-using-the-mysql-workbench-over-ssl"></a>使用 MySQL Workbench 透過 SSL 連線至伺服器
設定使 MySQL Workbench 安全地透過 SSL 連線。 瀏覽至 MySQL Workbench [設定新連線] 對話方塊中的 [SSL] 索引標籤。 在 [SSL CA 檔案:] 欄位中輸入 **MyServerCACert.pem** 的檔案位置。
![儲存自訂的圖格](./media/howto-configure-ssl/mysql-workbench-ssl.png)

## <a name="next-steps"></a>後續步驟
檢閱[「適用於 MySQL 的 Azure 資料庫」的連線庫](concepts-connection-libraries.md)後面的各種應用程式連線能力選項

