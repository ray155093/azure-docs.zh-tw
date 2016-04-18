虛擬機器擴展集可讓您以一個集合來管理多部 VM。概括而言，擴展集具有下列優缺點︰

優點：

1. 高可用性。每個擴展集都會將它的 VM 放入具有 5 個容錯網域 (FD) 和 5 個更新網域 (UD) 的可用性設定組，以確保可用性 (如需 FD 和 UD 的詳細資訊，請參閱 [VM 可用性](../articles/virtual-machines/virtual-machines-linux-manage-availability.md))。
2. 與 Azure 負載平衡器和應用程式閘道輕鬆整合。
3. 與 Azure 自動調整輕鬆整合。
4. 簡化 VM 的部署、管理和清除。
5. 支援常見 Windows 和 Linux 類別以及自訂映像。

缺點：

1. 無法將資料磁碟連接至擴展集中的 VM 執行個體。相反地，必須使用 Blob 儲存體、Azure 檔案、Azure 資料表或其他儲存體解決方案。

## 使用 Azure CLI 快速建立

如果您尚未這麼做，可以取得 [Azure 訂用帳戶免費試用](https://azure.microsoft.com/pricing/free-trial/)以及[連線至 Azure 帳戶](../articles/xplat-cli-connect.md)的 [Azure CLI](../articles/xplat-cli-install.md)。這麼做之後，即可執行下列命令，以快速建立 VM 擴展集︰

```bash
# make sure we are in resource manager mode 
azure config mode arm

# quick-create a VM scale set
#
# generic syntax:
# azure vmss quick-create -n VMSS-NAME -g RESOURCE-GROUP-NAME -l LOCATION -u USERNAME -p PASSWORD -C INSTANCE-COUNT -Q IMAGE-URN
#
# example:
azure vmss quick-create -n negatvmss -g negatvmssrg -l westus -u negat -p P4$$w0rd -C 5 -Q Canonical:UbuntuServer:14.04.4-LTS:latest
```

如果您想要自訂位置或 image-urn，請查看 `azure location list` 和 `azure vm image {list-publishers|list-offers|list-skus|list|show}` 命令。

傳回此命令之後，即已建立擴展集。此擴展集的負載平衡器具有 NAT 規則，可將負載平衡器上的連接埠 50,000+i 對應至 VM i 上的連接埠 22。因此，找出負載平衡器的 FQDN 之後，即可 SSH 到 VM：

```bash
# list load balancers in the resource group we created
#
# generic syntax:
# azure network lb list -g RESOURCE-GROUP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb list -g nsgvmsrg | grep nsgvmssrg)
split_line=( $line )
lb_name=${split_line[1]}

# now that we have the name of the load balancer, we can show the details to find which Public IP (PIP) is associated with it
#
# generic syntax:
# azure network lb show -g RESOURCE-GROUP-NAME -n LB-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb show -g nsgvmssrg -n $lb_name | grep loadBalancerFrontEnd)
split_line=( $line )
pip_name=${split_line[4]}

# now that we have the name of the public IP address, we can show the details to find the FQDN
#
# generic syntax:
# azure network public-ip show -g RESOURCE-GROUP-NAME -n PIP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network public-ip show -g nsgvmssrg -n $pip_name | grep FQDN)
split_line=( $line )
FQDN=${split_line[3]}

# now that we have the FQDN, we can ssh on port 50,000+i to ssh into VM i (where i is 0-indexed)
#
# example to ssh into VM "0":
ssh -p 50000 $FQDN
```

## 後續步驟

如需一般資訊，請參閱 [VM 擴展集的主要登陸頁面](https://azure.microsoft.com/services/virtual-machine-scale-sets/)。

如需文件，請參閱 [VM 擴展集的主要文件頁面](https://azure.microsoft.com/documentation/services/virtual-machines-scale-sets/)。

如需使用 VM 擴展集的範例 Azure Resource Manager 範本，請在 [Azure 快速入門範本 github 儲存機制](https://github.com/Azure/azure-quickstart-templates)中搜尋 "vmss"。

<!---HONumber=AcomDC_0406_2016-->