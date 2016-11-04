## 如何在 Azure 入口網站中建立 VNet 對等互連
若要透過 Azure 入口網站依據上述案例建立 VNet 對等互連，請依照下列步驟執行。

1. 透過瀏覽器瀏覽至 http://portal.azure.com，並視需要使用您的 Azure 帳戶登入。
2. 若要建立 VNET 對等互連，您必須在兩個 VNet 之間建立兩個連結，每個方向各一。您可以先建立 VNET1 至 VNET2 的 VNET 對等互連連結。在入口網站中，按一下 [瀏覽] > 選擇 [虛擬網路]
   
    ![在 Azure 入口網站中建立 VNet 對等互連](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure01.png)
3. 在 [虛擬網路] 刀鋒視窗中，選擇 [VNET1]，按一下 [對等互連]，然後按一下 [新增]
   
    ![選擇對等互連](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure02.png)
4. 在 [新增對等互連] 刀鋒視窗中，輸入對等互連連結名稱 LinkToVnet2，選擇訂用帳戶和對等虛擬網路 VNET2，按一下 [確定]。
   
    ![連結至 VNet](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure03.png)
5. 建立此 VNET 對等互連連結之後，您就可以看到如以下的連結狀態︰
   
    ![連結狀態](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure04.png)
6. 接下來，建立 VNET2 至 VNET1 的 VNet 對等互連連結。在 [虛擬網路] 刀鋒視窗中，選擇 VNET2，按一下 [對等互連]，然後按一下 [新增]
   
    ![從其他 VNet 對等互連](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure05.png)
7. 在 [新增對等互連] 刀鋒視窗中，輸入對等互連連結名稱 LinkToVnet1，選擇訂用帳戶和對等虛擬網路，按一下 [確定]。
   
    ![建立虛擬網路磚](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure06.png)
8. 建立此 VNET 對等互連連結之後，您就可以看到如以下的連結狀態︰
   
    ![最終連結狀態](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure07.png)
9. 檢查 LinkToVnet2 的狀態，它現在也變更為 [已連接]。
   
    ![最終連結狀態 2](./media/virtual-networks-create-vnetpeering-arm-pportal-include/figure08.png)
10. 注意：只有當兩個連結都已連接時，才會建立 VNET 對等互連。

<!---HONumber=AcomDC_0803_2016-->