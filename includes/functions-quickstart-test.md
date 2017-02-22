
由於 Azure Functions 快速入門包含功能程式碼，您可以立即測試您的新函式。

1. 在 [開發] 索引標籤中，檢閱 [程式碼] 視窗，並請注意此 Node.js 程式碼預期 HTTP 要求有在訊息內文中或在查詢字串中傳遞的 name 值。 此函式執行時，回應訊息中會傳回這個值。
   
2. 按一下 [測試] 以顯示函式的內建 HTTP 測試要求窗格。
 
    ![](./media/functions-quickstart-test/function-app-develop-tab-testing.png)

3. 在 [要求本文] 文字方塊中，將 name 屬性的值變更為您的名稱，然後按一下 [執行]。 您會看到執行是由測試 HTTP 要求所觸發，資訊會寫入至記錄檔，而 "hello..." 回應會顯示在 [輸出] 中。 

4. 若要從 HTTP 測試工具或另一個瀏覽器視窗觸發相同函式的執行，請從 [開發] 索引標籤複製 [函式 URL] 值並將它貼到工具或瀏覽器網址列中。 將查詢字串值 `&name=yourname` 附加至 URL 並執行要求。 請注意，相同的資訊會寫入記錄檔，而相同的字串包含在回應訊息的本文中。

    ![](./media/functions-quickstart-test/function-app-browser-testing.png)


<!--HONumber=Feb17_HO2-->


