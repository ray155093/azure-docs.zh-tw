---
title: "為 Web 應用程式有效地使用 DevOps 環境 | Microsoft Docs"
description: "了解如何使用部署位置來設定和管理應用程式的多個開發環境"
services: app-service\web
documentationcenter: 
author: sunbuild
manager: yochayk
editor: 
ms.assetid: 16a594dc-61f5-4984-b5ca-9d5abc39fb1e
ms.service: app-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 10/24/2016
ms.author: sumuth
translationtype: Human Translation
ms.sourcegitcommit: 385eb87ec32f5f605b28cc8c76b1c89c7e90bfec
ms.openlocfilehash: 5284022ea473db893800b0f64b5bf4f811d994aa


---
# <a name="use-devops-environments-effectively-for-your-web-apps"></a>為 Web 應用程式有效地使用 DevOps 環境
本文將說明當應用程式的多個版本在不同環境時 (例如開發、預備、品質保證 (QA) 和生產)，如何設定和管理 Web 應用程式部署。 您應用程式的每個版本可視為適用於部署程序之特定用途的開發環境。 例如，開發人員可以使用 QA 環境來測試應用程式的品質，之後才將變更推入至生產環境。
多個開發環境是具挑戰性的工作，因為您需要追蹤程式碼、管理資源 (計算、Web 應用程式、資料庫、快取等) 和跨環境部署程式碼。

## <a name="set-up-a-non-production-environment-stage-dev-qa"></a>設定非生產環境 (預備、開發、QA)
生產 Web 應用程式啟動並執行之後，下一步是建立非生產環境。 若要使用部署位置，請確定您在標準或進階 Azure App Service 方案模式中執行。 部署位置是具有專屬主機名稱的即時 Web 應用程式。 兩個部署位置 (包括生產位置) 之間的 Web 應用程式內容與設定項目可以互相交換。 當您將應用程式部署到部署位置時，會有下列優點：

- 您可以在預備部署位置中驗證 Web 應用程式的變更，之後再將應用程式與生產位置交換。
- 當您將 Web 應用程式先部署至某個位置，然後再將它交換到生產位置時，該位置的所有執行個體在交換到生產位置之前都已準備就緒。 此程序可排除部署 Web 應用程式時的停機情況。 流量能夠順暢地重新導向，且不會因為交換作業而捨棄任何要求。 若要自動化這整個工作流程，請在不需要預先交換驗證時設定[自動交換](web-sites-staged-publishing.md#configure-auto-swap)。
- 交換之後，含有之前預備 Web 應用程式的位置，現已擁有之前的生產 Web 應用程式。 若交換到生產位置的變更不是您需要的變更，您可以立即執行相同的交換，以取回「上一個已知良好的」Web 應用程式。

若要設定預備部署位置，請參閱[針對 Azure App Service 中的 Web 應用程式設定預備環境](web-sites-staged-publishing.md)。 每個環境都應該包含專屬的一組資源。 例如，如果 Web 應用程式使用資料庫，則生產和預備 Web 應用程式就應該使用不同的資料庫。 新增預備開發環境資源 (例如資料庫、儲存體或快取) 以設定您的預備開發環境。

## <a name="examples-of-using-multiple-development-environments"></a>使用多個開發環境的範例
任何專案都應該至少有兩個環境應遵循原始程式碼管理：開發和生產環境。 如果您使用內容管理系統 (CMS)、應用程式架構等等，在不進行自訂的情況下，應用程式可能無法支援此案例。 此問題常見於一些熱門的架構，我們將在下列數節中討論。 使用 CMS/架構時會浮現很多問題，例如：

- 如何將內容分散到不同環境中？
- 可以變更哪些檔案而不影響架構版本更新？
- 如何管理各環境的設定？
- 如何為模組、外掛程式及核心架構管理版本更新？

有許多方法可以為專案設定多個環境。 下列範例會針對個別應用程式逐一說明方法。

### <a name="wordpress"></a>WordPress
在本節中，您將學習如何使用 WordPress 位置來設定部署工作流程。 WordPress 如同大部分的 CMS 解決方案，在不進行自訂的情況下將不支援多個開發環境。 Azure App Service 的 Web Apps 功能，有一些功能可讓您更輕鬆地將組態設定儲存在您的程式碼之外。

1. 在您建立預備位置之前，請先設定應用程式程式碼來支援多個環境。 若要在 WordPress 中支援多個環境，您需要在您的本機開發 Web 應用程式上編輯 `wp-config.php`，並在檔案的開頭加入下列程式碼。 此程序這會讓您的應用程式根據所選環境挑選正確的組態。

    ```
    // Support multiple environments
    // set the config file based on current environment
    if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) {
    // local development
     $config_file = 'config/wp-config.local.php';
    }
    elseif ((strpos(getenv('WP_ENV'),'stage') !== false) || (strpos(getenv('WP_ENV'),'prod' )!== false ))
    //single file for all azure development environments
     $config_file = 'config/wp-config.azure.php';
    }
    $path = dirname(__FILE__). '/';
    if (file_exists($path. $config_file)) {
    // include the config file if it exists, otherwise WP is going to fail
    require_once $path. $config_file;
    ```

2. 在 Web 應用程式根目錄下建立名為 `config` 的資料夾，並新增 `wp-config.azure.php` 和 `wp-config.local.php` 檔案，它們分別代表 Azure 環境和本機環境。

3. 複製 `wp-config.local.php` 中的下列項目：

    ```
    <?php
    // MySQL settings
    /** The name of the database for WordPress */

    define('DB_NAME', 'yourdatabasename');

    /** MySQL database username */
    define('DB_USER', 'yourdbuser');

    /** MySQL database password */
    define('DB_PASSWORD', 'yourpassword');

    /** MySQL hostname */
    define('DB_HOST', 'localhost');
    /**
     * For developers: WordPress debugging mode.
     * * Change this to true to enable the display of notices during development.
     * It is strongly recommended that plugin and theme developers use WP_DEBUG
     * in their development environments.
     */
    define('WP_DEBUG', true);

    //Security key settings
    define('AUTH_KEY', 'put your unique phrase here');
    define('SECURE_AUTH_KEY','put your unique phrase here');
    define('LOGGED_IN_KEY','put your unique phrase here');
    define('NONCE_KEY', 'put your unique phrase here');
    define('AUTH_SALT', 'put your unique phrase here');
    define('SECURE_AUTH_SALT', 'put your unique phrase here');
    define('LOGGED_IN_SALT', 'put your unique phrase here');
    define('NONCE_SALT', 'put your unique phrase here');

    /**
     * WordPress Database Table prefix.
     *
     * You can have multiple installations in one database if you give each a unique
     * prefix. Only numbers, letters, and underscores please!
     */
    $table_prefix = 'wp_';
    ```

    設定上述程式碼中的安全性金鑰可以協助防止 Web 應用程式受到駭客攻擊，因此請使用唯一值。 如果您需要為程式碼中所述的安全性金鑰產生字串，您可[透過自動產生器 (英文)](https://api.wordpress.org/secret-key/1.1/salt) 來產生新的金鑰/值組。

4. 複製 `wp-config.azure.php`中的下列程式碼：

    ```    
    <?php
    // MySQL settings
    /** The name of the database for WordPress */

    define('DB_NAME', getenv('DB_NAME'));

    /** MySQL database username */
    define('DB_USER', getenv('DB_USER'));

    /** MySQL database password */
    define('DB_PASSWORD', getenv('DB_PASSWORD'));

    /** MySQL hostname */
    define('DB_HOST', getenv('DB_HOST'));

    /**
    * For developers: WordPress debugging mode.
    *
    * Change this to true to enable the display of notices during development.
    * It is strongly recommended that plugin and theme developers use WP_DEBUG
    * in their development environments.
    * Turn on debug logging to investigate issues without displaying to end user. For WP_DEBUG_LOG to
    * do anything, WP_DEBUG must be enabled (true). WP_DEBUG_DISPLAY should be used in conjunction
    * with WP_DEBUG_LOG so that errors are not displayed on the page */

    */
    define('WP_DEBUG', getenv('WP_DEBUG'));
    define('WP_DEBUG_LOG', getenv('TURN_ON_DEBUG_LOG'));
    define('WP_DEBUG_DISPLAY',false);

    //Security key settings
    /** If you need to generate the string for security keys mentioned above, you can go the automatic generator to create new keys/values: https://api.wordpress.org/secret-key/1.1/salt **/
    define('AUTH_KEY',getenv('DB_AUTH_KEY'));
    define('SECURE_AUTH_KEY', getenv('DB_SECURE_AUTH_KEY'));
    define('LOGGED_IN_KEY', getenv('DB_LOGGED_IN_KEY'));
    define('NONCE_KEY', getenv('DB_NONCE_KEY'));
    define('AUTH_SALT', getenv('DB_AUTH_SALT'));
    define('SECURE_AUTH_SALT', getenv('DB_SECURE_AUTH_SALT'));
    define('LOGGED_IN_SALT',  getenv('DB_LOGGED_IN_SALT'));
    define('NONCE_SALT',  getenv('DB_NONCE_SALT'));

    /**
    * WordPress Database Table prefix.
    *
    * You can have multiple installations in one database if you give each a unique
    * prefix. Only numbers, letters, and underscores please!
    */
    $table_prefix = getenv('DB_PREFIX');
```

#### <a name="use-relative-paths"></a>使用相對路徑
最後一件事是將 WordPress 應用程式設定為使用相對路徑。 WordPress 會在資料庫中儲存 URL 資訊。 這種儲存方式會使將內容從一個環境移至另一個環境變得更加困難。 每當您從本機移至預備環境，或是從預備環境移至生產環境時，都必須更新資料庫。 若要減少每次在不同環境間部署資料庫時可能造成問題的風險，請使用[相對根連結外掛程式 (英文)](https://wordpress.org/plugins/root-relative-urls/)，您可以使用 WordPress 管理員儀表板來安裝它。

將下列項目加入至您的 `wp-config.php` 檔案中 `That's all, stop editing!` 註解之前：

```

  define('WP_HOME', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_SITEURL', 'http://'. filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
    define('WP_CONTENT_URL', '/wp-content');
    define('DOMAIN_CURRENT_SITE', filter_input(INPUT_SERVER, 'HTTP_HOST', FILTER_SANITIZE_STRING));
```

透過 WordPress 管理員儀表板中的 `Plugins` 功能表啟用外掛程式。 儲存您的永久連結設定供 WordPress 應用程式使用。

#### <a name="the-final-wp-configphp-file"></a>最終 `wp-config.php` 檔案
任何 WordPress 核心更新並不會影響您的 `wp-config.php`、`wp-config.azure.php` 和 `wp-config.local.php` 檔案。 以下是 `wp-config.php` 檔案的最終版本：

```
<?php
/**
 * The base configurations of the WordPress.
 *
 * This file has the following configurations: MySQL settings, Table Prefix,
 * Secret Keys, and ABSPATH. You can find more information by visiting
 *
 * Codex page. You can get the MySQL settings from your web host.
 *
 * This file is used by the wp-config.php creation script during the
 * installation. You don't have to use the web web app, you can just copy this file
 * to "wp-config.php" and fill in the values.
 *
 * @package WordPress
 */

// Support multiple environments
// set the config file based on current environment
if (strpos($_SERVER['HTTP_HOST'],'localhost') !== false) { // local development
  $config_file = 'config/wp-config.local.php';
}
elseif ((strpos(getenv('WP_ENV'),'stage') !== false) ||(strpos(getenv('WP_ENV'),'prod' )!== false )){
  $config_file = 'config/wp-config.azure.php';
}


$path = dirname(__FILE__). '/';
if (file_exists($path. $config_file)) {
  // include the config file if it exists, otherwise WP is going to fail
  require_once $path. $config_file;
}

/** Database Charset to use in creating database tables. */
define('DB_CHARSET', 'utf8');

/** The Database Collate type. Don't change this if in doubt. */
define('DB_COLLATE', '');


/* That's all, stop editing! Happy blogging. */

define('WP_HOME', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_SITEURL', 'http://'. $_SERVER['HTTP_HOST']);
define('WP_CONTENT_URL', '/wp-content');
define('DOMAIN_CURRENT_SITE', $_SERVER['HTTP_HOST']);

/** Absolute path to the WordPress directory. */
if ( !defined('ABSPATH') )
    define('ABSPATH', dirname(__FILE__). '/');

/** Sets up WordPress vars and included files. */
require_once(ABSPATH. 'wp-settings.php');
```

#### <a name="set-up-a-staging-environment"></a>設定預備環境
1. 如果您已經在 Azure 訂用帳戶中執行 WordPress Web 應用程式，請登入 [Azure 入口網站](http://portal.azure.com)，然後移至 WordPress Web 應用程式。 如果您沒有 WordPress Web 應用程式，您可以從 Azure Marketplace 建立一個。 若要深入了解，請參閱[在 Azure App Service 中建立 WordPress Web 應用程式](web-sites-php-web-site-gallery.md)。
按一下 [設定]  >  [部署位置]  >  [新增] 來建立名稱為 *stage* 的部署位置。 部署位置是與您之前建立的主要 Web 應用程式共用相同資源的另一個 Web 應用程式。

    ![建立階段部署位置](./media/app-service-web-staged-publishing-realworld-scenarios/1setupstage.png)

2. 將另一個 MySQL 資料庫 (假設為 `wordpress-stage-db`) 加入至您的資源群組 `wordpressapp-group`。

    ![將 MySQL 資料庫新增至資源群組](./media/app-service-web-staged-publishing-realworld-scenarios/2addmysql.png)

3. 將預備部署位置的連接字串更新，以指向新的資料庫 `wordpress-stage-db`。 您的生產 Web 應用程式 `wordpressprodapp` 和預備 Web 應用程式 `wordpressprodapp-stage` 必須指向不同的資料庫。

#### <a name="configure-environment-specific-app-settings"></a>設定環境特定的應用程式設定
開發人員可以隨著與 Web 應用程式相關聯的組態資訊 (稱為「應用程式設定」) 在 Azure 中儲存索引鍵/值字串組。 在執行階段，Web 應用程式會為您自動擷取這些值，並使該值可供您 Web 應用程式中執行的程式碼使用。 從安全性觀點來看，這是不錯的附帶效益，因為資料庫連接字串與密碼之類的機密資訊永遠不會在檔案 (例如 `wp-config.php`) 中顯示為純文字。

在下列段落中說明的這個程序非常實用，因為它同時包含 WordPress 應用程式的檔案變更和資料庫變更：

* WordPress 版本升級
* 加入新的或編輯或升級外掛程式
* 加入新的或編輯或升級佈景主題

設定下列項目的應用程式設定：

* 資料庫資訊
* 開啟/關閉 WordPress 記錄
* WordPress 安全性設定

![Wordpress Web 應用程式的應用程式設定](./media/app-service-web-staged-publishing-realworld-scenarios/3configure.png)

請確定您為您的生產 Web 應用程式和預備位置加入下列應用程式設定。 請注意，生產 Web 應用程式和預備 Web 應用程式使用不同的資料庫。

1. 清除 WP_ENV 以外所有設定參數的 [位置設定] 核取方塊。 這個程序將會交換 Web 應用程式的組態、檔案內容和資料庫。 如果已選取 [位置設定]，執行「交換」作業時，Web 應用程式的應用程式設定、連接字串組態將「不會」跨環境移動。 任何的資料庫變更將不會中斷您的生產 Web 應用程式。

2. 使用 WebMatrix 或您選擇的工具 (例如 FTP、Git 或 PhpMyAdmin)，將本機開發環境 Web 應用程式部署到預備 Web 應用程式和資料庫。

    ![WordPress Web 應用程式的 WebMatrix 發佈對話方塊](./media/app-service-web-staged-publishing-realworld-scenarios/4wmpublish.png)

3. 瀏覽及測試您的預備 Web 應用程式。 假設我們要更新 Web 應用程式的佈景主題，以下是預備的 Web 應用程式。

    ![交換位置之前瀏覽預備 Web 應用程式](./media/app-service-web-staged-publishing-realworld-scenarios/5wpstage.png)

4. 如果一切已就緒，請在預備 Web 應用程式上按一下 [交換] 按鈕，將您的內容移到生產環境。 在此情況下，您會在每個「交換」作業期間交換環境之間的 Web 應用程式和資料庫。

    ![交換 WordPress 的變更的預覽](./media/app-service-web-staged-publishing-realworld-scenarios/6swaps1.png)

    > [!NOTE]
    > 如果您的案例只需要推送檔案 (沒有資料庫更新)，那麼請在執行「交換」之前，於 Azure 入口網站 [Web 應用程式設定] 刀鋒視窗中選取與所有資料庫相關的 [應用程式設定] 和 [連接字串設定] 的 [位置設定]。 在此情況下，DB_NAME、DB_HOST、DB_PASSWORD、DB_USER 預設連接字串設定在執行**交換**的時候應該不會顯示在預覽變更中。 在此時，當您完成「交換」作業，WordPress Web 應用程式將只會更新檔案。
    >
    >

    執行「交換」之前，這裡是生產 WordPress Web 應用程式。
    ![交換位置之前的生產 Web 應用程式](./media/app-service-web-staged-publishing-realworld-scenarios/7bfswap.png)

    在「交換」作業之後，佈景主題已在您的生產 Web 應用程式上更新。

    ![交換位置之後的生產 Web 應用程式](./media/app-service-web-staged-publishing-realworld-scenarios/8afswap.png)

5. 當您需要回復時，您可以移至生產 Web [應用程式設定]，並按一下 [交換] 按鈕，將 Web 應用程式與資料庫從生產交換至預備位置。 請記住，只要「交換」作業伴隨有資料庫變更，則在下次重新部署至預備 Web 應用程式時，您就必須將資料庫變更部署到預備 Web 應用程式目前的資料庫。 目前的資料庫可能是先前的生產資料庫或預備資料庫。

#### <a name="summary"></a>摘要
以下是適用於任何具有資料庫之應用程式的通用程序：

1. 在本機環境上安裝應用程式。
2. 包含環境特定組態 (本機和 Azure Web Apps)。
3. 針對 Web Apps 設定預備和生產環境。
4. 如果您已經在 Azure 上執行生產應用程式，請將您的生產內容 (檔案/程式碼和資料庫) 同步到本機和預備環境。
5. 在您的本機環境上開發應用程式。
6. 將您的生產 Web 應用程式置於維護或鎖定模式，並將資料庫內容從生產環境同步到預備環境和開發環境。
7. 部署至預備環境並測試。
8. 部署至生產環境。
9. 重複步驟 4 至 6。

### <a name="umbraco"></a>Umbraco
在本節中，您將了解 Umbraco CMS 如何使用自訂模組在多個 DevOps 環境間進行部署。 此範例將提供不同的方法來管理多個開發環境。

[Umbraco CMS (英文)](http://umbraco.com/) 是許多開發人員常用的 .NET CMS 解決方案。 它提供 [Courier2 (英文)](http://umbraco.com/products/more-add-ons/courier-2) 模組，可從開發環境部署到預備環境或生產環境。 您可以使用 Visual Studio 或 WebMatrix，輕鬆地建立 Umbraco CMS Web 應用程式的本機開發環境。

- [使用 Visual Studio 建立 Umbraco Web 應用程式 (英文)](https://our.umbraco.org/documentation/Installation/install-umbraco-with-nuget)
- [使用 WebMatrix 建立 Umbraco Web 應用程式 (英文)](http://umbraco.tv/videos/umbraco-v7/implementor/fundamentals/installation/creating-umbraco-site-from-webmatrix-web-gallery/)

務必記得移除應用程式下的 `install` 資料夾，而且永遠不要將它上傳至預備或生產 Web 應用程式。 本教學課程使用 WebMatrix。

#### <a name="set-up-a-staging-environment"></a>設定預備環境
1. 如上所述為 Umbraco CMS Web 應用程式建立部署位置 (假設您已有 Umbraco CMS Web 應用程式運作且執行中)。 如果沒有，您可以從 Marketplace 建立一個。
2. 將預備部署位置的連接字串更新，以指向新的 **umbraco-stage-db** 資料庫。 您的生產 Web 應用程式 (umbraositecms-1) 和預備 Web 應用程式 (umbracositecms-1-stage)「必須」指向不同的資料庫。

    ![使用新預備資料庫更新預備 Web 應用程式的連接字串](./media/app-service-web-staged-publishing-realworld-scenarios/9umbconnstr.png)

3. 按一下部署位置 **stage** 的 [取得發佈設定]。 此程序會下載發佈設定檔，它可儲存 Visual Studio 或 WebMatrix 將您的應用程式從本機開發 Web 應用程式發佈到 Azure Web 應用程式所需的一切資訊。

    ![取得預備 Web 應用程式的發佈設定](./media/app-service-web-staged-publishing-realworld-scenarios/10getpsetting.png)
4. 在 WebMatrix 或 Visual Studio 中開啟您的本機開發 Web 應用程式。 本教學課程使用 WebMatrix。 首先，您必須為您的預備 Web 應用程式匯入發佈設定檔。

    ![使用 WebMatrix 匯入 Umbraco 的發佈設定](./media/app-service-web-staged-publishing-realworld-scenarios/11import.png)

5. 在對話方塊中檢閱變更，並將本機 Web 應用程式部署至您的 Azure Web 應用程式 *umbracositecms-1-stage*。 當您將檔案直接部署到預備 Web 應用程式時，會略過 `~/app_data/TEMP/` 資料夾中的任何檔案，因為這些檔案將在預備 Web 應用程式首次啟動時重新產生。 您也應該省略 `~/app_data/umbraco.config` 檔案，該檔案也將重新產生。

    ![在 WebMatrix 中檢閱發佈變更](./media/app-service-web-staged-publishing-realworld-scenarios/12umbpublish.png)

6. 在您成功發佈 Umbraco 本機 Web 應用程式至預備 Web 應用程式之後，請瀏覽至預備 Web 應用程式，並執行一些測試來排除任何問題。

#### <a name="set-up-the-courier2-deployment-module"></a>設定 Courier2 部署模組
透過 [Courier2 (英文)](http://umbraco.com/products/more-add-ons/courier-2) 模組，您只要以右鍵按一下即可從預備 Web 應用程式將內容、樣式表及部署模組推送到生產 Web 應用程式。 此程序可以降低在部署更新時中斷生產 Web 應用程式的風險。
針對 `*.azurewebsites.net` 網域和您的自訂網域 (假設為 http://abc.com) 購買 Courier2 授權。 購買授權後，將下載的授權 (.LIC 檔案) 放置到 `bin` 資料夾中。

![將授權檔案拖放到 bin 資料夾下](./media/app-service-web-staged-publishing-realworld-scenarios/13droplic.png)

1. [下載 Courier2 套件 (英文)](https://our.umbraco.org/projects/umbraco-pro/umbraco-courier-2/)。 登入您的預備 Web 應用程式 (假設是 http://umbracocms-site-stage.azurewebsites.net/umbraco)，按一下 [開發人員] 功能表然後按一下 [套件]  >  [安裝本機套件]。

    ![Umbraco 套件安裝程式](./media/app-service-web-staged-publishing-realworld-scenarios/14umbpkg.png)

2. 使用安裝程式將 Courier2 套件上傳。

    ![上傳 courier 模組的套件](./media/app-service-web-staged-publishing-realworld-scenarios/15umbloadpkg.png)

3. 若要設定套件，您必須更新 Web 應用程式 [Config] 資料夾下的 courier.config 檔案。

    ```xml
    <!-- Repository connection settings -->
     <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
     <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
        <repository name="production web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
          <url>http://umbracositecms-1.azurewebsites.net</url>
          <user>0</user>
          <!--<login>user@email.com</login> -->
          <!-- <password>user_password</password>-->
          <!-- <passwordEncoding>Clear</passwordEncoding>-->
          </repository>
     </repositories>
     ```

4. 在 `<repositories>`底下，輸入生產網站 URL 和使用者資訊。
    如果您使用預設 Umbraco 成員資格提供者，則請在 &lt;user&gt; 區段中新增管理員使用者的識別碼。
    如果您使用自訂 Umbraco 成員資格提供者，請使用 Courier2 模組中的 `<login>`、`<password>` 來連接到生產網站。
    如需詳細資訊，[請檢閱 Courier2 模組的文件 (英文)](http://umbraco.com/help-and-support/customer-area/courier-2-support-and-download/developer-documentation)。

5. 同樣地，請在您的生產網站上安裝 Courier2 模組，並如此處所示，將其設定為在各自的 courier.config 檔案中指向預備 Web 應用程式。

    ```xml
     <!-- Repository connection settings -->
     <!-- For each site, a custom repository must be configured, so Courier knows how to connect and authenticate-->
     <repositories>
        <!-- If a custom Umbraco Membership provider is used, specify login & password + set the passwordEncoding to clear: -->
        <repository name="Stage web app" alias="stage" type="CourierWebserviceRepositoryProvider" visible="true">
          <url>http://umbracositecms-1-stage.azurewebsites.net</url>
          <user>0</user>
          </repository>
     </repositories>
    ```

6. 在 Umbraco CMS Web 應用程式儀表板中的 [Courier2] 索引標籤上按一下，並選取 [位置]。 您應該會看到 `courier.config`中所提及的儲存機制名稱。 請在生產和預備 Web 應用程式上執行此程序。

    ![檢視目的地 Web 應用程式儲存機制](./media/app-service-web-staged-publishing-realworld-scenarios/16courierloc.png)

7. 若要將內容從預備網站部署至生產網站，請移至 [內容]，然後選取現有的頁面或建立新頁面。 我將從我的 Web 應用程式選取現有的網頁 (其中頁面的標題會是「開始使用 - 新增」)，接著按一下 [儲存並發佈]。

    ![變更頁面的標題並發佈](./media/app-service-web-staged-publishing-realworld-scenarios/17changepg.png)

8. 以滑鼠右鍵按一下已修改的頁面來檢視所有選項。 按一下 [Courier] 開啟 [部署] 對話方塊。 按一下 [部署] 來起始部署。

    ![Courier 模組部署對話方塊](./media/app-service-web-staged-publishing-realworld-scenarios/18dialog1.png)

9. 檢閱變更，然後按一下 [繼續]。

    ![Courier 模組部署對話方塊檢閱變更](./media/app-service-web-staged-publishing-realworld-scenarios/19dialog2.png)

    部署記錄檔會顯示部署是否成功。

     ![檢視 Courier 模組的部署記錄檔](./media/app-service-web-staged-publishing-realworld-scenarios/20successdlg.png)

10. 瀏覽您的生產 Web 應用程式以查看是否反映了變更。

     ![瀏覽實際執行 Web 應用程式](./media/app-service-web-staged-publishing-realworld-scenarios/21umbpg.png)

若要深入了解如何使用 Courier，請檢閱文件集。

#### <a name="how-to-upgrade-the-umbraco-cms-version"></a>如何升級 Umbraco CMS 版本
從某個版本的 Umbraco CMS 升級至另一個版本時，Courier 並沒有幫助。 升級 Umbraco CMS 版本時，您必須檢查與您的自訂模組或合作夥伴模組和 Umbraco 核心程式庫的不相容項目。 以下是最佳作法：

* 務必在升級之前備份您的 Web 應用程式和資料庫。 在 Azure 中的 Web 應用程式上，您可以使用備份功能設定網站的自動備份，並在需要時使用還原功能還原您的網站。 如需詳細資訊，請參閱[如何備份您的 Web 應用程式](web-sites-backup.md)和[如何還原您的 Web 應用程式](web-sites-restore.md)。
* 檢查您要升級的版本是否與合作夥伴的套件相容。 在套件的下載頁面，檢閱與 Umbraco CMS 版本的專案相容性。

如需如何在本機升級 Web 應用程式的詳細資料，[請參閱一般升級指導方針 (英文)](https://our.umbraco.org/documentation/getting-started/setup/upgrading/general)。

在本機開發網站升級之後，將變更發佈至預備 Web 應用程式。 測試您的應用程式。 如果一切看起來正常，請使用 [交換] 按鈕，將預備網站交換為生產 Web 應用程式。 使用「交換」作業時，您可以檢視將在您的 Web 應用程式組態中受到影響的變更。 此「交換」作業會交換 Web 應用程式和資料庫。 在「交換」之後，生產 Web 應用程式會指向 umbraco-stage-db 資料庫，而預備 Web 應用程式會指向 umbraco-prod-db 資料庫。

![交換部署 Umbraco CMS 的預覽](./media/app-service-web-staged-publishing-realworld-scenarios/22umbswap.png)

以下為交換 Web 應用程式和資料庫的優點：

* 如果發生任何應用程式問題，您可以利用另一個「交換」回復至舊版的 Web 應用程式。
* 針對升級，您需要將來自預備 Web 應用程式的檔案和資料庫部署到生產 Web 應用程式和資料庫。 當您部署檔案和資料庫時，有許多情況可能發生錯誤。 若使用位置的「交換」功能，我們就可以在升級期間減少停機時間，並降低部署變更時可能發生的失敗風險。
* 您可以使用「在生產環境中測試」功能執行 [A/B 測試 (英文)](https://azure.microsoft.com/documentation/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)。

此範例將示範平台的彈性，讓您得以建置類似於 Umbraco Courier 模組的自訂模組，以便管理整個環境的部署。

## <a name="references"></a>參考
[敏捷式軟體開發 (Agile Software Development) 與 Azure App Service](app-service-agile-software-development.md)

[針對 Azure App Service 中的 Web 應用程式設定預備環境](web-sites-staged-publishing.md)

[封鎖對非生產部署位置的 Web 存取](http://ruslany.net/2014/04/azure-web-sites-block-web-access-to-non-production-deployment-slots/)



<!--HONumber=Dec16_HO3-->


