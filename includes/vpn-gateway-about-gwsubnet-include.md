虛擬網路閘道會使用稱為 'GatewaySubnet' 的特定子網路。 閘道子網路包含 VPN 閘道服務所使用的 IP 位址。 當您建立閘道子網路時，請將它命名為「GatewaySubnet」。  將子網路命名為 'GatewaySubnet'，可讓 Azure 知道建立閘道服務的位置。 如果您將子網路命名為其他名稱，您的 VPN 閘道設定將會失敗。

GatewaySubnet 中的 IP 位址會配置給閘道服務。 當您建立 GatewaySubnet 時，您可指定子網路包含的 IP 位址數目。 您所要建立的 VPN 閘道組態會決定您需要為 GatewaySubnet 指定的大小。 雖然您可以建立小至 /29 的 GatewaySubnet，但我們會建議您選取 /27 或 /28，以建立包含更多位址的較大子網路。 使用較大的閘道子網路可讓您擁有足夠的 IP 位址，以因應未來可能的組態。