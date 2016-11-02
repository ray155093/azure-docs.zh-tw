### 為虛擬機器建立 TCP 端點

若要從網際網路存取 SQL Server，虛擬機器必須具有端點才能接聽傳入 TCP 通訊。此 Azure 組態步驟能將傳入 TCP 連接埠流量導向虛擬機器可存取的 TCP 連接埠。

>[AZURE.NOTE] 如果您在相同的雲端服務或虛擬網路內連接，則不需要建立可公開存取的端點在此情況下，您可以繼續進行下一個步驟。如需詳細資訊，請參閱[連接案例](../articles/virtual-machines/virtual-machines-windows-classic-sql-connect.md#connection-scenarios)。

1. 在 Azure 入口網站中，選取 [虛擬機器 (傳統)]。

2. 然後選取您的 SQL Server 虛擬機器。

3. 選取 [端點]，然後按一下位於 [端點] 刀鋒視窗頂端的 [新增] 按鈕。

	![建立端點的入口網站步驟](./media/virtual-machines-sql-server-connection-steps/portal-endpoint-creation.png)

4. 在 [新增端點] 刀鋒視窗上，提供 [名稱]，例如 SQLEndpoint。

5. [通訊協定].請選取 [TCP]。

6. 對於 [公用連接埠]，請指定連接埠號碼，例如 **57500**。

7. 對於 [私用連接埠]，請指定 SQL Server 的接聽連接埠，預設為 **1433**。

6. 按一下 [確定] 以建立端點。

<!---HONumber=AcomDC_0629_2016-->