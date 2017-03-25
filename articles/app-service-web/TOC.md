# 概觀    
## [關於 Web Apps](app-service-web-overview.md)
## [比較主機選項](choose-web-site-cloud-service-vm.md)

# 快速入門    
## [建立靜態 HTML 網站](app-service-web-get-started-html.md)
## [建立 ASP.NET 應用程式](app-service-web-get-started-dotnet.md)        
## [建立 PHP 應用程式](app-service-web-get-started-php.md) 
## [建立 Node.js 應用程式](app-service-web-get-started-nodejs.md) 
## [建立 Java 應用程式](app-service-web-get-started-java.md)        
## [建立 Python 應用程式](app-service-web-get-started-python.md)    

# 範例
## [Azure CLI](app-service-cli-samples.md) 
## [PowerShell](app-service-powershell-samples.md) 

# 教學課程
## [在 Web 應用程式中新增功能](app-service-web-get-started-2.md)
## [ASP.NET 應用程式搭配 SQL Database](web-sites-dotnet-get-started.md)
## [Laravel 應用程式搭配 MySQL](app-service-web-php-get-started.md)
## [Sails.js 應用程式搭配 NOSQL DB](app-service-web-nodejs-sails.md)
## [Java 應用程式搭配 Eclipse](app-service-web-eclipse-create-hello-world-web-app.md)
## [Java 應用程式搭配 IntelliJ](app-service-web-intellij-create-hello-world-web-app.md)
## [Django 應用程式搭配 MySQL](web-sites-python-ptvs-django-mysql.md)

# 概念
## [App Service 的運作方式](../app-service/app-service-how-works-readme.md?toc=%2fazure%2fapp-service-web%2ftoc.json)    
## [App Service 方案](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md?toc=%2fazure%2fapp-service-web%2ftoc.json)    
## [App Service 環境](app-service-app-service-environment-intro.md)
## [驗證和授權](../app-service/app-service-authentication-overview.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
## [透過內部部署進行驗證](web-sites-authentication-authorization.md)


# 使用說明指南
## 開發您的應用程式    
### ASP.NET

#### [使用 VS 程式碼開發 ASP.NET 核心應用程式](web-sites-create-web-app-using-vscode.md)
### PHP
#### [設定 PHP 專案](web-sites-php-configure.md)
#### [設定 WordPress 多網站](web-sites-php-convert-wordpress-multisite.md) 
### Node.js
#### [使用 io.js](web-sites-nodejs-iojs.md)
#### [偵錯 Node.js 應用程式](web-sites-nodejs-debug.md)
### Java

#### [使用 Azure SDK for Java](java-create-azure-website-using-java-sdk.md)
#### [上傳現有應用程式](web-sites-java-add-app.md)
#### [遠端偵錯 Eclipse](app-service-web-debug-java-web-app-in-eclipse.md)
#### [遠端偵錯 IntelliJ](app-service-web-debug-java-web-app-in-intellij.md)

### [使用 SendGrid 傳送電子郵件](sendgrid-dotnet-how-to-send-email.md)

### 設定執行階段    
#### [PHP on Windows](web-sites-php-configure.md)

#### [Java](web-sites-java-add-app.md)
#### [Node.js on Linux](app-service-linux-using-nodejs-pm2.md)
#### [Python](web-sites-python-configure.md)
            
### 設定應用程式
#### [使用應用程式設定](web-sites-configure.md)
            
## [部署至 Azure](web-sites-deploy.md)
### [透過 FTP 進行部署](app-service-deploy-content-sync.md)
### [透過雲端同步進行部署](web-sites-deploy.md)
### [連續部署](app-service-continuous-deployment.md)
### [部署至預備環境](web-sites-staged-publishing.md)
### [從本機 Git 進行部署](app-service-deploy-local-git.md)
### [透過範本進行部署](app-service-deploy-complex-application-predictably.md)
### [敏捷式部署](app-service-agile-software-development.md)
### [Beta 版測試](app-service-web-test-in-production-controlled-test-flight.md)

### [設定部署認證](app-service-deployment-credentials.md)

### 對應自訂網域
#### [購買網域](custom-dns-web-site-buydomains-web-app.md)
#### [對應第三方網域](web-sites-custom-domain-name.md)
#### [使用流量管理員對應網域](web-sites-traffic-manager-custom-domain-name.md)
#### [對應 GoDaddy 網域](web-sites-godaddy-custom-domain-name.md)
#### [移轉作用中的網域](app-service-custom-domain-name-migrate.md)

### [從 IIS 移轉](web-sites-migration-from-iis-server.md)
### [在生產環境中測試](app-service-web-test-in-production-get-start.md)

## 連線至 DB/資源        

### [連線至內部部署資料](web-sites-hybrid-connection-get-started.md) 
### [連線至 Azure VNet](web-sites-integrate-with-vnet.md)
### [使用 PowerShell 連線至 Azure VNet](app-service-vnet-integration-powershell.md)
### [連線至 Azure VM 上的 MongoDB](web-sites-dotnet-store-data-mongodb-vm.md)
            
##安全的應用程式
### 驗證使用者        
#### [使用 Azure AD 進行驗證](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [使用 Facebook 進行驗證](../app-service-mobile/app-service-mobile-how-to-configure-facebook-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [使用 Google 進行驗證](../app-service-mobile/app-service-mobile-how-to-configure-google-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [使用 Microsoft 帳戶進行認證](../app-service-mobile/app-service-mobile-how-to-configure-microsoft-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [使用 Twitter 進行驗證](../app-service-mobile/app-service-mobile-how-to-configure-twitter-authentication.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
#### [使用內部部署 AD 進行驗證](web-sites-authentication-authorization.md)
#### [搭配多租用戶資料庫的應用程式](web-sites-dotnet-entity-framework-row-level-security.md)
### 指派自訂的 SSL
#### [購買 SSL 憑證](web-sites-purchase-ssl-web-site.md)
#### [設定第三方 SSL 憑證](web-sites-configure-ssl-certificate.md)
### [強制使用 HTTPS](web-sites-configure-ssl-certificate.md#enforce-https-on-your-app)
### [設定 TLS 相互驗證](app-service-web-configure-tls-mutual-auth.md)
            
##調整應用程式        
### [向上擴充](web-sites-scale.md)
### [橫向擴充](../monitoring-and-diagnostics/insights-how-to-scale.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
### [包含流量管理員的負載平衡](web-sites-traffic-manager.md)
### [可高度調整的 App Service 環境](../app-service/app-service-app-service-environments-readme.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
### [使用 Azure CDN 將觸角擴及全球](cdn-websites-with-cdn.md)
### [透過 Memcache 連線至 Redis 快取](web-sites-connect-to-redis-using-memcache-protocol.md)
### [建立 Redis 快取](../redis-cache/cache-redis-cache-arm-provision.md?toc=%2fazure%2fapp-service-web%2ftoc.json)
### [使用 Azure Redis 快取管理工作階段狀態](web-sites-dotnet-session-state-caching.md)

## 監視 
### [監視應用程式](web-sites-monitor.md)
### [啟用記錄](web-sites-enable-diagnostic-log.md)
### [串流記錄](web-sites-streaming-logs-and-console.md)

## 備份內容        
### [備份您的應用程式](web-sites-backup.md)
### [從備份還原您的應用程式](web-sites-restore.md)
### [使用 REST API 進行備份](websites-csm-backup.md)

## 管理應用程式資源
### [使用 PowerShell 複製應用程式](app-service-web-app-cloning.md)
### [使用入口網站複製應用程式](app-service-web-app-cloning-portal.md)
### [移動資源](app-service-move-resources.md)
### [使用包含 PowerShell 的 Azure Resource Manager](app-service-web-app-azure-resource-manager-powershell.md)
### [使用 Azure 自動化管理應用程式](automation-manage-web-app.md)



# 參考    
## [CLI 2.0](/cli/azure/appservice)
## [PowerShell](/powershell)
## [REST API](/rest/api/appservice/) 
        
# 資源    
## 疑難排解        
### [使用 Visual Studio 進行疑難排解](web-sites-dotnet-troubleshoot-visual-studio.md)
### [針對 Node.js 應用程式進行疑難排解](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
### [疑難排解 HTTP 502 與 503](app-service-web-troubleshoot-http-502-http-503.md)
### [針對效能問題進行疑難排解](app-service-web-troubleshoot-performance-degradation.md)
## [價格](https://azure.microsoft.com/pricing/details/app-service/)     
## [配額資訊](../azure-subscription-service-limits.md#app-service-limits)    
## [服務更新與版本資訊](https://azure.microsoft.com/updates/?product=app-service)    
## [最佳做法](app-service-best-practices.md)
## [範例](https://azure.microsoft.com/resources/samples/?service=app-service)    
## [影片](https://azure.microsoft.com/resources/videos/index/?services=app-service)
## Cookbooks    
### [參考架構](../guidance/guidance-ra-app-service.md)    
### [部署指令碼](https://azure.microsoft.com/documentation/scripts/)    
