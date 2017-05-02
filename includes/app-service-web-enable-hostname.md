切換回包含 Azure 入口網站的瀏覽器視窗。

#### <a name="add-hostname"></a>新增主機名稱

按一下 [新增主機名稱] 旁的 **+** 圖示。

![新增主機名稱](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

#### <a name="validate-hostname"></a>驗證主機名稱

輸入您稍早設定 CNAME 記錄的完整網域名稱 (例如 `www.contoso.com`)，然後按一下 [驗證]。

選取 [主機名稱記錄類型] 標頭底下的 [CNAME (www.example.com 或任何子網域)] 選項。

按一下 [新增主機名稱]，將 DNS 名稱新增至您的應用程式。

![將 DNS 名稱新增至應用程式](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

可能需要一些時間，新的主機名稱才會反映在應用程式的 [自訂網域] 頁面中。 嘗試重新整理瀏覽器以更新資料。