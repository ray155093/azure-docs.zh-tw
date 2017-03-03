---
title: "Azure RemoteApp 疑難排解 - 應用程式啟動和連線失敗 | Microsoft Docs"
description: "瞭解如何疑難排解啟動及連線至 Azure RemoteApp 中之應用程式的問題。"
services: remoteapp
documentationcenter: 
author: ericorman
manager: mbaldwin
ms.assetid: e5cf7171-d1c2-4053-a38b-5af7821305e1
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 4e6c6571be85cd5fe374c7df644741ae017ccd19
ms.openlocfilehash: 6db4cf048147cb9559ca1a2e6921c679fe6ec9e5
ms.lasthandoff: 01/05/2017


---
# <a name="troubleshoot-azure-remoteapp---application-launch-and-connection-failures"></a>為 Azure RemoteApp 進行疑難排解 - 應用程式啟動和連線失敗
> [!IMPORTANT]
> Azure RemoteApp 即將中止。 如需詳細資訊，請參閱 [公告](https://go.microsoft.com/fwlink/?linkid=821148) 。
> 
> 

Azure RemoteApp 中裝載的應用程式可能因幾個不同原因而無法啟動。 此文章說明使用者嘗試啟動應用程式時可能會收到的各種原因和錯誤訊息。 文章中也會討論連線失敗。 (但此文章不會說明登入 Azure RemoteApp 用戶端時出現的問題。)  

請閱讀此文章以瞭解因應用程式啟動和連線失敗而顯示之一般錯誤訊息的相關資訊。

## <a name="were-getting-you-set-up-try-again-in-10-minutes"></a>我們正在引導您設定...請在 10 分鐘內再試一次。
此錯誤表示 Azure RemoteApp 正在向上擴充以滿足您使用者的容量需求。 正在背景建立更多 Azure RemoteApp VM，以處理您使用者的容量需求。 通常此工作約需要五分鐘來完成，但最多可能需要 10 分鐘。 有時候此工作完成速度不夠快，但是資源的需求卻是立即性的。 例如，9 AM 時許多使用者需要同時使用 Azure RemoteAppn 中的應用程式。 如果您遇到這個問題，我們可以在後端啟用 **容量模式** 。 若要這樣做，請開啟一個 Azure 支援票證。 請務必在要求中包含您的訂用帳戶識別碼。  

![我們正在引導您設定](./media/remoteapp-apptrouble/ra-apptrouble1.png)

## <a name="could-not-auto-reconnect-to-your-applications-please-re-launch-your-application"></a>無法自動重新連線至您的應用程式，請重新啟動您的應用程式
如果您是使用 Azure RemoteApp 接著讓電腦睡眠超過 4 小時，然後喚醒電腦且 Azure RemoteApp 用戶端嘗試自動重新連線且逾時時，您就會常常看到這個錯誤訊息。  請指示使用者瀏覽回應用程式並嘗試從 Azure RemoteApp 用戶端開啟應用程式。

![無法自動重新連線至您的應用程式](./media/remoteapp-apptrouble/ra-apptrouble2.png) 

## <a name="problems-with-the-temp-profile"></a>暫存設定檔的問題
當您的使用者設定檔 (使用者設定檔磁碟) 無法掛接且使用者收到暫存設定檔時，就會發生這項錯誤。  系統管理員應瀏覽至 Azure 入口網站中的集合，然後移到 [工作階段] 索引標籤並嘗試 [登出] 使用者。 這會強制完整登出使用者工作階段 - 然後讓使用者嘗試再次啟動應用程式。 如果失敗，請連絡 Azure 支援服務。

## <a name="azure-remoteapp-has-stopped-working"></a>Azure RemoteApp 已停止運作
此錯誤訊息表示 Azure RemoteApp 用戶端發生問題並需要重新啟動。 請指示使用者：選取 [關閉程式]  然後再次啟動 Azure RemoteApp 用戶端。  如果持續發生此問題，請開啟一個 Azure 支援票證。

![Azure RemoteApp 已停止運作](./media/remoteapp-apptrouble/ra-apptrouble3.png)  

## <a name="an-error-occurred-while-remote-desktop-connection-was-accessing-this-resource-retry-the-connection-or-contact-your-system-administrator"></a>「遠端桌面連線」存取此資源時發生錯誤。 請嘗試重新連線，或連絡您的系統管理員。
這是一般錯誤訊息 - 請連絡 Azure 支援服務，以便我們進行調查。 

![一般 Azure RemoteApp 訊息](./media/remoteapp-apptrouble/ra-apptrouble4.png) 


