<properties
   pageTitle="使用 Visual Studio 疑難排解 Windows 上的 Docker 用戶端錯誤 | Microsoft Azure"
   description="疑難排解使用 Visual Studio 在 Windows 上建立及部署 Web 應用程式到 Docker 時您會遇到的問題。"
   services="azure-container-service"
   documentationCenter="na"
   authors="allclark"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="06/08/2016"
   ms.author="allclark" />

# 疑難排解 Visual Studio Docker 開發

使用 Visual Studio Tools for Docker Preview 時，可能會因預覽本質而發生一些問題。以下是一些常見問題和解決方法。

##無法設定 Program.cs 進行 Docker 支援

新增 Docker 支援時，必須將 `.UseUrls(Environment.GetEnvironmentVariable("ASPNETCORE_SERVER.URLS"))` 新增至 WebHostBuilder()。如果找不到 `Main()` 函式或新的 WebHostBuilder 類別，則會顯示警告。需要有 `.UseUrls()`，才能讓 Kestrel 接聽連入流量 (在 Docker 容器內執行時，localhost 除外)。完成時，一般程式碼看起來如下︰

```
public class Program
{
    public static void Main(string[] args)
    {
        var host = new WebHostBuilder()
            .UseUrls(Environment.GetEnvironmentVariable("ASPNETCORE_URLS") ?? String.Empty)
            .UseKestrel()
            .UseContentRoot(Directory.GetCurrentDirectory() ?? "")
            .UseIISIntegration()
            .UseStartup<Startup>()
            .Build();

        host.Run();
    }
}
```

UseUrls() 已設定 WebHost 接聽連入 URL 流量。[Docker Tools for Visual Studio](http://aka.ms/DockerToolsForVS) 將以 dockerfile.debug/release 模式設定環境變數，如下所示：

```
# Configure the listening port to 80
ENV ASPNETCORE_SERVER.URLS http://*:80
```

## 磁碟區對應未運作
若要啟用「編輯和重新整理」功能，磁碟區對應設定成將專案的原始程式碼共用到容器內的 .app 資料夾。主機電腦上的檔案變更時，容器 /app 目錄會使用相同的目錄。在 docker-compose.debug.yml 中，下列組態會啟用磁碟區對應︰

```
volumes:
    - ..:/app
```

若要測試磁碟區對應是否運作，請嘗試下列命令︰

**從 Windows**

```
a
/ # ls
```

您應該會看到「使用者/公用」資料夾中的目錄清單。如果未顯示任何檔案，而且「/c/使用者/公用」資料夾不是空的，則未正確地設定磁碟區對應。

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

切換至 wormhole 目錄，以查看 `/c/Users/Public` 目錄的內容：

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     a.txt
Documents        Libraries        Pictures         desktop.ini
/wormhole #
```

**注意：**使用 Linux VM 時，容器檔案系統區分大小寫。

如果您看不到內容，請嘗試下列動作︰

**Docker for Windows Beta**
- 請在系統匣中尋找白鯨圖示並確定它是白色且運作，確認 Docker for Windows 桌面應用程式正在執行。
- 在系統匣中的白鯨圖示上按一下滑鼠右鍵，選取 [設定]，然後按一下 [管理共用磁碟機...]，來確認已設定磁碟區對應。

**Docker 工具箱 (含 VirtualBox)**

根據預設，VirtualBox 會將 `C:\Users` 當做 `c:/Users` 來共用。可能的話，請將您的專案移到此目錄下方。否則，您可以手動將它新增至 VirtualBox [共用資料夾](https://www.virtualbox.org/manual/ch04.html#sharedfolders)。
	
##建置：無法建置映像、查看 TLS 連線時發生錯誤：主機並未執行

- 請確認預設的 Docker 主機正在執行。請參閱[設定 Docker 用戶端](./vs-azure-tools-docker-setup.md)一文。

##把 Microsoft Edge 當做預設瀏覽器

如果您在使用 Microsoft Edge 瀏覽器，網站可能不會開啟，原因是 Edge 認為該 IP 位址不安全。如要修正這個問題，請執行下列步驟：

1. 移至**網際網路選項**。
    - 在 Windows 10 上，您可以在 Windows [執行] 方塊中輸入 `Internet Options`。
    - 在 Internet Explorer 中，您可以移至 [設定] 功能表，然後選取 [網際網路選項]。 
1. 選取出現的 [網際網路選項]。 
1. 選取 [安全性] 索引標籤。
1. 選取 [近端內部網路] 區域。
1. 選取 [網站]。 
1. 將您虛擬機器 (在這個案例中為 Docker 主機) 的 IP 位址新增到清單中。 
1. 在 Edge 中重新整理網頁，此時您應該會看到網站已正常運作。 
1. 如需這個問題的詳細資訊，請瀏覽 Scott Hanselman 的部落格文章：[Microsoft Edge can't see or open VirtualBox-hosted local web sites](http://www.hanselman.com/blog/FixedMicrosoftEdgeCantSeeOrOpenVirtualBoxhostedLocalWebSites.aspx) (Microsoft Edge 無法看到或開啟裝載在 VirtualBox 上的本機網站)。 

##疑難排解 0.15 版或更舊版本


###執行應用程式會導致 PowerShell 開啟、顯示錯誤訊息，然後關閉。瀏覽器頁面未開啟。

這可能是在 `docker-compose-up` 期間發生的錯誤。如要檢視該錯誤，請執行下列步驟：

1. 開啟 `Properties\launchSettings.json` 檔案
1. 找出 Docker 項目。
1. 找出開頭為下列內容的那一行：

    ```
    "commandLineArgs": "-ExecutionPolicy RemoteSigned …”
    ```
	
1. 新增 `-noexit` 參數，讓該行看起來類似下列內容。這會使 PowerShell 保持開啟，讓您能檢視錯誤訊息。

    ```
	"commandLineArgs": "-noexit -ExecutionPolicy RemoteSigned …”
    ```

<!---HONumber=AcomDC_0622_2016-->