<properties
   pageTitle="測試 Azure Web 應用程式的效能 |Microsoft Azure"
   description="執行 Azure Web 應用程式效能測試，檢查您的應用程式如何處理使用者負載。測量回應時間及尋找可能表示問題的失敗狀況。"
   services="app-service\web"
   documentationCenter=""
   authors="ecfan"
   manager="douge"
   editor="jimbe"/>

<tags
   ms.service="app-service-web"
   ms.workload="web"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="05/25/2016"
   ms.author="estfan; manasma; ahomer"/>

# 測試 Azure Web 應用程式在低負載下的效能

在您啟動 Web 應用程式或將更新部署至生產環境之前，請先檢查該應用程式的效能。如此一來，您即可更妥善地評估您的應用程式是否已準備好發行。對您的應用程式更具信心，其可在尖峰使用期間或在您下一波推式行銷時處理流量。

在公開預覽期間，您可以在 Azure 入口網站中免費測試您的應用程式的效能。這些測試會模擬您的應用程式在特定期間內的使用者負載並測量您的應用程式的回應。例如，您的測試結果會顯示您的應用程式對指定數量的使用者的回應速度。也會顯示多少要求失敗，這可能表示您的應用程式有問題。

![尋找您的 Web 應用程式中的效能問題](./media/app-service-web-app-performance-test/azure-np-perf-test-overview.png)

## 開始之前

* 您將需要 [Azure 訂用帳戶](https://account.windowsazure.com/subscriptions) (如果您還沒有的話)。了解如何[免費申請 Azure 帳戶](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F)。

* 您需要 [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) 帳戶才能保留您的效能測試記錄。當您設定效能測試時，會自動建立適當的帳戶。或者，如果您是帳戶擁有者，可以建立新的帳戶，或使用現有的帳戶。

* 部署您的應用程式以便在非生產環境中進行測試。讓您的應用程式使用生產環境中所用方案以外的 App Service 方案。這樣一來，您不會影響任何現有的客戶或讓您的應用程式在生產環境中變慢。

## 設定和執行效能測試

0.  登入 [Azure 入口網站](https://portal.azure.com)。若要使用您所擁有的 Visual Studio Team Services 帳戶，請以帳戶擁有者的身分登入。

0.  移至您的 Web 應用程式。

    ![請移至 [全部瀏覽]、[Web Apps]、您的 Web 應用程式](./media/app-service-web-app-performance-test/azure-np-web-apps.png)

0.  移至 [效能測試]。

    ![移至 [工具]、[效能測試]](./media/app-service-web-app-performance-test/azure-np-web-app-details-tools-expanded.png)
 
0. 現在您將連結 [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) 帳戶來保留您的效能測試記錄。

    如果您已經有 Team Services 帳戶，請選取該帳戶。如果沒有，請建立新帳戶。

    ![選取現有的 Team Services 帳戶，或建立新的帳戶](./media/app-service-web-app-performance-test/azure-np-no-vso-account.png)

0.  建立您的效能測試。設定詳細資料並執行測試。

您可以在測試執行時即時觀看結果。

例如，假設我們有在去年節日特賣時發放優待券的應用程式。這個活動持續了 15 分鐘，尖峰負載為 100 位並行客戶。我們希望今年的客戶數變成兩倍。我們還想讓頁面載入時間從 5 秒降低為 2 秒，以改善客戶滿意度。因此，我們將以 250 名使用者測試更新後應用程式的效能達 15 分鐘之久。

我們會產生同時造訪我們的網站的虛擬使用者 (客戶)，藉此模擬應用程式的負載。這會顯示有多少個要求失敗或回應速度較慢。

  ![設定、設定和執行效能測試](./media/app-service-web-app-performance-test/azure-np-new-performance-test.png)

   *  系統會自動加入您的 Web 應用程式的預設 URL。您可以變更此 URL 以測試其他頁面 (僅限 HTTP GET 要求)。

   *  若要模擬本機情況並降低延遲，請選取最靠近使用者的位置來產生負載。

  以下是進行中的測試。在第一分鐘內，我們的頁面載入速度低於預期。

  ![使用即時資料的進行中效能測試](./media/app-service-web-app-performance-test/azure-np-running-perf-test.png)

  測試完成後，我們了解頁面載入速度在第一分鐘後變快許多。這有助於找出我們要開始排解疑難問題的位置。

  ![完成的效能測試會顯示結果，包括失敗的要求](./media/app-service-web-app-performance-test/azure-np-perf-test-done.png)

## 測試多個 URL

您也可以藉由上傳 Visual Studio Web 測試檔案，來執行合併多個代表端對端使用者案例之 URL 的效能測試。您可以使用下列數種方式來建立 Visual Studio Web 測試檔案：

* [使用 Fiddler 擷取流量，並匯出為 Visual Studio Web 測試檔案](http://docs.telerik.com/fiddler/Save-And-Load-Traffic/Tasks/VSWebTest)
* [在 Visual Studio 中建立負載測試檔案](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release)

上傳並執行 Visual Studio Web 測試檔案︰
 
0. 請遵循上述步驟來開啟 [新增效能測試] 刀鋒視窗。在此刀鋒視窗中，選擇 [設定測試使用條件] 選項來開啟 [設定測試使用條件] 刀鋒視窗。  

    ![開啟 [設定負載測試] 刀鋒視窗](./media/app-service-web-app-performance-test/multiple-01-authoring-blade.png)

0. 檢查測試類型是否已設為 [Visual Studio Web 測試]，然後選取 HTTP 封存檔案。使用 [資料夾] 圖示來開啟檔案選擇器對話方塊。

    ![上傳多個 URL Visual Studio Web 測試檔案](./media/app-service-web-app-performance-test/multiple-01-authoring-blade2.png)

    上傳檔案之後，您會在 [URL 詳細資料] 區段中看見要測試的 URL 清單。
 
0. 指定使用者負載和測試持續期間，然後選擇 [執行測試]。

    ![選取使用者負載和持續時間](./media/app-service-web-app-performance-test/multiple-01-authoring-blade3.png)

    測試完成之後，您會在兩個窗格中看見結果。左窗格會以一系列圖表顯示效能資訊。

    ![效能結果窗格](./media/app-service-web-app-performance-test/multiple-01a-results.png)

    右窗格會顯示失敗的要求清單，以及錯誤類型和其發生的次數。

    ![要求失敗窗格](./media/app-service-web-app-performance-test/multiple-01b-results.png)

0. 藉由選擇右窗格頂端的 [重新執行] 圖示來重新執行測試。

    ![重新執行測試](./media/app-service-web-app-performance-test/multiple-rerun-test.png)

##  問答集

#### 問：我可持續執行測試的時間是否有所限制？ 

**答**：是，您最多可以在 Azure 入口網站中執行一小時的測試。

#### 問：我可執行效能測試的時間有多少？ 

**答**：公開預覽後，透過您的 Visual Studio Team Services 帳戶，您每個月可免費取得 20,000 虛擬使用者分鐘數 (VUM)。VUM 是虛擬使用者數目乘以您測試中的分鐘數。如果您的需求超過免費限制，您可以購買更多時間，而且僅支付您所用的時間。

#### 問：哪裡可以檢查到目前為止我已使用多少 VUM？

**答**：您可以在 Azure 入口網站中檢查此數量。

![移至您的 Team Services 帳戶](./media/app-service-web-app-performance-test/azure-np-vso-accounts.png)

![檢查已使用的 VUM](./media/app-service-web-app-performance-test/azure-np-vso-accounts-vum-summary.png)

#### 問︰什麼是預設選項，我現有的測試是否會受到影響？

**答**︰效能負載測試的預設選項是手動測試 - 與在將多個 URL 測試選項新增至入口網站之前相同。您現有的測試會繼續使用已設定的 URL，並如往常般運作。

#### 問︰Visual Studio Web 測試檔案中不支援哪些功能？

**答**︰此功能目前不支援 Web 測試外掛程式、資料來源和擷取規則。您必須編輯 Web 測試檔案來移除這些功能。我們希望在未來更新中加入對這些功能的支援。

#### 問︰它是否支援任何其他 Web 測試檔案格式？
  
**答**︰目前只支援 Visual Studio Web 測試格式檔案。如果您需要其他檔案格式的支援，我們很樂意聆聽您的意見反應。請透過下列電子郵件地址連絡我們：[vsoloadtest@microsoft.com](mailto:vsoloadtest@microsoft.com)。

#### 問：Visual Studio Team Services 帳戶還有什麼用途？

**答**：若要尋找您的新帳戶，請移至 ```https://{accountname}.visualstudio.com```。使用任何工具或語言來共用您的程式碼、建置、測試、追蹤工作及軟體出貨 – 一切盡在雲端。深入了解 [Visual Studio Team Services](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) 功能及服務如何協助您的小組更輕鬆地共同作業及持續進行部署。

## 另請參閱

* [執行簡單的雲端效能測試](https://www.visualstudio.com/docs/test/performance-testing/getting-started/get-started-simple-cloud-load-test)
* [執行 Apache Jmeter 效能測試](https://www.visualstudio.com/docs/test/performance-testing/getting-started/get-started-jmeter-test)
* [Record and replay cloud-based load tests (記錄並重新執行雲端架構負載測試)](https://www.visualstudio.com/docs/test/performance-testing/getting-started/record-and-replay-cloud-load-tests)
* [在雲端中進行 App 效能測試](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)

<!---HONumber=AcomDC_0525_2016-->