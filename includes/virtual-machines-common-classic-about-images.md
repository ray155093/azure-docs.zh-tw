

Azure 會使用映像透過作業系統提供新的虛擬機器。一個映像可能有一或多個資料磁碟。映像可來自多個來源：

-	Azure 在 [Marketplace](https://azure.microsoft.com/gallery/virtual-machines/) 中提供映像。您可以找到最新版本的 Windows Server 和 Linux 作業系統散發套件。有些映像也包含應用程式，例如 SQL Server。MSDN 權益與 MSDN 隨收隨付的訂閱者可以存取其他映像。
-	開放原始碼社群透過 [VM Depot](http://vmdepot.msopentech.com/List/Index) 提供映像。
-	您也可以擷取現有的 Azure 虛擬機器作為映像或上傳映像，並在 Azure 中儲存和使用自己的映像。

## 關於 VM 映像和 OS 映像

在 Azure 中可用的映像有兩種類型：*VM 映像*和 *OS 映像*。VM 映像包含作業系統和建立映像時所有連接至虛擬機器的磁碟。這是較新的映像類型。導入 VM 映像前，Azure 中的映像可能只會有一般化的作業系統，而沒有其他磁碟。僅包含一般化作業系統的 VM 映像基本上與映像的原始類型相同，即 OS 映像。

您可以根據 Azure 上的虛擬機器，或您複製及上傳並正在其他地方執行的虛擬機器，來建立自己的映像。如果您想要使用一個映像來建立多個虛擬機器，您必須先將映像一般化，以便準備作為映像使用。若要建立 Windows Server 映像，請在上傳.vhd 檔案前，先在伺服器上執行 Sysprep 命令進行一般化。如需關於 Sysprep 的詳細資訊，請參閱 [How to Use Sysprep: An Introduction (如何使用 Sysprep：簡介)](http://go.microsoft.com/fwlink/p/?LinkId=392030) 和 [Sysprep Support for Server Roles (伺服器角色的 Sysprep 支援)](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles)。若要建立 Linux 映像，取決於該軟體發佈，您需要執行一組特定用於該發佈的命令，並執行 Azure Linux 代理程式。

<!---HONumber=AcomDC_0817_2016-->