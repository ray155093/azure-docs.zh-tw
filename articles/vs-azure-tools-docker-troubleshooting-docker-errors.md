---
title: "使用 Visual Studio 疑難排解 Windows 上的 Docker 用戶端錯誤 | Microsoft Docs"
description: "疑難排解使用 Visual Studio 在 Windows 上建立及部署 Web 應用程式到 Docker 時您會遇到的問題。"
services: azure-container-service
documentationcenter: na
author: mlearned
manager: douge
editor: 
ms.assetid: 346f70b9-7b52-4688-a8e8-8f53869618d3
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/08/2016
ms.author: mlearned
translationtype: Human Translation
ms.sourcegitcommit: 84b998adfbd43a8e70478e3840e1086ef66cdd99
ms.openlocfilehash: 3b89cc9469886633be49e953f4ad11166ffa7dad


---

# <a name="troubleshoot-visual-studio-docker-development"></a>疑難排解 Visual Studio Docker 開發

使用 Visual Studio Tools for Docker Preview 時，可能會因預覽本質而發生一些問題。
以下是一些常見問題和解決方法。  

## <a name="visual-studio-2017-rc"></a>Visual Studio 2017 RC

### <a name="linux-containers"></a>**Linux 容器**

####  <a name="build-errors-occur-when-debugging-a-net-core-web-or-console-application"></a>偵錯 .NET Core web 或主控台應用程式時發生建置錯誤  

這可能與未共用專案搭配適用於 Windows 的 Docker 所在的磁碟機有關。  您可能會收到類似下列錯誤︰

```
The "PrepareForLaunch" task failed unexpectedly.
Microsoft.DotNet.Docker.CommandLineClientException: Creating network "webapplication13628050196_default" with the default driver
Building webapplication1
Creating webapplication13628050196_webapplication1_1
ERROR: for webapplication1  Cannot create container for service webapplication1: C: drive is not shared. Please share it in Docker for Windows Settings
```
若要解決此問題︰

1. 以滑鼠右鍵按一下通知區域中的 [適用於 Windows 的 Docker]，然後選取 [設定]。  
2. 選取 [共用磁碟機] 並共用專案所在的磁碟機。

### <a name="windows-containers"></a>**Windows 容器**

下列問題特定於偵錯 Windows 容器中的 .NET Framework web 和主控台應用程式。

#### <a name="prerequisites"></a>必要條件

1. 必須先安裝具有 .NET Core 和 Docker 預覽工作負載的 Visual Studio 2017 RC (或更新版本)。
2. 必須安裝 Windows 10 週年紀念日更新與最新 Windows Update 修補檔案。
3. 必須安裝[適用於 Windows (Beta) 的 Docker](https://docs.docker.com/docker-for-windows/) (組建 1.12.2-beta28 7813 或更新版本)。
4. 必須選取**切換至 Windows 容器**。 在通知區域中，按一下的 [適用於 Windows 的 Docker]，然後選取 [切換至 Windows 容器]。 在機器重新啟動之後，請確定此設定會保留下來。

#### <a name="console-output-does-not-appear-in-visual-studios-output-window-while-debugging-a-console-application"></a>主控台應用程式進行偵錯時，主控台輸出不會出現在 Visual Studio 的輸出視窗

這是 Visual Studio 偵錯工具 (msvsmon.exe) 的已知問題，目前不適用於此案例。 此案例的支援的可能會包含在未來的版本中。 若要查看 Visual Studio 主控台應用程式中的輸出，請使用 **Docker︰啟動專案**，這相當於**啟動但不偵錯**。

#### <a name="debugging-web-applications-with-the-release-configuration-fails-with-403-forbidden-error"></a>偵錯 web 應用程式發生發行組態失敗，錯誤碼 (403) 禁止使用

若要解決此問題，請在方案中開啟 web.release.config，並標記為註解或刪除下列幾行︰

```
<compilation xdt:Transform="RemoveAttributes(debug)" />
```

#### <a name="when-switching-to-windows-containers-you-see-an-error-stating-error-response-from-daemon-io-timeout"></a>切換至 Windows 容器時，您會看到錯誤指出，「回應來自精靈時發生錯誤︰i/o 逾時」

可在 https://github.com/docker/for-win/issues/178 追蹤適用於 Windows 的 Docker 中的這個問題。


## <a name="visual-studio-2015"></a>Visual Studio 2015

### <a name="linux-containers"></a>**Linux 容器**

#### <a name="unable-to-validate-volume-mapping"></a>無法驗證磁碟區對應
需要磁碟區對應才能與容器中的應用程式資料夾共用應用程式的原始程式碼和二進位檔。  docker-compose.dev.debug.yml 和 docker-compose.dev.release.yml 中包含特定的磁碟區對應。 當主機電腦上的檔案變更時，容器就會在類似的資料夾結構中反映這些變更。

若要啟用磁碟區對應︰

1. 在通知區域中按一下 [白鯨]，然後選取 [設定]。
2. 選取 [共用的磁碟機]。
3. 選取裝載您專案的磁碟機和 %USERPROFILE% 所在的磁碟機。
4. 按一下 [Apply (套用)] 。

若要測試磁碟區對應功能是否正常，請在共用一個或多個磁碟機之後，從 Visual Studio 內重建並選取 F5，或從命令提示字元執行下列程式碼。

> [!NOTE]
> 此範例假設您的 [使用者] 資料夾位於已共用的磁碟機 C 上。
> 如果您共用的是不同磁碟機，請視需要修訂。

```
docker run -it -v /c/Users/Public:/wormhole busybox
```

在 Linux 容器中執行下列程式碼。

```
/ # ls
```

您應該會看到「使用者/公用」資料夾中的目錄清單。 如果未顯示任何檔案，而且「/c/使用者/公用」資料夾不是空的，則未正確地設定磁碟區對應。

```
bin       etc       proc      sys       usr       wormhole
dev       home      root      tmp       var
```

切換至 wormhole 目錄，以查看 `/c/Users/Public` 目錄的內容：

```
/ # cd wormhole/
/wormhole # ls
AccountPictures  Downloads        Music            Videos
Desktop          Host             NuGet.Config     desktop.ini
Documents        Libraries        Pictures
/wormhole #
```

> [!NOTE]
> 使用 Linux VM 時，容器檔案系統會區分大小寫。

## <a name="build-prepareforbuild-task-failed-unexpectedly"></a>建置："PrepareForBuild" 工作意外失敗

Microsoft.DotNet.Docker.CommandLine.ClientException︰嘗試連線時發生錯誤。

請確認預設的 Docker 主機正在執行。 開啟命令提示字元並執行︰

```
docker info
```

如果此命令傳回錯誤，則請嘗試啟動 **Docker For Windows** 桌面應用程式。 如果桌面應用程式正在執行，則應該可以在通知區域中看到 [白鯨]。 以滑鼠右鍵按一下 [白鯨] 並開啟 [設定]。 按一下 [重設]，然後重新啟動 Docker。

## <a name="manually-upgrading-from-version-031-to-040"></a>從 0.31 版手動升級至 0.40 版
若要手動升級適用於 Windows 的 Docker:

1. 備份專案。
1. 刪除專案中的下列檔案︰

    ```
      Dockerfile
      Dockerfile.debug
      DockerTask.ps1
      docker-compose-yml
      docker-compose.debug.yml
      .dockerignore
      Properties\Docker.props
      Properties\Docker.targets
    ```

1. 關閉方案，並從 .xproj 檔案中移除下列幾行︰

    ```
      <DockerToolsMinVersion>0.xx</DockerToolsMinVersion>
      <Import Project="Properties\Docker.props" />
      <Import Project="Properties\Docker.targets" />
    ```

1. 重新開啟方案。
1. 從 Properties\launchSettings.json 檔案移除下列幾行︰

    ```
      "Docker": {
        "executablePath": "%WINDIR%\\System32\\WindowsPowerShell\\v1.0\\powershell.exe",
        "commandLineArgs": "-ExecutionPolicy RemoteSigned .\\DockerTask.ps1 -Run -Environment $(Configuration) -Machine '$(DockerMachineName)'"
      }
    ```

1. 在 publishOptions 中從 project.json 移除下列與 Docker 相關的檔案︰

    ```
    "publishOptions": {
      "include": [
        ...
        "docker-compose.yml",
        "docker-compose.debug.yml",
        "Dockerfile.debug",
        "Dockerfile",
        ".dockerignore"
      ]
    },
    ```

1. 解除安裝舊版本，並安裝 Docker Tools 0.40。 然後從 ASP.NET 核心 web 或主控台應用程式的內容功能表移至 [新增] > [Docker 支援]。 此動作會將新的必要 Docker 構件新增回您的專案。

## <a name="an-error-dialog-occurs-when-attempting-to-add-docker-support-or-debug-f5-an-aspnet-core-application-in-a-container"></a>嘗試選取 [新增] -> [Docker 支援] 或偵錯 (F5) 容器中的 ASP.NET 核心應用程式時出現錯誤對話方塊

先解除安裝再重新安裝擴充功能之後，Visual Studio 中的 Managed Extensibility Framework (MEF) 快取會損毀。 若發生這種情況，就會導致在新增 Docker 支援和 (或) 嘗試執行或偵錯 (F5) ASP.NET 核心應用程式時出現各種錯誤訊息。 暫時的解決方法是使用下列步驟來刪除和重新產生 MEF 快取。

1. 關閉所有 Visual Studio 執行個體。
1. 開啟 %USERPROFILE%\AppData\Local\Microsoft\VisualStudio\14.0\。
1. 刪除下列資料夾：
     ```
       ComponentModelCache
       Extensions
       MEFCacheBackup
    ```
1. 開啟 Visual Studio。
1. 再次嘗試案例。



<!--HONumber=Jan17_HO1-->


