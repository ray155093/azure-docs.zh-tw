如果您尚未這麼做，可以取得 [Azure 訂用帳戶免費試用](https://azure.microsoft.com/pricing/free-trial/)以及[連線至 Azure 帳戶](../articles/xplat-cli-connect.md)的 [Azure CLI](../articles/xplat-cli-install.md)。這麼做之後，即可執行下列命令，以快速建立擴展集︰

```bash
# make sure we are in Resource Manager mode (https://azure.microsoft.com/documentation/articles/resource-manager-deployment-model/)
azure config mode arm

# quick-create a scale set
#
# generic syntax:
# azure vmss quick-create -n SCALE-SET-NAME -g RESOURCE-GROUP-NAME -l LOCATION -u USERNAME -p PASSWORD -C INSTANCE-COUNT -Q IMAGE-URN
#
# example:
azure vmss quick-create -n negatvmss -g negatvmssrg -l westus -u negat -p P4ssw0rd -C 5 -Q Canonical:UbuntuServer:14.04.4-LTS:latest
```

如果您想要自訂位置或 image-urn，請查看 `azure location list` 和 `azure vm image {list-publishers|list-offers|list-skus|list|show}` 命令。

傳回此命令之後，即已建立擴展集。此擴展集的負載平衡器具有 NAT 規則，可將負載平衡器上的連接埠 50,000+i 對應至 VM i 上的連接埠 22。因此，找出負載平衡器的 FQDN 之後，即可透過 SSH 連接到 VM：

```bash
# (if you decide to run this as a script, please invoke using bash)

# list load balancers in the resource group we created
#
# generic syntax:
# azure network lb list -g RESOURCE-GROUP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb list -g negatvmssrg | grep negatvmssrg)
split_line=( $line )
lb_name=${split_line[1]}

# now that we have the name of the load balancer, we can show the details to find which Public IP (PIP) is associated to it
#
# generic syntax:
# azure network lb show -g RESOURCE-GROUP-NAME -n LOAD-BALANCER-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network lb show -g negatvmssrg -n $lb_name | grep loadBalancerFrontEnd)
split_line=( $line )
pip_name=${split_line[4]}

# now that we have the name of the public IP address, we can show the details to find the FQDN
#
# generic syntax:
# azure network public-ip show -g RESOURCE-GROUP-NAME -n PIP-NAME
#
# example with some quick-and-dirty grep-fu to store the result in a variable:
line=$(azure network public-ip show -g negatvmssrg -n $pip_name | grep FQDN)
split_line=( $line )
FQDN=${split_line[3]}

# now that we have the FQDN, we can use ssh on port 50,000+i to connect to VM i (where i is 0-indexed)
#
# example to connct via ssh into VM "0":
ssh -p 50000 negat@$FQDN
```

<!---HONumber=AcomDC_0413_2016-->