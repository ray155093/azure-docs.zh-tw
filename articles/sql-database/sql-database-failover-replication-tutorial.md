---
title: "複寫及容錯移轉 Azure SQL Database 方案 | Microsoft Docs"
description: "了解如何設定您的 Azure SQL Database 和應用程式來容錯移轉至複寫資料庫，並測試容錯移轉。"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jstrauss
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-failover
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/18/2017
ms.author: sashan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7e5b82da402ab9d20410746c2b6c6b3aaab5b754
ms.contentlocale: zh-tw
ms.lasthandoff: 05/10/2017


---

# <a name="replicate-and-failover-an-azure-sql-database-solution"></a>複寫及容錯移轉 Azure SQL Database 方案

在本教學課程中，您要設定 Azure SQL Database 和應用程式來容錯移轉到遠端區域，然後測試您的容錯移轉計畫。 

如果您沒有 Azure 訂用帳戶，請在開始前建立[免費帳戶](https://azure.microsoft.com/free/) 。

若要完成本教學課程，請確定您具有下列項目︰

- 最新版的 [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS)。 安裝 SSMS 時亦會安裝最新版的 SQLPackage，此命令列公用程式可用於自動化處理資料庫開發工作的範圍。 
- 要移轉的 Azure 資料庫。 本教學課程使用下列其中一個快速入門中，名稱為 **mySampleDatabase 的 AdventureWorksLT 範例資料庫︰

   - [建立 DB - 入口網站](sql-database-get-started-portal.md)
   - [建立 DB - CLI](sql-database-get-started-cli.md)

## <a name="create-azure-active-directory-users-optional"></a>建立 Azure Active Directory 使用者 (選擇性)

在此步驟中，您要建立或識別 Azure Active Directory 使用者，並新增至您的 Azure SQL Database 邏輯伺服器和範例資料庫做為使用者。
- 如果您的訂用帳戶屬於有現有使用者帳戶的 Azure Active Directory 公司環境，請識別 3 個使用者帳戶以做為本教學課程中的 Active Directory 管理使用者、應用程式管理，以及應用程式使用者，並繼續進行步驟 3︰建立 SQL Database 登入和使用者。 
- 如果您的訂用帳戶不屬於 Azure Active Directory 公司環境，或屬於沒有現有使用者帳戶的 Azure Active Directory 公司環境，而且您有權限建立新的 Azure Active Directory 使用者帳戶。

1. 登入 [Azure 入口網站](http://portal.azure.com)。
2. 在左側功能表中，按一下 [更多服務]。
3. 在 [篩選] 文字方塊中，輸入 **Azure**，然後選取 [Azure Active Directory]。
4. 在 [Azure Active Directory] 頁面上的 [快速工作] 窗格中，按一下 [加入使用者]。
5. 在 [使用者] 表單上，建立下列使用者。
   - 名稱︰**ad-admin**
   - 使用者名稱︰**ad-admin@yourdomain** (Yopu4708)
6. 選取 [顯示密碼] 核取方塊，並記錄此使用者帳戶的密碼以供稍後使用。
7. 按一下 [建立] 。
8. 重複上述 3 個步驟來建立下列 2 個新的使用者。
   - 名稱︰**app-admin**
   - 使用者名稱︰**app-admin@yourdomain** (Buju4319)
   - 名稱︰**app-user**
   - 使用者名稱︰**app-user@yourdomain** (Nonu4001)。

9. 開啟新的瀏覽器視窗並使用新建立的 **ad-admin** 帳戶登入 Azure 入口網站。
10. 在 [更新您的密碼] 頁面上，在 [目前密碼] 方塊中輸入系統產生的密碼。 
11. 在 [新密碼] 和 [確認密碼] 方塊中，輸入您選擇的密碼。
12. 按一下 [更新密碼並登入]。

## <a name="configure-sql-database-integration-with-azure-active-directory"></a>使用 Azure Active Directory 設定 SQL Database 整合

1. 按一下左側功能表中的 [更多服務]，在篩選文字方塊中輸入 **sql**，然後選取 [SQL Server]。
2. 在 [SQL Server] 頁面上，按一下您的 SQL Database 伺服器。
3. 在伺服器 [概觀] 頁面的 [程式集] 窗格中，按一下 [Active Directory 管理] 下的 [未設定]。
4. 在 [Active Directory 管理] 頁面上，按一下 [設定管理員]。
5. 選取 [ad-admin] Azure Active Directory 帳戶 (或其他現有的帳戶，例如您自己的帳戶) 做為 Azure SQL Database 伺服器的伺服器管理員。
6. 按一下 [選取] 。
7. 按一下 [儲存] 。

## <a name="create-users-with-permissions-for-your-database"></a>為資料庫建立具有權限的使用者

<TO DO: need to change script to grant app-user sufficient permissions to perform operation in java app>

使用 SQL Server Management Studio 連線到您的資料庫，並建立使用者帳戶。 這些使用者帳戶會自動複寫到次要伺服器。 如果您從 IP 位址尚未設定防火牆的用戶端連線，您可能需要設定防火牆規則。 如需步驟，請參閱[使用 Azure 入口網站建立 SQL DB](sql-database-get-started-portal.md)。

1. 開啟 SQL Server Management Studio。
2. 將 [驗證] 模式變更為 [Active Directory 密碼驗證]。
3. 使用全新設計的 Azure Active Directory 伺服器管理帳戶連線到您的伺服器。 
4. 在 [物件總管] 中，展開 [系統資料庫]，在 **mySampleDatabase** 上按一下滑鼠右鍵，然後按一下 [新增查詢]。
5. 在查詢視窗中，執行下列查詢以在您的資料庫中建立使用者帳戶，授與 **db_owner** 權限給兩個管理帳戶。 將網域名稱的預留位置取代為您的網域。

   ```tsql
   --Create Azure AD user account
   CREATE USER [app-admin@<yourdomain>] FROM EXTERNAL PROVIDER;
   --Add Azure AD user to db_owner role
   ALTER ROLE db_owner ADD MEMBER [app-admin@<yourdomain>]; 
   --Create additional Azure AD user account
   CREATE USER [app-user@<yourdomain>] FROM EXTERNAL PROVIDER;
   --Create SQL user account
   CREATE USER app_admin WITH PASSWORD = 'MyStrongPassword1';
   --Add SQL user to db_owner role
   ALTER ROLE db_owner ADD MEMBER app_admin; 
   --Create additional SQL user
   CREATE USER app_user WITH PASSWORD = 'MyStrongPassword1';
   ```

## <a name="create-database-level-firewall"></a>建立資料庫層級防火牆

使用 SQL Server Management Studio 建立資料庫的資料庫層級防火牆規則。 此資料庫層級防火牆規則將會自動複寫到次要伺服器。 基於測試目的，您可以為所有 IP 位址 (0.0.0.0 和 255.255.255.255) 建立防火牆規則、可以為您已建立伺服器防火牆規則的單一 IP 位址建立防火牆規則，或者可以為您想要用來測試本教學課程之電腦的 IP 位址設定一或多個防火牆規則。  

- 在開啟的查詢視窗中，將先前的查詢取代為下列查詢，將 IP 位址取代為環境的適當 IP 位址。 

   ```tsql
   -- Create database-level firewall setting for your publich IP address
   EXECUTE sp_set_database_firewall_rule N'mySampleDatabase','0.0.0.1','0.0.0.1';
   ```  

## <a name="create-a-failover-group"></a>建立容錯移轉群組 

選擇容錯移轉區域、在該區域中建立空的伺服器，然後在現有伺服器與新的空伺服器之間建立新的容錯移轉群組。

1. 填入變數。

   ```powershell
   $secpasswd = ConvertTo-SecureString "yourstrongpassword" -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential (“ServerAdmin”, $secpasswd)
   $myresourcegroup = "<your resource group>"
   $mylocation = "<resource group location>"
   $myserver = "<your existing server>"
   $mydatabase = "<your existing database>"
   $mydrlocation = "<your disaster recovery location>"
   $mydrserver = "<your disaster recovery server>"
   $myfailovergroup = "<your failover group"
   ```

2. 在容錯移轉區域中建立空的備份伺服器。

   ```powershell
   $mydrserver = New-AzureRmSqlServer -ResourceGroupName $myresourcegroup -Location $mydrlocation -ServerName $mydrserver -ServerVersion "12.0" -SqlAdministratorCredentials $mycreds
   ```

3. 建立容錯移轉群組。

   ```powershell
   $myfailovergroup = New-AzureRMSqlDatabaseFailoverGroup –ResourceGroupName $myresourcegroup -ServerName "$myserver" -PartnerServerName $mydrserver  –FailoverGroupName $myfailovergroupname –FailoverPolicy "Automatic" -GracePeriodWithDataLossHours 2
   ```

4. 在容錯移轉群組中新增資料庫

   ```powershell
   $mydrserver | Add-AzureRMSqlDatabaseToFailoverGroup –FailoverGroupName $myfailovergroup  -Database $mydatabase
   ```

## <a name="add-empty-backup-server-to-domain"></a>在網域中新增空的備份伺服器

1. 在 Azure 入口網站中，按一下左側功能表中的 [更多服務]，在篩選文字方塊中輸入 **sql**，然後選取 [SQL Server]。
2. 在 [SQL Server] 頁面上，按一下新的 SQL Database 災害復原伺服器。
3. 在伺服器 [概觀] 頁面的 [程式集] 窗格中，按一下 [Active Directory 管理] 下的 [未設定]。
4. 在 [Active Directory 管理] 頁面上，按一下 [設定管理員]。
5. 選取 [ad-admin] Azure Active Directory 帳戶 (或其他現有的帳戶，例如您自己的帳戶) 做為新 Azure SQL Database 災害復原伺服器的伺服器管理員。
6. 按一下 [選取] 。
7. 按一下 [儲存] 。

## <a name="prepare-client-tier"></a>準備用戶端層

1. 使用容錯移轉設定檔 AWProfile 建立 TM 設定檔。
2. 設定監視

   ```powershell
   $profile = New-AzureRmTrafficManagerProfile -Name AWProfile -ResourceGroupName MYRG -TrafficRoutingMethod Failover -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
   $webapp1 = Get-AzureRMWebApp -Name WebappUSWest
   Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
   $webapp2 = Get-AzureRMWebApp -Name WebappUSEast
   Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
   Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
   ```

## <a name="deploy-java-application-and-connect-to-database"></a>部署 Java 應用程式，並連線到資料庫

<In progress> 請參閱[與 Java 連線](sql-database-connect-query-java.md)。

<TO DO: change user to app-user>

1. 安裝 java 8。
2. 安裝 Maven。
3. 建立 Maven 專案。
4. 在 pom.xml 中新增下列內容 

   - 相依性

      ```java
      <dependency>
         <groupId>com.microsoft.sqlserver</groupId>
         <artifactId>mssql-jdbc</artifactId>
         <version>6.1.0.jre8</version>
       </dependency>
      ```
   - 語言層級

      ```java
      <properties>
         <maven.compiler.source>1.8</maven.compiler.source>
         <maven.compiler.target>1.8</maven.compiler.target>
      </properties>
      ```

   - 在 jar 中建置支援資訊清單檔案的選項

      ```java
      <build>
        <plugins>
           <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-jar-plugin</artifactId>
              <version>3.0.0</version>
              <configuration>
                 <archive>
                    <manifest>
                       <mainClass>com.sqldbsamples.App</mainClass>
                    </manifest>
                 </archive>
              </configuration>
           </plugin>
        </plugins>
      </build>
      ```
5. 在 App.java 檔案中新增下列內容︰

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String PRIMARY_HOST_HAME = "your_primary_server_name";
      private static final String SECONDARY_HOST_NAME = "your_secondary_server_name";
      private static final String PRIMARY_HOST_HAME = "janengsampleserver";
      private static final String SECONDARY_HOST_NAME = PRIMARY_HOST_HAME;
    
      private static final String DB_NAME = "mySampleDatabase";
      private static final String USER = "ServerAdmin";
      private static final String PASSWORD = "ChangeYourAdminPassword1";

      private static final String PRIMARY_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", PRIMARY_HOST_HAME, DB_NAME, USER, PASSWORD);
      private static final String SECONDARY_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", SECONDARY_HOST_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println(""); 

         // todo: get the max id from the table and initialize INSERT COUNTER with it so that the code will always run (avoid duplicate keys)  
         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1h
                System.out.print(i + ": insert on primary " + (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " + (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name that we can use to find the product again later
      String sql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(PRIMARY_URL); 
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data that was previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(SECONDARY_URL); 
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id that is stored in the table to be able to make unique inserts 
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
        
      try (Connection connection = DriverManager.getConnection(SECONDARY_URL); 
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```
6. 儲存檔案。

## <a name="compile-and-run"></a>編譯和執行

1. 移至主控台並執行

   ```java
   mvn package
   ```
2. 完成時，執行以執行應用程式 (除非以手動方式停止，否則會執行約 1 小時)︰

   ```
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   如果執行成功，輸出會看起來像這樣︰

   #######################################
   ## <a name="geo-distributed-database-tutorial"></a>異地資料庫教學課程 ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful

## <a name="perform-dr-drill"></a>執行 DR 深入探詢

1. 使用強制容錯移轉呼叫 FG 的手動容錯移轉。 如果無法接受深入探詢期間的資料遺失，您應該移除 -AllowDataLoss

   ```powershell
   $fg | Switch-AzureRMSqlDatabaseFailoverGroup -AllowDataLoss
   ```

2.    停用 TM 設定檔中的主要端點 (以觸發端點容錯移轉) 

   ```powershell
   Disable-AzureRmTrafficManagerEndpoint -Name webapp1ep -Type AzureEndpoints -ProfileName $profile.Name -ResourceGroupName MYRG -Force
   ```

3.    再次執行應用程式。


## <a name="relocate-application-to-primary-region"></a>將應用程式重新放置到主要區域

1.    呼叫 FG 的手動易用容錯移轉。 不要指定 -AllowDataLoss

   ```powershell
   $fg | Switch-AzureRMSqlDatabaseFailoverGroup 
   ```

2.    停用 TM 設定檔中的次要端點 (webapp2ep) (以觸發端點容錯移轉) 

   ```powershell
   Disable-AzureRmTrafficManagerEndpoint -Name webapp2ep -Type AzureEndpoints -ProfileName $profile.Name -ResourceGroupName MYRG -Force
   ```

3.    再次執行應用程式。

## <a name="troubleshoot-failover"></a>對容錯移轉進行疑難排解 

了解哪個區域現在是主要區域以確保發生容錯移轉。 角色會顯示它是主要或次要。

   ```powershell
   $fg = Get-AzureRMSqlDatabaseFailoverGroup -ResourceGroupName "myrg" -ServerName "AWserver" 
   print $fg.role
   ```

## <a name="next-steps"></a>後續步驟 

- 即將提供
