

![獨立雲端服務中的虛擬機器](./media/virtual-machines-common-classic-connect-vms/CloudServiceExample.png)

如果您將虛擬機器放在虛擬網路，您可以決定要將多少雲端服務用於負載平衡和可用性集。 此外，您可以利用內部部署網路的相同方式，在子網路上組織虛擬機器，並將虛擬網路連線到內部部署網路。 以下是範例：

![虛擬網路中的虛擬機器](./media/virtual-machines-common-classic-connect-vms/VirtualNetworkExample.png)

若要在 Azure 中連線虛擬機器，建議使用虛擬網路。 最佳作法是在個別的雲端服務中設定應用程式的每一層。 不過，您可能需要將不同應用程式層的部分虛擬機器結合至相同的雲端服務，以維持在每個訂用帳戶最多有 200 項雲端服務的限制內。 若要檢閱本限制和其他限制，請參閱 [Azure 訂用帳戶和服務限制、配額與條件約束](../articles/azure-subscription-service-limits.md)。

## <a name="connect-vms-in-a-virtual-network"></a>連接虛擬網路中的 VM
若要連線虛擬網路中的虛擬機器：

1. 在 [Azure 入口網站](../articles/virtual-network/virtual-networks-create-vnet-classic-pportal.md)中建立虛擬網路並指定「傳統部署」。
2. 為部署一組建立雲端服務，以反映可用性設定組和負載平衡的設計。 在 Azure 入口網站中，針對每個雲端服務，按一下 [新增] > [計算] > [雲端服務]。

  當您填寫雲端服務詳細資料時，請選擇搭配虛擬網路使用的相同「資源群組」。

3. 若要建立每個新的虛擬機器，請按一下 [新增] > [計算]，然後從 [精選 App] 中選取適當的 VM 映像。

  在 VM 的 [基本概念] 刀鋒視窗中，選擇搭配虛擬網路使用的相同「資源群組」。

  ![使用 VNet 時的 VM 基本概念刀鋒視窗](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_VN.png)

4. 當您填寫 VM 的 [設定] 時，為 VM 選擇正確的「雲端服務」和「虛擬網路」。

  Azure 會根據您選取的項目來選取其他項目。

  ![使用 VNet 時的 VM 設定刀鋒視窗](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_VN.png)


## <a name="connect-vms-in-a-standalone-cloud-service"></a>連接獨立雲端服務中的 VM
若要在獨立雲端服務中連接虛擬機器：

1. 在 [Azure 入口網站](http://portal.azure.com)中建立雲端服務。 按一下 [新增] > [計算] > [雲端服務]。 或者，當您建立第一部虛擬機器時，您可以為您的部署建立雲端服務。
2. 當您建立虛擬機器時，請選擇搭配雲端服務使用的相同資源群組。

  ![將虛擬機器加入至現有的雲端服務。](./media/virtual-machines-common-classic-connect-vms/CreateVM_Basics_SA.png)

3.  當您填寫 VM 詳細資料時，請選擇在第一個步驟中建立的雲端服務名稱。

  ![選取虛擬機器的雲端服務](./media/virtual-machines-common-classic-connect-vms/CreateVM_Settings_SA.png)
