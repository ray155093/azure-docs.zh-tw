---
title: "Azure Linux 虛擬機器文件 - 教學課程和 API 參考 | Microsoft Docs"
description: "了解如何使用您所選的 Linux 散發版本建立虛擬機器。 文件會示範不同的建立 VM 範本方式。"
services: virtual-machines\linux
author: carolz
manager: carolz
layout: LandingPage
ms.service: virtual-machines\linux
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: landing-page
ms.date: 01/23/2017
ms.author: carolz
translationtype: Human Translation
ms.sourcegitcommit: 260208e7c7a08110eb3c885ef86ec4c18ff42fc9
ms.openlocfilehash: e4fede90a5df7c4a74c1e1d41ecbe669d731a1b5
ms.lasthandoff: 04/23/2017

---
<div class="content">
    <h1>Linux 虛擬機器</h1>
    <div class="introHolder" style="justify-content: space-between;">
        <div class="intro" style="min-width: 200px">
            <p>Azure Linux 虛擬機器依您的選擇使用 Red Hat、Ubuntu 或 Linux 發行版本提供隨選、高度可調整、安全且虛擬化的基礎結構。 了解如何使用快速入門、教學課程和範例建立、設定、管理和調整 Linux VM。</p>
        </div>
        <a href="https://azure.microsoft.com/en-us/resources/videos/create-linux-virtual-machine/">
            <div class="calloutHolder" style="max-width: 250px">
                <div>
                    <img src="media/index/create-linux-virtual-machine.png" style="width: 250px" />
                </div>
                <div>
                    <p style="margin-top: 0; color: #6e6e6e">建立 Linux 虛擬機器。 (4:11)</p>
                </div>
            </div>
        </a>
    </div>
<h2 style="margin-top: 0px; margin-bottom: 0px;">5 分鐘快速入門</h2>
<p style="margin-top: 6px; margin-bottom: 6px;">了解如何在執行 Ubuntu 的虛擬機器中部署 NGINX 網頁伺服器：</p>
<div class="ico48Case">
    <div class="ico48Link">
        <a href="/azure/virtual-machines/virtual-machines-linux-quick-create-cli?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json">
            <img src="media/index/cli.svg" alt="">
            <span>Azure CLI</span>
        </a>
    </div>
    <div class="ico48Link">
        <a href="/azure/virtual-machines/virtual-machines-linux-quick-create-portal?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json">
        <img src="media/index/portal.svg" alt="">
            <span>Azure 入口網站</span>
        </a>
    </div>
    <div class="ico48Link">
        <a href="/azure/virtual-machines/virtual-machines-linux-quick-create-powershell?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json">
            <img src="media/index/logo_powershell.svg" alt="">
            <span>Azure PowerShell</span>
        </a>
    </div>
<div>

<h2 style="margin-top: 36px">逐步教學課程</h2>
<p>了解如何在 Azure 上部署、管理及調整 Linux VM</p>
<ol>
    <li><a href="/azure/virtual-machines/linux/tutorial-manage-vm">建立並管理 Linux VM</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-manage-disks">建立和管理 VM 磁碟</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-automate-vm-deployment">自動化 VM 組態</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-custom-images">建立自訂的 VM 映像</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-availability-sets">建立高可用性 VM</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-create-vmss">建立 VM 擴展集</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-load-balancer">負載平衡的 VM</a></li>
    <li><a href="/azure/virtual-machines/linux/tutorial-virtual-network">管理 VM 網路功能</a></li>
</ol>

<h2 style="margin-top: 36px">免費 PluralSight 影片訓練</h2>
<ul class="panelContent cardsW">
    <li style="flex: 0 1 25%">
        <a href="https://www.pluralsight.com/courses/managing-infrastructure-microsoft-azure-getting-started?twoid=d6abac77-7dcc-4d33-9e03-f85e78989f02"> 
            <div class="cardSize">
                <div class="cardPadding">
                    <div class="card">
                       <div class="cardImageOuter">
                            <div class="cardImage">
                                <img style="max-width: 100%" alt="" src="media/index/video-training-infrastructure.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText">
                            <p>管理基礎結構</p>
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <li style="flex: 0 1 25%">
        <a href="https://www.pluralsight.com/courses/azure-vms-getting-started?twoid=d6abac77-7dcc-4d33-9e03-f85e78989f02"> 
            <div class="cardSize">
                <div class="cardPadding">
                    <div class="card">
                       <div class="cardImageOuter">
                            <div class="cardImage">
                                <img style="max-width: 100%" alt="" src="media/index/video-training-vms.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText">
                            <p>開始使用虛擬機器</p>
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
    <li style="flex: 0 1 25%">
        <a href="https://www.pluralsight.com/courses/azure-iaas-monitoring-management-getting-started?twoid=d6abac77-7dcc-4d33-9e03-f85e78989f02"> 
            <div class="cardSize">
                <div class="cardPadding">
                    <div class="card">
                       <div class="cardImageOuter">
                            <div class="cardImage">
                                <img style="max-width: 100%" alt="" src="media/index/video-training-iaas-monitoring.png" data-linktype="external">
                            </div>
                        </div>
                        <div class="cardText">
                            <p>開始使用 IaaS 監視</p>
                        </div>
                    </div>
                </div>
            </div>
        </a>
    </li>
</ul>

<h2>範例</h2>
<p>將第一個應用程式部署至 Azure。</p>
<ul class="spaced">
    <li><a href="/azure/virtual-machines/virtual-machines-linux-cli-samples">Azure CLI</a></li>
    <li><a href="/azure/virtual-machines/virtual-machines-linux-powershell-samples">Azure PowerShell</a></li>
</ul>

<h2 style="margin-top: 36px">參考</h2>
<ul class="panelContent cardsW">
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>命令列</h3>
                        <p><a href="/cli/azure/vm">Azure CLI</a></p>
                        <p><a href="/powershell/azureps-cmdlets-docs">Azure PowerShell</a></p>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>語言</h3>
                        <p><a href="/dotnet/api/microsoft.azure.management.compute">.NET</a></p>
                        <p><a href="/java/api">Java</a></p>
                        <p><a href="https://azure.microsoft.com/en-us/develop/nodejs/#azure-sdk">Node.js</a></p>
                        <p><a href="http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.mgmt.compute.html">Python</a></p>
                    </div>
                </div>
            </div>
        </div>
    </li>
    <li>
        <div class="cardSize">
            <div class="cardPadding">
                <div class="card">
                    <div class="cardText">
                        <h3>REST</h3>
                        <p><a href="/rest/api/compute">REST API 參考</a></p>
                    </div>
                </div>
            </div>
        </div>
    </li>
</ul>
</div>

