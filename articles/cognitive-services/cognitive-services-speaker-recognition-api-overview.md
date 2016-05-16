<properties
   pageTitle="概觀︰Cognitive Services 的說話者辨識 API | Microsoft Azure"
   description="了解說話者辨識如何增強應用程式。說話者辨識 API 有進階演算法可處理說話者驗證和識別。"
   services="cognitive-services"
   documentationCenter="na"
   authors="cjgronlund"
   manager="paulettem"
   editor="cjgronlund"/>

<tags
   ms.service="cognitive-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/02/2016"
   ms.author="cgronlun"/>

# 概觀︰來自 Azure Cognitive Services 的說話者辨識 API

了解說話者辨識功能如何增強應用程式。說話者辨識 API 是雲端式 API，提供最先進的演算法進行說話者驗證和說話者識別。說話者辨識可以分為兩類︰

  * 說話者驗證
  * 說話者識別

## 說話者驗證

語音都有唯一的特性，像指紋一樣可以用來識別個人。將語音用為存取控制和驗證案例的訊號，已經成為新的創新工具，其主要提供高一級的安全性以簡化客戶的驗證體驗。

說話者驗證 API 可以使用其聲音或話語內容自動檢查並驗證使用者。

### 說話者驗證註冊

說話者驗證註冊因文字而異，這表示說話者需要選擇在註冊和驗證階段期間所用的特定通關語。

註冊會錄下說話者說出特定語句的聲音，然後擷取數個特點並辨識所選語句。擷取的特點和所選語句兩者一起形成唯一的語音簽章。

### 驗證輸入的語音及語句

驗證會比對輸入的語音和語句與註冊的語音簽章和語句，以確認它們是否來自同一人，以及是否說出正確的語句。

如需說話者驗證的詳細資訊，請參閱[說話者辨識 API 文件的驗證部分](https://dev.projectoxford.ai/docs/services/563309b6778daf02acc0a508/operations/563309b7778daf06340c9652)。

## 說話者識別

說話者識別 API 會自動找出音訊檔案中說話的人，假定有一群可能的說話者。輸入的音訊會比對所提供的一群 說話者，如果找到相符的項目，即傳回說話者的身分識別。

所有的說話者都應該先完成註冊程序，向系統註冊其語音，並建立聲紋。

### 說話者識別註冊

說話者識別註冊與文字無關，這表示說話者在音訊中什麼都可以說。說話者的聲音會被錄下，並擷取多項特點以形成唯一的語音簽章。

### 說話者辨識︰比對輸入的語音

辨識期間會提供未知說話者和一群可能的說話者的音訊。比對輸入的語音和所有說話者，以判斷這是誰的語音，如果找到相符項目，就會傳回說話者的身分識別。

如需說話者識別的詳細資訊，請參閱[說話者辨識 API 文件的識別部分](https://dev.projectoxford.ai/docs/services/563309b6778daf02acc0a508/operations/5645c068e597ed22ec38f42e)。

<!---HONumber=AcomDC_0504_2016-->