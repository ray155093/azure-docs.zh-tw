## 快速步驟 

本文假設您已登入您在入口網站中的訂用帳戶，並已使用 Resource Manager 部署模型以可用的映像建立虛擬機器。一旦您的虛擬機器開始執行，請依照下列步驟。

1.  檢視入口網站上的虛擬機器設定，並按一下 [公用 IP 位址]。

    ![找出 IP 資源](./media/virtual-machines-common-portal-create-fqdn/locatePublicIP.PNG)

2.  請注意，公用 IP 的 DNS 名稱是空白的。按一下 [公用 IP] 刀鋒視窗中的 [組態]。

    ![設定 IP](./media/virtual-machines-common-portal-create-fqdn/settingsIP.PNG)

3.  輸入想要的 DNS 名稱標籤，然後 [儲存] 此組態。

    ![輸入 DNS 名稱標籤](./media/virtual-machines-common-portal-create-fqdn/dnsNameLabel.PNG)

    公用 IP 資源現在會在其刀鋒視窗上顯示這個新的 DNS 標籤。

4.  關閉 [公用 IP] 刀鋒視窗，然後返回入口網站中的虛擬機器刀鋒視窗。請確認 DNS 名稱/FQDN 顯示在公用 IP 資源的 IP 位址旁邊。

    ![FQDN 已建立](./media/virtual-machines-common-portal-create-fqdn/fqdnCreated.PNG)

<!---HONumber=AcomDC_0831_2016-->