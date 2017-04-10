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
ms.sourcegitcommit: 73ee330c276263a21931a7b9a16cc33f86c58a26
ms.openlocfilehash: 5a8813e08d10bc2fa34a49b8b7dd01074da7426e
ms.lasthandoff: 04/08/2017

---
<div class="content">
    <h1>Linux 虛擬機器文件</h1>
    <p style="padding-bottom: 0px; max-width: 1050px;">Azure Linux 虛擬機器依您的選擇使用 Red Hat、Ubuntu 或 Linux 發行版本提供隨選、高度可調整、安全且虛擬化的基礎結構。  了解如何使用快速入門、教學課程和範例建立、設定、管理和調整 Linux VM。</p>
<h2 style="margin-top: 36px; margin-bottom: 0px;">快速入門</h2>
<p style="margin-top: 6px; margin-bottom: 6px;">使用下列方法建立 Linux VM：</p>
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
<div class="columnHolder">
    <div class="column50">
        <h2 style="margin-top: 36px">教學課程</h2>
        <p>建置並部署 Linux 虛擬機器。</p>
        <ul class="spaced">
            <li><a href="/azure/virtual-machines/virtual-machines-linux-tutorial-manage-vm?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json">建立並管理 Linux VM</a></li>
            <li><a href="/azure/virtual-machines/virtual-machines-linux-tutorial-load-balance-nodejs?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json">負載平衡高可用性 VM</a></li>
        </ul>
        <h2>範例</h2>
        <p>將第一個應用程式部署至 Azure。</p>
        <ul class="spaced">
            <li><a href="/azure/virtual-machines/virtual-machines-linux-cli-samples">Azure CLI</a></li>
            <li><a href="/azure/virtual-machines/virtual-machines-linux-powershell-samples">Azure PowerShell</a></li>
        </ul>
    </div>
    <div class="column50" style="max-width: 400px">
        <h2 style="margin-top: 36px">免費影片訓練</h2>
        <a href="https://www.pluralsight.com/courses/managing-infrastructure-microsoft-azure-getting-started?twoid=d6abac77-7dcc-4d33-9e03-f85e78989f02">
            <img src="media/index/video-training-infrastructure.png" width="400" alt="" />
            <p style="margin-top: 0px">免費 PluralSight 影片訓練 - 管理基礎結構</a></p>
            <p style="margin-top: 36px"></p>
                <a href="https://www.pluralsight.com/courses/azure-vms-getting-started?twoid=d6abac77-7dcc-4d33-9e03-f85e78989f02">
            <img src="media/index/video-training-vms.png" width="400" alt="" />
            <p style="margin-top: 0px">免費 Pluralsight 影片訓練 – 開始使用虛擬機器</a></p>
        <p style="margin-top: 36px"></p>
        <a href="https://www.pluralsight.com/courses/azure-iaas-monitoring-management-getting-started?twoid=d6abac77-7dcc-4d33-9e03-f85e78989f02">
            <img src="media/index/video-training-iaas-monitoring.png" width="400" alt="" />
            <p style="margin-top: 0px">免費 Pluralsight 影片訓練 – 開始使用 IaaS 監視</a></p>
    </div>
</div>
<hr />
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

