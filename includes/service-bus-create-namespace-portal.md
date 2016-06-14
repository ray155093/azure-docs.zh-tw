1. 登入 Azure 傳統入口網站。

2. 在入口網站的左方瀏覽窗格中，按一下 [服務匯流排]。

3. 在入口網站的下方窗格中，按一下 [建立]。

    ![選取建立][1]
   
4. 在 [Add a new namespace] 對話方塊中，輸入命名空間名稱。系統會立即檢查此名稱是否可用。

    ![命名空間名稱][2]
  
5. 確定命名空間名稱可用之後，選擇要裝載命名空間的國家或區域。

6. 讓對話方塊中的其他欄位保留其預設值 ([傳訊] 和 [標準層])，然後按一下 [確定] 核取記號。此時系統會建立並啟用命名空間。系統為帳戶提供資源時，您可能需要等幾分鐘。
 
    ![已成功建立][3]

###取得認證
1. 在左方瀏覽窗格中，按一下 [服務匯流排] 節點，以顯示可用的命名空間清單：
 
    ![選取服務匯流排][4]
  
2. 從顯示的清單中，選取您剛建立的命名空間：
 
    ![選取命名空間][5]
 
3. 按一下 [連線資訊]。

    ![連線資訊][6]
  
4. 在 [**存取連線資訊**] 窗格中，尋找包含 SAS 金鑰和金鑰名稱的連接字串。

    ![存取連線資訊][7]
  
5. 記下金鑰，或將它複製到剪貼簿。

<!--Image references-->

[1]: ./media/service-bus-create-namespace-portal/select-create.png
[2]: ./media/service-bus-create-namespace-portal/namespace-name.png
[3]: ./media/service-bus-create-namespace-portal/created-successfully.png
[4]: ./media/service-bus-create-namespace-portal/select-service-bus.png
[5]: ./media/service-bus-create-namespace-portal/select-namespace.png
[6]: ./media/service-bus-create-namespace-portal/connection-information.png
[7]: ./media/service-bus-create-namespace-portal/access-connection-information.png


<!--Reference style links - using these makes the source content way more readable than using inline links-->
[classic-portal]: https://manage.windowsazure.com

<!---HONumber=AcomDC_0608_2016-->