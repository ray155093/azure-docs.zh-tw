## <a name="tcp-settings-for-azure-vms"></a>Azure VM 的 TCP 設定
Azure VM 是使用 [NAT][nat] (網路位址轉譯) 與公用網際網路進行通訊。 NAT 裝置會將公用 IP 位址和連接埠指派給 Azure VM，以供該 VM 建立與其他裝置通訊的通訊端。 如果封包在特定時間過後不再流經該通訊端，NAT 裝置就會刪除對應，以讓其他 VM 使用這個通訊端。

這是常見的 NAT 行為，但卻可能會造成以 TCP 為基礎之應用程式的通訊問題，因為這些應用程式即使超過逾時期間仍需保留通訊端。 針對處於「已建立的連線」  狀態的工作階段，您可以考慮下列兩個閒置逾時設定︰

* 透過 [Azure load balancer][azure-lb-timeout] **輸入**。 這個逾時預設為 4 分鐘的時間，而且可以增加調整為 30 分鐘。
* 使用 [SNAT][snat] (來源 NAT) **輸出**。 此逾時設為 4 分鐘，且無法調整。

若要確保不會在超過逾時限制時遺失連線，應用程式必須讓工作階段保持運作；或者，您可以針對基礎的作業系統進行這項設定。 針對 Linux 和 Windows 系統要使用不同的設定，如下所示。

若是 [Linux][linux]，您應該變更以下的核心變數。
net.ipv4.tcp_keepalive_time = 120 net.ipv4.tcp_keepalive_intvl = 30 net.ipv4.tcp_keepalive_probes = 8

若是 [Windows][windows]，您應該變更以下的登錄值。
KeepAliveInterval = 30 KeepAliveTime = 120 TcpMaxDataRetransmissions = 8

上述設定可讓保持連線封包於 2 分鐘 (120 秒) 的閒置時間之後傳送，並每隔 30 秒再次傳送。 如果封包失敗 8 次，即會卸除工作階段。

<!-- links -->
[nat]: http://computer.howstuffworks.com/nat.htm
[snat]: ../load-balancer/load-balancer-overview.md/#source-nat
[linux]: http://tldp.org/HOWTO/TCP-Keepalive-HOWTO/usingkeepalive.html
[windows]: http://blogs.technet.com/b/nettracer/archive/2010/06/03/things-that-you-may-want-to-know-about-tcp-keepalives.aspx
[azure-lb-timeout]: ../load-balancer/load-balancer-tcp-idle-timeout.md

<!--HONumber=Jan17_HO3-->


