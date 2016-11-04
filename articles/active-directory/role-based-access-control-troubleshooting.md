---
title: 角色型存取控制疑難排解 | Microsoft Docs
description: 取得有關角色型存取控制資源問題或疑問的協助。
services: azure-portal
documentationcenter: na
author: kgremban
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2016
ms.author: kgremban

---
# 角色型存取控制疑難排解
## 簡介
[角色型存取控制](role-based-access-control-configure.md)是一項強大的功能，能讓您將精細的存取權限委派給 Azure 中的資源。亦即您可以放心且精確地授予特定個人所需的權限。然而，Azure 資源的資源模型有時可能很複雜，且不容易完全了解您所授予的權限內容。

本文說明在 Azure 入口網站中使用一些角色時，會有哪些情況。這三種角色涵蓋了所有資源類型︰

* 擁有者
* 參與者
* 讀取者

擁有者與參與者都可以完整存取管理經驗，但參與者無法將存取權限授予其他使用者或群組。讀取者角色則是比較有趣，因此我們會在本文中多花點時間介紹。請參閱[角色型存取控制入門文章](role-based-access-control-configure.md)，以詳細了解如何授與存取權。

## 應用程式服務工作負載
### 寫入存取功能
如果您授與對單一 Web 應用程式授與使用者唯讀存取權限，部分功能可能會在未預期的情況下停用。以下管理功能需要 Web 應用程式的**寫入**存取權限 (參與者或擁有者角色)，而且無法在任何唯讀情況中使用。

* 命令 (例如，開始、停止等)
* 變更像是一般組態的設定、調整設定、備份設定與監視設定
* 存取發佈認證與其他機密，像是應用程式設定與連接字串
* 串流記錄
* 診斷記錄組態
* 主控台 (命令提示字元)
* 有效的近期部署項目 (以便本機 git 持續部署)
* 預估的花費
* Web 測試
* 虛擬網路 (只有當虛擬網路先前是由具備寫入存取權限的使用者所設定，才會顯示出來)。

如果您無法存取上述任何一個磚，請洽詢您的系統管理員，取得 Web 應用程式的參與者存取權限。

### 處理相關資源
Web 應用程式因為幾個互有關聯的資源而顯得複雜。以下是具有多個網站的典型資源群組：

![Web 應用程式資源群組](./media/role-based-access-control-troubleshooting/website-resource-model.png)

如此一來，如果您只授予某人 Web 應用程式存取權限，Azure 入口網站的網站刀鋒視窗上的諸多功能將會停用。

這些項目需要對應至您網站的「應用程式服務方案」的寫入權：

* 檢視 Web 應用程式的定價層 (免費或標準)
* 調整組態 (執行個體數量、虛擬機器大小、自動調整設定)
* 配額 (儲存容量、頻寬、CPU)

這些項目都需要包含您網站的整個「資源群組」的寫入權：

* SSL 憑證與繫結 (這是因為相同的資源群組與地理位置的各個網站之間，會共用 SSL 憑證)
* 警示規則
* 自動調整設定
* 應用程式見解元件
* Web 測試

## 虛擬機器工作負載
差不多與 Web 應用程式相同的是，虛擬機器分頁上的某些功能需要具備虛擬機器 (或是資源群組中的其他資源) 的寫入存取權限。

虛擬機器與網域名稱、虛擬網路、儲存體帳戶及警示規則相關。

這些項目都需要具備「虛擬機器」的寫入權：

* Endpoints
* IP 位址
* 磁碟
* 擴充功能

這些項目都需要同時具備「虛擬機器」與所屬之「資源群組」(連同網域名稱) 的寫入權：

* 可用性集合
* 負載平衡集合
* 警示規則

如果您無法存取上述任何一個磚，請洽詢您的系統管理員，取得資源群組的參與者存取權限。

## 更多資訊
* [角色型存取控制](role-based-access-control-configure.md)：開始在 Azure 入口網站中使用 RBAC。
* [內建角色](role-based-access-built-in-roles.md)︰取得有關 RBAC 中標準角色的詳細資訊。
* [Azure RBAC 中的自訂角色](role-based-access-control-custom-roles.md)︰了解如何建立自訂角色，以符合您的存取需求。
* [建立存取權變更歷程記錄報告](role-based-access-control-access-change-history-report.md)︰追蹤 RBAC 中的角色指派變更。

<!---HONumber=AcomDC_0713_2016-->