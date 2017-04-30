
1. 在 Mac 上，造訪 [Azure 入口網站]。 按一下 [瀏覽全部] > [行動應用程式] > 您剛才建立的後端。 在行動應用程式設定中，按一下 [快速入門]  >  [iOS (Objective-C)]。 如果您偏好使用 Swift，請改為按一下 [快速入門]  >  [iOS (Swift)]。 在 [下載並執行您的 iOS 專案] 中，按一下 [下載]。 這樣會為已預先設定成連接到您後端的應用程式，下載完整的 Xcode 專案。 使用 Xcode 開啟專案。
2. 按 [執行]  按鈕以建置專案，並在 iOS 模擬器中啟動應用程式。
3. 在應用程式中輸入有意義的文字 (例如 *Complete the tutorial* )，然後按一下加號 (**+**) 圖示。 這會將 POST 要求傳送至先前部署的 Azure 後端。 後端會將要求中的資料插入 TodoItem SQL 資料表，並將新儲存之項目的相關資訊傳回給行動應用程式。 行動應用程式會以清單顯示此資料。 

   ![在 iOS 上執行的快速入門應用程式](./media/app-service-mobile-ios-quickstart/mobile-quickstart-startup-ios.png)

[Azure 入口網站]: https://portal.azure.com/
