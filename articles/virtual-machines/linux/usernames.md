---
title: "選取 Linux 的使用者名稱 | Microsoft Docs"
description: "了解如何在 Azure 中選取適用於 Linux 虛擬機器的使用者名稱。"
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 33b50c97-92f1-46c9-a623-e37f67459c5c
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1874d72e5f88816036667932371ff28704d186c8
ms.lasthandoff: 04/03/2017


---
# <a name="selecting-user-names-for-linux-on-azure"></a>在 Azure 上選取適用於 Linux 的使用者名稱
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

在 Azure 上佈建 Linux 虛擬機器時，您必須指定非根使用者的名稱供稍後使用，以使用登入 VM。 您可以選擇新使用者的名稱；如果是透過 Azure 傳統入口網站佈建，可以使用預設名稱「azureuser」。

在大多數的情況中，這位使用者不會存在於基本映像中，而是會在佈建程序期間建立。 若此使用者存在於基本 VM 映像，則 Azure Linux 代理程式只會根據建立 VM 時所指定的資訊，為該使用者設定密碼及 (或) SSH 金鑰。

**不過**，Linux 會定義一組不應該使用的使用者名稱。 如果您嘗試使用現有的系統使用者 (定義為具有 UID 0-99 的使用者) 佈建 Linux VM，佈建程序將 **失敗** 。 常見的範例是 `root` 使用者，其 UID 為 0。

* 另請參閱： [Linux 標準基礎 - 使用者 ID 範圍](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

以下是在 Azure 上佈建 Linux 虛擬機器時應避免使用的 CentOS 和 Ubuntu 常見內建系統使用者清單。 此清單只是範例，請參閱散發套件的文件，以確保您所選擇的使用者名稱沒有與現有的系統使用者衝突。

## <a name="centos"></a>CentOS
* abrt
* adm
* audio
* bin
* cdrom
* cgred
* daemon
* dbus
* dialout
* dip
* disk
* floppy
* ftp
* ftp
* games
* gopher
* haldaemon
* halt
* kmem
* lock
* lp
* mail
* man
* mem
* nfsnobody
* nobody
* ntp
* operator
* oprofile
* postdrop
* postfix
* qpidd
* root
* rpc
* rpcuser
* saslauth
* shutdown
* slocate
* sshd
* stapdev
* stapusr
* sync
* sys
* tape
* test
* tcpdump
* tty
* users
* utempter
* utmp
* uucp
* vcsa
* video
* wheel

## <a name="ubuntu"></a>Ubuntu
* adm
* admin
* audio
* backup
* bin
* cdrom
* crontab
* daemon
* dialout
* dip
* disk
* fax
* floppy
* fuse
* games
* gnats
* irc
* kmem
* landscape
* libuuid
* list
* lp
* mail
* man
* messagebus
* mlocate
* netdev
* news
* nobody
* nogroup
* operator
* plugdev
* proxy
* root
* sasl
* shadow
* src
* ssh
* sshd
* staff
* sudo
* sync
* sys
* syslog
* tape
* tty
* users
* utmp
* uucp
* video
* voice
* whoopsie
* www-data


