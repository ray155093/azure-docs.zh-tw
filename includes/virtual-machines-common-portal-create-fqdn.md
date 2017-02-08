## <a name="quick-steps"></a>快速步驟
本文假設您已登入您在入口網站中的訂用帳戶，並已使用 Resource Manager 部署模型以可用的映像建立虛擬機器。 一旦您的虛擬機器開始執行，請依照下列步驟。

1. 在入口網站中選取您的虛擬機器。 DNS 名稱是空白。 按一下 [公用 IP 位址]：
   
   ![在入口網站中按一下 [公用 IP] 資源](./media/virtual-machines-common-portal-create-fqdn/locatePublicIP.PNG)

2. 輸入想要的 DNS 名稱標籤，然後按一下 [儲存]。
   
   ![輸入您公用 IP 資源的 DNS 名稱標籤](./media/virtual-machines-common-portal-create-fqdn/dnsNameLabel.PNG)
   
   公用 IP 資源現在會在其刀鋒視窗上顯示這個新的 DNS 標籤。

3. 關閉 [公用 IP] 刀鋒視窗，然後返回入口網站中的 [VM 概觀] 刀鋒視窗。 幾秒鐘後，入口網站應該會更新您的設定。 請確認 DNS 名稱/FQDN 顯示在**公用 IP 位址**資源的 IP 位址旁邊。
   
   ![確認已設定新的 DNS 標籤](./media/virtual-machines-common-portal-create-fqdn/fqdnCreated.PNG)



<!--HONumber=Nov16_HO3-->


