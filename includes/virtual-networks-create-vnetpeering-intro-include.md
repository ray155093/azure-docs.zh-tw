VNet 對等互連是透過 Azure 骨幹網路來連接同一區域中兩個虛擬網路的機制。一旦對等互連，針對用作連線而言兩個虛擬網路看起來就像單一的虛擬網路。如果您不熟悉 VNet 對等互連，請參閱 [VNet 對等互連概觀](../articles/virtual-network/virtual-network-peering-overview.md)。

VNet 對等互連處於公開預覽狀態，若要能使用您必須使用下列命令註冊︰

> [AZURE.NOTE] Register-AzureRmProviderFeature -FeatureName AllowVnetPeering -ProviderNamespace Microsoft.Network –force
 

<!---HONumber=AcomDC_0803_2016-->