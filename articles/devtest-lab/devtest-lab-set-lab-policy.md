---
title: 在 Azure DevTest Labs 中定義實驗室原則 | Microsoft Docs
description: 了解如何定義實驗室原則，例如 VM 大小、每位使用者的 VM 數目上限，以及自動關機。
services: devtest-lab,virtual-machines
documentationcenter: na
author: tomarcher
manager: douge
editor: ''

ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2016
ms.author: tarcher

---
# 在 Azure DevTest Labs 中定義實驗室原則
> [!VIDEO https://channel9.msdn.com/Blogs/Windows-Azure/How-to-set-VM-policies-in-a-DevTest-Lab/player]
> 
> 

Azure DevTest Labs 可讓您指定可協助您控制成本並減少實驗室浪費的主要原則。這些實驗室原則包括每位使用者和每個實驗室可建立的 VM 數目上限，以及各種自動關機和自動啟動選項。

## 在 Azure DevTest Labs 中存取實驗室的原則
下列步驟將引導您完成在 Azure DevTest Labs 中設定實驗室原則的步驟︰

若要檢視 (及變更) 實驗室的原則，請依照下列步驟操作：

1. 登入 [Azure 入口網站](http://go.microsoft.com/fwlink/p/?LinkID=525040)。
2. 選取 [更多服務]，然後從清單中選取 [DevTest Labs]。
3. 從實驗室清單中，選取所需的實驗室。
4. 選取 [原則設定]。
5. [原則設定] 刀鋒視窗包含一個功能表，內含您可以指定的設定︰
   
    ![[原則設定] 刀鋒視窗](./media/devtest-lab-set-lab-policy/policies.png)
   
    若要深入了解如何設定某個原則，請從下列清單中選取它：
   
   * [允許的虛擬機器大小](#set-allowed-virtual-machine-sizes) - 選取實驗室中允許的 VM 大小清單。使用者只能從此清單中建立 VM。
   * [每位使用者的虛擬機器數目](#set-virtual-machines-per-user) - 指定一位使用者可以建立的 VM 數目上限。
   * [每個實驗室的虛擬機器數目](#set-virtual-machines-per-lab) - 指定可以為一個實驗室建立的 VM 數目上限。
   * [自動關機](#set-auto-shutdown) - 指定目前實驗室的 VM 自動關機時間。
   * [自動啟動](#set-auto-start) - 指定目前實驗室的 VM 自動啟動時間。

## 設定允許的虛擬機器大小
設定允許的 VM 大小的原則有助於將實驗室的成本浪費降至最低，方式是讓您指定實驗室中允許的 VM 大小。如果啟用此原則，就只能使用此清單中的 VM 大小來建立 VM。

1. 在實驗室的 [原則設定] 刀鋒視窗上，選取 [允許的虛擬機器大小]。
   
    ![允許的虛擬機器大小](./media/devtest-lab-set-lab-policy/allowed-vm-sizes.png)
2. 選取 [開啟] 來啟用此原則，以及選取 [關閉] 來停用它。
3. 如果啟用這個原則，請選取一或多個可在實驗室中建立的 VM 大小。
4. 選取 [**儲存**]。

## 設定每位使用者的虛擬機器數目
[每位使用者的虛擬機器數目] 原則可讓您指定個別使用者可以建立的 VM 數目上限。當達到使用者限制時，如果使用者嘗試建立 VM，就會顯示錯誤訊息，指出無法建立 VM。

1. 在實驗室的 [原則設定] 刀鋒視窗上，選取 [每位使用者的虛擬機器數目]。
   
    ![每位使用者的虛擬機器數目](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)
2. 選取 [開啟] 來啟用此原則，以及選取 [關閉] 來停用它。
3. 如果您啟用這個原則，請輸入一個指出使用者可建立之 VM 數目上限的數值。如果您輸入無效的數字，UI 將會顯示此欄位允許的最大數目。
4. 選取 [儲存]。

## 設定每個實驗室的虛擬機器數目
[每個實驗室的虛擬機器數目] 原則可讓您指定可為目前實驗室建立的 VM 數目上限。當達到實驗室限制時，如果使用者嘗試建立 VM，就會顯示錯誤訊息，指出無法建立 VM。

1. 在實驗室的 [原則設定] 刀鋒視窗上，選取 [每個實驗室的虛擬機器數目]。
   
    ![每個實驗室的虛擬機器數目](./media/devtest-lab-set-lab-policy/total-vms-allowed.png)
2. 選取 [開啟] 來啟用此原則，以及選取 [關閉] 來停用它。
3. 如果啟用這個原則，請輸入一個指出可為目前實驗室建立之 VM 數目上限的數值。如果您輸入無效的數字，UI 將會顯示此欄位允許的最大數目。
4. 選取 [儲存]。

## 設定自動關機
自動關機原則有助於將實驗室的成本浪費降至最低，方式是讓您指定這個實驗室中 VM 的關機時間。

1. 在實驗室的 [原則設定] 刀鋒視窗上，選取 [自動關機]。
   
    ![自動關機](./media/devtest-lab-set-lab-policy/auto-shutdown.png)
2. 選取 [開啟] 來啟用此原則，以及選取 [關閉] 來停用它。
3. 如果啟用這個原則，您可指定目前實驗室中所有 VM 關機的當地時間。
4. 選取 [**儲存**]。
5. 根據預設，這個原則一經啟用，就會套用到目前實驗室的所有 VM。若要移除特定 VM 的這項設定，請開啟 VM 的刀鋒視窗並變更其 [自動關機] 設定。

## 設定自動啟動
自動啟動原則可讓您指定目前實驗室 VM 應該啟動的時間。

1. 在實驗室的 [原則設定] 刀鋒視窗上，選取 [自動啟動]。
   
    ![自動啟動](./media/devtest-lab-set-lab-policy/auto-start.png)
2. 選取 [開啟] 來啟用此原則，以及選取 [關閉] 來停用它。
3. 如果您啟用這個原則，請指定本機排定的啟動時間和該時間適用於星期幾。
4. 選取 [**儲存**]。
5. 這個原則一經啟用，就不會自動套用到目前實驗室中的任何 VM。若要將這項設定套用至特定的 VM，請開啟 VM 的刀鋒視窗並變更其 [自動啟動] 設定。

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## 後續步驟
實驗室一旦定義並套用了各種 VM 原則設定，接下來就要嘗試一些作業：

* [設定成本管理](devtest-lab-configure-cost-management.md) - 示範如何使用 [每月估計成本趨勢] 圖表，來檢視本月到目前為止的估計成本，以及預計的月底成本。
* [建立自訂映像](devtest-lab-create-template.md) - 當您建立 VM 時，您要指定一個基本映像，它可以是自訂映像或 Marketplace 映像。本文會示範如何從 VHD 檔案建立自訂的映像。
* [設定 Marketplace 映像](devtest-lab-configure-marketplace-images.md) - Azure DevTest Labs 支援根據 Azure Marketplace 映像建立 VM。本文會示範在實驗室中建立 VM 時，如何指定可以使用哪些 Azure Marketplace 映像 (如果有的話)。
* [在實驗室中建立 VM](devtest-lab-add-vm-with-artifacts.md) - 示範如何從基本映像 (自訂或 Marketplace) 建立 VM，以及如何使用 VM 中的構件。

<!---HONumber=AcomDC_0914_2016-->