## 使用 Azure 入口網站

1. 選取您想要重新部署的 VM，然後按一下 [設定] 刀鋒視窗中的 [重新部署] 按鈕︰

	![Azure VM 刀鋒視窗](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)

2. 按一下 [重新部署] 按鈕，確認此作業︰

	![重新部署 VM 刀鋒視窗](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)

3. 當 VM 準備重新部署時，您會看到 VM 的 [狀態] 變成 [更新中]︰

	![VM 正在更新](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)

4. 當 VM 在新的 Azure 主機上開機時，[狀態] 會接著變成 [啟動中]︰

	![VM 正在啟動](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)

5. 在 VM 完成開機程序之後，[狀態] 將回復為 [執行中]，表示已順利重新部署 VM︰

	![VM 正在執行](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

<!---HONumber=AcomDC_0706_2016-->