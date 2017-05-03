內部部署網路的站對站連線需要 VPN 裝置。 雖然我們不會提供所有 VPN 裝置的組態步驟，但您可能會發現下列連結中的資訊很有幫助︰

- 如需有關相容的 VPN 裝置資訊，請參閱[ VPN 裝置](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md)。 
- 如需裝置組態設定的連結，請參閱[已經驗證的 VPN 裝置](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable)。 會以最佳方式來提供裝置組態連結。 最好一律洽詢您的裝置製造商以取得最新的組態資訊。
- 如需編輯裝置組態範例的相關資訊，請參閱[編輯範例](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#editing)。
- 如需 IPsec/IKE 參數，請參閱[參數](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec)。
- 請在設定 VPN 裝置之前，檢查您要使用的 VPN 裝置是否有任何[已知裝置相容性問題](../articles/vpn-gateway/vpn-gateway-about-vpn-devices.md#known)。

在設定 VPN 裝置時，您需要下列項目：

- 共用金鑰。 這個共同金鑰與您建立站對站 VPN 連線時指定的共用金鑰相同。 在我們的範例中，我們會使用基本的共用金鑰。 我們建議您產生更複雜的金鑰以供使用。
- 虛擬網路閘道的公用 IP 位址。 您可以使用 Azure 入口網站、PowerShell 或 CLI 來檢視公用 IP 位址。