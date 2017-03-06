

「自訂」虛擬機器單指您使用從 **Marketplace** 取得的**熱門應用程式**所建立的虛擬機器，因為它為您做了大部分的工作。 但是，您仍然可以在組態選項中包括下列項目：

* 將虛擬機器連線至虛擬網路
* 安裝 Azure 虛擬機器代理程式和 Azure 虛擬機器擴充程式，例如用於反惡意程式碼。
* 將虛擬機器加入至現有的雲端服務。
* 將虛擬機器加入現有的儲存體帳戶。
* 將虛擬機器加入至可用性集合。

<!--
> [!IMPORTANT]
> If you want your virtual machine to use a virtual network so you can connect to it directly by host name or set up cross-premises connections, make sure that you specify the virtual network when you create the virtual machine. A virtual machine can be configured to join a virtual network only when you create the virtual machine. For details on virtual networks, see [Azure Virtual Network overview](../articles/virtual-network/virtual-networks-overview.md).
>
>
 -->

> [!IMPORTANT]
> 如果您想要讓虛擬機器使用虛擬網路，請務必在建立虛擬機器時指定虛擬網路。

> * 使用虛擬網路的兩個優點是直接連接到虛擬機器，以及設定跨單位連線。

> * 只有在建立虛擬機器時，才能將虛擬機器設定為加入虛擬網路。 如需虛擬網路的詳細資訊，請參閱 [Azure 虛擬網路概觀](../articles/virtual-network/virtual-networks-overview.md)。
>
>

## <a name="to-create-the-virtual-machine"></a>建立虛擬機器
