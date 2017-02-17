# 概觀
## [虛擬網路](virtual-networks-overview.md)
## [使用者定義的路由和 IP 轉送](virtual-networks-udr-overview.md)
## [虛擬網路對等互連](virtual-network-peering-overview.md)
## [商務持續性](virtual-network-disaster-recovery-guidance.md)
## [常見問題集](virtual-networks-faq.md)
## IP 位址
### [資源管理員](virtual-network-ip-addresses-overview-arm.md)
### [傳統](virtual-network-ip-addresses-overview-classic.md)
## 虛擬機器
### [網路介面](virtual-network-network-interface-overview.md)
### [名稱解析](virtual-networks-name-resolution-for-vms-and-role-instances.md)

# 開始使用
## [建立虛擬網路](virtual-networks-create-vnet-arm-pportal.md)
## [將 VM 部署至虛擬網路](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

# 作法
## 規劃和設計
### [虛擬網路](virtual-network-vnet-plan-design-arm.md)
### [網路安全性群組](virtual-networks-nsg.md)

## 部署
### 虛擬網路
#### [入口網站](virtual-networks-create-vnet-arm-pportal.md)
#### [PowerShell](virtual-networks-create-vnet-arm-ps.md)
#### [CLI](virtual-networks-create-vnet-arm-cli.md)
#### [範本](virtual-networks-create-vnet-arm-template-click.md)
#### [入口網站 (傳統)](virtual-networks-create-vnet-classic-pportal.md)
#### [PowerShell (傳統)](virtual-networks-create-vnet-classic-netcfg-ps.md)
#### [CLI (傳統)](virtual-networks-create-vnet-classic-cli.md)

### 網路安全性群組
#### [入口網站](virtual-networks-create-nsg-arm-pportal.md)
#### [PowerShell](virtual-networks-create-nsg-arm-ps.md)
#### [CLI](virtual-networks-create-nsg-arm-cli.md)
#### [範本](virtual-networks-create-nsg-arm-template.md)
#### [PowerShell (傳統)](virtual-networks-create-nsg-classic-ps.md)
#### [CLI (傳統)](virtual-networks-create-nsg-classic-cli.md)

### 使用者定義的路由
#### [PowerShell](virtual-network-create-udr-arm-ps.md)
#### [CLI](virtual-network-create-udr-arm-cli.md)
#### [範本](virtual-network-create-udr-arm-template.md)
#### [PowerShell (傳統)](virtual-network-create-udr-classic-ps.md)
#### [CLI (傳統)](virtual-network-create-udr-classic-cli.md)

### 虛擬網路對等互連
#### [入口網站](virtual-networks-create-vnetpeering-arm-portal.md)
#### [PowerShell](virtual-networks-create-vnetpeering-arm-ps.md)
#### [範本](virtual-networks-create-vnetpeering-arm-template-click.md)

### 虛擬機器

#### 靜態公用 IP 位址
##### [入口網站](virtual-network-deploy-static-pip-arm-portal.md)
##### [PowerShell](virtual-network-deploy-static-pip-arm-ps.md)
##### [CLI](virtual-network-deploy-static-pip-arm-cli.md)
##### [範本](virtual-network-deploy-static-pip-arm-template.md)
##### [PowerShell (傳統)](virtual-networks-reserved-public-ip.md)

#### 靜態私人 IP 位址
##### [入口網站](virtual-networks-static-private-ip-arm-pportal.md)
##### [PowerShell](virtual-networks-static-private-ip-arm-ps.md)
##### [CLI](virtual-networks-static-private-ip-arm-cli.md)
##### [入口網站 (傳統)](virtual-networks-static-private-ip-classic-pportal.md)
##### [PowerShell (傳統)](virtual-networks-static-private-ip-classic-ps.md)
##### [CLI (傳統)](virtual-networks-static-private-ip-classic-cli.md)

#### 多個網路介面
##### [PowerShell](virtual-network-deploy-multinic-arm-ps.md)
##### [CLI](virtual-network-deploy-multinic-arm-cli.md)
##### [範本](virtual-network-deploy-multinic-arm-template.md)
##### [PowerShell (傳統)](virtual-network-deploy-multinic-classic-ps.md)
##### [CLI (傳統)](virtual-network-deploy-multinic-classic-cli.md)

#### 多個 IP 位址
##### [Azure 入口網站](virtual-network-multiple-ip-addresses-portal.md)
##### [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
##### [CLI](virtual-network-multiple-ip-addresses-cli.md)
##### [範本](virtual-network-multiple-ip-addresses-template.md)

### 連線能力案例
#### [虛擬網路 (VNet) 至 VNet](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet (Resource Manager) 至 VNet (傳統)](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet 至內部部署網路 (VPN)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [VNet 至內部部署網路 (ExpressRoute)](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [高可用性的混合式網路架構](../guidance/guidance-hybrid-network-expressroute-vpn-failover.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### 安全性案例
#### [使用虛擬設備維護網路安全](virtual-network-scenario-udr-gw-nva.md)
#### [Azure 和網際網路之間的 DMZ](../guidance/guidance-iaas-ra-secure-vnet-dmz.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
#### [雲端服務和網路安全性](../best-practices-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
##### [具有 NSG 的簡單 DMZ](virtual-networks-dmz-nsg-asm.md)
##### [具有 NSG 和防火牆的 DMZ](virtual-networks-dmz-nsg-fw-asm.md)
##### [具有防火牆、UDR 和 NSG 的 DMZ](virtual-networks-dmz-nsg-fw-udr-asm.md)
##### [範例應用程式](virtual-networks-sample-app.md)

## 設定
### VM 加速網路
#### [Azure 入口網站](virtual-network-accelerated-networking-portal.md)
#### [PowerShell](virtual-network-accelerated-networking-powershell.md)
### [最佳化 VM 網路輸送量](virtual-network-optimize-network-bandwidth.md)
### 存取控制清單
#### [傳統入口網站](virtual-networks-acl.md)
#### [PowerShell](virtual-networks-acl-powershell.md)

## 管理
### 網路安全性群組
#### [入口網站](virtual-network-manage-nsg-arm-portal.md)
#### [PowerShell](virtual-network-manage-nsg-arm-ps.md)
#### [CLI](virtual-network-manage-nsg-arm-cli.md)
#### [記錄檔](virtual-network-nsg-manage-log.md)
### 虛擬機器
#### [檢視及修改主機名稱](virtual-networks-viewing-and-modifying-hostnames.md)
#### [將 VM 移至不同的子網路](virtual-networks-move-vm-role-to-subnet.md)

## 疑難排解
### 網路安全性群組
#### [入口網站](virtual-network-nsg-troubleshoot-portal.md)
#### [PowerShell](virtual-network-nsg-troubleshoot-powershell.md)
### 路由
#### [入口網站](virtual-network-routes-troubleshoot-portal.md)
#### [PowerShell](virtual-network-routes-troubleshoot-powershell.md)

# 參考
## [PowerShell (Resource Manager)](https://msdn.microsoft.com/library/mt163510(v=azure.300))
## [PowerShell (傳統)](https://msdn.microsoft.com/library/mt270335(v=azure.300))
## [Azure CLI](/cli/azure/)
## [Java](/java/api/)
## [REST (Resource Manager)](https://msdn.microsoft.com/library/mt163658.aspx)
## [REST (傳統)](https://msdn.microsoft.com/library/jj157182.aspx)


# 相關參考
## [虛擬機器](/azure/virtual-machines/)
## [應用程式閘道](/azure/application-gateway/)
## [Azure DNS](/azure/dns/)
## [流量管理員](/azure/traffic-manager/)
## [負載平衡器](/azure/load-balancer/)
## [VPN 閘道](/azure/vpn-gateway/)
## [ExpressRoute](/azure/expressroute/)

# 資源
## [網路部落格](http://azure.microsoft.com/blog/topics/networking)
## [網路論壇](https://social.msdn.microsoft.com/Forums/azure/home?forum=WAVirtualMachinesVirtualNetwork)
## [價格](https://azure.microsoft.com/pricing/details/virtual-network)
## [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-virtual-network)


<!--HONumber=Feb17_HO2-->


