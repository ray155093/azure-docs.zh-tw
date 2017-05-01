在網域名稱的記錄傳播完成後，您必須將這些記錄與 Web 應用程式產生關聯。 利用下列步驟，使用網頁瀏覽器啟用網域名稱。

> [!NOTE]
> 需要一些時間，上述步驟建立的 TXT 記錄才能傳播至整個 DNS 系統。 完成 TXT 記錄傳播前，您無法將網域名稱新增至 Web 應用程式。 如果您是使用 A 記錄，則在傳輸上一個步驟建立的 TXT 記錄後，才能將 A 記錄網域名稱新增至 Web 應用程式。
> 
> 您可以使用 <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> 之類的服務來驗證 TXT 記錄是否已生效。
> 
> 

1. 在瀏覽器中，開啟 [Azure 入口網站](https://portal.azure.com)。
2. 在 [Web Apps] 索引標籤中，按一下您 Web 應用程式的名稱，然後選取 [自訂網域]
   
    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)
3. 在 [自訂網域] 刀鋒視窗中，按一下 [新增主機名稱]。
4. 使用 [主機名稱]  文字方塊輸入網域名稱來與此 Web 應用程式關聯。
   
    ![](./media/custom-dns-web-site/add-custom-domain.png)
5. 按一下 [驗證] 。
6. 按一下 [驗證] 後，Azure 便會開始進行網域驗證工作流程。 此流程將會檢查網域擁有權及主機名稱可用性，並回報成功與否。在發生錯誤的情況下，將會提供錯誤詳細資料，以及錯誤修復方式的規範指引。    

此時，您應該能夠在瀏覽器中輸入自訂網域名稱，並且能成功移至您的 Web 應用程式。

