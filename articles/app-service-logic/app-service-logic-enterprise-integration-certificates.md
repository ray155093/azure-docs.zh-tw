
<properties
	pageTitle="使用憑證搭配企業整合套件 | Microsoft Azure"
	description="了解如何使用憑證搭配企業整合套件與 Logic Apps"
	services="logic-apps"
	documentationCenter=".net,nodejs,java"
	authors="msftman"
	manager="erikre"
	editor="cgronlun"/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/06/2016"
	ms.author="deonhe"/>

# 了解憑證與企業整合套件

## Overview
企業整合使用憑證來保護 B2B 通訊的安全。您可以在企業整合應用程式中使用兩種憑證類型：

- 公開憑證，必須先向憑證授權單位 (CA) 購買。
- 私人憑證，您可以自行核發。這些憑證有時也稱為自我簽署憑證。


## 什麼是憑證？
憑證是數位文件，可驗證電子通訊參與者的身分識別，同時還能保護電子通訊的安全。

## 為什麼要使用憑證？
B2B 通訊有時必須確保機密。企業整合透過下列兩種方式，使用憑證來保護這些通訊的安全：

- 將訊息的內容加密
- 以數位方式簽署訊息

## 如何上傳憑證？

### 公開憑證
若要在具備 B2B 功能的 Logic Apps 中使用「公開憑證」，您必須先將憑證上傳到整合帳戶。另一方面，若要使用「自我簽署憑證」，則必須先將它上傳到 [Azure 金鑰保存庫](../key-vault/key-vault-get-started.md "了解金鑰保存庫")。

上傳憑證之後，當您在自己建立的[合約](./app-service-logic-enterprise-integration-agreements.md)中定義 B2B 訊息的屬性時，就能使用它來保護這些訊息的安全。

以下是您登入 Azure 入口網站後，將公開憑證上傳到整合帳戶的詳細步驟：

1. 選取 [瀏覽]。![選取瀏覽](./media/app-service-logic-enterprise-integration-overview/overview-1.png)

2. 在篩選搜尋方塊中輸入**整合**，然後從結果清單中選取 [整合帳戶]。![選取整合帳戶](./media/app-service-logic-enterprise-integration-overview/overview-2.png)

3. 選取要將憑證新增到的整合帳戶。![選取要將憑證新增到的整合帳戶](./media/app-service-logic-enterprise-integration-overview/overview-3.png)

4.  選取 [憑證] 圖格。![選取憑證圖格](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)

5. 在開啟的 [憑證] 刀鋒視窗中選取 [新增] 按鈕。![選取新增按鈕](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)

6. 輸入憑證的**名稱**，然後選取憑證類型 (在此範例中，我們使用了公開憑證類型)。 然後選取 [憑證] 文字方塊右邊的資料夾圖示。

7. 當檔案選擇器開啟時，尋找並選取您要上傳到整合帳戶的憑證檔案。

8. 選取憑證，然後在檔案選擇器中選取 [確定]。這會驗證憑證並上傳到您的整合帳戶。

8. 最後，返回 [新增憑證] 刀鋒視窗，然後選取 [確定] 按鈕。![選取確定按鈕](./media/app-service-logic-enterprise-integration-certificates/certificate-3.png)

9. 您會在大約一分鐘內看見一則通知，表示憑證上傳已完成。

10. 選取 [憑證] 圖格。您應該會看到新增的憑證。![查看新憑證](./media/app-service-logic-enterprise-integration-certificates/certificate-4.png)

### 私人憑證
您可以採取下列步驟將私人憑證上傳到整合帳戶：

1. [將您的私人金鑰上傳到金鑰保存庫](../key-vault/key-vault-get-started.md "了解金鑰保存庫")。

	> [AZURE.TIP] 您必須授權 Azure App Service 的 Logic Apps 功能對金鑰保存庫執行作業。您可以使用下列 PowerShell 命令授與 Logic Apps 服務主體的存取權︰`Set-AzureRmKeyVaultAccessPolicy -VaultName 'TestcertKeyVault' -ServicePrincipalName '7cd684f4-8a78-49b0-91ec-6a35d38739ba' -PermissionsToKeys decrypt, sign, get, list`

2. 建立私人憑證。

3. 將私人憑證上傳到整合帳戶中。

採取過上述步驟之後，您就可以使用私人憑證來建立合約。

以下是您登入 Azure 入口網站後，將私人憑證上傳到整合帳戶的詳細步驟：

1. 選取 [瀏覽]。![將私人憑證上傳到整合帳戶中](./media/app-service-logic-enterprise-integration-overview/overview-1.png)

2. 在篩選搜尋方塊中輸入**整合**，然後從結果清單中選取 [整合帳戶]。![選取整合帳戶](./media/app-service-logic-enterprise-integration-overview/overview-2.png)

3. 選取要將憑證新增到的整合帳戶。![選取要將憑證新增到的整合帳戶](./media/app-service-logic-enterprise-integration-overview/overview-3.png)

4. 選取 [憑證] 圖格。![選取憑證圖格](./media/app-service-logic-enterprise-integration-certificates/certificate-1.png)

5. 在開啟的 [憑證] 刀鋒視窗中選取 [新增] 按鈕。![選取新增按鈕](./media/app-service-logic-enterprise-integration-certificates/certificate-2.png)

6. 輸入憑證的**名稱**，然後選取憑證類型 (在此範例中，我們使用了公開憑證類型)。 然後選取 [憑證] 文字方塊右邊的資料夾圖示。

7. 當檔案選擇器開啟時，尋找並選取您要上傳到整合帳戶的憑證檔案。

8. 當您選取憑證之後，請在檔案選擇器中選取 [確定]。此動作會驗證憑證並將其上傳到您的整合帳戶。

9. 最後，返回 [新增憑證] 刀鋒視窗，然後選取 [確定] 按鈕。![新增憑證](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-1.png)

10. 您會在大約一分鐘內看見一則通知，表示憑證上傳已完成。

11. 選取 [憑證] 圖格。您應該會看到新增的憑證。![查看新憑證](./media/app-service-logic-enterprise-integration-certificates/privatecertificate-2.png)

上傳憑證之後，當您在[合約](./app-service-logic-enterprise-integration-agreements.md)中定義 B2B 訊息的屬性時，就能使用它來保護這些訊息的安全。

## 後續步驟
- [建立使用 B2B 功能的邏輯應用程式](./app-service-logic-enterprise-integration-b2b.md)
- [建立 B2B 合約](./app-service-logic-enterprise-integration-agreements.md)
- [深入了解金鑰保存庫](../key-vault/key-vault-get-started.md "了解金鑰保存庫")

<!---HONumber=AcomDC_0928_2016-->