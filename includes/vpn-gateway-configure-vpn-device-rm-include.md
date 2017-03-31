內部部署網路的站對站連線需要 VPN 裝置。 有多種將與 Azure 一起運作的不同 VPN 裝置。 如需有關 VPN 裝置和組態設定的詳細資訊，請參閱 [VPN 裝置](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md)。 請在設定 VPN 裝置之前，檢查您要使用的 VPN 裝置是否有任何[已知裝置相容性問題](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#known)。 如需特定 VPN 裝置組態資訊，請洽詢您的裝置製造商。

在設定 VPN 裝置時，您需要下列項目：

- 虛擬網路閘道的**公用 IP 位址**。

    -  若要使用 Azure 入口網站尋找公用 IP 位址，請瀏覽至 [虛擬網路閘道]，然後按一下閘道名稱。 

    - 若要使用 PowerShell 尋找虛擬網路閘道的公用 IP 位址，請使用下面範例，將這些值替換為自己的值。

            Get-AzureRmPublicIpAddress -Name GW1PublicIP -ResourceGroupName TestRG
- **共用金鑰**。 這是當您建立站對站 VPN 連線時將要指定的相同共用金鑰。 在我們的範例中，我們會使用非常基本的共用金鑰。 您應該產生更複雜的金鑰來使用。




